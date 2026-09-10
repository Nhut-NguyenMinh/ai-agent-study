# Claude Code Masterclass --- Tổng hợp bài học & điểm cần lưu ý

> **Nguồn:** Claude Code Masterclass 4 Hours: Build & Sell (2026) ---
> Michele Torti\
> **Mục tiêu:** Đúc kết tư duy, kiến trúc và workflow thực tế khi xây
> dựng hệ thống làm việc với Claude Code/Agent.

------------------------------------------------------------------------

## 1. Tư tưởng cốt lõi

Điểm quan trọng nhất của bài học:

> **Claude Code không chỉ là công cụ sinh code. Hãy xem nó như một AI
> employee / hệ điều hành cho workflow của dự án.**

Một hệ thống agent hiệu quả không chỉ có model. Nó cần nhiều lớp:

**Goal → Context → Instructions → Skills → Tools/MCP → Hooks/Guardrails
→ Agents → Verification → Automation → Deployment**

Nếu chỉ đưa prompt cho AI mà thiếu context, quy trình và kiểm soát, kết
quả sẽ thiếu ổn định.

------------------------------------------------------------------------

# 2. CLAUDE.md --- "Sổ tay vận hành" của dự án

`CLAUDE.md` là một trong những thành phần quan trọng nhất.

Có thể hình dung:

> **CLAUDE.md = Blueprint + Project Handbook + Memory của dự án**

Nó giúp Claude hiểu:

-   Dự án này là gì
-   Mục tiêu là gì
-   Cấu trúc thư mục
-   Quy tắc coding
-   Quy ước đặt tên
-   Cách chạy/test project
-   Những điều không được làm
-   Quy trình làm việc
-   Các quyết định quan trọng của dự án

### Nguyên tắc

Đừng bắt Claude phải "đoán" lại những điều đã biết.

Nếu một quy tắc được lặp lại nhiều lần trong prompt:

**→ Đưa quy tắc đó vào `CLAUDE.md`.**

------------------------------------------------------------------------

# 3. Skills --- biến prompt thành workflow có thể tái sử dụng

### Skills là gì?

Skill có thể hiểu như:

> **Instruction Manual --- tài liệu hướng dẫn Claude thực hiện một loại
> công việc cụ thể.**

Ví dụ:

-   Skill nghiên cứu
-   Skill phân tích comment
-   Skill tạo báo cáo
-   Skill thiết kế UI
-   Skill tạo diagram
-   Skill phân tích dữ liệu
-   Skill tạo content

Không có skill:

> Claude phải tự suy đoán cách thực hiện.

Có skill:

> Claude có SOP rõ ràng để thực hiện.

### Giá trị lớn nhất

Nếu bạn thường xuyên viết cùng một loại prompt:

**Prompt lặp lại → Skill**

Thay vì mỗi lần giải thích lại 20--50 dòng:

**Skill → gọi lại workflow bất cứ lúc nào.**

------------------------------------------------------------------------

# 4. Slash Commands --- biến workflow thành "nút bấm"

Slash command giúp gọi nhanh một workflow.

Ví dụ về tư duy:

``` text
/analyze
/report
/research
/compare
/test
/deploy
```

Thay vì phải mô tả toàn bộ quy trình mỗi lần, người dùng chỉ cần gọi
command.

### Công thức

``` text
Slash Command
      ↓
Skill / Workflow
      ↓
Claude thực hiện các bước
      ↓
Output chuẩn hóa
```

------------------------------------------------------------------------

# 5. Hooks --- Guardrails cho Agent

Hooks được dùng để tạo các điểm kiểm soát trong workflow.

Có thể xem:

> **Hooks = hệ thống bảo vệ / luật an toàn của agent**

Mục đích:

-   Kiểm tra trước khi thực hiện hành động
-   Kiểm tra sau khi thay đổi
-   Tự động chạy validation
-   Ngăn một số hành động nguy hiểm
-   Giữ workflow nhất quán

Điều này đặc biệt quan trọng khi agent có quyền thực thi lệnh hoặc thay
đổi project.

------------------------------------------------------------------------

# 6. MCP --- mở rộng "tay chân" của Claude

MCP giúp Claude kết nối với các công cụ/dịch vụ bên ngoài.

Ví dụ trong bài học:

-   Google Maps
-   Supabase
-   Chrome
-   Database
-   Các external tools khác

Có thể hiểu:

``` text
Claude
  │
  ├── Reasoning
  │
  ├── Skills
  │
  ├── MCP
  │     ├── Database
  │     ├── Browser
  │     ├── Maps
  │     └── External APIs
  │
  └── Project files
```

### Nhưng không nên cài quá nhiều MCP

Một điểm rất đáng lưu ý:

> **Tool càng nhiều không đồng nghĩa agent càng mạnh.**

System prompt, built-in tools, MCP tools và conversation đều tiêu tốn
context/token.

Vì vậy:

**Chỉ bật những MCP thực sự cần thiết.**

------------------------------------------------------------------------

# 7. Context quan trọng hơn Prompt

Một trong những bài học lớn nhất:

> **Context tốt thường quan trọng hơn prompt dài.**

Sai lầm phổ biến:

``` text
“Build me a CRM with:
- authentication
- database
- API
- dashboard
- emails
- deployment...
”
```

Một prompt khổng lồ khiến:

-   Context quá lớn
-   Token tiêu tốn nhiều
-   Agent phải xử lý quá nhiều mục tiêu
-   Chất lượng output giảm
-   Khó review
-   Khó sửa lỗi

### Cách tốt hơn

Chia thành các task:

``` text
Task 1 → Requirements
Task 2 → Architecture
Task 3 → Database
Task 4 → Backend
Task 5 → Frontend
Task 6 → Authentication
Task 7 → Testing
Task 8 → Deployment
```

Sau mỗi bước:

**Review → Fix → Continue**

------------------------------------------------------------------------

# 8. Plan Mode --- Plan trước khi Code

Plan Mode phù hợp với:

-   Feature lớn
-   Task nhiều bước
-   Refactor phức tạp
-   Nghiên cứu trước khi implementation
-   Những task cần thay đổi nhiều file

Workflow:

``` text
User Goal
   ↓
Explore / Research
   ↓
Plan
   ↓
Review & Approve
   ↓
Execute
   ↓
Verify
```

### Khi nào không cần Plan Mode?

Task rất đơn giản có thể làm trực tiếp:

-   Sửa typo
-   Đổi màu
-   Đổi text
-   Sửa một lỗi nhỏ đã rõ nguyên nhân

### Quy tắc thực tế

> **Task càng phức tạp → càng nên plan trước.**

------------------------------------------------------------------------

# 9. Spec → Todo → Code

Đây là workflow rất đáng áp dụng.

## Bước 1 --- Spec

Tạo `spec.md` mô tả:

-   Requirements
-   Mục tiêu
-   Tech stack
-   Inputs
-   Outputs
-   UI layout
-   User flow
-   Demo data
-   Milestones
-   Acceptance criteria

Spec đóng vai trò:

> **SOP + persistent context cho feature.**

## Bước 2 --- Todo

Từ spec tạo danh sách task:

``` text
[ ] Setup
[ ] Architecture
[ ] Database
[ ] API
[ ] UI
[ ] Integration
[ ] Testing
[ ] Deployment
```

## Bước 3 --- Code

Agent thực hiện từng task.

### Workflow chuẩn

``` text
Idea
 ↓
Spec
 ↓
Todo
 ↓
Implementation
 ↓
Test
 ↓
Review
 ↓
Iterate
```

------------------------------------------------------------------------

# 10. "Never give one prompt to start"

Một nguyên tắc được nhấn mạnh:

> **Đừng bắt đầu một dự án phức tạp bằng một prompt khổng lồ.**

Thay vào đó:

``` text
Goal
 ↓
Explore
 ↓
Plan
 ↓
Spec
 ↓
Todo
 ↓
Implement
 ↓
Verify
```

Agentic workflow tốt không phải là:

> "AI, hãy xây toàn bộ sản phẩm."

Mà là:

> "Đây là mục tiêu, đây là context, đây là spec. Hãy lập kế hoạch, thực
> hiện từng bước và xác minh kết quả."

------------------------------------------------------------------------

# 11. Sub-Agents --- chia nhỏ chuyên môn

Sub-agent phù hợp khi một task có thể tách thành những công việc độc
lập.

Ví dụ:

``` text
Main Agent
   │
   ├── Research Agent
   ├── UI Agent
   ├── Coding Agent
   └── Review Agent
```

Mỗi agent có:

-   Nhiệm vụ riêng
-   Context riêng
-   Output riêng

### Khi nên dùng?

Khi task có thể chạy song song:

``` text
Research ─────┐
UI Analysis ──┼──→ Main Agent
Data Analysis ─┘
```

### Lợi ích

