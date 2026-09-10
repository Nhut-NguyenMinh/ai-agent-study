# Chương 05 — Skills & Slash Commands

> [← Chương 04](04-workflow-plan-execute.md) | [Mục lục](README.md) | [Chương 06 →](06-tools-va-mcp.md)

---

## 5.1. Vấn đề: giải thích lại quy trình lần thứ ba

Bạn viết một đoạn hướng dẫn dài để AI làm đúng một loại việc. Nó làm tốt. Tuần sau
việc đó quay lại, bạn viết lại đoạn hướng dẫn đó — lần này thiếu mất hai ý. Kết quả
kém hơn lần trước, và bạn không hiểu vì sao.

```
Prompt lặp lại lần 1 → tốt
Prompt lặp lại lần 2 → thiếu 2 ý → kết quả kém
Prompt lặp lại lần 3 → viết vội hơn → kết quả kém hơn nữa
```

> **Nguyên tắc: prompt lặp lại lần thứ ba là dấu hiệu nó phải trở thành skill.**

Skill là quy trình được viết ra một lần, kiểm chứng một lần, và dùng lại mãi — không
suy giảm theo trí nhớ.

---

## 5.2. Skill là gì

Có thể hiểu skill như một **quy trình thao tác chuẩn (SOP)** viết cho AI:

```
Skill
├── Khi nào dùng          ← điều kiện kích hoạt
├── Điều kiện tiên quyết  ← cần gì trước khi chạy
├── Nguyên tắc            ← điều bắt buộc và điều cấm
├── Quy trình từng bước   ← các bước cụ thể
└── Đầu ra mong đợi       ← thế nào là chạy xong
```

Không có skill: AI phải tự suy đoán cách thực hiện, và mỗi lần suy đoán một kiểu.
Có skill: AI có quy trình rõ để bám theo, kết quả lặp lại được.

### Skill khác gì `CLAUDE.md`

| | `CLAUDE.md` / rules | Skill |
|---|---|---|
| Nạp khi nào | **Mọi** phiên | Chỉ khi được gọi |
| Nội dung | Sự thật và luật luôn đúng | Quy trình cho một loại việc |
| Chi phí context | Trả ở mọi phiên | Chỉ trả khi dùng |
| Ví dụ | "Tech stack là FastAPI + PostgreSQL" | "Cách tạo một migration" |

Đây là lý do kỹ thuật để tách: kiến thức dùng ở mọi phiên thì đưa vào ngữ cảnh nền;
quy trình chỉ dùng khi làm loại việc đó thì đóng thành skill, để nó không tính phí
lên những phiên không cần.

---

## 5.3. Cấu trúc một skill

Tối thiểu là một file `SKILL.md` với frontmatter:

```markdown
---
name: ten-skill
description: Mô tả skill làm gì, VÀ nói rõ khi nào nên dùng — kể cả những câu người
  dùng hay nói khi cần nó. Nêu cả điều kiện tiên quyết nếu có.
---

# Tiêu đề

## Nguyên tắc quan trọng
- Điều bắt buộc
- Điều cấm

## Điều kiện tiên quyết
- Cần gì trước khi chạy được

## Quy trình
### Bước 1 — ...
### Bước 2 — ...

## Đầu ra
- Thế nào là xong
```

### `description` là phần quan trọng nhất

`description` là thứ duy nhất AI đọc để quyết định **có nên dùng skill này không**.
Một mô tả mơ hồ khiến skill hoặc không bao giờ được gọi, hoặc bị gọi nhầm lúc.

**Ví dụ thật — mô tả của skill `task-report` trong PM-AGENT:**

```yaml
description: Gửi báo cáo có cấu trúc cho MỘT task lên PM-AGENT sau mỗi lượt làm việc —
  nhật ký nhiều bản theo thời gian, không ghi đè bản trước. Dùng skill này khi vừa
  làm xong một lượt trên task (phân tích xong, code xong, test xong, hoặc bị chặn
  phải dừng), khi người dùng nói "gửi báo cáo", "báo cáo task này", "cập nhật kết quả
  lên hệ thống", hoặc trước khi chuyển task sang trạng thái kết thúc. KHÁC với báo cáo
  cuối ngày (`{prefix}_submit_daily_report`) vốn là báo cáo cấp project. Yêu cầu project
  đã cài `tools/agent-gateway/`.
```

Mô tả này làm bốn việc cùng lúc:

| Thành phần | Tác dụng |
|---|---|
| Skill làm gì | "Gửi báo cáo có cấu trúc cho MỘT task" |
| Khi nào dùng | Liệt kê tình huống + **nguyên văn câu người dùng hay nói** |
| Phân biệt với skill gần giống | "KHÁC với báo cáo cuối ngày..." |
| Điều kiện tiên quyết | "Yêu cầu project đã cài `tools/agent-gateway/`" |

Phần "KHÁC với..." là chi tiết đáng học nhất. PM-AGENT có bốn skill liên quan tới báo
cáo (`task-report`, `daily-task-report`, `personal-report-review`, `pm-report-analyze`)
và chúng rất dễ nhầm. Mỗi mô tả đều có một câu tự phân biệt với những skill còn lại.

---

## 5.4. Ví dụ thật — giải phẫu skill `get-task`

Skill `get-task` của PM-AGENT gói ba bước đầu của quy trình làm việc thành một lệnh:
**nhận task → đọc ngữ cảnh → viết phân tích**. Nó minh hoạ đủ mọi thành phần của một
skill viết tốt.

### Ranh giới được tuyên bố ngay từ đầu

```markdown
Phần code vẫn luôn do người dùng ra lệnh tiếp — skill này KHÔNG tự code.
```

Một câu, đặt ngay dưới tiêu đề. Skill có phạm vi rõ nghĩa là nó dừng đúng chỗ.

### Nguyên tắc — nói rõ điều cấm

```markdown
- Tự nhận task: CHỈ task đang trống người, và chỉ nhận về chính mình.
  Tuyệt đối KHÔNG giành task đang là của người khác, KHÔNG nhận hộ ai
  — server trả 403 assignee_scope_mismatch nếu thử.
- Không code trước khi phân tích.
- Yêu cầu chưa rõ → phải hỏi trước, không suy đoán.
- Không tự ý viết plan hay đổi status task trong skill này.
```

Chú ý cách viết: mỗi điều cấm đi kèm **hậu quả cụ thể** (`403 assignee_scope_mismatch`).
Điều cấm có hậu quả rõ thì khó bị lách hơn điều cấm chung chung.

### Điều kiện tiên quyết — nói rõ cần gì và cách kiểm

```markdown
- .mcp.json đã cấu hình đúng và Claude Code kết nối được MCP {prefix}
  (các tool {prefix}_* xuất hiện), HOẶC đã export {PREFIX}_AGENT_TOKEN để dùng CLI.
- Token cần tối thiểu scope task:read, qa:read, qa:write.
  Nếu thiếu, {prefix}_whoami sẽ cho biết scope hiện có.
```

Skill nói luôn **cách tự chẩn đoán** khi điều kiện không thoả (`{prefix}_whoami`), thay
vì để agent đoán mò khi gặp lỗi quyền.

### Quy trình — chống lại giả định sai

Đoạn đáng giá nhất của skill này:

```markdown
Bước 2 — Lấy task đang giao cho tôi

Không hard-code status — mỗi project có task_taxonomy riêng
(nhiều project không hề có status "todo").

1. Gọi {prefix}_get_taxonomy(project) — lấy toàn bộ statuses[] (kèm is_done).
2. Tính tập status cần quét = mọi status có is_done=false.
3. Gọi {prefix}_list_tasks(project, assignee="@me")
   — 1 lần duy nhất, KHÔNG truyền status (tránh gọi lặp theo từng status).
4. Lọc kết quả theo tập đã tính ở bước 2.
```

Đây là kiến thức đến từ một lỗi thật: bản đầu tiên hard-code `status="todo"`, và nó im
lặng trả về danh sách rỗng ở mọi project không dùng tên trạng thái đó. Cách sửa không
phải là "nhắc AI cẩn thận hơn" mà là **viết thẳng quy trình đúng vào skill**.

Chú ý cả những chỉ dẫn về hiệu quả: *"1 lần duy nhất, KHÔNG truyền status"* — ngăn agent
gọi API mười lần cho mười trạng thái.

---

## 5.5. Slash command — biến workflow thành nút bấm

Slash command là lối vào ngắn cho một quy trình:

```
/new-feature notification-system
/test-doc-first
/feature-verify
/task-report
```

Công thức:

```
Slash command
      ↓
Quy trình đã định nghĩa
      ↓
AI thực hiện từng bước
      ↓
Đầu ra chuẩn hoá
```

### Ví dụ thật — bộ command của PM-AGENT

Thư mục `.claude/commands/` chứa các workflow gọi được bằng `/`:

| Command | Việc nó làm |
|---|---|
| `/new-feature` | Vòng đời đầy đủ: ý tưởng → spec → tài liệu test → code → verify → đóng |
| `/test-doc-first` | Sinh YAML test case từ spec, **trước khi** code |
| `/feature-verify` | Chạy test + coverage + báo cáo, rồi mới cho đóng feature |
| `/db-migration` | Tạo và kiểm tra Alembic migration |
| `/doc-sync-code` | Cập nhật tài liệu kỹ thuật sau khi implement |
| `/run-tests` | Chạy test theo YAML (unit + browser) và lập báo cáo |
| `/tc-review` | Review chất lượng test case do AI viết |

Bộ này đáng chú ý ở chỗ nó **phủ đúng vòng đời** mô tả ở chương 04, mỗi giai đoạn một
command. Không có command nào thừa, và không giai đoạn nào thiếu command.

### Cấu trúc một command

```markdown
---
name: "New Feature"
description: "Full feature lifecycle: idea → spec → test-doc → implement → verify → close.
  TRIGGER when: user nói 'tạo feature mới', 'thêm tính năng'...
  SKIP: chỉ sửa bug, refactor, hoặc đang giữa workflow new-feature rồi."
command: "new-feature"
---

# Skill: new-feature

## Input
/new-feature <tên feature ngắn gọn>

## Workflow 11 bước (interactive)
### Bước 1 — Phân tích & xác nhận chức năng *(auto)*
### Bước 2 — Đặt tên issue & tạo thư mục lịch sử *(auto)*
...
### Bước 7.5 — Tài liệu test **[PAUSE]**
```

Hai chi tiết đáng sao chép:

**1. `description` có cả TRIGGER và SKIP.** Nói rõ khi nào KHÔNG dùng cũng quan trọng
ngang nói khi nào dùng — nó ngăn command bị gọi chồng lên chính nó.

**2. Đánh dấu `[PAUSE]` cho bước cần người.** Bước nào phải dừng chờ người thì ghi
rõ trong chính tài liệu quy trình, không dựa vào việc AI tự đoán chỗ nên hỏi.

---

## 5.6. Rule, Skill hay Command — chọn cái nào

Ba cơ chế dễ nhầm. Bảng quyết định:

| Câu hỏi | Câu trả lời |
|---|---|
| Luôn đúng, mọi phiên, mọi loại việc? | **Rule** (`.claude/rules/`) |
| Là quy trình cho một loại việc, AI tự nhận biết khi nào cần? | **Skill** (`.claude/skills/`) |
| Là quy trình người dùng chủ động gọi? | **Command** (`.claude/commands/`) |
| Là lời dặn hay bị quên dù đã viết? | **Hook** (chương 07) |

Ranh giới skill và command trên thực tế mờ — trong Claude Code, skill có thể gọi bằng
`/tên-skill`. Sự khác biệt thật nằm ở **ai khởi động**: skill được model tự chọn dựa
trên `description`; command là người gõ.

### Ví dụ so sánh trong PM-AGENT

| Nội dung | Cơ chế | Vì sao |
|---|---|---|
| "Không `git push` khi chưa được yêu cầu" | Rule (`{prefix}-git-safety.md`) | Luôn đúng, mọi lúc |
| "Cách viết YAML test case" | Command (`/test-doc-first`) | Người chủ động gọi khi đến giai đoạn đó |
| "Gửi báo cáo cho một task" | Skill (`task-report`) | AI nhận ra khi vừa làm xong một lượt |
| "Nhắc gửi báo cáo khi phiên có sửa code" | Hook (`Stop`) | Lời dặn hay bị quên → cần cơ chế cứng |

Dòng cuối là ví dụ điển hình của việc **leo thang cơ chế**: ban đầu là một câu trong
rule, bị quên nhiều lần, cuối cùng phải thành hook. Chương 07 nói kỹ vì sao.

---

## 5.7. Skill Mapping — ánh xạ loại việc sang quy trình

Có nhiều skill rồi thì nảy sinh vấn đề mới: chọn cái nào. PM-AGENT giải quyết bằng một
bảng tra trong `CLAUDE.md`:

```markdown
| Loại công việc | Skill |
|----------------|-------|
| Tạo feature mới | new-feature |
| Fix bug / debug | {prefix}-debug |
| Refactor | {prefix}-refactor |
| DB schema change | db-migration |
| Tạo YAML test cases trước implement | test-doc-first |
| Verify & đóng feature sau implement | feature-verify |
| Review code | {prefix}-code-review |
| Security audit | {prefix}-security-audit |
```

Kèm một luật buộc phải tra bảng này trước khi bắt tay (`{prefix}-skill-clarify`): nhận diện
loại việc từ từ khoá trong yêu cầu, tra bảng, thông báo *"Tôi sẽ dùng workflow `X` cho
tác vụ này"*, rồi mới làm.

