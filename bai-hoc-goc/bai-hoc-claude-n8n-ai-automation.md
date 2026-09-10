# Bài học: Claude Code / Co-work, GitHub, Routines và n8n + AI Agent

## 1. Tổng quan

Bài học trình bày một workflow thực tế để xây dựng và vận hành sản phẩm bằng AI, tập trung vào:

- Claude Code / Claude Desktop / Co-work
- Plan Mode và quy trình Plan → Review → Implement
- Cấu trúc project và `CLAUDE.md`
- Git và GitHub
- GitHub Pages
- MCP, Skills và Subagents
- Routines chạy trên cloud
- n8n để xây dựng automation
- Kết hợp workflow deterministic với AI Agent
- Gmail + Google Sheets + Gemini
- Debug và kiểm thử workflow

Ý tưởng xuyên suốt:

> Dùng automation có tính xác định cho những việc cần ổn định, lặp lại và dễ kiểm soát; dùng AI Agent cho những phần cần hiểu ngữ nghĩa, phân loại hoặc đưa ra phán đoán.

---

# 2. Claude Code: tư duy làm việc theo Plan → Execute

## 2.1. Plan Mode

Khi thực hiện một feature hoặc thay đổi lớn, không nên yêu cầu Claude sửa code ngay.

Quy trình đề xuất:

1. Đưa yêu cầu cho Claude.
2. Cho Claude phân tích project.
3. Claude tạo implementation plan.
4. Người dùng review plan.
5. Trao đổi/chỉnh sửa nếu cần.
6. Khi plan đã đúng, chuyển sang chế độ thực thi.
7. Claude triển khai theo plan.
8. Kiểm tra kết quả.

Mô hình:

```text
Requirement
    ↓
Analyze
    ↓
Plan
    ↓
Human Review
    ↓
Revise
    ↓
Implement
    ↓
Test
```

Điểm quan trọng: Plan Mode giúp giảm việc AI tự ý thay đổi sai kiến trúc hoặc làm những thứ không nằm trong phạm vi yêu cầu.

---

# 3. Context của project và CLAUDE.md

Claude cần hiểu project trước khi làm việc.

Một project nên có:

- cấu trúc thư mục rõ ràng
- file cấu hình
- tài liệu hướng dẫn
- quy tắc coding
- các quyết định kiến trúc
- context nghiệp vụ

`CLAUDE.md` có thể được sử dụng để cung cấp context và quy tắc cho Claude.

Có thể xem nó như:

```text
CLAUDE.md
    ↓
Project Context
    ↓
Claude hiểu:
- Project làm gì
- File nằm ở đâu
- Quy tắc nào cần tuân thủ
- Cách build/test
- Điều gì không được thay đổi
```

---

# 4. Git và GitHub

## 4.1. Vì sao cần GitHub?

Code trên máy cá nhân chỉ tồn tại ở local.

Nếu muốn:

- lưu trữ tập trung
- backup
- cộng tác
- deploy
- chạy automation trên cloud
- cho các dịch vụ khác truy cập project

thì nên đưa project lên GitHub.

Mô hình:

```text
Local Computer
      ↓
     Git
      ↓
   GitHub
      ↓
Cloud / Deployment / Automation
```

## 4.2. GitHub CLI

Bài học sử dụng GitHub CLI (`gh`).

Sau khi cài đặt, xác thực bằng:

```bash
gh auth login
```

Sau đó có thể làm việc với repository trực tiếp từ terminal/Claude Code.

---

# 5. GitHub Pages

GitHub không chỉ dùng để lưu code.

Có thể sử dụng GitHub Pages để publish website.

Workflow:

```text
Code
 ↓
GitHub Repository
 ↓
GitHub Pages
 ↓
Website Live
```

Một điểm quan trọng khi yêu cầu Claude push code:

> Không được đưa dữ liệu riêng tư hoặc dữ liệu nội bộ lên repository công khai.

Ví dụ trong bài học có dữ liệu liên quan đến sales/testimony/customer information. Khi publish project, phải yêu cầu Claude loại bỏ hoặc không commit những dữ liệu nhạy cảm này.

