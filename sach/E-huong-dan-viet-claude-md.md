# Phụ lục E — Hướng dẫn viết `CLAUDE.md` hiệu quả

> [← Phụ lục D](D-phu-luc-thuat-ngu.md) | [Mục lục](README.md) | [Phụ lục F →](F-phu-luc-bang-quyet-dinh.md)

> Bài này viết từ một góc nhìn khác với chương 03. Chương 03 nói `CLAUDE.md` **là gì**
> và đặt ở đâu. Bài này nói **dòng nào thực sự đổi hành vi của AI, dòng nào bị bỏ qua,
> và vì sao** — dựa trên cách một model thực sự tiếp nhận file đó.

---

## E.1. Trước hết: file này được dùng như thế nào

Ba điều cần hiểu trước khi viết dòng đầu tiên. Không hiểu chúng thì mọi lời khuyên về
sau chỉ là mẹo vặt.

### 1. Nó không phải "bộ nhớ", nó là văn bản đọc lại liên tục

Cách gọi "bộ nhớ dự án" gây hiểu lầm. Thực tế `CLAUDE.md` được đặt vào ngữ cảnh ở đầu
phiên và **nằm đó suốt cuộc trò chuyện**, cạnh tranh chỗ với mọi thứ khác: file vừa
đọc, đầu ra lệnh, lịch sử hội thoại.

Hệ quả trực tiếp:

- Mỗi dòng bạn viết là chi phí trả ở **mọi phiên**, kể cả phiên không liên quan.
- Một file 800 dòng không khiến AI hiểu dự án gấp bốn lần file 200 dòng. Nó khiến mọi
  thứ khác có ít chỗ hơn.
- Trong phiên dài, thông tin gần đây có sức nặng thực tế lớn hơn thông tin ở đầu. Lời
  dặn quan trọng bị đẩy xa dần khỏi điểm ra quyết định.

> **Kết luận thực hành:** viết `CLAUDE.md` như viết một biển báo giao thông, không phải
> như viết sổ tay nhân viên.

### 2. Code hiện có mạnh hơn lời dặn

Đây là điều quan trọng nhất trong cả bài, và hầu như không ai viết ra.

Khi `CLAUDE.md` nói một đằng nhưng 200 file trong repo làm một nẻo, **code thường thắng**.
Không phải vì lời dặn bị bỏ qua có chủ ý, mà vì viết code mới luôn kèm việc đọc code
xung quanh, và code xung quanh là ví dụ cụ thể, ngay trước mắt, trong khi lời dặn là
một dòng trừu tượng ở xa.

**Ví dụ hỏng:**

```markdown
Dùng `X | None`, không dùng `Optional[X]`.
```

Trong repo có 200 file dùng `Optional[X]`. Kết quả: code mới sẽ lẫn lộn cả hai kiểu,
và bạn sẽ nghĩ AI "không nghe lời".

**Ví dụ đúng:**

```markdown
Type hints: dùng `X | None`, KHÔNG dùng `Optional[X]`.

⚠️ Code cũ trong `app/modules/sync/` và `app/adapters/` vẫn dùng `Optional[X]`.
Đây là nợ kỹ thuật, KHÔNG phải mẫu để bắt chước.
- Code MỚI: luôn `X | None`
- Code CŨ: chỉ sửa khi đang chạm vào dòng đó vì lý do khác, không sửa hàng loạt
```

Đoạn thứ hai hoạt động vì nó **dự đoán trước xung đột** mà AI sẽ gặp và giải quyết sẵn.

> **Quy tắc:** mọi luật đi ngược lại đa số code hiện có đều phải nói rõ điều đó, nếu
> không nó sẽ thua.

### 3. Điều cấm mạnh hơn điều khuyên

Một câu khuyên ("nên viết code dễ đọc") không loại bỏ nhánh hành vi nào — mọi phương án
đều có thể tự nhận là dễ đọc. Một câu cấm ("KHÔNG gọi SDK trực tiếp, luôn qua AIRouter")
cắt bỏ hẳn một hướng đi, kể cả hướng phổ biến nhất trên internet.

Mặc định của model được hình thành từ cách làm phổ biến nhất. Việc của `CLAUDE.md`
không phải dạy lại mọi thứ, mà là **đánh dấu những chỗ dự án của bạn khác với mặc định đó**.

