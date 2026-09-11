# Chương 04 — Context Engineering

> [← Chương 03](03-dac-ta-yeu-cau.md) | [Mục lục](README.md) | [Chương 05 →](05-workflow-plan-execute.md)

---

## 4.1. Vấn đề: kiến thức nằm trong đầu người

Một kỹ sư làm lâu trong dự án mang theo rất nhiều thứ trong đầu mà không viết ra đâu cả:

- Kiến trúc hệ thống và lý do chọn nó
- Quy ước đặt tên, bố cục thư mục
- File nào quan trọng, file nào là rác lịch sử
- Lệnh chạy test, lệnh build, lệnh deploy
- Những quyết định đã chốt và không được đảo lại
- Những chỗ "đừng đụng vào, đụng là vỡ"

AI không có gì trong số đó. Mỗi phiên mới, nó bắt đầu từ số không.

Có hai cách xử lý:

```
Cách 1 (sai):   Mỗi phiên lại giải thích lại bằng lời
Cách 2 (đúng):  Ghi kiến thức vào môi trường làm việc, AI tự nạp
```

> **Đừng bắt AI nhớ bằng lời nói trong từng cuộc hội thoại.
> Hãy đưa kiến thức quan trọng vào môi trường làm việc của nó.**

Đó là toàn bộ nội dung của Context Engineering.

---

> **Hướng dẫn thực hành chi tiết:** chương này nói `CLAUDE.md` **là gì** và đặt ở đâu.
> Để biết **viết dòng nào thì đổi được hành vi AI, dòng nào bị bỏ qua** — kèm mẫu đầy
> đủ, cách kiểm chứng và bảo trì — xem [Phụ lục E](E-huong-dan-viet-claude-md.md).

---

## 4.2. `CLAUDE.md` — sổ tay vận hành của dự án

`CLAUDE.md` đặt ở gốc dự án (hoặc `.claude/CLAUDE.md`) được nạp tự động khi phiên bắt đầu.

Có thể hình dung:

```
CLAUDE.md = Blueprint + Sổ tay dự án + Bộ nhớ dài hạn
```

### Nội dung nên có

| Mục | Vì sao cần |
|---|---|
| Dự án là gì, cho ai | Định hướng mọi đánh đổi kỹ thuật |
| Tech stack đã chốt | Ngăn AI đề xuất thư viện ngoài luồng |
| Kiến trúc & patterns bắt buộc | Ngăn AI tạo pattern mới trong dự án đã có pattern |
| Quy ước đặt tên | Code mới trông giống code cũ |
| Lệnh thường dùng | AI tự chạy được test/build mà không phải hỏi |
| Điều KHÔNG được làm | Rẻ hơn nhiều so với sửa hậu quả |
| Danh sách tài liệu quan trọng | Chỉ đường đến ngữ cảnh sâu hơn khi cần |

### Nguyên tắc vàng

> **Nếu một lời dặn bị lặp lại ở nhiều phiên → nó thuộc về `CLAUDE.md`, không thuộc về khung chat.**

### Đừng viết quá dài

`CLAUDE.md` được nạp vào **mọi** phiên. Mỗi dòng thừa là thuế đánh lên mọi cuộc trò chuyện.

| Triệu chứng | Cách sửa |
|---|---|
| File dài hơn 300–400 dòng | Tách phần chi tiết ra `docs/`, ở `CLAUDE.md` chỉ để con trỏ |
| Có nội dung chỉ đúng với một module | Chuyển xuống `CLAUDE.md` của thư mục con |
| Có nội dung chỉ dùng cho một loại việc | Chuyển thành skill (chương 05) |
| Có lời dặn hay bị quên | Chuyển thành hook (chương 07) |

---

## 4.3. Ví dụ thật — `CLAUDE.md` của PM-AGENT

File `.claude/CLAUDE.md` của PM-AGENT tổ chức theo các khối sau. Đây là bộ khung đáng
sao chép cho dự án khác:

