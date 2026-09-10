# Tổng hợp workshop Anthropic — Claude Code

> **Lưu ý về tên gọi:** Trong transcript, tên công cụ bị nhận diện thành “QR code” hoặc “Quad code”. Dựa trên ngữ cảnh (Anthropic, Claude, `CLAUDE.md`, MCP, GitHub, SDK, `-P`), nội dung đang nói về **Claude Code** của Anthropic.

## 1. Claude Code là gì?

Claude Code là một thế hệ trợ lý AI dành cho lập trình theo kiểu **agentic** (tác nhân tự chủ).

Khác với các công cụ AI trước đây chủ yếu:
- Tự động hoàn thành từng dòng code.
- Hoàn thành vài dòng code tại một thời điểm.

Claude Code hướng tới những công việc lớn hơn:
- Xây dựng cả một feature.
- Viết toàn bộ function.
- Viết hoặc chỉnh sửa toàn bộ file.
- Sửa cả một bug.
- Tự khám phá codebase và thực hiện chuỗi thao tác cần thiết.

Điểm quan trọng là Claude Code không bắt người dùng phải thay đổi workflow. Nó hoạt động với:
- VS Code.
- Xcode.
- JetBrains IDEs.
- Các terminal khác nhau.
- Local environment.
- Remote SSH.
- Tmux.
- Nhiều môi trường phát triển khác.

---

## 2. Bắt đầu với Claude Code: Codebase Q&A

Khuyến nghị quan trọng nhất cho người mới là:

> **Đừng bắt đầu bằng việc yêu cầu Claude Code viết code. Hãy bắt đầu bằng việc hỏi đáp về codebase.**

Có thể hỏi:
- Đoạn code này được sử dụng như thế nào?
- Làm thế nào để instantiate class này?
- Function này được gọi ở đâu?
- Tại sao function này có 15 arguments?
- Tại sao các arguments lại được đặt tên như vậy?

Claude Code không chỉ tìm kiếm text đơn thuần. Nó có thể:
1. Khám phá codebase.
2. Tìm nơi class/function được sử dụng.
3. Tìm các ví dụ thực tế.
4. Đọc thêm context liên quan.
5. Tổng hợp câu trả lời.

Kết quả gần giống việc đọc wiki hoặc documentation, thay vì chỉ dùng `Command + F`.

### Tại Anthropic

Đây là một phần trong technical onboarding của Anthropic.

Trước đây, onboarding kỹ thuật có thể mất khoảng **2–3 tuần**.

Khi sử dụng Claude Code để hỏi đáp về codebase, thời gian này được rút xuống khoảng **2–3 ngày**.

Claude Code giúp người mới:
- Tự tìm hiểu codebase.
- Giảm số câu hỏi phải hỏi các kỹ sư khác.
- Hiểu cách các công cụ trong project hoạt động.

---

## 3. Git history là một nguồn context rất mạnh

Claude Code có thể được yêu cầu xem lịch sử Git.

Ví dụ:

> “Look through Git history.”

Sau đó Claude Code có thể tìm hiểu:
- Một argument được thêm vào khi nào.
- Ai thêm nó.
- Vì sao nó được thêm.
- Commit nào liên quan.
- Commit đó liên kết tới GitHub issue nào.
- Bối cảnh của thay đổi.

Ví dụ thực tế:

> “Why does this function have 15 arguments and why are the arguments named this weird way?”

Claude Code có thể truy ngược lịch sử để giải thích nguồn gốc của thiết kế hiện tại.

Điểm đáng chú ý là người dùng không cần mô tả toàn bộ quy trình tìm kiếm. Chỉ cần nói mục tiêu, Claude Code có thể tự biết nên dùng Git như thế nào.

---

## 4. GitHub Issues

Claude Code cũng có thể sử dụng web fetch để:
- Đọc GitHub issue.
- Lấy context từ issue.
- Kết hợp thông tin từ issue với codebase.

Điều này hữu ích khi cần hiểu:
- Vì sao một thay đổi được thực hiện.
- Bug xuất phát từ đâu.
- Một feature được yêu cầu như thế nào.
- Context lịch sử của một phần code.

---