```
CLAUDE.md hiệu quả = danh sách những chỗ dự án này KHÁC với cách làm phổ biến
```

Đây là nguyên tắc lọc mạnh nhất khi bạn phân vân có nên đưa một mục vào hay không:
**nếu bỏ dòng này ra thì AI vẫn làm đúng như vậy → bỏ đi.**

---

## E.2. Bốn loại nội dung — chỉ hai loại đáng viết

| Loại | Ví dụ | Có nên viết |
|---|---|---|
| **A. Sự thật không suy ra được từ repo** | "AI Router route theo rule trong DB, cache Redis 5 phút" | ✅ Bắt buộc |
| **B. Ràng buộc đi ngược mặc định** | "Dùng `typing.Protocol`, KHÔNG dùng ABC" | ✅ Bắt buộc |
| **C. Suy ra được từ repo** | "Dự án có thư mục `app/models/`" | ❌ Bỏ — tốn chỗ |
| **D. Lời khuyên chung chung** | "Viết code sạch, chú ý bảo mật" | ❌ Bỏ — không đổi gì |

Loại C thường chiếm 30–50% các file `CLAUDE.md` mới viết. AI đọc được cấu trúc thư mục
bằng một lệnh; nó không đọc được **vì sao** cấu trúc đó như vậy.

Loại D nguy hiểm hơn loại C: nó tạo cảm giác đã dặn dò, trong khi không thay đổi hành
vi nào. Kiểm nhanh: nếu một câu đúng với **mọi** dự án phần mềm trên đời, nó không
thuộc về `CLAUDE.md` của dự án bạn.

---

## E.3. Bảy loại dòng thực sự đổi hành vi

Sắp theo sức mạnh giảm dần.

### 1. Cặp đúng/sai đối chiếu

Mạnh nhất, vì nó vừa nêu luật vừa cho mẫu.

```markdown
# ĐÚNG — dùng AIRouter
response = await ai_router.execute(AIRequest(task_type="tagging", ...))

# SAI — gọi SDK trực tiếp
client = anthropic.Anthropic(api_key=...)
```

Bốn dòng này hiệu quả hơn ba đoạn văn giải thích kiến trúc.

### 2. Điều cấm cụ thể, kèm hậu quả

```markdown
KHÔNG `session.commit()` trực tiếp trong repository
→ transaction phải được quản lý ở tầng service, nếu không sẽ commit nửa chừng
  khi service còn đang xử lý các bước sau.
```

Hậu quả cụ thể làm hai việc: giải thích vì sao, và giúp suy luận đúng ở tình huống biên
mà luật chưa nói tới.

### 3. Bảng tra cứu

```markdown
| Loại công việc | Quy trình |
|---|---|
| Tạo feature mới | /new-feature |
| Fix bug | /{prefix}-debug |
| Đổi schema DB | /db-migration |
```

Bảng biến câu hỏi "giờ làm gì" thành một phép tra. Văn xuôi mô tả cùng nội dung đó yếu
hơn hẳn vì phải suy diễn mới ra hành động.

### 4. Con trỏ tới nguồn sự thật

```markdown
`docs/04-tech-stack.md` — NGUỒN SỰ THẬT cho mọi quyết định kỹ thuật, đọc trước khi code
```

Chỉ đường thay vì chép lại. Chi tiết được nạp **khi cần**, không nạp ở mọi phiên. Và
quan trọng hơn: chỉ có một bản duy nhất, nên không bao giờ lệch nhau.

### 5. Lệnh chạy được

```markdown
| Việc | Lệnh |
|---|---|
| Chạy test | `make test` |
| Test có browser | `make test-browser` (cần `make dev-d` trước) |
| Tạo migration | `make migrate-create` |
```

Bọc qua `make` thay vì ghi nguyên câu lệnh dài: giao diện ổn định, không lỗi thời khi
cờ bên dưới đổi.

### 6. Ngưỡng số cụ thể

```markdown
Commit: tối đa 5 file, 300 dòng tổng diff. Vượt → chia nhỏ trước khi làm.
Coverage: service ≥ 80%, router ≥ 70%, code bảo mật ≥ 90%.
```

Con số kiểm được. "Commit nhỏ thôi" thì không.

### 7. Ranh giới phạm vi