Và quan trọng không kém — nếu không phân loại được thì **bắt buộc hỏi**:

```
Để áp dụng đúng workflow, bạn đang thực hiện công việc gì?
1. Tạo feature / chức năng mới
2. Fix bug / lỗi
3. Refactor / cải thiện code hiện có
4. Viết hoặc cập nhật spec / tài liệu
5. Khác: ___
```

Lý do: áp sai quy trình rất tốn kém. Dùng luồng `new-feature` cho một bug fix sẽ sinh
ra spec và kế hoạch không ai cần.

---

## 5.8. Cảnh báo model tối thiểu

Chi tiết nhỏ nhưng đáng học. Nhiều skill của PM-AGENT mở đầu bằng:

```markdown
> **Model tối thiểu: Sonnet.** Không chạy skill này bằng Haiku — thực nghiệm 2026-08-17
> (cùng token, cùng task, chỉ đổi model) cho kết quả phân tích/thao tác kém rõ rệt.
> Đây là cảnh báo, không phải chặn cứng: bạn vẫn chạy được, nhưng nếu kết quả kỳ lạ
> thì kiểm model đang dùng trước khi đi tìm bug ở chỗ khác.
```

Ba điều đáng học từ đoạn này:

1. **Có bằng chứng, có ngày** — không phải cảm tính, mà là thực nghiệm có kiểm soát biến.
2. **Nói rõ đây là cảnh báo, không phải chặn** — người dùng biết mình được quyền bỏ qua.
3. **Chỉ đường chẩn đoán** — "kiểm model trước khi đi tìm bug ở chỗ khác" tiết kiệm hàng
   giờ cho người gặp kết quả lạ.

---

## 5.9. Bẫy thường gặp

> **Bẫy 1 — Skill quá rộng.**
> Một skill "làm mọi thứ liên quan đến task". Nó không bao giờ được gọi đúng lúc vì
> `description` không khớp tình huống cụ thể nào. **Cách sửa:** một skill một việc.

> **Bẫy 2 — `description` viết cho người, không viết cho model.**
> "Skill hỗ trợ quản lý task" — quá mơ hồ để model quyết định. **Cách sửa:** viết cả
> những câu người dùng hay nói khi cần skill này.

> **Bẫy 3 — Skill chồng chéo mà không tự phân biệt.**
> Bốn skill báo cáo, không cái nào nói mình khác ba cái kia chỗ nào. **Cách sửa:** mỗi
> `description` có một câu "KHÁC với X ở chỗ...".

> **Bẫy 4 — Skill chép lại nội dung `CLAUDE.md`.**
> Nội dung bị trùng, và khi sửa thì chỉ sửa một chỗ. **Cách sửa:** skill trỏ tới tài
> liệu nguồn thay vì chép.

> **Bẫy 5 — Viết skill trước khi làm việc đó bằng tay.**
> Skill viết theo tưởng tượng sẽ bỏ sót đúng những bước khó. **Cách sửa:** làm thủ công
> hai lần, ghi lại chỗ vấp, rồi mới đóng gói.

---

## 5.10. Bài tập

**Bài 1 — Tìm ứng viên.**
Xem lại ba phiên gần nhất, tìm một quy trình bạn đã mô tả nhiều hơn một lần. Đó là
skill đầu tiên của bạn.

**Bài 2 — Viết skill.**
Viết `SKILL.md` cho quy trình đó, đủ năm phần: mô tả (có câu kích hoạt), nguyên tắc
(gồm điều cấm), điều kiện tiên quyết, quy trình từng bước, đầu ra.

**Bài 3 — Kiểm chứng mô tả.**
Mở phiên mới, nói một câu tự nhiên như khi cần skill đó. Nếu nó không được gọi,
`description` của bạn chưa đủ — bổ sung đúng câu bạn vừa nói vào đó.

---

## Tóm tắt chương

- Prompt lặp lại lần thứ ba → đóng thành skill.
- Skill = SOP cho AI: khi nào dùng, cấm gì, các bước, đầu ra.
- `description` quyết định skill có được gọi đúng lúc không — viết cả câu kích hoạt
  thật và câu tự phân biệt với skill gần giống.
- Điều cấm nên đi kèm hậu quả cụ thể.
- Skill nạp theo nhu cầu, rule nạp mọi phiên — tách đúng để tiết kiệm ngân sách context.
- Có nhiều skill thì cần **bảng ánh xạ loại việc → quy trình**, và phải hỏi khi không
  phân loại được.

> Chương tiếp: [06 — Tools & MCP](06-tools-va-mcp.md)