-   Giảm context cho main agent
-   Có thể chạy song song
-   Phân chia chuyên môn
-   Tiết kiệm thời gian

------------------------------------------------------------------------

# 12. Agent Teams --- nhiều agent phối hợp

Agent Teams mạnh hơn khi các agent cần:

-   Trao đổi với nhau
-   Chia sẻ tiến độ
-   Review lẫn nhau
-   Phối hợp trên một bài toán phức tạp

Ví dụ:

``` text
             Lead Agent
                 │
       ┌─────────┼─────────┐
       ↓         ↓         ↓
   Frontend   Backend    QA/Review
       │         │         │
       └─────────┼─────────┘
                 ↓
              Final
```

### Sub-Agent vs Agent Team

  Tình huống                           Nên dùng
  ------------------------------------ -------------
  Task độc lập                         Sub-agents
  Cần chạy song song                   Sub-agents
  Workflow đơn giản                    Main agent
  Bài toán phức tạp                    Agent Teams
  Agent cần trao đổi                   Agent Teams
  Software lớn, nhiều phần liên quan   Agent Teams

### Lưu ý

Agent Teams có thể tốn nhiều tài nguyên/context hơn.

Vì vậy:

> **Không dùng multi-agent chỉ vì "trông rất AI".**

Dùng khi nó thực sự tạo lợi ích.

------------------------------------------------------------------------

# 13. 8 Hack cho Agentic Workflow

## Hack 1 --- Project Folder Structure

Cấu trúc project phải rõ ràng.

Ví dụ:

``` text
AI-WORKSPACE/
├── CLAUDE.md
├── directives/
│   ├── requirements.md
│   ├── ui-spec.md
│   ├── coding-rules.md
│   └── workflow-rules.md
├── skills/
│   ├── ui-design/
│   ├── pixel-spec/
│   ├── content/
│   ├── research/
│   └── documentation/
├── agents/
│   ├── researcher/
│   ├── designer/
│   ├── developer/
│   └── reviewer/
├── references/
├── execution/
└── outputs/
```

Folder structure giúp agent định vị:

-   Context
-   Instructions
-   Tools
-   Work
-   Outputs

------------------------------------------------------------------------

## Hack 2 --- Three-Layer Agent Architecture

Một kiến trúc quan trọng:

``` text
CLAUDE.md
    ↓
Directives
    ↓
Agents / Skills
    ↓
Execution
    ↓
Output
```

Trong đó:

### CLAUDE.md

Blueprint tổng thể.

### Directives

Quy định/SOP chi tiết.

Ví dụ:

-   Requirements
-   UI rules
-   Coding rules
-   Workflow rules

### Agents / Skills

Thực thi chuyên môn.

------------------------------------------------------------------------

# 14. Context Management

Agent càng mạnh thì việc quản lý context càng quan trọng.

Nguồn context gồm:

``` text
System Instructions
+ Project Context
+ CLAUDE.md
+ Conversation
+ Skills
+ Tools
+ MCP
+ Files
```

Nếu tất cả đều quá lớn:

**→ context bị phân mảnh và chi phí token tăng.**

### Nguyên tắc

> **Context phải đủ, không phải càng nhiều càng tốt.**

Hãy đưa cho agent:

-   Đúng thông tin
-   Đúng thời điểm
-   Đúng task

------------------------------------------------------------------------

# 15. Reusable Workflow

Nếu một công việc được thực hiện nhiều lần:

**Đừng tiếp tục copy/paste prompt.**

Hãy biến nó thành:

``` text
Prompt
 ↓
Standardize
 ↓
Skill
 ↓
Slash Command
 ↓
Reusable Workflow
```

Ví dụ:

``` text
/research-news
/analyze-comments
/create-report
/compare-products
/create-diagram
```

Một lần xây workflow tốt có thể dùng hàng trăm lần.

------------------------------------------------------------------------

# 16. Verification --- Không tin output mù quáng

Một nguyên tắc cực kỳ quan trọng:

> **AI-generated code không nên được copy thẳng vào production mà không
> review/test.**

Agent có thể:

-   Hiểu sai yêu cầu
-   Chọn sai implementation
-   Bỏ sót edge case
-   Tạo bug
-   Không hiểu đầy đủ business context

Do đó workflow phải có:

``` text
Implement
 ↓
Test
 ↓
Verify
 ↓
Review
 ↓
Fix
```

### Đặc biệt

Khi agent có quyền thực thi command hoặc thay đổi hệ thống:

**Approval + Hooks + Verification** càng quan trọng.