## 5. Một workflow thực tế: “Tôi đã ship gì tuần này?”

Một ví dụ được chia sẻ trong talk:

Mỗi thứ Hai, tác giả hỏi Claude Code:

> “What did I ship this week?”

Claude Code:
1. Xem Git log.
2. Biết username của người dùng.
3. Tìm các commit tương ứng.
4. Tổng hợp những gì người dùng đã ship.
5. Xuất ra một bản tóm tắt dễ đọc.

Sau đó người dùng chỉ cần copy kết quả vào tài liệu.

---

# 6. Khi đã quen Q&A: chuyển sang chỉnh sửa code

Sau khi hiểu cách Claude Code hoạt động qua Q&A, có thể chuyển sang editing.

Claude Code có một tập công cụ tương đối nhỏ nhưng mạnh:

- Tool để chỉnh sửa file.
- Tool để chạy Bash commands.
- Tool để tìm kiếm file.

Claude Code có thể kết hợp các tool này thành chuỗi:

**Explore → Brainstorm → Plan → Edit → Test → Iterate**

Người dùng không cần nói:

> “Hãy dùng tool A, sau đó tool B, rồi tool C.”

Chỉ cần nói mục tiêu.

Claude Code sẽ tự quyết định cách sử dụng các tool.

---

# 7. Đừng vội yêu cầu feature quá lớn

Một lỗi phổ biến:

> “Implement this enormous 3,000-line feature.”

Đôi khi Claude Code có thể làm đúng ngay lần đầu.

Nhưng cũng có thể xảy ra trường hợp:
- Code chạy được.
- Nhưng kiến trúc hoặc cách triển khai hoàn toàn không phải thứ người dùng mong muốn.

Cách tốt hơn là yêu cầu Claude Code **suy nghĩ trước khi code**.

Ví dụ:

> “Before you write code, brainstorm ideas, make a plan, run it by me, and ask for approval.”

Hoặc đơn giản:

> “Before you write code, make a plan.”

Không nhất thiết phải dùng một “plan mode” đặc biệt.

---

# 8. Commit → Push → Pull Request

Một workflow được tác giả sử dụng thường xuyên:

Claude Code có thể được yêu cầu thực hiện toàn bộ quy trình:
1. Tạo branch.
2. Thực hiện thay đổi.
3. Tạo commit.
4. Push branch.
5. Tạo Pull Request trên GitHub.

Điểm hay là không cần mô tả chi tiết format commit.

Claude Code có thể:
- Đọc code.
- Xem Git history.
- Xem Git log.
- Tìm hiểu convention của repository.
- Tạo commit phù hợp.

---

# 9. Tích hợp các tool của team

Khi sử dụng Claude Code nâng cao hơn, nên đưa các tool mà team đang sử dụng vào workflow.

Có hai nhóm chính:

## Bash tools

Claude Code có thể sử dụng CLI.

Ví dụ:

> “Use the CLI to do something.”

Có thể yêu cầu Claude Code dùng:

```bash
--help
```

để tự tìm hiểu cách sử dụng CLI.

Nếu một command được dùng thường xuyên, có thể ghi lại hướng dẫn trong `CLAUDE.md` để Claude Code nhớ qua các session.

---

## MCP tools

Claude Code cũng hỗ trợ MCP.

Có thể:
- Thêm MCP server.
- Cung cấp thông tin về tool.
- Hướng dẫn Claude Code cách sử dụng.

Khi bắt đầu làm việc trên một codebase mới, có thể cung cấp cho Claude Code toàn bộ các tool mà team vốn đã sử dụng trong project.

Điều này tạo ra hiệu ứng “network effect”:

> Một người cấu hình một lần → cả team cùng hưởng lợi.

---

# 10. Feedback loop: cho Claude Code cách kiểm tra kết quả

Một trong những use case mạnh nhất là:

> **Cho Claude Code một cách để tự kiểm tra kết quả của chính nó.**

Ví dụ:
- Unit tests.
- Integration tests.
- Screenshot bằng Puppeteer.
- Screenshot từ iOS Simulator.
- Kiểm thử web.
- Kiểm thử ứng dụng.

Workflow:

**Write → Check → Observe → Fix → Check again**

Ví dụ, nếu đưa một mock UI và yêu cầu Claude Code xây dựng giao diện:
- Lần đầu có thể khá tốt.
- Sau 2–3 vòng tự kiểm tra và chỉnh sửa, kết quả có thể gần như hoàn hảo.

Vì vậy, nếu domain của bạn có cách tạo feedback, hãy cung cấp nó cho Claude Code.

---

# 11. Context là yếu tố cực kỳ quan trọng

Một kỹ sư làm việc lâu trong codebase có rất nhiều context trong đầu:
- Kiến trúc hệ thống.
- Lịch sử thay đổi.
- Convention.
- Những file quan trọng.
- Các tool thường dùng.
- Những quyết định thiết kế trước đây.

Claude Code không tự có toàn bộ context đó.

Vì vậy:

> **Càng cung cấp đúng context, Claude Code càng đưa ra quyết định tốt.**

---

# 12. `CLAUDE.md`

Cách đơn giản nhất để cung cấp context là file:

```text
CLAUDE.md
```

Có thể đặt ở project root.

Claude Code sẽ tự động đọc file này khi bắt đầu session.

Nội dung có thể gồm:

- Các Bash command thường dùng.
- MCP tools.
- Style guide.
- Architectural decisions.
- Các file quan trọng.
- Convention của project.
- Những điều kỹ sư cần biết để làm việc trong codebase.

### Ví dụ

```text
CLAUDE.md

- Common bash commands
- Style guide
- Core files
- Architectural decisions
- Important workflows
```

### Không nên viết quá dài

`CLAUDE.md` nên ngắn gọn.

Nếu quá dài:
- Chiếm nhiều context.
- Có thể làm giảm tính hữu ích.
- Không phải thông tin nào cũng cần được đưa vào mọi session.

---

# 13. `CLAUDE.md` ở các thư mục con

Có thể đặt thêm `CLAUDE.md` trong các nested directories.

Ví dụ:

```text
project/
├── CLAUDE.md
├── frontend/
│   └── CLAUDE.md
└── backend/
    └── CLAUDE.md
```

File ở thư mục con sẽ được đưa vào context khi Claude Code làm việc trong khu vực đó.

Điều này cho phép:
- Context chung ở root.
- Context chuyên biệt cho frontend.
- Context chuyên biệt cho backend.
- Context chỉ áp dụng cho một phần codebase.

---

# 14. Context cá nhân và context dùng chung

Có thể có context:
- Dùng chung cho team.
- Chỉ dành cho cá nhân.

`CLAUDE.md` của project nên được check vào source control nếu muốn chia sẻ với team.

Ngoài ra có thể có cấu hình local/personal không check vào source control.

Nên phân biệt:

**Team context**
→ Chia sẻ với mọi người.

**Personal context**
→ Chỉ dành cho workflow/sở thích cá nhân.

---

# 15. Enterprise policies

Ở cấp enterprise, công ty có thể triển khai context/configuration chung cho toàn bộ nhân viên.

Có thể dùng để:
- Áp dụng policy chung.
- Cấu hình tool.
- Thiết lập permissions.
- Cho phép một số command tự động được approve.
- Chặn những command hoặc URL nguy hiểm.

Ví dụ, nếu toàn bộ nhân viên đều dùng một test command, enterprise policy có thể cấu hình để command đó được auto-approved.

Ngược lại, nếu có một URL không bao giờ được phép fetch:
- Thêm URL vào policy.
- Nhân viên không thể override policy đó.

Điều này vừa:
- Giúp workflow thuận tiện hơn.
- Vừa bảo vệ codebase.

---

# 16. MCP JSON dùng chung

Có thể check MCP JSON vào codebase.

Khi một kỹ sư chạy Claude Code trong repository:
- Claude Code có thể phát hiện MCP configuration.
- Người dùng được nhắc cài đặt MCP server.
- Cả team sử dụng cùng một cấu hình.

Ví dụ tại Anthropic:
- Apps repository có Puppeteer MCP server.
- Team chia sẻ MCP configuration.
- Kỹ sư có thể dùng Puppeteer để:
  - Chạy end-to-end tests.
  - Screenshot.
  - Kiểm tra UI.
  - Iterate tự động.

