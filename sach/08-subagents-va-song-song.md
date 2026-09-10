# Chương 08 — Subagents & chạy song song

> [← Chương 07](07-hooks-va-guardrails.md) | [Mục lục](README.md) | [Chương 09 →](09-verification-feedback-loop.md)

---

## 8.1. Vì sao cần chia việc

Một phiên làm việc có ngân sách context hữu hạn. Khi một agent phải vừa đọc mười file,
vừa nhớ yêu cầu, vừa theo dõi kế hoạch, vừa đọc log lỗi, thì hai chuyện xảy ra:

- Thông tin đầu phiên bị đẩy ra khỏi tầm nhìn.
- Chi phí mỗi lượt tăng vì phải mang theo mọi thứ đã đọc.

Subagent giải quyết bằng cách **cô lập việc tốn ngữ cảnh**:

```
Agent chính (giữ mạch công việc)
   │
   ├── Subagent A: tìm kiếm rộng   → chỉ trả về kết luận
   ├── Subagent B: phân tích module → chỉ trả về tóm tắt
   └── Subagent C: review độc lập   → chỉ trả về danh sách vấn đề
```

Điểm mấu chốt: subagent đọc mười file nhưng **chỉ trả về kết luận**. Agent chính nhận
một đoạn tóm tắt thay vì mười file.

---

## 8.2. Khi nào dùng subagent

| Tình huống | Có nên dùng |
|---|---|
| Tìm kiếm rộng, chưa biết nằm ở đâu | **Có** — đọc nhiều, kết luận ngắn |
| Cần góc nhìn độc lập (review, phản biện) | **Có** — không bị neo vào lập luận cũ |
| Nhiều việc thật sự độc lập, chạy song song được | **Có** |
| Đã biết chính xác file cần đọc | Không — đọc thẳng nhanh hơn |
| Việc cần bám sát mạch hội thoại | Không — subagent không có ngữ cảnh đó |
| Một câu hỏi tra cứu đơn giản | Không — chi phí khởi tạo lớn hơn lợi ích |

> **Đừng dùng multi-agent chỉ vì nó "trông rất AI".** Ba agent cho một task sửa CSS
> tốn nhiều hơn tiết kiệm.

---

## 8.3. Ví dụ thật — `/auto-review` với đội chuyên gia

Skill `auto-review` của PM-AGENT là ví dụ tốt về subagent dùng đúng chỗ.

### Kiến trúc ba mức

```
full:  5 specialist agent (song song) → tổng hợp → Codex double-check
light: 3 specialist agent (song song) → tổng hợp
quick: review ngay trong ngữ cảnh chính
```

### Tự chọn độ sâu theo tài nguyên còn lại

| Điều kiện | Mức | Lý do |
|---|---|---|
| Codex MCP sẵn sàng + context còn > 50% | **full** | Đội đầy đủ + kiểm chéo |
| Codex không có hoặc context < 50% | **light** | Đội rút gọn |
| Context còn < 20% | **quick** | Tiết kiệm context |

Đây là chi tiết đáng học nhất: skill **tự đo tài nguyên còn lại và hạ cấp cho phù hợp**,
thay vì luôn chạy chế độ nặng nhất rồi cạn context giữa chừng.

### Chọn công cụ theo chi phí

Ghi chú thiết kế trong chính skill:

```
Codex tốn nhiều token → chỉ dùng làm "double-check cuối cùng".
Team chuyên gia Claude chạy song song = chi phí 0, tốc độ cao.
```

Nguyên tắc tổng quát: **cái rẻ chạy rộng, cái đắt chạy hẹp**. Năm agent quét toàn bộ;
công cụ đắt tiền chỉ xác nhận kết luận cuối.

### Chọn quy mô theo quy mô thay đổi

| Tình huống | Cách review |
|---|---|
| ≥ 3 file, hoặc đụng code security/auth | `/auto-review` (full hoặc light) |
| 1–2 file, code thông thường | `/{prefix}-code-review` (nhanh, đủ dùng) |
| Chỉ quan tâm bảo mật | `/{prefix}-security-audit` (chuyên sâu OWASP) |
| Thay đổi nhỏ + ít context | `/auto-review --quick` |

---

## 8.4. Subagent không có ngữ cảnh của bạn

Đây là nguồn lỗi phổ biến nhất khi giao việc cho subagent.

Subagent bắt đầu từ số không. Nó không biết:

- Quyết định thiết kế đã chốt trong quá khứ
- Thứ gì không được đụng vào
- Ranh giới phạm vi giữa các giai đoạn dự án
- Những gì đã thử và đã loại

Hệ quả: nó sẽ vô tư phá vỡ những thứ đó, một cách rất tự tin.

**Ví dụ thật — luật `{prefix}-verify-before-change.md` của PM-AGENT nói thẳng:**