---

# 6. MCP, Skills và Subagents

Đây là các thành phần giúp Claude mở rộng khả năng.

## MCP

MCP cho phép Claude kết nối với các hệ thống/dịch vụ bên ngoài.

Ví dụ:

```text
Claude
  ↓
MCP
  ↓
External Service
```

Có thể dùng MCP để truy cập hoặc thao tác với các công cụ/dịch vụ mà Claude không có trực tiếp.

## Skills

Skills là những năng lực/quy trình được đóng gói để Claude sử dụng.

Ví dụ:

- research customer favorites
- research competitors
- tạo report
- xử lý một loại task cụ thể

## Subagents

Subagents có thể đảm nhiệm những nhiệm vụ chuyên biệt, giúp chia nhỏ công việc.

Tư duy:

```text
Main Agent
 ├── Research Agent
 ├── Analysis Agent
 └── Report Agent
```

---

# 7. Routines: automation chạy trên cloud

Một điểm quan trọng của bài học là sự khác nhau giữa automation chạy local và Routines chạy trên cloud.

## Local Scheduler

Nếu automation chạy trên máy cá nhân:

```text
Computer ON  → chạy
Computer OFF → không chạy
```

## Routines

Routines chạy trên server/cloud của Anthropic.

Do đó:

```text
Computer OFF
      ↓
Routine vẫn có thể chạy
      ↓
Cloud
```

Đây là một lợi thế rất lớn khi muốn xây dựng những tác vụ định kỳ.

---

# 8. GitHub trở thành nguồn code/context cho Routine

Routine có thể sử dụng repository GitHub làm codebase.

Ví dụ:

```text
GitHub Repository
 ├── Code
 ├── CLAUDE.md
 ├── Skills
 └── Project Context
          ↓
       Routine
          ↓
       Claude
          ↓
       Research
          ↓
       Report
```

Routine có thể lấy code/skills từ GitHub rồi thực hiện công việc trên cloud.

Ví dụ trong bài học:

- Chạy hàng tuần
- Kết nối Google Drive
- Sử dụng hai skills
- Research customer favorites
- Research competitors
- Kết hợp kết quả
- Tạo report / Google Doc

Điểm mấu chốt:

> GitHub không chỉ là nơi lưu code; nó có thể trở thành “nguồn sự thật” để các automation cloud sử dụng project và skills.

---

# 9. Tổng kết phần Claude

Các khái niệm chính:

```text
Permission Modes
       ↓
Plan Mode
       ↓
CLAUDE.md
       ↓
MCP + Skills + Subagents
       ↓
GitHub
       ↓
GitHub Pages
       ↓
Routines
```

Đây là một workflow từ:

**xây dựng → quản lý → deploy → automation**

---

# 10. Phần 2: n8n + Claude + AI Agent

Bài học chuyển sang một ví dụ automation thực tế.

## Công ty giả lập

Tên: **Canvas and Co.**

Đây là một studio nghệ thuật nhỏ tại Portland.

Vấn đề:

- Inbox có rất nhiều email.
- Có email hỏi sales.
- Có email support.
- Có email cảm ơn.
- Có email hỏi thông tin chung.
- Nhân viên phải xử lý thủ công.

Mục tiêu:

1. Tự động chào khách hàng.
2. Nhớ khách hàng cũ.
3. Phân loại email.
4. Tóm tắt email.
5. Đưa dữ liệu vào Google Sheets.
6. Gắn label Gmail.
7. Nhưng vẫn giữ quyền trả lời email cho con người.

---

# 11. Nguyên tắc quan trọng: Deterministic Automation vs AI Agent

Đây là một trong những bài học quan trọng nhất.

## Deterministic Workflow

Workflow có logic rõ ràng:

```text
IF A
THEN B
ELSE C
```

Ví dụ:

```text
Email mới
   ↓
Tìm email trong CRM
   ↓
Có?
 ┌─┴─┐
Có  Không
↓     ↓
Update Add
```

Ưu điểm:

- predictable
- dễ kiểm tra
- dễ debug
- phù hợp với business process
- ít bất ngờ