```markdown
# PM-AGENT — Claude Code Rules

## Skill Mapping           ← loại công việc nào thì dùng workflow nào
| Loại công việc | Skill |
| Tạo feature mới | new-feature |
| Fix bug / debug | {prefix}-debug |
| DB schema change | db-migration |
...

## Vòng đời chuẩn          ← quy trình bắt buộc cho mọi thay đổi code
[1] TÀI LIỆU TEST → [2] IMPLEMENT → [3] VERIFY & CLOSE

## Dự án                   ← bối cảnh nghiệp vụ + con trỏ tới docs
docs/04-tech-stack.md — NGUỒN TRUTH cho tech decisions (đọc trước khi code)

## Tech Stack (CHỐT)       ← danh sách đóng, không đổi nếu không hỏi
Python 3.12+ / FastAPI / SQLAlchemy 2.0 async / PostgreSQL 16 / HTMX / uv

## Architecture Patterns   ← 4 pattern bắt buộc, có ví dụ code
1. Source Adapter Pattern (typing.Protocol, không dùng ABC)
2. AI Router Pattern (không gọi SDK trực tiếp)
3. Repository Pattern
4. Module Boundaries (không import chéo giữa feature modules)

## Code Conventions        ← naming, Python style, FastAPI, SQLAlchemy, Pydantic
## Security Rules          ← 6 luật, gồm cấm hard-code secret
## Testing / ARQ Jobs / Alembic / Git Conventions
## Checklist trước khi tạo file mới
```

Ba điểm thiết kế đáng học từ file này:

**1. Nó chỉ đường thay vì chép lại.**

```markdown
docs/04-tech-stack.md — NGUỒN TRUTH cho tech decisions (đọc trước khi code)
```

Chi tiết tech stack nằm trong `docs/`, `CLAUDE.md` chỉ nói "nguồn sự thật ở đây".
Ngữ cảnh sâu được nạp *khi cần*, không nạp mọi phiên.

**2. Nó nói cả điều cấm, không chỉ điều nên.**

```markdown
# KHÔNG gọi Anthropic/OpenAI SDK trực tiếp từ service
# Luôn dùng AIRouter
```

Điều cấm có giá trị cao hơn điều nên, vì AI mặc định sẽ làm theo cách phổ biến nhất
trên internet — thường là cách gọi SDK trực tiếp.

**3. Nó có bảng ánh xạ loại việc → quy trình.**

Bảng Skill Mapping biến câu "làm gì bây giờ" thành một tra cứu, thay vì để AI tự chọn.

---

## 4.4. Ngữ cảnh phân cấp

Ngữ cảnh không phải một file duy nhất. Nó xếp thành nhiều tầng, tầng hẹp hơn đè lên
tầng rộng hơn:

```
┌──────────────────────────────────────────────────┐
│ Enterprise policy      — do tổ chức áp đặt        │  ← không override được
├──────────────────────────────────────────────────┤
│ ~/.claude/CLAUDE.md    — sở thích cá nhân        │  ← theo người, mọi dự án
│ ~/.claude/rules/*.md                              │
├──────────────────────────────────────────────────┤
│ <repo>/.claude/CLAUDE.md — quy tắc dự án          │  ← theo dự án, chia sẻ cả team
│ <repo>/.claude/rules/*.md                         │
├──────────────────────────────────────────────────┤
│ <repo>/module/CLAUDE.md  — quy tắc riêng module   │  ← nạp khi làm việc ở vùng đó
└──────────────────────────────────────────────────┘
```

### Ví dụ thật — phân tầng trong PM-AGENT

| Tầng | File | Nội dung điển hình |
|---|---|---|
| Cá nhân | `~/.claude/CLAUDE.md` | "Trả lời bằng tiếng Việt, thuật ngữ kỹ thuật giữ tiếng Anh" |
| Cá nhân | `~/.claude/rules/{prefix}-doc-sync.md` | Bắt buộc cập nhật doc sau mỗi thay đổi code |
| Cá nhân | `~/.claude/rules/{prefix}-session-wrap.md` | Quy trình tổng kết phiên + bảng token |
| Dự án | `.claude/CLAUDE.md` | Tech stack, patterns, conventions |
| Dự án | `.claude/rules/{prefix}-git-safety.md` | Danh sách lệnh Git bị cấm |
| Dự án | `.claude/rules/{prefix}-test-first.md` | Vòng đời test-first bắt buộc |