------------------------------------------------------------------------

# 17. Parallelization

Không phải task nào cũng cần chạy tuần tự.

Nếu hai task độc lập:

``` text
Task A ─────┐
            ├──→ Combine
Task B ─────┘
```

Có thể chạy song song.

Ví dụ:

-   Agent A nghiên cứu
-   Agent B phân tích UI
-   Agent C phân tích dữ liệu

Sau đó main agent tổng hợp.

### Nhưng:

Nếu task B phụ thuộc kết quả task A:

``` text
A → B → C
```

Không nên ép chạy song song.

------------------------------------------------------------------------

# 18. Deployment --- biến Agent thành hệ thống chạy thực tế

Một workflow chỉ chạy trên máy cá nhân chưa phải là hệ thống hoàn chỉnh.

Bài học đề cập tới deployment thông qua các nền tảng như:

-   Modal
-   Railway
-   Vercel

Tư duy quan trọng:

``` text
Local Prototype
      ↓
Production
      ↓
Deployment
      ↓
Automation
      ↓
24/7 System
```

Khi workflow được deploy:

> AI không còn chỉ là công cụ bạn mở lên dùng, mà trở thành một hệ thống
> có thể vận hành liên tục.

------------------------------------------------------------------------

# 19. Build #1 --- YouTube Content Pipeline

Một ví dụ thực tế trong video là xây pipeline phục vụ content YouTube.

Tư duy pipeline:

``` text
Research / Ideation
        ↓
Content Strategy
        ↓
Packaging
        ↓
Content Production
        ↓
Publishing
        ↓
Analytics
        ↓
Optimization
```

Điểm đáng học không phải chỉ là "làm YouTube bằng AI".

Điểm quan trọng hơn:

> **Hãy biến một quy trình kinh doanh lặp lại thành pipeline có thể tự
> động hóa.**

------------------------------------------------------------------------

# 20. Build #2 --- Website giá trị cao

Ví dụ thứ hai là xây website premium.

Bài học cần rút ra:

Không bắt đầu bằng:

> "Hãy code cho tôi một website thật đẹp."

Mà nên đi theo:

``` text
Business Goal
 ↓
Requirements
 ↓
Research
 ↓
Design Direction
 ↓
Spec
 ↓
Plan
 ↓
Implementation
 ↓
Review
 ↓
Deploy
```

AI có thể hỗ trợ cả:

-   Research
-   UX/UI
-   Content
-   Coding
-   Testing
-   Deployment

Nhưng chất lượng phụ thuộc rất lớn vào **context + spec + workflow**.

------------------------------------------------------------------------

# 21. Bài học kinh doanh: Đừng bán "AI"

Một insight quan trọng:

> **Khách hàng không mua Claude Code. Họ mua kết quả.**

Không nên định vị:

> "Tôi biết dùng Claude Code."

Nên định vị:

> "Tôi xây hệ thống giúp doanh nghiệp giảm X giờ công / tăng Y output /
> tự động hóa Z quy trình."

Ví dụ:

``` text
AI Skill
    ↓
Workflow
    ↓
Automation
    ↓
Business System
    ↓
Measurable Result
```

Giá trị nằm ở **outcome**, không nằm ở việc bạn dùng model nào.

------------------------------------------------------------------------

# 22. Kiến trúc AI Workspace nên hướng tới

Một workspace có thể tổ chức như sau:

``` text
AI-WORKSPACE/
│
├── CLAUDE.md
│
├── directives/
│   ├── requirements.md
│   ├── ui-spec.md
│   ├── coding-rules.md
│   └── workflow-rules.md
│
├── skills/
│   ├── ui-design/
│   ├── pixel-spec/
│   ├── content/
│   ├── research/
│   └── documentation/
│
├── agents/
│   ├── researcher/
│   ├── designer/
│   ├── developer/
│   └── reviewer/
│
├── references/
│
├── execution/
│
└── outputs/
```

Đây không phải một cấu trúc bắt buộc duy nhất, mà là một cách tư duy:

> **Tách context --- rules --- skills --- agents --- execution ---
> output.**

------------------------------------------------------------------------

# 23. 10 nguyên tắc cần nhớ nhất

## 1. Context trước Prompt

Đừng chỉ chăm chăm viết prompt hay.

Hãy xây context tốt.

------------------------------------------------------------------------

## 2. Plan trước Code

Task phức tạp:

**Plan → Review → Execute**

------------------------------------------------------------------------

## 3. Prompt lặp lại → Skill