## AI Agent

AI Agent phù hợp với những việc cần hiểu ngôn ngữ hoặc đánh giá nội dung.

Ví dụ:

```text
Email
 ↓
AI đọc nội dung
 ↓
Hiểu ý định
 ↓
Classification
 ↓
Summary
```

AI Agent linh hoạt hơn nhưng khó dự đoán hơn.

## Nguyên tắc

```text
High-risk / repetitive / rule-based
            ↓
        n8n Workflow

Subjective / semantic / language-based
            ↓
          AI Agent
```

Hai loại này không cạnh tranh với nhau.

Chúng bổ sung cho nhau.

---

# 12. Setup n8n + Claude

Cần:

- Claude Desktop / Co-work
- n8n account
- Gmail
- Google Sheets
- Gemini
- MCP connection

Bài học sử dụng n8n free trial.

---

# 13. Kết nối n8n với Claude bằng MCP

Trong n8n:

1. Mở phần settings/connection.
2. Tìm MCP.
3. Lấy MCP server URL.
4. Copy URL.

Trong Claude Desktop / Co-work:

1. Add connector.
2. Paste MCP server URL.
3. Approve/trust URL.
4. Authorize.

Mô hình:

```text
Claude / Co-work
       ↓
      MCP
       ↓
      n8n
       ↓
   Workflows
```

Claude có thể dùng MCP để hỗ trợ tạo/chỉnh sửa workflow n8n.

---

# 14. Chuẩn bị dữ liệu CRM

Tutorial folder chứa:

- business information
- context cho AI
- Excel starter data

File starter:

**Canvas Co CRM starter**

Có các sheet/tab:

- CRM
- Leads
- Support

File được upload lên Google Sheets.

Mục đích là dùng Google Sheets như một CRM đơn giản.

---

# 15. Workflow 1: CRM + Greeting

## Mục tiêu

Khi có email mới:

1. Nhận email Gmail.
2. Lấy:
   - tên
   - email
   - message
3. Kiểm tra CRM.
4. Nếu khách mới:
   - thêm vào CRM
   - gửi welcome email
5. Nếu khách cũ:
   - update CRM
   - gửi email chào lại.

---

# 16. Logic Workflow 1

```text
New Gmail Email
       ↓
Extract sender
       ↓
Search CRM
       ↓
Customer exists?
    ┌───────┴───────┐
   YES             NO
    ↓               ↓
Update CRM       Add CRM
    ↓               ↓
Send "good      Send warm
to hear..."      welcome
```

Đây là một workflow deterministic.

Không cần AI.

---

# 17. Credentials

n8n cần credentials cho:

- Gmail
- Google Sheets

Khi cấp quyền:

> Chỉ nên cấp những permission cần thiết.

Trong tutorial, người hướng dẫn dùng Gmail cá nhân vì không có email của Canvas and Co.

---

# 18. Prompt cho Claude để xây workflow

Ý tưởng prompt:

> Build an n8n workflow where every new email triggers the workflow, checks the CRM Google Sheet, determines whether the sender is new or existing, adds/updates the CRM row, and sends the appropriate greeting email. Do not use AI for this workflow.

Điểm quan trọng:

- nói rõ trigger
- nói rõ data source
- nói rõ branching logic
- nói rõ output
- nói rõ không dùng AI

Claude có thể:

1. phân tích
2. tạo plan
3. hỏi clarification
4. build workflow

---

# 19. Test Workflow 1

## Email đầu tiên

Khi một email mới đến:

```text
Email
 ↓
Không tìm thấy trong CRM
 ↓
Create CRM record
 ↓
Send warm welcome
```

## Email thứ hai

Cùng người gửi tiếp tục email:

```text
Email
 ↓
Found in CRM
 ↓
Update record
 ↓
Send "good to hear from you again"
```

Như vậy hệ thống đã “nhớ” khách hàng.

---

# 20. Debug workflow

Một kỹ thuật được minh họa rất rõ:

Cố tình đổi tên column CRM.

Ví dụ:

```text
Email
```