```markdown
Chỉ sửa những gì được yêu cầu. KHÔNG tự refactor, thêm comment, hay "cải thiện"
code xung quanh.
```

Đây là dòng cứu nhiều thời gian review nhất trong một file `CLAUDE.md` điển hình.

---

## E.4. Sáu thứ nên bỏ ra khỏi `CLAUDE.md`

| Thứ | Vì sao bỏ | Đưa đi đâu |
|---|---|---|
| Mô tả cấu trúc thư mục cơ bản | AI đọc được bằng một lệnh | Bỏ hẳn |
| Nguyên tắc lập trình phổ quát | Không loại trừ nhánh hành vi nào | Bỏ hẳn |
| Quy trình dài cho một loại việc | Chỉ dùng khi làm loại việc đó | Skill / command ([ch.06](06-skills-va-slash-commands.md)) |
| Lời dặn hay bị quên dù đã viết | Lời dặn không tự thực thi | Hook ([ch.08](08-hooks-va-guardrails.md)) |
| Đặc tả chi tiết một tính năng | Quá cụ thể, thay đổi thường xuyên | `docs/` + con trỏ |
| Sở thích cá nhân của bạn | Người khác clone repo không cần | `~/.claude/CLAUDE.md` |

Hàng thứ tư đáng nhấn mạnh. Nếu một dòng trong `CLAUDE.md` đã bị bỏ sót **từ hai lần
trở lên**, viết to hơn hay in đậm không giải quyết được gì. Vấn đề nằm ở cơ chế: lời
dặn cần được nhớ, hook thì không.

---

## E.5. Thứ tự các mục có ảnh hưởng

Không phải mọi vị trí trong file đều như nhau. Đầu và cuối file có sức nặng thực tế lớn
hơn phần giữa.

Thứ tự đề xuất:

```
1. Dự án là gì            ← ngắn, 2-3 câu, định hướng mọi đánh đổi
2. Điều KHÔNG được làm    ← đặt sớm, đây là phần đắt nhất nếu vi phạm
3. Tech stack (chốt)      ← danh sách đóng
4. Patterns bắt buộc      ← kèm cặp đúng/sai
5. Quy ước & lệnh         ← bảng tra cứu
6. Con trỏ tới docs       ← phần tham chiếu, nạp khi cần
7. Checklist trước khi báo xong  ← đặt cuối, gần điểm ra quyết định nhất
```

Hai chi tiết đáng chú ý:

- **Mục "Điều KHÔNG được làm" đặt sớm**, không nhét xuống cuối như phần phụ lục. Đây
  là mục có tỷ lệ giá trị trên số dòng cao nhất.
- **Checklist đặt cuối cùng.** Nó được dùng ở thời điểm cuối của công việc, và vị trí
  cuối file giữ nó gần với thời điểm đó hơn.

---

## E.6. Quy trình xây dựng — năm bước

Đừng viết `CLAUDE.md` bằng cách ngồi nghĩ ra mọi thứ. Cách đó tạo ra file dài, đầy loại
C và D, và thiếu đúng những thứ quan trọng.

### Bước 1 — Viết bản tối thiểu (30–50 dòng)

Chỉ sáu mục, mỗi mục vài dòng:

```markdown
# <Dự án> — Quy tắc làm việc

## Dự án
<2-3 câu: làm gì, cho ai>

## Tech stack (CHỐT)
<danh sách, không giải thích>

## Lệnh
| Chạy dev | ... |
| Chạy test | ... |

## Điều KHÔNG được làm
- <3-5 điều đắt nhất nếu vi phạm>

## Nguồn sự thật
- `docs/...` — <cho quyết định gì>
```

Đừng viết nhiều hơn ở bước này. Bạn chưa biết cái gì thực sự cần.

### Bước 2 — Chạy Codebase Q&A và ghi lại chỗ AI đoán sai

Mở phiên mới, hỏi 10–15 câu về dự án ([mục 2.5](02-prompting-va-context.md)). Mỗi lần
AI trả lời sai hoặc phải đoán — đó là một lỗ hổng ngữ cảnh có thật, không phải giả định.

Danh sách chỗ đoán sai chính là nội dung bước tiếp theo.

### Bước 3 — Bổ sung theo từng lần vấp thật

Quy tắc bổ sung:

```
AI làm sai một lần   → chưa làm gì (có thể ngẫu nhiên)
AI làm sai lần thứ 2 → thêm một dòng vào CLAUDE.md, có cặp đúng/sai
AI vẫn sai sau đó    → vấn đề không nằm ở CLAUDE.md → xem E.8
```

Cách này giữ file chỉ chứa những gì đã được chứng minh là cần.

### Bước 4 — Kiểm chứng bằng phiên mới

Xem mục E.7.

### Bước 5 — Rà định kỳ

Mỗi quý, hoặc sau mỗi lần refactor lớn. Xem mục E.9.

---

## E.7. Cách kiểm chứng một `CLAUDE.md`

File này cần được test như code. Ba cách, từ rẻ đến đắt.

### Test 1 — Đọc hiểu (2 phút)

Mở phiên mới, hỏi:

```
Đọc CLAUDE.md và trả lời gọn:
1. Dự án này làm gì?
2. Chạy test bằng lệnh nào?
3. Ba điều tuyệt đối không được làm là gì?
4. Nếu tôi cần thêm một nguồn dữ liệu mới thì phải theo pattern nào?
5. Có chỗ nào trong file này mâu thuẫn nhau không?
```

Câu 5 hay bắt được lỗi thật, đặc biệt ở file đã tích luỹ nhiều tháng.

### Test 2 — Bẫy hành vi (10 phút)

Giao một task nhỏ **có sẵn cạm bẫy** mà `CLAUDE.md` lẽ ra phải chặn:

```
Thêm một hàm gọi API OpenAI để tóm tắt nội dung task.
```

Nếu `CLAUDE.md` có luật "không gọi SDK trực tiếp, luôn qua AIRouter", phản ứng đúng
phải là dùng AIRouter hoặc hỏi lại. Nếu AI gọi thẳng SDK → luật đó đang không hoạt động.

Đây là test giá trị nhất vì nó đo **hành vi**, không đo trí nhớ.

### Test 3 — Kiểm chứng chéo bằng người mới (30 phút)

Đưa `CLAUDE.md` cho một người chưa từng làm dự án này. Hỏi họ ba câu ở Test 1. Chỗ họ
không trả lời được thường cũng là chỗ AI phải đoán.

---

## E.8. Khi `CLAUDE.md` không hoạt động — chẩn đoán

Một luật đã viết mà vẫn bị vi phạm. Trước khi viết to hơn, chẩn đoán theo bảng này:

| Triệu chứng | Nguyên nhân thật | Cách sửa |
|---|---|---|
| Luật bị bỏ qua ở phiên dài, đầu phiên vẫn đúng | Ngân sách context, luật bị đẩy xa | Rút gọn file; chuyển thành hook nếu quan trọng |
| Code mới vẫn theo kiểu cũ | Code hiện có mạnh hơn lời dặn | Nói rõ "code cũ là nợ kỹ thuật, không phải mẫu" (mục E.1) |
| AI làm đúng khi được hỏi, sai khi tự làm | Luật ở dạng kiến thức, không ở dạng bước hành động | Chuyển thành checklist hoặc bước trong quy trình |
| Luật bị hiểu theo nghĩa khác | Diễn đạt trừu tượng | Thêm cặp đúng/sai cụ thể |
| Luật đúng lúc này sai lúc khác | Thiếu điều kiện áp dụng | Ghi rõ "áp dụng khi..., không áp dụng khi..." |
| Vi phạm gây hậu quả không hoàn tác được | Cơ chế quá yếu cho mức rủi ro | Chặn cứng ở tầng hệ thống, không dừng ở lời dặn |

Thang leo thang khi một luật liên tục thất bại:

```
Câu trong CLAUDE.md
      ↓ vẫn bị quên
Bước trong skill/command của loại việc đó
      ↓ vẫn bị quên
Hook do harness chạy
      ↓ vẫn bị vi phạm
Chặn cứng: phạm vi quyền, quyền file, CI gác cổng
```

→ [Chương 08, mục 8.10](08-hooks-va-guardrails.md)

---

## E.9. Bảo trì: ngữ cảnh lỗi thời nguy hiểm hơn không có ngữ cảnh