Không cần từng kỹ sư tự cài đặt riêng.

---

# 17. `/memory`

Claude Code có các công cụ để quản lý memory/context.

Ví dụ:

```text
/memory
```

Có thể xem những memory files đang được đưa vào context.

Có thể tồn tại:
- Enterprise policy.
- User memory.
- Project `CLAUDE.md`.
- Nested `CLAUDE.md`.

Có thể chỉnh sửa memory file cụ thể.

Khi muốn Claude Code nhớ một điều gì đó, có thể dùng cơ chế `#` và chọn memory phù hợp.

---

# 18. Slash commands

Có thể tạo slash commands trong:

```text
.claude/commands
```

Các command có thể nằm:
- Trong home directory.
- Hoặc được check vào project.

Ví dụ workflow tại Claude Code repo:
- Có slash command để label GitHub issues.
- GitHub Action chạy workflow.
- Claude Code thực hiện command.
- Issues được tự động gán label.

Nhờ vậy con người không cần làm thủ công.

---

# 19. Một số phím tắt và mẹo sử dụng terminal

## `Shift + Tab`

Chuyển sang **auto-accept edits mode**.

Khi bật:
- Các file edits được tự động chấp nhận.
- Bash commands vẫn cần approval.

Phù hợp khi:
- Claude Code đang đi đúng hướng.
- Đang viết unit tests.
- Đang lặp lại test nhiều lần.
- Không muốn xác nhận từng edit.

Có thể yêu cầu Claude Code undo các thay đổi sau đó.

---

## `#`

Dùng để nói cho Claude Code biết một điều cần nhớ.

Ví dụ:

> Claude đang sử dụng một tool không đúng cách.

Có thể nói:

> `# Always use this tool in this way...`

Claude Code có thể ghi nhớ điều đó và đưa vào `CLAUDE.md`.

---

## `!`

Chuyển xuống Bash mode.

Ví dụ:

```text
!git status
```

Command sẽ:
- Chạy local.
- Hiển thị output.
- Output cũng được đưa vào context để Claude Code nhìn thấy ở lượt tiếp theo.

Rất hữu ích với:
- Command chạy lâu.
- Command mà người dùng biết chính xác cần chạy.
- Bất kỳ command nào muốn đưa output vào context.

---

## `Esc`

Có thể nhấn `Esc` để dừng Claude Code.

Có thể dùng an toàn khi:
- Claude đang edit file.
- Claude đang chạy một workflow.
- Muốn thay đổi hướng triển khai.

Việc nhấn Esc không làm hỏng session.

Nếu Claude vừa đề xuất một thay đổi gần đúng, có thể dừng lại và yêu cầu nó thực hiện lại theo hướng khác.

---

## `Esc` hai lần

Có thể dùng để quay lại history.

---

## Resume / Continue

Sau khi kết thúc session, có thể tiếp tục session trước đó bằng:

```bash
claude --resume
```

hoặc:

```bash
claude --continue
```

---

## `Ctrl + R`

Dùng để xem thêm output.

Nó hiển thị toàn bộ output tương ứng với những gì Claude Code nhìn thấy trong context window.

---

# 20. Claude Code SDK

Claude Code có SDK để xây dựng các workflow khác dựa trên nó.

CLI SDK có thể sử dụng:

```bash
claude -p
```

Có thể:
- Truyền prompt.
- Chỉ định allowed tools.
- Cho phép các Bash commands cụ thể.
- Chọn output format.
- Sử dụng JSON.
- Sử dụng streaming JSON.

Điều này rất phù hợp để xây dựng:
- CI pipelines.
- Incident response.
- Automation.
- Internal tools.
- Các workflow tùy chỉnh.

Có thể coi nó như:

> **Một Unix utility cực kỳ thông minh.**

Bạn đưa prompt vào → nhận kết quả JSON → tiếp tục pipe kết quả sang tool khác.

---

# 21. Unix piping + Claude Code

Một trong những ý tưởng mạnh là kết hợp Claude Code với Unix pipeline.

Ví dụ:

```bash
git status | claude -p "..."
```