```markdown
## Khi delegate cho sub-agent

Khi giao việc thay đổi spec cho sub-agent, phải đưa rõ vào prompt các facts đã chốt
(items không được bỏ, design decisions đã chốt, Phase boundaries, v.v.).

Sub-agent không có context đầy đủ — nếu không brief, nó sẽ thực hiện thay đổi
phá vỡ những quyết định quá khứ.
```

### Khung giao việc cho subagent

```markdown
## Nhiệm vụ
[Một việc cụ thể, có điểm kết thúc rõ]

## Ngữ cảnh cần biết
- Dự án: [một dòng]
- Stack: [một dòng]
- File/module liên quan: [danh sách]

## Ràng buộc đã chốt — KHÔNG được phá
- [Quyết định 1 và lý do]
- [Quyết định 2 và lý do]

## Phạm vi
- Được làm: [...]
- KHÔNG được làm: [...]

## Đầu ra mong đợi
[Định dạng cụ thể: danh sách vấn đề, bản tóm tắt, đường dẫn file...]
```

Mục "Ràng buộc đã chốt" là mục quyết định. Không có nó, subagent sẽ "cải thiện" đúng
những thứ mà cả nhóm đã cân nhắc và cố tình để nguyên.

---

## 8.5. Bàn giao giữa các agent

Khi công việc đi qua nhiều agent hoặc nhiều công cụ, cần một tài liệu bàn giao chuẩn.

**Ví dụ thật — `{prefix}-codex-handoff.md`** quy định file `CODEX_BRIEF.md` cho việc bàn
giao từ agent viết spec/test sang agent implement:

```markdown
# Codex Brief: <Tên feature>

## Task
<1-2 câu. Không mô tả chi tiết — agent nhận việc sẽ tự đọc spec.>

## Spec files (đọc theo thứ tự)
1. docs/feature-specs/<module>/01-backend-spec.md  — business rules, ACs
2. docs/feature-specs/<module>/02-api-design.md    — endpoints, schemas
3. docs/feature-specs/<module>/03-database-mapping.md
4. docs/feature-specs/<module>/04-frontend-spec.md  ← bỏ nếu không có UI

## Files cần tạo/sửa
| File | Action | Ghi chú |
|------|--------|---------|
| app/models/<name>.py | CREATE | SQLAlchemy model |
| alembic/versions/xxxx.py | CREATE | migration |
| app/main.py | EDIT | đăng ký router |

## Test commands
<copy chính xác từ TEST_OVERVIEW.md — không tự viết lại>

## Definition of Done
- [ ] Tất cả ut-*.yaml pass
- [ ] Tất cả browser-*.yaml pass
- [ ] Commit đúng commit plan
```

Hai quy tắc điền đáng chú ý:

- **"Task viết 1-2 câu, không lặp lại spec"** — bản brief chỉ đường, không chép nội dung.
  Chép thì sẽ có hai nguồn sự thật và chúng sẽ lệch nhau.
- **"Test commands copy chính xác, không tự viết lại"** — lệnh viết lại theo trí nhớ là
  lệnh sai.

---

## 8.6. Chạy nhiều phiên song song

Khi việc đủ lớn và đủ độc lập, có thể chạy nhiều phiên cùng lúc:

```
Terminal 1 → Feature A
Terminal 2 → Feature B
Terminal 3 → Sửa lỗi
Terminal 4 → Viết test
```

Vấn đề: nhiều phiên cùng sửa một thư mục làm việc thì giẫm chân nhau.

### Git worktree — mỗi phiên một thư mục làm việc

```bash
git worktree add ../repo-feature-a feature/a
git worktree add ../repo-feature-b feature/b
```

```
repo/                    ← nhánh chính
repo-feature-a/          ← phiên 1, nhánh riêng
repo-feature-b/          ← phiên 2, nhánh riêng
```

Mỗi worktree có thư mục làm việc riêng nhưng dùng chung kho Git. Hai phiên sửa file
độc lập, không xung đột, và merge lại theo đường Git bình thường.

---

## 8.7. Kiểm tra trước khi chạy song song

Chạy song song chỉ tiết kiệm thời gian khi hai việc **không đụng cùng một chỗ** và
**không phải chờ nhau**. Kiểm điều đó *trước*, đừng phát hiện lúc đã viết nửa đường.

**Ví dụ thật — skill `task-parallel-planner`** làm đúng việc này.

### Ước lượng phạm vi file theo độ tin cậy giảm dần

```
1. Đường dẫn ghi thẳng trong mô tả task hoặc Q&A   ← tin cậy nhất, dùng nguyên văn
2. Spec liên quan (mục "Files cần tạo/sửa", commit plan)
3. Grep sơ bộ theo từ khoá:
   grep -rln "<từ khoá>" app/ tools/ static/ | head -20
```

Rồi đối chiếu các tập file: giao nhau nhiều → không nên song song.

### Nguyên tắc của skill này