Phân tầng đúng cách quan trọng ở chỗ: quy tắc cá nhân (viết tiếng Việt) không nên nằm
trong repo dùng chung, và quy tắc dự án (tech stack) không nên nằm ở cấu hình cá nhân
của một người.

### Nguyên tắc chia tầng

| Câu hỏi | Nếu "có" thì để ở |
|---|---|
| Người khác clone repo về có cần điều này không? | Repo (`.claude/` trong dự án) |
| Đây là sở thích riêng của tôi? | Cấu hình cá nhân (`~/.claude/`) |
| Chỉ đúng với một module? | `CLAUDE.md` của module đó |
| Cả công ty phải tuân theo, không được phá? | Enterprise policy |

---

## 4.5. Tách nhỏ rules thay vì một file khổng lồ

PM-AGENT không nhét mọi luật vào `CLAUDE.md`. Nó tách thành 14 file, mỗi file một chủ đề:

```
.claude/rules/
├── {prefix}-coding-standards.md        — chuẩn code chung
├── {prefix}-critique-loop.md           — vòng phản biện trước khi thực hiện
├── {prefix}-codex-handoff.md           — bàn giao giữa các agent
├── {prefix}-frontend-consistency.md    — macro UI bắt buộc
├── {prefix}-frontend-layout-verify.md  — chụp màn hình đối chiếu spec
├── {prefix}-git-safety.md              — lệnh Git bị cấm
├── {prefix}-no-speculation.md          — cấm suy đoán khi bàn spec/code
├── {prefix}-professional.md            — tự nhận diện ngôn ngữ và áp best practice
├── {prefix}-require-context.md         — bắt buộc xác định phạm vi trước khi làm
├── {prefix}-session-management.md      — HANDOFF giữa các phiên
├── {prefix}-small-commits.md           — giới hạn kích thước commit
├── {prefix}-test-first.md              — vòng đời test-first
├── {prefix}-ux-quality.md              — tiêu chuẩn trải nghiệm người dùng
└── {prefix}-verify-before-change.md    — kiểm quyết định cũ trước khi sửa
```

Lợi ích của việc tách:

1. **Sửa được từng phần** — đổi quy tắc commit không phải mở file 900 dòng.
2. **Review được** — một pull request đổi luật test chỉ đụng một file.
3. **Giải thích được lý do** — mỗi file có mục "Lý do" riêng ở cuối.
4. **Tắt/bật được** — bỏ một luật là bỏ một file.

### Mỗi rule nên có mục "Lý do"

Đây là chi tiết dễ bỏ qua nhưng quan trọng nhất. Ví dụ cuối file `{prefix}-small-commits.md`:

```markdown
## Lý do

Commit lớn gây ra:
- Review khó — reviewer phải hiểu toàn bộ thay đổi cùng lúc
- Revert tốn công — không thể revert 1 phần
- Merge conflict nhiều hơn
- Khó debug với git bisect
```

Không có mục này, luật trở thành mệnh lệnh vô cớ, và cả người lẫn AI đều sẽ tìm cách
lách khi thấy bất tiện. Có nó, luật trở thành lập luận có thể đồng ý.

---

## 4.6. Memory — kiến thức tích luỹ qua các phiên

`CLAUDE.md` là kiến thức bạn chủ động viết. **Memory** là kiến thức đọng lại từ những
lần vấp.

Mô hình dùng trong PM-AGENT: mỗi bài học là một file, kèm chỉ mục `MEMORY.md`.

```
memory/
├── MEMORY.md                              ← chỉ mục, mỗi memory 1 dòng
├── feedback_docker_healthcheck.md
├── feedback_overflow_dropdown.md
├── feedback_nginx_stale_upstream.md
└── project_{prefix}.md
```