Nếu bạn viết lại cùng một hướng dẫn nhiều lần:

**→ Chuẩn hóa thành Skill.**

------------------------------------------------------------------------

## 4. CLAUDE.md là bộ nhớ dự án

Những kiến thức/quy tắc lâu dài của project nên được ghi lại.

------------------------------------------------------------------------

## 5. Tool ít nhưng đúng

MCP quá nhiều có thể làm context/token phình to.

------------------------------------------------------------------------

## 6. Chia task thay vì prompt khổng lồ

``` text
Big Task
 ↓
Small Tasks
 ↓
Review từng bước
```

------------------------------------------------------------------------

## 7. Sub-agent cho task độc lập

Parallel work → Sub-agents.

------------------------------------------------------------------------

## 8. Agent Teams cho bài toán cần phối hợp

Không dùng multi-agent nếu không cần.

------------------------------------------------------------------------

## 9. Luôn Verification

``` text
Code ≠ Done

Code
 ↓
Test
 ↓
Review
 ↓
Verify
 = Done
```

------------------------------------------------------------------------

## 10. Xây hệ thống, không xây demo

Mục tiêu cuối cùng:

``` text
One-off AI task
      ↓
Repeatable Workflow
      ↓
Reusable Skill
      ↓
Agent
      ↓
Automation
      ↓
Production System
```

------------------------------------------------------------------------

# 24. Mental Model cuối cùng

Có thể ghi nhớ toàn bộ bài học bằng chuỗi sau:

``` text
GOAL
  ↓
CONTEXT
  ↓
CLAUDE.md
  ↓
SPEC
  ↓
PLAN
  ↓
SKILLS + MCP + HOOKS
  ↓
AGENTS
  ↓
IMPLEMENT
  ↓
TEST / VERIFY
  ↓
DEPLOY
  ↓
AUTOMATE
  ↓
MONITOR
  ↓
IMPROVE
```

Hoặc ngắn gọn hơn:

> **Goal → Context → Spec → Plan → Execute → Verify → Automate → Deploy
> → Improve**

------------------------------------------------------------------------

# 25. Checklist áp dụng thực tế

Trước khi giao một task lớn cho Claude Code, hãy tự hỏi:

### Context

-   [ ] Claude đã hiểu mục tiêu chưa?
-   [ ] Có `CLAUDE.md` chưa?
-   [ ] Có reference cần thiết chưa?

### Requirements

-   [ ] Đã có spec chưa?
-   [ ] Input/output đã rõ chưa?
-   [ ] Acceptance criteria đã rõ chưa?

### Execution

-   [ ] Task có cần Plan Mode không?
-   [ ] Có thể chia nhỏ không?
-   [ ] Task nào có thể chạy song song?

### Agent Architecture

-   [ ] Có cần Skill không?
-   [ ] Có cần Sub-agent không?
-   [ ] Có thực sự cần Agent Team không?
-   [ ] MCP nào thực sự cần?

### Safety

-   [ ] Có approval cần thiết không?
-   [ ] Có hooks/guardrails không?
-   [ ] Có test/verification không?

### Reusability

-   [ ] Đây có phải task lặp lại không?
-   [ ] Có nên biến thành Skill không?
-   [ ] Có nên tạo Slash Command không?

### Production

-   [ ] Đã test chưa?
-   [ ] Đã review chưa?
-   [ ] Có cần deployment không?
-   [ ] Có thể tự động hóa không?

------------------------------------------------------------------------

# 26. Kết luận

Bài học lớn nhất của Claude Code Masterclass không phải là học thuộc các
command.

Đó là học cách **thiết kế một hệ thống làm việc với AI**.

Người mới thường nghĩ:

``` text
Prompt → AI → Code
```

Tư duy agentic tốt hơn:

``` text
Goal
 ↓
Context
 ↓
Instructions
 ↓
Skills
 ↓
Tools
 ↓
Plan
 ↓
Agents
 ↓
Execution
 ↓
Verification
 ↓
Automation
 ↓
Production
```

Và sự khác biệt lớn nhất nằm ở đây:

> **Đừng cố trở thành người viết prompt giỏi nhất. Hãy trở thành người
> thiết kế workflow và hệ thống AI tốt.**

Khi một workflow đã được chuẩn hóa thành:

**Context + Skill + Tool + Guardrail + Agent + Verification**

thì bạn không chỉ giải quyết một task.

Bạn đang xây một **AI system có khả năng tái sử dụng và mở rộng**.