đổi thành:

```text
S
```

Workflow bị lỗi vì node không tìm thấy column email.

Trong n8n có thể xem:

- execution
- run count
- failures
- failure rate
- execution details
- error message

Sau đó có thể đưa error cho Claude:

> Đây là error của workflow, hãy phân tích nguyên nhân và hướng dẫn sửa.

Hoặc đơn giản khôi phục lại column đúng tên rồi chạy lại execution thất bại.

Kết quả: workflow hoạt động lại.

---

# 21. Workflow 2: Thêm AI Classification

Sau khi workflow deterministic hoạt động ổn định, thêm AI vào một phần riêng.

AI sử dụng:

**Gemini**

Lý do được nêu trong tutorial:

- free tier tương đối hào phóng
- phù hợp để thử nghiệm.

---

# 22. Gemini làm gì?

Gemini đọc:

- email subject
- email body

Sau đó trả về:

1. Category
2. One-sentence summary

Ba category:

```text
sales
support
general
```

Quan trọng:

> AI chỉ phân loại và tóm tắt. AI KHÔNG tự trả lời khách hàng.

---

# 23. Logic Classification

```text
New Email
    ↓
CRM Workflow
    ↓
Gemini
    ↓
Classify + Summarize
    ↓
 ┌────────┼────────┐
Sales    Support  General
  ↓         ↓         ↓
Label     Label     Label
Sales     Support   General
  ↓         ↓
Leads     Support
Sheet      Sheet
```

---

# 24. Gmail Labels

Tạo các label:

- sales
- support
- general

Sau đó workflow tự gắn label dựa trên kết quả AI.

---

# 25. Gemini API

Quy trình setup:

1. Mở Google AI Studio.
2. Đăng nhập bằng Google account.
3. Chọn Get API Key / Create API Key.
4. Tạo project.
5. Ví dụ project có thể đặt tên tutorial.
6. Copy API key.
7. Quay lại n8n.
8. Tạo Gemini credential.
9. Paste API key.
10. Save.

Sau đó Gemini có thể được gọi trong workflow.

---

# 26. Prompt cho AI Classification

Ý tưởng:

> Every email should be read by Gemini. Read the subject and body, classify the email as sales, support, or general, and create a one-sentence summary. Then branch based on the category. The AI is only sorting/classifying; it should never reply to the customer.

Điểm quan trọng nhất:

**AI không được phép tự gửi reply.**

---

# 27. Các test case

## Test 1: Refund

Ví dụ email:

> Tôi muốn hoàn tiền vì sản phẩm rất tệ.

AI phân loại:

```text
support
```

Workflow:

- gắn Gmail label `support`
- tạo record trong Support sheet
- ghi nội dung/tóm tắt
- đánh dấu priority cao nếu phù hợp.

---

## Test 2: General

Ví dụ:

> Tôi muốn biết thêm thông tin về pricing.

Workflow có thể phân loại:

```text
general
```

và gắn label `general`.

---

## Test 3: Sales

Email mang tính mua hàng/kinh doanh:

```text
sales
```

Workflow:

- gắn Gmail label `sales`
- thêm record vào Leads sheet
- có thể ghi priority.

---

# 28. Một lưu ý từ phần test

AI classification có thể đôi lúc phân loại chưa hoàn toàn chính xác.

Ví dụ một số email thử nghiệm có thể bị xếp vào category không hoàn toàn phù hợp.

Điều này minh họa một nguyên tắc:

> AI Agent linh hoạt nhưng không deterministic.

Do đó không nên giao toàn bộ business logic quan trọng cho AI nếu một rule-based workflow có thể xử lý chính xác hơn.

---

# 29. Workflow cuối cùng

Sau khi ghép hai phần:

```text
                 Gmail
                   ↓
              New Email
                   ↓
             Extract Data
                   ↓
             Check CRM
             ┌─────┴─────┐
           New          Existing
            ↓              ↓
        Add CRM         Update CRM
            ↓              ↓
      Welcome Email   Return Greeting
             \            /
              \          /
                Gemini
                   ↓
           Classify + Summary
                   ↓
          ┌────────┼────────┐
        Sales     Support   General
          ↓          ↓         ↓
      Gmail Label Gmail Label Gmail Label
          ↓          ↓
       Leads       Support
       Sheet        Sheet
```