**Ví dụ thật — một memory có giá trị cao:**

```markdown
[overflow-hidden/x-auto clip dropdown]
overflow:hidden và overflow-x-auto clip absolute dropdown
— đã bị 2 lần, không dùng trên table có ⋮ menu
```

Điểm đắt giá nằm ở cụm **"đã bị 2 lần"**. Đó là loại kiến thức không đọc được từ code,
không có trong Git, và sẽ mất đi nếu không ghi lại.

### Phân loại memory

| Loại | Nội dung | Ví dụ |
|---|---|---|
| `user` | Người dùng là ai, thích cách làm việc nào | "Trả lời tiếng Việt, thuật ngữ giữ tiếng Anh" |
| `feedback` | Hướng dẫn về cách làm việc, kèm lý do | "ERROR trong pytest thường là fixture lỗi thời, mở ra xem trước khi gán nhãn 'không liên quan'" |
| `project` | Trạng thái công việc không suy ra được từ code | "Wave 5 đã đóng, chờ merge; D7/D12/D13 còn treo" |
| `reference` | Con trỏ tới tài nguyên ngoài | Link dashboard, ticket, tài liệu |

### Điều KHÔNG nên đưa vào memory

- Thứ mà repo đã ghi lại: cấu trúc code, lịch sử fix, nội dung `CLAUDE.md`
- Thứ chỉ đúng trong một cuộc hội thoại

Nếu memory chép lại thứ repo đã có, nó sẽ lỗi thời và trở thành nguồn thông tin sai.

---

## 4.7. Ngân sách context

Cửa sổ ngữ cảnh là tài nguyên có hạn, và nhiều thứ cùng tranh nhau chỗ:

```
System prompt
+ CLAUDE.md (mọi tầng)
+ Rules đang áp dụng
+ Định nghĩa tool (bao gồm mọi MCP server đang bật)
+ Skill đang được nạp
+ File đã đọc trong phiên
+ Lịch sử hội thoại
─────────────────────────────
= Ngân sách context
```

> **Context phải đủ, không phải càng nhiều càng tốt.**

Khi ngân sách bị tiêu hoang, triệu chứng rất đặc trưng: model bắt đầu quên lời dặn ở
đầu phiên, chọn nhầm tool, hoặc lặp lại việc đã làm.

### Ba nguồn lãng phí lớn nhất

| Nguồn | Cách nhận biết | Cách sửa |
|---|---|---|
| `CLAUDE.md` phình to | File > 400 dòng | Tách sang `docs/`, để lại con trỏ |
| Bật quá nhiều MCP server | Danh sách tool dài, có tool cả tháng không dùng | Chỉ bật MCP cần cho dự án này (chương 06) |
| Đọc nguyên file lớn | Đọc cả file 2000 dòng để xem một hàm | Đọc đúng đoạn cần, hoặc dùng subagent tìm hộ (chương 08) |

### Nguyên tắc thực hành

> Đưa cho agent **đúng thông tin, đúng thời điểm, đúng task** — không phải mọi thông
> tin ở mọi thời điểm.

---

## 4.8. Bảo mật ngữ cảnh: đừng để bí mật lọt vào

Ngữ cảnh là thứ được gửi đi. Bất cứ gì lọt vào đó nên được coi là đã ra khỏi máy bạn.

**Ví dụ thật từ PM-AGENT** — skill `get-task` cần đọc một trường cấu hình trong
`.mcp.json`, nhưng file đó chứa token thật. Chỉ dẫn trong skill viết rõ:

```
Chỉ trích đúng 1 field, KHÔNG bao giờ đọc nguyên .mcp.json
(file này chứa {PREFIX}_AGENT_TOKEN thật, đọc cả file sẽ lộ token vào ngữ cảnh của bạn)
```

Kèm lệnh trích đúng một trường:

```bash
python3 -c "import json; d=json.load(open('.mcp.json')); \
print(d.get('mcpServers',{}).get('{prefix}',{}).get('env',{}).get('{PREFIX}_GET_TASK_EXCLUDE_STATUSES',''))"
```