Hoặc dùng `jq` để xử lý JSON output.

Có thể:
- Đọc dữ liệu từ GCP bucket.
- Đọc một log rất lớn.
- Pipe log vào Claude Code.
- Yêu cầu Claude Code tìm ra điều đáng chú ý.

Hoặc:
- Lấy dữ liệu từ Sentry CLI.
- Pipe dữ liệu vào Claude Code.
- Yêu cầu Claude Code phân tích.

Điểm quan trọng:

> Các tổ hợp gần như vô hạn.

Claude Code có thể được sử dụng giống một **super-intelligent Unix utility**.

---

# 22. Chạy nhiều Claude Code song song

Power users thường không chỉ chạy một session.

Họ có thể:
- Dùng SSH sessions.
- Dùng Tmux.
- Có nhiều checkout của cùng một repository.
- Chạy nhiều Claude Code sessions song song.
- Sử dụng Git worktree để tạo isolation.

Ví dụ:

```text
repo/
├── worktree-feature-a/
├── worktree-feature-b/
└── worktree-bug-fix/
```

Mỗi worktree có thể chạy một Claude Code session riêng.

Nhờ vậy có thể thực hiện nhiều công việc song song.

Tác giả cho biết đây là một trong những workflow nâng cao mà power users tại Anthropic và bên ngoài Anthropic thường sử dụng.

---

# 23. Workflow tổng thể được khuyến nghị

Có thể cô đọng toàn bộ workshop thành workflow:

```text
1. Codebase Q&A
        ↓
2. Hiểu Claude Code có thể làm gì
        ↓
3. Cung cấp context
        ↓
4. Cấu hình CLAUDE.md
        ↓
5. Cấu hình MCP / tools
        ↓
6. Brainstorm
        ↓
7. Make a plan
        ↓
8. Xin confirmation
        ↓
9. Implement
        ↓
10. Test / Screenshot / Feedback
        ↓
11. Iterate
        ↓
12. Commit
        ↓
13. Push
        ↓
14. Pull Request
```

---

# 24. Những nguyên tắc quan trọng nhất

## Nguyên tắc 1 — Bắt đầu bằng Q&A

Đừng bắt đầu bằng việc yêu cầu AI code ngay.

Hãy hỏi:

> “How is this code used?”

> “How do I instantiate this?”

> “Why was this designed this way?”

Điều này giúp người dùng hiểu khả năng của Claude Code.

---

## Nguyên tắc 2 — Cho AI context

Sử dụng:
- `CLAUDE.md`.
- Nested `CLAUDE.md`.
- Memory.
- Slash commands.
- MCP.
- Enterprise policies.

Context tốt → quyết định tốt hơn.

---

## Nguyên tắc 3 — Cho AI feedback

Nếu AI có cách kiểm tra kết quả thì hãy cung cấp cách đó.

Ví dụ:

```text
Code
 ↓
Test
 ↓
Screenshot
 ↓
Evaluate
 ↓
Fix
 ↓
Test again
```

AI có feedback loop sẽ mạnh hơn rất nhiều.

---

## Nguyên tắc 4 — Đừng ép một workflow

Claude Code có thể làm rất nhiều thứ.

Không nhất thiết phải tuân theo một workflow cố định.

Là kỹ sư, bạn nên sử dụng nó theo cách phù hợp với công việc của mình.

---

## Nguyên tắc 5 — Chia sẻ cấu hình với team

Nếu một người đã tìm ra cách tốt để:
- Sử dụng CLI.
- Dùng MCP.
- Chạy test.
- Build.
- Deploy.
- Kiểm tra UI.

Hãy đưa knowledge đó vào context dùng chung.

Một người làm một lần → cả team hưởng lợi.

---

# 25. Câu nói cô đọng nhất của workshop

Nếu phải rút toàn bộ nội dung thành một ý:

> **Đừng chỉ bảo Claude Code viết code. Hãy cung cấp cho nó context, tools và feedback loop để nó có thể tự khám phá, lập kế hoạch, thực hiện, kiểm tra và cải thiện kết quả.**

Đó chính là cách chuyển từ **AI autocomplete** sang **AI coding agent**.