Đây là mô hình kết hợp:

**Deterministic Automation + AI Agent**

---

# 30. Khi nào publish workflow?

Trong quá trình phát triển:

```text
Manual execution
      ↓
Test
      ↓
Debug
      ↓
Fix
      ↓
Retest
      ↓
Satisfied
      ↓
Publish / Activate
```

Khi workflow được publish/activate:

> n8n sẽ lắng nghe Gmail và tự động chạy khi email mới đến.

---

# 31. Hai workflow chính của bài học

## Workflow A — Customer Memory

Mục tiêu:

- nhớ khách hàng
- phân biệt khách mới/cũ
- thêm/update CRM
- gửi greeting phù hợp

Đặc điểm:

**Deterministic**

---

## Workflow B — AI Email Classification

Mục tiêu:

- đọc email
- hiểu nội dung
- phân loại
- tóm tắt
- gắn label
- đưa vào đúng Google Sheet

Đặc điểm:

**AI-assisted**

---

# 32. Kiến trúc tư duy quan trọng nhất

Có thể rút gọn toàn bộ bài học thành:

```text
                 USER / EMAIL
                      ↓
              Deterministic Layer
                      ↓
             Business Process
                      ↓
               AI Agent Layer
                      ↓
            Semantic Understanding
                      ↓
             Structured Output
                      ↓
             Deterministic Layer
                      ↓
           Action / Database / CRM
```

Không nên:

```text
Email
 ↓
AI Agent
 ↓
AI tự quyết định mọi thứ
 ↓
AI tự gửi email
 ↓
AI tự sửa CRM
```

Nên:

```text
n8n kiểm soát workflow
        +
AI xử lý phần cần hiểu ngôn ngữ
        +
n8n kiểm soát action cuối cùng
```

---

# 33. Những bài học có thể áp dụng vào project thực tế

## 33.1. Không phải cái gì cũng cần AI

Nếu logic có thể viết:

```text
IF / ELSE
```

thì thường nên để automation xử lý.

Ví dụ:

- kiểm tra khách đã tồn tại chưa
- update CRM
- gửi email template
- ghi dữ liệu vào sheet
- chuyển dữ liệu sang branch

---

## 33.2. AI phù hợp với dữ liệu phi cấu trúc

AI hữu ích khi input là:

- email
- feedback
- review
- nội dung tự nhiên
- câu hỏi khách hàng
- văn bản dài

Ví dụ:

```text
"What does this customer actually want?"
```

Đây là việc khó viết rule cố định.

---

## 33.3. Tách AI khỏi business action

Một thiết kế an toàn hơn:

```text
AI
 ↓
Return:
{
  category: "support",
  summary: "...",
  priority: "high"
}
 ↓
n8n
 ↓
Validate
 ↓
Execute action
```

Thay vì để AI tự do thực hiện mọi action.

---

# 34. Debugging là một phần của AI workflow

Không nên kỳ vọng:

> Prompt một lần → workflow hoàn hảo.

Thực tế:

```text
Build
 ↓
Run
 ↓
Error
 ↓
Inspect execution
 ↓
Understand error
 ↓
Ask Claude
 ↓
Fix
 ↓
Run again
```

n8n execution history là công cụ quan trọng để tìm lỗi.

---

# 35. Human-in-the-loop

Một tư tưởng quan trọng trong tutorial:

AI có thể hỗ trợ xử lý inbox nhưng không nhất thiết phải thay thế con người.

Ví dụ:

```text
AI đọc email
      ↓
AI phân loại
      ↓
AI tóm tắt
      ↓
Con người xem
      ↓
Con người quyết định reply
```

Đây là thiết kế phù hợp với các tác vụ có rủi ro cao hơn.

---

# 36. Toàn bộ hệ sinh thái trong bài học