```markdown
- Chỉ gợi ý và cảnh báo — quyết định là của người dùng.
- Không tự code, không tự nhận task, không đổi status.
- Không suy đoán phạm vi ảnh hưởng. Chỗ nào không đủ dữ kiện thì ghi thẳng
  "chưa xác định được", không đoán bừa rồi kết luận "an toàn".
- Song song ≠ gộp chung. Mỗi task vẫn giữ commit riêng, test doc riêng,
  vòng verify riêng.
```

Nguyên tắc thứ ba đáng nhấn mạnh. Kết luận "an toàn" dựa trên dữ kiện thiếu là kết
luận nguy hiểm nhất — nó tạo cảm giác đã kiểm tra trong khi chưa.

Nguyên tắc thứ tư ngăn một sai lầm hay gặp: làm song song rồi gộp vào một commit khổng
lồ, mất hết lợi ích của [chia nhỏ commit](04-workflow-plan-execute.md).

---

## 8.8. Bẫy khi làm việc song song

> **Bẫy 1 — Phiên khác commit chồng lên thay đổi của bạn.**
> Sự cố thật đã ghi trong PM-AGENT: repo có phiên khác đang commit song song, và thay
> đổi của mình trong file dùng chung bị cuốn vào commit của người khác.
> **Cách sửa:** `git log` **trước và sau** khi làm; kiểm `git status` toàn repo trước
> mỗi commit, đừng giả định phạm vi.

> **Bẫy 2 — File dùng chung bị nhiều việc động vào.**
> Ví dụ thật: `tests/fixtures/task.py` chứa fixture của nhiều task song song. Cách xử
> lý đã dùng: sao lưu, tách theo mốc đánh dấu rồi ghép lại — thay vì `git add -p` dễ
> nhầm.
> **Cách phòng:** nhận diện file dùng chung ngay ở bước lập kế hoạch, và xếp lịch cho
> hai việc đụng nó chạy nối tiếp thay vì song song.

> **Bẫy 3 — Môi trường dùng chung bị đổi cấu hình.**
> Ví dụ thật: chạy browser test làm container ứng dụng chuyển sang môi trường test, và
> phiên khác đang dùng container đó nhận lỗi 401 khó hiểu.
> **Cách sửa:** khôi phục môi trường ngay sau khi chạy; ghi bước khôi phục vào chính
> script chạy test.

> **Bẫy 4 — Song song việc phụ thuộc nhau.**
> Việc B cần schema của việc A. **Cách sửa:** vẽ đồ thị phụ thuộc trước; chỉ song song
> những nhánh không nối với nhau.

---

## 8.9. Chọn hình thức: một agent, subagent, hay đội

| Tình huống | Hình thức |
|---|---|
| Việc đơn giản, một mạch | Một agent |
| Việc phụ độc lập, tốn ngữ cảnh (tìm kiếm, phân tích rộng) | Subagent |
| Cần góc nhìn độc lập (review, phản biện) | Subagent với ngữ cảnh sạch |
| Nhiều nhánh việc độc lập, chạy được song song | Nhiều subagent |
| Nhiều nhánh cần trao đổi qua lại | Đội agent (tốn kém — cân nhắc kỹ) |
| Nhiều tính năng lớn, nhiều ngày | Nhiều phiên + git worktree |

> Đội agent tốn nhiều tài nguyên và context hơn hẳn. Chỉ dùng khi lợi ích thật sự rõ.

---

## 8.10. Bài tập

**Bài 1 — Subagent tìm kiếm.**
Giao cho một subagent việc: *"Tìm mọi chỗ trong dự án đang xử lý phân trang, trả về
danh sách file:dòng và mô tả ngắn cách tiếp cận của từng chỗ."* So sánh lượng ngữ cảnh
tiêu tốn với việc tự đọc từng file.

**Bài 2 — Brief đầy đủ.**
Viết bản brief theo khung ở mục 8.4 cho một việc bạn định giao. Kiểm: người lạ đọc bản
này có làm được không mà không hỏi thêm câu nào?

**Bài 3 — Phân tích song song.**
Lấy hai việc bạn định làm cùng lúc. Liệt kê tập file dự kiến của từng việc. Nếu giao
nhau, xếp lịch nối tiếp thay vì song song.

---

## Tóm tắt chương

- Subagent để **cô lập việc tốn ngữ cảnh**: đọc nhiều, trả về kết luận ngắn.
- Subagent với ngữ cảnh sạch là công cụ tốt nhất cho **review và phản biện độc lập**.
- Subagent **không biết** quyết định đã chốt — phải brief rõ, nếu không nó sẽ phá.
- Bàn giao cần tài liệu chuẩn: chỉ đường, không chép lại spec; lệnh test copy nguyên văn.
- `git worktree` cho phép nhiều phiên chạy song song mà không giẫm chân nhau.
- Kiểm phạm vi file trùng nhau **trước** khi chạy song song; thiếu dữ kiện thì ghi
  "chưa xác định", không kết luận "an toàn".
- Song song ≠ gộp chung: mỗi việc vẫn giữ commit riêng, test riêng, vòng verify riêng.

> Chương tiếp: [09 — Verification & Feedback loop](09-verification-feedback-loop.md)
