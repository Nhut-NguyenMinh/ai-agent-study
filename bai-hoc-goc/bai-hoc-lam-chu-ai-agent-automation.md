# Bài học --- Làm chủ AI Coding Agent & AI Automation

> **Mục tiêu:** Chuyển từ cách dùng AI như một công cụ viết code sang
> cách xây dựng một môi trường để AI có thể **hiểu → lập kế hoạch → thực
> hiện → kiểm tra → nhận feedback → cải thiện**.

------------------------------------------------------------------------

## 1. Tư duy cốt lõi

Điểm quan trọng nhất của bài học không phải là học thêm một câu prompt
hay một công cụ AI mới.

Mà là thay đổi cách làm việc:

**Từ:**

``` text
Con người nghĩ → AI viết code
```

**Sang:**

``` text
Con người định hướng
        ↓
AI khám phá context
        ↓
AI lập kế hoạch
        ↓
Con người review
        ↓
AI thực hiện
        ↓
AI chạy / kiểm tra
        ↓
Nhận lỗi + feedback
        ↓
AI sửa
        ↓
Kiểm tra lại
```

### Công thức tổng quát

``` text
AI Agent
= Context
+ Tools
+ Rules
+ Feedback Loop
```

AI càng có đủ 4 thành phần này thì càng có khả năng làm việc như một
**AI teammate**, thay vì chỉ là một chatbot sinh code.

------------------------------------------------------------------------

# 2. Prompt không phải là tất cả

Một sai lầm phổ biến là cố viết một prompt thật dài để giải thích mọi
thứ cho AI.

Trong thực tế, **context của dự án quan trọng hơn việc prompt phải thật
thông minh**.

AI cần biết:

-   Project này dùng để làm gì?
-   Cấu trúc project ra sao?
-   Quy tắc coding là gì?
-   Những phần nào không được tự ý thay đổi?
-   Cách chạy project?
-   Cách test?
-   Cách deploy?
-   Business logic quan trọng nằm ở đâu?
-   Những quyết định trước đây của team là gì?

Vì vậy nên xây dựng context lâu dài thay vì mỗi lần lại giải thích từ
đầu.

------------------------------------------------------------------------

# 3. CLAUDE.md --- bộ nhớ và hướng dẫn cho project

Một trong những cơ chế quan trọng là sử dụng `CLAUDE.md`.

File này có thể chứa:

-   Project overview
-   Architecture
-   Coding conventions
-   Commands
-   Testing instructions
-   Git conventions
-   Business rules
-   Những điều AI không được làm
-   Cách verify kết quả

Ví dụ:

``` text
project/
├── CLAUDE.md
├── src/
├── tests/
├── docs/
└── ...
```

Có thể sử dụng nhiều cấp context:

``` text
root/
└── CLAUDE.md

project/
└── CLAUDE.md

specific-module/
└── CLAUDE.md
```

Ý tưởng chính:

> **Đừng bắt AI nhớ bằng lời nói trong từng cuộc hội thoại. Hãy đưa kiến
> thức quan trọng vào môi trường làm việc của AI.**

------------------------------------------------------------------------

# 4. Workflow quan trọng: Plan → Review → Execute

Đối với task lớn, không nên yêu cầu AI:

> "Hãy code toàn bộ tính năng này."

Thay vào đó:

``` text
1. Understand
2. Explore
3. Plan
4. Review
5. Implement
6. Test
7. Iterate
```

## Bước 1 --- Understand

AI phải hiểu:

-   yêu cầu
-   code hiện tại
-   dependencies
-   architecture
-   các module liên quan

## Bước 2 --- Explore

Cho AI tìm hiểu project trước khi sửa.

Ví dụ các câu hỏi hữu ích:

``` text
How is this code used?

How do I instantiate this?

Why was this designed this way?

What files are related to this feature?

What could break if we change this?
```

## Bước 3 --- Plan

AI tạo plan:

``` text
Task
├── Step 1
├── Step 2
├── Step 3
├── Tests
└── Verification
```

## Bước 4 --- Review

Con người kiểm tra plan trước.

Nếu sai:

``` text
Plan sai
↓
Sửa plan
↓
AI mới bắt đầu code
```

## Bước 5 --- Implement

AI thực hiện theo plan.

## Bước 6 --- Test

Không chỉ kiểm tra bằng mắt.

Hãy cho AI cách:

-   build
-   run
-   test
-   lint
-   type check
-   inspect logs

## Bước 7 --- Iterate

``` text
Build
↓
Error
↓
Inspect
↓
Understand
↓
Fix
↓
Run again
```

Đây chính là **feedback loop**.

------------------------------------------------------------------------

# 5. Feedback loop là yếu tố làm AI mạnh lên

AI không nhất thiết phải đúng ngay lần đầu.

Một workflow tốt cho phép AI tự phát hiện lỗi:

``` text
Implement
   ↓
Run
   ↓
Error
   ↓
Read error
   ↓
Analyze
   ↓
Fix
   ↓
Run again
```

Vì vậy, thay vì hỏi:

> "Làm sao để prompt để AI viết đúng ngay lần đầu?"

Nên hỏi:

> "Làm sao để AI có thể tự kiểm tra và tự sửa?"

Đây là sự khác biệt rất lớn giữa **prompt engineering** và **agent
engineering**.

------------------------------------------------------------------------

# 6. Cho AI tools thay vì chỉ cho AI instructions

Một AI Agent mạnh không chỉ biết phải làm gì.

Nó còn phải có khả năng **làm việc với hệ thống**.

Các nhóm tools có thể gồm:

-   Terminal
-   File system
-   Git
-   GitHub
-   Browser
-   Database
-   APIs
-   MCP
-   CI/CD
-   Monitoring
-   Sentry
-   Cloud services

Mô hình:

``` text
Instructions
      +
Context
      +
Tools
      +
Feedback
      ↓
   Agent
```

------------------------------------------------------------------------

# 7. MCP

MCP giúp AI kết nối với các hệ thống và công cụ bên ngoài.

Có thể hình dung:

``` text
AI
 ↓
MCP
 ↓
External Tools / Services
```

Ví dụ phạm vi sử dụng:

-   Database
-   GitHub
-   Google services
-   Internal tools
-   APIs
-   Documentation
-   Monitoring systems

Điểm quan trọng:

> MCP không phải chỉ là "thêm tool". Nó giúp mở rộng môi trường mà Agent
> có thể quan sát và thao tác.

------------------------------------------------------------------------

# 8. Skills

Skills là cách đóng gói một năng lực / quy trình để AI có thể sử dụng
lại.

Thay vì mỗi lần nói:

``` text
Hãy làm A
Sau đó làm B
Sau đó kiểm tra C
Cuối cùng xuất D
```

Có thể biến quy trình đó thành một skill.

Mô hình:

``` text
Skill
├── Instructions
├── Workflow
├── Rules
└── Expected Output
```

Điều này giúp:

-   tái sử dụng
-   chuẩn hóa
-   giảm prompt lặp lại
-   chia sẻ workflow cho team

------------------------------------------------------------------------

# 9. Subagents

Một task lớn có thể chia cho nhiều Agent / session.

Ví dụ:

``` text
                Main Agent
                    │
       ┌────────────┼────────────┐
       ↓            ↓            ↓
   Agent A       Agent B      Agent C
   Feature       Tests        Research
```

Hoặc:

``` text
Session 1 → Feature A
Session 2 → Bug fixes
Session 3 → Tests
Session 4 → Documentation
```

Mục tiêu là tăng throughput và giảm việc một Agent phải xử lý tất cả
trong một context duy nhất.

------------------------------------------------------------------------

# 10. Parallel sessions

Khi project lớn, có thể sử dụng nhiều terminal / checkout / Git
worktree.

Ví dụ:

``` text
Terminal 1 → Feature A
Terminal 2 → Feature B
Terminal 3 → Bug fixing
Terminal 4 → Testing
```

Có thể kết hợp với:

-   Git
-   Git worktree
-   SSH
-   tmux
-   CI/CD
-   GitHub

Điều này biến môi trường development thành một hệ thống có nhiều Agent
hoạt động song song.

------------------------------------------------------------------------