```text
                    CLAUDE
                      │
          ┌───────────┼───────────┐
          │           │           │
      Claude Code   Co-work      Routines
          │           │           │
          └───────┬───┘           │
                  │               │
                GitHub ◄──────────┘
                  │
            GitHub Pages

                  +

                 MCP
                  │
                  ↓
                 n8n
                  │
       ┌──────────┼──────────┐
       ↓          ↓          ↓
     Gmail    Google Sheets  Gemini
       │          │           │
       └──────────┼───────────┘
                  ↓
              Automation
```

---

# 37. Các khái niệm cần nhớ

| Khái niệm | Vai trò |
|---|---|
| Claude Code | Xây dựng/chỉnh sửa project bằng AI |
| Plan Mode | Lập kế hoạch trước khi implement |
| `CLAUDE.md` | Context/quy tắc của project |
| Git | Version control |
| GitHub | Repository/cloud source |
| GitHub Pages | Publish website |
| MCP | Kết nối AI với external tools |
| Skills | Đóng gói capability/workflow |
| Subagents | Chia task cho agent chuyên biệt |
| Routines | Tác vụ định kỳ chạy trên cloud |
| n8n | Automation engine |
| Gemini | AI xử lý ngôn ngữ/phân loại |
| Gmail | Email trigger + labels |
| Google Sheets | CRM/database đơn giản |

---

# 38. Công thức tư duy có thể áp dụng

## Công thức 1 — Xây feature

```text
Requirement
→ Plan
→ Review
→ Implement
→ Test
→ Deploy
```

## Công thức 2 — Xây automation

```text
Trigger
→ Deterministic Logic
→ AI nếu cần
→ Validate
→ Action
→ Log
→ Monitor
```

## Công thức 3 — AI Agent

```text
Unstructured Input
→ AI Understanding
→ Structured Output
→ Deterministic Action
```

---

# 39. Checklist khi xây một automation

### Trước khi build

- [ ] Xác định trigger
- [ ] Xác định input
- [ ] Xác định output
- [ ] Xác định business rules
- [ ] Xác định phần nào cần AI
- [ ] Xác định dữ liệu nhạy cảm
- [ ] Xác định quyền truy cập

### Khi build

- [ ] Build deterministic workflow trước
- [ ] Test từng node
- [ ] Kiểm tra credentials
- [ ] Kiểm tra data mapping
- [ ] Thêm AI sau khi workflow cơ bản ổn định
- [ ] Ép AI trả structured output nếu có thể

### Khi test

- [ ] Test happy path
- [ ] Test khách mới
- [ ] Test khách cũ
- [ ] Test email support
- [ ] Test sales
- [ ] Test general
- [ ] Test dữ liệu sai
- [ ] Kiểm tra execution log
- [ ] Kiểm tra failure

### Trước khi publish

- [ ] Không còn credential/test data không cần thiết
- [ ] Không commit dữ liệu riêng tư
- [ ] Kiểm tra permission
- [ ] Kiểm tra AI không có quyền vượt quá yêu cầu
- [ ] Activate workflow
- [ ] Monitor execution

---

# 40. Kết luận

Bài học không chỉ dạy cách dùng Claude, GitHub hay n8n riêng lẻ.

Thông điệp lớn hơn là cách xây dựng một **AI-powered system có kiểm soát**.

Mô hình tư duy:

```text
                    HUMAN
                      ↓
                  PLAN / RULES
                      ↓
             DETERMINISTIC SYSTEM
                  (n8n / Git)
                      ↓
              ┌───────┴───────┐
              │               │
        AI / Claude         External
        Reasoning           Services
              │               │
              └───────┬───────┘
                      ↓
               Structured Result
                      ↓
              Deterministic Action
                      ↓
                  HUMAN REVIEW
```

## Takeaway quan trọng nhất

**Đừng hỏi “AI có thể làm tất cả không?”**

Hãy hỏi:

> **Phần nào nên là rule cố định, phần nào cần AI, và phần nào vẫn cần con người kiểm soát?**

Đó là tư duy cốt lõi để xây dựng automation bằng AI một cách ổn định, dễ debug và có thể mở rộng.