`CLAUDE.md` nói `make test-unit` nhưng target đó đã đổi tên. AI chạy lệnh sai, nhận lỗi,
rồi kết luận môi trường hỏng — và đi sửa nhầm chỗ trong nửa tiếng.

Một file không có thông tin đó thì AI sẽ hỏi. Một file có thông tin sai thì AI tin.

### Ba thời điểm bắt buộc rà lại

| Khi | Rà cái gì |
|---|---|
| Đổi tên lệnh, đổi cấu trúc thư mục, đổi tech | Sửa `CLAUDE.md` **trong cùng commit** |
| Sau refactor lớn | Mọi con trỏ tới file/module có còn đúng không |
| Định kỳ mỗi quý | Toàn bộ, theo checklist dưới |

### Checklist rà quý

```
[ ] File còn dưới ngưỡng độ dài không?
[ ] Mọi lệnh trong file còn chạy được không? (thử từng lệnh)
[ ] Mọi đường dẫn còn tồn tại không?
[ ] Có luật nào không còn đúng nữa không? → xoá
[ ] Có luật nào mâu thuẫn luật khác không?
[ ] Có mục nào đã chuyển thành skill/hook mà quên xoá bản cũ không?
[ ] Có mục nào thuộc loại C hoặc D (mục E.2) lọt vào không?
```

Điểm quan trọng: **xoá cũng là bảo trì**. Một file chỉ tăng chưa bao giờ giảm sẽ chết
vì trọng lượng của chính nó.

---

## E.10. Độ dài: con số thực tế

| Quy mô dự án | Vùng hợp lý | Ngưỡng cảnh báo |
|---|---|---|
| Script, thư viện nhỏ | 20–50 dòng | > 100 |
| Ứng dụng cỡ vừa | 80–200 dòng | > 350 |
| Hệ thống lớn, nhiều module | 150–300 dòng ở gốc + `CLAUDE.md` con | > 400 ở một file |

Vượt ngưỡng thì tách theo thứ tự ưu tiên này:

```
1. Quy trình cho một loại việc  → skill / command
2. Đặc tả chi tiết              → docs/ + con trỏ
3. Luật theo chủ đề             → .claude/rules/<chủ-đề>.md
4. Luật riêng một module        → <module>/CLAUDE.md
5. Sở thích cá nhân             → ~/.claude/CLAUDE.md
```

---

## E.11. Mẫu đầy đủ — sao chép và điền

```markdown
# <Tên dự án> — Quy tắc làm việc

## Dự án
<2-3 câu: hệ thống làm gì, phục vụ ai, quy mô người dùng>

**Nguồn sự thật:**
- `docs/<...>.md` — <cho loại quyết định nào>
- `docs/<...>.md` — <cho loại quyết định nào>

---

## Điều KHÔNG được làm

- KHÔNG <hành động> — <hậu quả cụ thể>
- KHÔNG <hành động> — <hậu quả cụ thể>
- Chỉ sửa những gì được yêu cầu. KHÔNG tự refactor / thêm comment /
  "cải thiện" code xung quanh.
- Khi thêm thư viện mới: hỏi trước.

---

## Tech Stack (CHỐT — không đổi nếu không hỏi)

Language:  <...>
Framework: <...>
ORM / DB:  <...>
Frontend:  <...>
Deploy:    <...>

---

## Patterns bắt buộc

### 1. <Tên pattern>

# ĐÚNG
<3-5 dòng code>

# SAI
<3-5 dòng code>

Vì sao: <một câu>

### 2. <Tên pattern>
<tương tự>

---

## Quy ước

| Loại | Kiểu | Ví dụ |
|---|---|---|
| File | snake_case | `backlog_adapter.py` |
| Class | PascalCase | `BacklogAdapter` |
| Cột DB | snake_case | `content_hash` |

⚠️ Code cũ trong `<đường dẫn>` không theo quy ước này — đó là nợ kỹ thuật,
KHÔNG phải mẫu để bắt chước.

---

## Lệnh

| Việc | Lệnh |
|---|---|
| Chạy dev | `make dev` |
| Chạy test | `make test` |
| Test browser | `make test-browser` (cần `make dev-d` trước) |
| Tạo migration | `make migrate-create` |

---

## Giới hạn

- Commit: ≤ 5 file, ≤ 300 dòng tổng diff. Vượt → chia nhỏ trước khi làm.
- Coverage: service ≥ 80%, router ≥ 70%, code bảo mật ≥ 90%.

---

## Quy trình theo loại việc

| Loại việc | Dùng |
|---|---|
| Feature mới | `/new-feature` |
| Fix bug | `/debug` |
| Đổi schema | `/db-migration` |

---

## Checklist trước khi báo xong

- [ ] Đã chạy test và xem đầu ra thật?
- [ ] Đã kiểm chưa vi phạm mục "Điều KHÔNG được làm"?
- [ ] Đã cập nhật tài liệu liên quan?
- [ ] Có phần nào chưa kiểm chứng được? → nói rõ, đừng bỏ qua
```