# 11. Git và GitHub

Git không chỉ là nơi lưu code.

Trong workflow AI Agent, Git còn giúp:

-   tạo checkpoint
-   review thay đổi
-   rollback
-   làm việc song song
-   kiểm soát phạm vi thay đổi
-   tạo worktree
-   phối hợp nhiều Agent

Workflow:

``` text
AI modifies code
       ↓
Git diff
       ↓
Review
       ↓
Test
       ↓
Commit
       ↓
Push / PR
```

Không nên để Agent thay đổi quá nhiều thứ mà không có checkpoint.

------------------------------------------------------------------------

# 12. Deterministic Logic và AI Logic

Một nguyên tắc rất quan trọng trong automation:

> **Không đưa mọi thứ cho AI quyết định.**

Có những việc nên dùng logic deterministic:

``` text
if amount > 10,000,000:
    require_approval()
```

Những việc có tính ngữ nghĩa / không cấu trúc mới phù hợp với AI:

``` text
Email
↓
AI hiểu nội dung
↓
Structured JSON
↓
Rule validation
↓
Action
```

### Kiến trúc an toàn

``` text
Unstructured Input
        ↓
      AI
        ↓
Structured Output
        ↓
Validation
        ↓
Deterministic Logic
        ↓
Action
```

AI nên làm phần:

> **Understand**

Hệ thống nên kiểm soát phần:

> **Decide + Execute**

đặc biệt với các hành động quan trọng.

------------------------------------------------------------------------

# 13. Structured Output

Không nên để AI trả về văn bản tự do nếu output sẽ được automation sử
dụng.

Thay vào đó:

``` json
{
  "intent": "create_task",
  "priority": "high",
  "assignee": "team-a",
  "confidence": 0.92
}
```

Sau đó hệ thống:

``` text
AI output
   ↓
Schema validation
   ↓
Business rules
   ↓
Action
```

Điều này giúp giảm rủi ro AI trả về nội dung không đúng format hoặc thực
hiện hành động ngoài ý muốn.

------------------------------------------------------------------------

# 14. Human-in-the-loop

Không phải automation nào cũng nên chạy 100% tự động.

Đối với hành động có rủi ro:

``` text
AI
 ↓
Prepare action
 ↓
Human approval
 ↓
Execute
```

Ví dụ những hành động có thể cần approval:

-   gửi email quan trọng
-   xóa dữ liệu
-   thay đổi production
-   giao dịch tài chính
-   thay đổi quyền truy cập
-   publish nội dung

Mô hình:

``` text
Low risk
→ Full automation

Medium risk
→ Validation

High risk
→ Human approval
```

------------------------------------------------------------------------

# 15. n8n + AI

n8n có thể đóng vai trò orchestration layer cho automation.

Một workflow điển hình:

``` text
Trigger
  ↓
Read data
  ↓
AI understanding
  ↓
Structured output
  ↓
Validate
  ↓
Business logic
  ↓
Action
  ↓
Log
  ↓
Monitor
```

Ví dụ:

``` text
Gmail
  ↓
AI / Gemini
  ↓
Extract information
  ↓
Google Sheets
  ↓
Rule
  ↓
Notification
```

Điểm quan trọng:

> AI không nên là toàn bộ workflow. AI là một thành phần trong workflow.

------------------------------------------------------------------------

# 16. Công thức Automation

Có thể cô đọng toàn bộ bài học thành:

``` text
Trigger
   ↓
Deterministic Logic
   ↓
AI if needed
   ↓
Validate
   ↓
Action
   ↓
Log
   ↓
Monitor
```

Hoặc với input không cấu trúc:

``` text
Unstructured Input
        ↓
   AI Understanding
        ↓
 Structured Output
        ↓
    Validation
        ↓
 Deterministic Action
```

------------------------------------------------------------------------

# 17. Debugging

Khi AI làm sai, đừng chỉ nói:

> "Sai rồi, sửa đi."

Hãy tạo vòng phản hồi có dữ liệu:

``` text
Run
 ↓
Error
 ↓
Read logs
 ↓
Identify root cause
 ↓
Fix
 ↓
Run
 ↓
Verify
```