Bài học tổng quát:

| Nguy cơ | Cách phòng |
|---|---|
| `cat .env` để xem một biến | Trích đúng biến cần: `grep '^APP_ENV=' .env` |
| Đọc file cấu hình chứa token | Trích đúng trường bằng `python3 -c` hoặc `jq` |
| Dán log có chứa header Authorization | Che trước khi dán |
| Commit `CLAUDE.md` có ghi khoá | Khoá phải nằm ở biến môi trường, `CLAUDE.md` chỉ ghi *tên* biến |

---

## 4.9. Bẫy thường gặp

> **Bẫy 1 — `CLAUDE.md` thành bãi rác.**
> Mỗi lần AI làm sai, thêm một dòng cấm. Sau ba tháng file dài 900 dòng, mâu thuẫn
> nội bộ, và không ai dám xoá dòng nào. **Cách sửa:** định kỳ rà lại; luật nào không
> còn đúng thì xoá, luật nào hay bị quên thì chuyển thành hook.

> **Bẫy 2 — Viết luật mà không viết lý do.**
> Luật không có lý do sẽ bị lách ngay khi bất tiện. **Cách sửa:** mỗi luật một mục
> "Lý do", nêu sự cố thật nếu có.

> **Bẫy 3 — Ngữ cảnh mâu thuẫn giữa các tầng.**
> `CLAUDE.md` cá nhân nói "luôn thêm type hint", rule dự án nói "không thêm annotation
> vào code không thay đổi". **Cách sửa:** tầng hẹp hơn thắng, và ghi rõ điều đó ra.

> **Bẫy 4 — Ngữ cảnh lỗi thời còn nguy hiểm hơn không có ngữ cảnh.**
> `CLAUDE.md` nói dùng `make test-unit`, nhưng target đó đã đổi tên. AI sẽ chạy lệnh
> sai rồi kết luận môi trường hỏng. **Cách sửa:** khi đổi lệnh/cấu trúc, sửa ngữ cảnh
> trong cùng commit.

---

## 4.10. Bài tập

**Bài 1 — Dựng `CLAUDE.md` tối thiểu.**
Viết một `CLAUDE.md` không quá 80 dòng cho dự án của bạn, gồm đúng sáu mục: dự án là
gì, tech stack, cấu trúc thư mục, lệnh chạy/test, quy ước đặt tên, điều cấm.

**Bài 2 — Kiểm chứng ngữ cảnh.**
Mở phiên mới, hỏi: *"Đọc `CLAUDE.md` rồi tóm tắt: dự án này là gì, chạy test bằng lệnh
nào, và có ba điều gì tuyệt đối không được làm?"* Chỗ nào nó trả lời sai hoặc mơ hồ,
chỗ đó `CLAUDE.md` viết chưa rõ.

**Bài 3 — Tách rule đầu tiên.**
Lấy phần dài nhất trong `CLAUDE.md`, tách ra `.claude/rules/<tên>.md`, và viết thêm
mục "Lý do" cho nó.

---

## Tóm tắt chương

- Kiến thức thuộc về **môi trường làm việc**, không thuộc về khung chat.
- `CLAUDE.md` = blueprint + sổ tay + bộ nhớ; ngắn gọn, và **chỉ đường** thay vì chép lại.
- Ngữ cảnh phân tầng: enterprise → cá nhân → dự án → module.
- Tách rules thành nhiều file nhỏ, mỗi file có mục **Lý do**.
- Memory ghi lại bài học từ những lần vấp — thứ không đọc được từ code hay Git.
- Ngân sách context có hạn: **đủ, đúng lúc, đúng việc** — không phải càng nhiều càng tốt.
- Đừng để bí mật lọt vào ngữ cảnh: trích đúng trường cần, không đọc cả file cấu hình.

> Chương tiếp: [05 — Workflow: Plan → Review → Execute → Verify](05-workflow-plan-execute.md)