---

## E.12. Mười lỗi thường gặp

| # | Lỗi | Dấu hiệu | Sửa |
|---|---|---|---|
| 1 | Chép lại thứ repo đã có | Mô tả cấu trúc thư mục | Xoá |
| 2 | Lời khuyên phổ quát | "Viết code sạch", "chú ý bảo mật" | Xoá hoặc làm cụ thể |
| 3 | Luật đi ngược code hiện có mà không nói | Code mới vẫn theo kiểu cũ | Ghi rõ "code cũ là nợ kỹ thuật" |
| 4 | Chỉ có điều nên, không có điều cấm | AI làm theo cách phổ biến nhất | Thêm mục "KHÔNG được làm" |
| 5 | Luật không có lý do | Bị lách ngay khi bất tiện | Thêm hậu quả cụ thể |
| 6 | Nhét quy trình dài vào | File phình > 400 dòng | Tách thành skill |
| 7 | Chép nội dung từ `docs/` | Hai bản, sửa một chỗ, lệch nhau | Để con trỏ |
| 8 | Chỉ thêm, không bao giờ xoá | Có luật đã sai từ lâu | Rà quý, xoá luật chết |
| 9 | Trộn sở thích cá nhân với luật dự án | Người khác clone về thấy khó chịu | Tách sang `~/.claude/` |
| 10 | Không bao giờ kiểm chứng | Tưởng luật đang hoạt động | Chạy Test 2 ở mục E.7 |

---

## E.13. Ba câu hỏi lọc — dùng cho mỗi dòng định viết

Trước khi thêm bất kỳ dòng nào:

```
1. Bỏ dòng này ra thì AI có làm khác đi không?
   → Không khác → bỏ.

2. AI có tự tìm được thông tin này từ repo trong 10 giây không?
   → Có → bỏ, hoặc đổi thành con trỏ.

3. Dòng này đúng ở mọi tình huống, hay chỉ đúng ở một loại việc?
   → Chỉ một loại việc → chuyển sang skill.
```

Ba câu này lọc được phần lớn nội dung thừa trước khi nó lọt vào file.

---

## Tóm tắt

- `CLAUDE.md` không phải bộ nhớ — nó là văn bản nằm trong ngữ cảnh **mọi phiên**, nên
  mỗi dòng là chi phí lặp lại.
- **Code hiện có mạnh hơn lời dặn.** Luật đi ngược codebase phải nói rõ điều đó.
- **Điều cấm mạnh hơn điều khuyên.** File hiệu quả = danh sách những chỗ dự án này khác
  với cách làm phổ biến.
- Bảy loại dòng có tác dụng: cặp đúng/sai, điều cấm kèm hậu quả, bảng tra cứu, con trỏ
  nguồn sự thật, lệnh chạy được, ngưỡng số, ranh giới phạm vi.
- Bỏ ra: thứ suy được từ repo, lời khuyên phổ quát, quy trình dài, lời dặn hay bị quên,
  đặc tả chi tiết, sở thích cá nhân.
- Xây theo **lần vấp thật**, không theo tưởng tượng: sai hai lần mới thêm một dòng.
- Kiểm chứng bằng **bẫy hành vi**, không bằng câu hỏi đọc hiểu.
- Ngữ cảnh lỗi thời nguy hiểm hơn không có ngữ cảnh — sửa `CLAUDE.md` trong cùng commit
  với thay đổi code.
- Luật vẫn bị vi phạm sau khi đã viết rõ → leo thang: skill → hook → chặn cứng.

> Về [Mục lục](README.md) | Đọc thêm: [Chương 04 — Context Engineering](04-context-engineering.md)