AI càng có quyền truy cập vào:

-   logs
-   tests
-   runtime
-   error messages
-   monitoring

thì càng có khả năng tự debug.

------------------------------------------------------------------------

# 18. Không có một workflow duy nhất

Một nguyên tắc được nhấn mạnh:

> **Đừng ép mọi task vào cùng một workflow.**

Task nhỏ:

``` text
Ask → Implement → Check
```

Task vừa:

``` text
Understand → Plan → Implement → Test
```

Task lớn:

``` text
Explore
→ Plan
→ Human Review
→ Implement
→ Test
→ Iterate
→ Review
→ Merge
```

Task nguy hiểm:

``` text
Understand
→ Plan
→ Validate
→ Human Approval
→ Execute
→ Audit
```

Workflow phải phù hợp với:

-   độ phức tạp
-   rủi ro
-   mức độ thay đổi
-   khả năng kiểm thử

------------------------------------------------------------------------

# 19. Claude Code / Claude Desktop / Co-work

Các môi trường khác nhau phục vụ những cách làm việc khác nhau.

### Claude Code

Phù hợp với:

-   coding
-   terminal
-   project exploration
-   Git
-   testing
-   automation
-   agentic development

### Claude Desktop

Phù hợp với việc làm việc với các capability / MCP và các tác vụ ngoài
terminal.

### Co-work

Hướng tới việc AI có thể hỗ trợ các tác vụ máy tính / công việc rộng
hơn.

Điểm cần nhớ không phải là "công cụ nào tốt nhất".

Mà là:

> **Chọn môi trường phù hợp với task.**

------------------------------------------------------------------------

# 20. Routines

Những công việc lặp lại có thể biến thành routine.

Ví dụ:

``` text
Every morning
→ Check data
→ Summarize
→ Detect anomalies
→ Notify
```

Tư duy:

``` text
One-time task
      ↓
Repeated task
      ↓
Routine
      ↓
Automation
```

------------------------------------------------------------------------

# 21. Các câu hỏi tốt để làm việc với AI

Thay vì ngay lập tức yêu cầu code, hãy bắt đầu bằng câu hỏi khám phá.

### Hiểu code

``` text
How is this code used?

How do I instantiate this?

Why was this designed this way?
```

### Hiểu architecture

``` text
How does this module interact with the rest of the system?

What depends on this?

What could break if we change this?
```

### Chuẩn bị implementation

``` text
What files need to change?

What are the risks?

What tests should be added?

Can you propose a plan before making changes?
```

### Verification

``` text
How can we verify this works?

Run the relevant tests.

Check for regressions.

What remains unverified?
```

------------------------------------------------------------------------

# 22. Những điều KHÔNG nên làm

## 22.1 Không code ngay khi chưa hiểu

Sai:

``` text
Requirement
↓
Code ngay
```

Tốt hơn:

``` text
Requirement
↓
Explore
↓
Understand
↓
Plan
↓
Code
```

------------------------------------------------------------------------

## 22.2 Không phụ thuộc hoàn toàn vào prompt

Prompt chỉ là một phần.

Cần:

``` text
Prompt
+
Context
+
Tools
+
Rules
+
Feedback
```

------------------------------------------------------------------------

## 22.3 Không yêu cầu AI hoàn hảo ngay lần đầu

Thay vào đó xây feedback loop.

``` text
Build → Test → Error → Fix → Test
```

------------------------------------------------------------------------

## 22.4 Không để AI quyết định mọi business logic

AI nên xử lý những phần phù hợp với khả năng hiểu ngữ nghĩa.

Business rules quan trọng nên được kiểm soát bằng logic deterministic.

------------------------------------------------------------------------

## 22.5 Không cho AI tự do thực hiện hành động nguy hiểm

Sử dụng:

``` text
Validation
+
Permissions
+
Human approval
+
Audit log
```

------------------------------------------------------------------------

# 23. Công thức tổng hợp về AI Agent

Có thể xem Agent như một hệ thống:

``` text
              ┌─────────────┐
              │   Context   │
              └──────┬──────┘
                     ↓
┌────────┐      ┌──────────┐      ┌─────────┐
│ Rules  │ ───→ │   Agent  │ ───→ │  Tools  │
└────────┘      └────┬─────┘      └─────────┘
                     ↓
                ┌──────────┐
                │ Feedback │
                └────┬─────┘
                     ↓
                  Improve
```

Một Agent tốt cần:

1.  **Context** --- biết hệ thống.
2.  **Rules** --- biết giới hạn.
3.  **Tools** --- có khả năng hành động.
4.  **Feedback** --- biết kết quả.
5.  **Validation** --- biết kết quả có hợp lệ không.

------------------------------------------------------------------------

# 24. Prompt Engineering vs Agent Engineering

### Prompt Engineering

Tập trung vào:

``` text
Làm sao nói với AI cho đúng?
```

### Agent Engineering

Tập trung vào:

``` text
Làm sao xây môi trường để AI có thể tự làm việc tốt?
```

So sánh:

  Prompt Engineering   Agent Engineering
  -------------------- -------------------
  Prompt               Context
  Instructions         Rules
  One response         Workflow
  AI output            Tools + Actions
  Correct answer       Verification
  Conversation         Feedback loop
  Một task             Hệ thống làm việc

Đây là một trong những insight quan trọng nhất của bài học.

------------------------------------------------------------------------

# 25. Framework thực hành

Khi bắt đầu một task mới, có thể sử dụng framework:

## A --- Understand

``` text
Tôi đang làm gì?
Project hiện tại hoạt động thế nào?
```

## B --- Explore

``` text
Tìm files liên quan.
Tìm dependencies.
Tìm business logic.
```

## C --- Plan

``` text
Đề xuất kế hoạch.
Liệt kê files cần thay đổi.
Liệt kê risks.
Liệt kê tests.
```

## D --- Review

``` text
Con người review plan.
```

## E --- Implement

``` text
AI thực hiện.
```

## F --- Verify

``` text
Build
Test
Lint
Type check
Runtime check
```

## G --- Iterate

``` text
Error
→ Analyze
→ Fix
→ Test again
```

## H --- Deliver

``` text
Git diff
→ Review
→ Commit
→ PR / Deploy
```

------------------------------------------------------------------------

# 26. Checklist cho một AI Agent tốt

## Context

-   [ ] AI hiểu project
-   [ ] Có `CLAUDE.md`
-   [ ] Có coding conventions
-   [ ] Có business rules
-   [ ] Có testing instructions

## Tools

-   [ ] Terminal
-   [ ] File access
-   [ ] Git
-   [ ] GitHub
-   [ ] MCP khi cần
-   [ ] Monitoring / logs khi cần

## Workflow

-   [ ] Understand
-   [ ] Explore
-   [ ] Plan
-   [ ] Review
-   [ ] Implement
-   [ ] Test
-   [ ] Iterate

## Safety

-   [ ] Structured output
-   [ ] Validation
-   [ ] Permissions
-   [ ] Human approval nếu cần
-   [ ] Audit log

## Automation

-   [ ] Trigger
-   [ ] AI nếu thực sự cần
-   [ ] Deterministic logic
-   [ ] Validation
-   [ ] Action
-   [ ] Logging
-   [ ] Monitoring

------------------------------------------------------------------------

# 27. Bảy nguyên tắc cần nhớ

### 1. Đừng code ngay --- hãy hiểu trước

``` text
Understand → Plan → Implement
```

### 2. Đừng chỉ prompt --- hãy cung cấp context

``` text
AI + Context > AI + Prompt dài
```

### 3. Task lớn → plan trước

``` text
Plan → Human Review → Execute
```

### 4. Đừng đòi AI hoàn hảo --- hãy xây feedback loop

``` text
Build → Error → Fix → Test
```

### 5. Không giao mọi thứ cho AI

``` text
AI = Understanding
System = Rules + Execution
```

### 6. Không cho AI hành động tự do

``` text
AI
↓
Structured Output
↓
Validation
↓
Approval nếu cần
↓
Action
```

### 7. Đừng chỉ dùng một session

Hãy nghĩ theo hệ sinh thái:

``` text
Agent
+
Tools
+
Skills
+
Subagents
+
MCP
+
Git
+
Automation
+
Monitoring
```

------------------------------------------------------------------------

# 28. Bài tập thực hành đề xuất

## Bài tập 1 --- Tạo context cho project

Tạo:

``` text
CLAUDE.md
```

Bao gồm:

-   Project overview
-   Architecture
-   Commands
-   Rules
-   Testing
-   Things AI must not change

------------------------------------------------------------------------

## Bài tập 2 --- Thực hành Plan Mode

Chọn một feature vừa phải.

Không cho AI code ngay.

Yêu cầu:

``` text
Explore the project.
Understand the current architecture.
Create an implementation plan.
List risks and tests.
Do not modify files yet.
```

Sau đó review plan.

------------------------------------------------------------------------

## Bài tập 3 --- Xây feedback loop

Cho AI một task có test.

Workflow:

``` text
Implement
↓
Run tests
↓
Read failure
↓
Fix
↓
Run tests again
```

Mục tiêu: để AI tự xử lý ít nhất một vòng lỗi.

------------------------------------------------------------------------

## Bài tập 4 --- AI + n8n

Xây workflow:

``` text
Input
↓
AI classification
↓
Structured JSON
↓
Validation
↓
Google Sheet / Action
```

------------------------------------------------------------------------

## Bài tập 5 --- Human-in-the-loop

Thiết kế:

``` text
AI prepares action
↓
Human approves
↓
Automation executes
```

------------------------------------------------------------------------

# 29. Mô hình tư duy cuối cùng

Đừng nghĩ:

> "Tôi đang dùng AI để viết code."

Hãy nghĩ:

> "Tôi đang xây một môi trường trong đó AI có thể làm việc cùng tôi."

Môi trường đó gồm:

``` text
                AI WORK ENVIRONMENT

        ┌──────────────────────────┐
        │         Context          │
        │ CLAUDE.md / Memory / Docs│
        └────────────┬─────────────┘
                     ↓
        ┌──────────────────────────┐
        │          Agent           │
        │ Understand / Plan / Act  │
        └────────────┬─────────────┘
                     ↓
        ┌──────────────────────────┐
        │          Tools           │
        │ Git / MCP / Terminal /   │
        │ APIs / Browser / Cloud   │
        └────────────┬─────────────┘
                     ↓
        ┌──────────────────────────┐
        │       Verification       │
        │ Test / Build / Logs      │
        └────────────┬─────────────┘
                     ↓
        ┌──────────────────────────┐
        │        Feedback          │
        │ Fix / Improve / Iterate  │
        └──────────────────────────┘
```

------------------------------------------------------------------------

# 30. Kết luận

Bài học có thể cô đọng thành một câu:

> **Đừng chỉ bảo AI viết code. Hãy cung cấp cho AI context, tools, rules
> và feedback loop để nó có thể tự khám phá, lập kế hoạch, thực hiện,
> kiểm tra và cải thiện kết quả.**

Và ở cấp độ cao hơn:

``` text
Prompt Engineering
        ↓
Context Engineering
        ↓
Agent Engineering
        ↓
Automation Engineering
        ↓
AI-powered Work System
```

Mục tiêu cuối cùng không phải là:

> "AI trả lời hay hơn."

Mà là:

> **AI có thể tham gia vào quy trình làm việc thực tế một cách có kiểm
> soát, có thể kiểm chứng và có khả năng cải thiện.**

------------------------------------------------------------------------

## Tóm tắt 1 phút

``` text
AI Agent tốt
=
Context
+
Tools
+
Rules
+
Feedback
+
Validation
```

Workflow:

``` text
Understand
→ Explore
→ Plan
→ Review
→ Implement
→ Test
→ Feedback
→ Iterate
→ Deliver
```

Automation:

``` text
Trigger
→ AI nếu cần
→ Structured Output
→ Validate
→ Deterministic Logic
→ Action
→ Log
→ Monitor
```

**Tư duy quan trọng nhất:**

``` text
Không chỉ hỏi:
"AI có thể làm gì?"

Hãy hỏi:
"Tôi cần xây môi trường nào
để AI có thể tự làm việc tốt?"
```
