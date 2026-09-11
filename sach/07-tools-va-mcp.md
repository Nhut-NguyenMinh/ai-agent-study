# Chương 07 — Tools & MCP

> [← Chương 06](06-skills-va-slash-commands.md) | [Mục lục](README.md) | [Chương 08 →](08-hooks-va-guardrails.md)

---

## 7.1. Chỉ dẫn không thay được công cụ

Một AI biết phải làm gì nhưng không làm được gì thì chỉ là cố vấn. Nó nói *"nên kiểm
tra log"* thay vì đọc log; nói *"nên chạy test"* thay vì chạy test.

```
Chỉ dẫn  → AI biết phải làm gì
Công cụ  → AI làm được
Ngữ cảnh → AI biết dự án này khác dự án khác chỗ nào
Phản hồi → AI biết mình làm đúng hay sai
```

Chương này nói về thành phần thứ hai.

### Các nhóm công cụ

| Nhóm | Ví dụ | Mở ra khả năng gì |
|---|---|---|
| Hệ thống tệp | đọc/ghi/tìm file | Hiểu và sửa được codebase |
| Terminal | Bash, lệnh CLI của dự án | Build, test, lint, chạy script |
| Git | log, diff, blame, status | Hiểu *vì sao* code thành ra thế này |
| Trình duyệt | Playwright, Puppeteer | Nhìn thấy giao diện thật, chụp màn hình |
| Hệ thống ngoài | MCP server | Đọc/ghi dữ liệu ngoài repo |
| Giám sát | log, tracing, error tracker | Chẩn đoán sự cố thật |

Nguyên tắc chung: **công cụ nào cho AI khả năng tự kiểm chứng thì ưu tiên trang bị trước.**

---

## 7.2. Bash tools — con đường rẻ nhất

Trước khi nghĩ đến MCP, hãy nhìn lại những gì dự án đã có sẵn dưới dạng CLI.

**Ví dụ thật — Makefile của PM-AGENT** là một bộ công cụ hoàn chỉnh mà AI dùng được ngay:

```make
dev / dev-d / down / prod       # vòng đời môi trường
migrate / migrate-create        # Alembic
seed / backfill-tasks           # dữ liệu
test / test-unit / test-unit-run
test-db-setup / test-browser / test-all / test-report
logs / logs-app / shell / ps    # chẩn đoán
backup / clean
```

Giá trị của Makefile ở đây không phải tiết kiệm gõ phím. Nó là **giao diện ổn định**:
`make test` luôn đúng, kể cả khi lệnh pytest bên dưới đổi cờ. Ghi `make test` vào
`CLAUDE.md` một lần là xong; ghi nguyên câu lệnh pytest thì sẽ lỗi thời.

### Dạy AI dùng CLI lạ

Không cần chép tài liệu vào ngữ cảnh:

```
Dùng gh CLI để tạo pull request. Nếu chưa rõ cú pháp, chạy `gh pr create --help` trước.
```

Cách này rẻ hơn nhiều so với dán tài liệu, và luôn khớp với phiên bản đang cài.

Nếu một lệnh được dùng thường xuyên thì ghi vào `CLAUDE.md` để không phải khám phá lại
ở mỗi phiên.

---

## 7.3. MCP là gì

MCP (Model Context Protocol) là chuẩn để AI nói chuyện với hệ thống bên ngoài:

```
Claude Code ──(stdio / JSON-RPC)──► MCP Server ──► Hệ thống thật
                                                   (DB, API, dịch vụ)
```

MCP server khai báo danh sách tool kèm schema tham số. AI thấy danh sách đó và gọi được
như gọi tool có sẵn.

Điểm quan trọng cần hiểu đúng: MCP không chỉ là "thêm tool". Nó **mở rộng phạm vi mà
agent quan sát và tác động được**. Một agent có MCP nối vào hệ thống quản lý task
không còn làm việc trong repo đơn độc — nó đọc được yêu cầu, hỏi lại được, và báo cáo
kết quả ngược lại.

---

## 7.4. Ví dụ thật — kiến trúc agent-gateway của PM-AGENT

PM-AGENT có một MCP server tự viết (`tools/agent-gateway/`) nối Claude Code vào chính hệ
thống quản lý dự án. Kiến trúc của nó đáng học vì nó giải quyết đúng những vấn đề mà
ai tự viết MCP server cũng gặp.

### Hai client, một tầng API

```
Claude Code ──(stdio, JSON-RPC MCP)──► mcp_server.py ──┐
                                                        ├──► HTTP /api/agent/*
Bash tool  ──(exec)──► {prefix}-cli.sh ────────────────────┘    (Bearer token)
```

Cả MCP server lẫn CLI đều gọi **cùng một tầng API** (`app/modules/agent_gateway/router.py`),
xác thực bằng token. Lợi ích:

- Không có logic nghiệp vụ nào nằm ở client — sửa quy tắc chỉ sửa ở server.
- Vẫn dùng được khi không có runtime MCP (rơi về CLI qua Bash tool).
- Quyền hạn kiểm soát ở một chỗ duy nhất.

> **Nguyên tắc: client mỏng.** MCP server chỉ ánh xạ tên tool sang endpoint, không tự
> quyết định gì. Mỗi tool map 1:1 vào đúng một endpoint.

### Dịch lỗi thay vì ném JSON thô

Chi tiết nhỏ, giá trị lớn: response lỗi (status ≥ 400) được ánh xạ qua một bảng thông
điệp thành **một câu tiếng Việt nói rõ cách sửa**, thay vì đưa JSON lỗi thô cho AI tự đoán.

```
Thô:  {"detail":{"code":"assignee_scope_mismatch"},"status":403}
Dịch: "Token không có quyền gán task cho người khác. Nhờ PM gán trong UI,
       hoặc bổ sung scope task:assign ở trang /agent-tokens."
```

Lý do: AI gặp JSON lỗi thô sẽ **thử lại nhiều lần với biến thể khác nhau**, đốt thời
gian và có khi làm hỏng thêm. Gặp câu nói rõ cách sửa thì nó dừng đúng lúc và báo lại.

### Phạm vi quyền (scope) trên token

Token của agent-gateway mang danh sách scope: `task:read`, `task:assign`, `qa:read`,
`qa:write`, `report:read`, `report:write`... Mỗi tool đòi scope tương ứng.

Đây là **guardrail ở tầng hệ thống**, không phải ở tầng lời dặn. Một agent không có
`task:assign` thì không thể gán task cho người khác — kể cả khi nó bị thuyết phục rằng
nên làm thế. Sự khác biệt so với việc viết "đừng gán task cho người khác" trong
`CLAUDE.md`: lời dặn có thể bị quên, còn 403 thì không.

### Ba tool giá trị nhất và vì sao

| Tool | Việc nó làm | Vì sao đáng có |
|---|---|---|
| `{prefix}_get_task_context` | Trả task + Q&A đã có + taxonomy trong **một lượt** | Gộp ba lời gọi thành một, tiết kiệm cả context lẫn thời gian |
| `{prefix}_ask_question` | Gửi câu hỏi cho PM ngay trong hệ thống | Biến "AI không rõ yêu cầu" từ ngõ cụt thành một bước quy trình |
| `{prefix}_submit_task_report` | Ghi nhật ký công việc lên hệ thống | Đóng vòng phản hồi ngược về phía con người |

Tool thứ nhất minh hoạ một nguyên tắc thiết kế MCP: **gộp những lời gọi luôn đi cùng
nhau**. Ba tool riêng lẻ tốn ba vòng và ba lần trả phí context.

---

## 7.5. Chia sẻ cấu hình cho cả team

Cấu hình MCP nằm trong repo thì cả team dùng chung:

```json
{
  "mcpServers": {
    "{prefix}": {
      "command": "uv",
      "args": ["run", "--with", "mcp", "--with", "httpx",
               "python", "tools/agent-gateway/mcp_server.py"],
      "env": {
        "{PREFIX}_BASE_URL": "http://localhost:8099",
        "{PREFIX}_AGENT_TOKEN": "***",
        "{PREFIX}_GET_TASK_EXCLUDE_STATUSES": ""
      }
    }
  }
}
```

> **Cảnh báo bảo mật:** ví dụ trên đã che token. Trong thực tế, file chứa token thật
> **không được commit**. Đưa file mẫu (`.mcp.json.example`) vào repo, để giá trị thật
> ở file bị `.gitignore`, hoặc đọc từ biến môi trường. Và như đã nói ở
> [mục 4.8](04-context-engineering.md): đừng bao giờ đọc nguyên file này vào ngữ cảnh —
> trích đúng trường cần.

### Hiệu ứng lan toả

Đây là lợi ích lớn nhất của cấu hình dùng chung:

> **Một người cấu hình một lần → cả team hưởng lợi.**

Khi một kỹ sư tìm ra cách tốt để chạy test, dùng CLI nội bộ, hay kiểm tra giao diện, thì
kiến thức đó nên đi vào ngữ cảnh dùng chung thay vì nằm trong đầu người đó. Ví dụ tại
Anthropic: repo ứng dụng có sẵn cấu hình MCP Puppeteer, nên mọi kỹ sư đều dùng được
chụp màn hình và chạy test đầu-cuối mà không ai phải tự cài.

---

## 7.6. Nhiều tool không đồng nghĩa mạnh hơn

Đây là bài học ngược trực giác quan trọng nhất của chương:

> **Tool càng nhiều không làm agent càng mạnh. Thường là ngược lại.**

Mỗi MCP server đang bật đều tiêu tốn ngân sách theo ba cách:

| Chi phí | Cơ chế |
|---|---|
| Context | Định nghĩa mọi tool nằm trong mọi lượt trò chuyện |
| Độ chính xác | Danh sách càng dài thì càng dễ chọn nhầm tool |
| Rủi ro | Tool không dùng vẫn là tool có quyền hành động |

### Quy tắc thực hành

```
[ ] MCP này có phục vụ công việc trong dự án HIỆN TẠI không?
[ ] Tháng vừa rồi tôi có dùng nó không?
[ ] Nó có tool nào trùng chức năng với tool đã có không?
[ ] Nếu tắt đi, tôi có mất gì không?
```

Bốn câu "không" → tắt nó đi.

Cách tiếp cận đúng: **bật theo dự án**, không bật toàn cục. Dự án nào cần MCP nào thì
khai báo trong repo dự án đó.

---

## 7.7. Bẫy thường gặp

> **Bẫy 1 — Token trong file được commit.**
> Dấu hiệu: `.mcp.json` có giá trị thật và nằm trong Git. **Cách sửa:** commit file
> mẫu, giá trị thật lấy từ biến môi trường; nếu đã lỡ commit thì **thu hồi token đó**,
> vì xoá khỏi lịch sử Git không đảm bảo nó chưa bị đọc.

> **Bẫy 2 — MCP server ném lỗi thô.**
> Agent thử lại vô ích nhiều lần. **Cách sửa:** dịch lỗi thành câu nói rõ cách sửa
> ngay ở tầng client.

> **Bẫy 3 — Tin rằng lỗi 401 là do token hỏng.**
> Sự cố thật đã ghi lại trong PM-AGENT: lỗi 401 lặp lại hoá ra là do ứng dụng đang trỏ
> vào **database khác** (môi trường test) chứ không phải token sai. **Cách sửa:** khi
> gặp lỗi xác thực, kiểm biến môi trường của container trước khi nghi ngờ token.

> **Bẫy 4 — Cho agent quyền rộng hơn mức cần.**
> Cấp token toàn quyền cho tiện. **Cách sửa:** cấp scope tối thiểu; thiếu thì bổ sung
> sau, và khi thiếu thì thông báo lỗi phải nói rõ thiếu scope nào.

> **Bẫy 5 — MCP làm thay việc mà CLI đã làm tốt.**
> Viết MCP server để chạy test trong khi `make test` đã đủ. **Cách sửa:** MCP dành cho
> hệ thống không có CLI; có CLI rồi thì dùng Bash.

---

## 7.8. Bài tập

**Bài 1 — Kiểm kê công cụ.**
Liệt kê mọi công cụ AI đang có quyền dùng trong dự án của bạn. Đánh dấu cái nào chưa
dùng lần nào trong tháng qua. Tắt chúng đi và làm việc một tuần — nếu không thiếu thì
đừng bật lại.

**Bài 2 — Bọc lệnh hay dùng vào Makefile.**
Tìm ba lệnh dài bạn hay gõ. Đưa vào `Makefile` với tên ngắn, ghi vào `CLAUDE.md`.

**Bài 3 — Đọc lỗi qua con mắt của AI.**
Cố tình tạo một lỗi (sai token, sai URL). Xem thông điệp lỗi mà AI nhận được. Nếu nó
không đủ để biết cách sửa, hãy cải thiện thông điệp đó.

---

## Tóm tắt chương

- Chỉ dẫn cho AI biết *phải làm gì*; công cụ cho nó *làm được*.
- Ưu tiên công cụ giúp AI **tự kiểm chứng** (test, log, ảnh chụp màn hình).
- Bash/CLI/Makefile là con đường rẻ nhất — dùng hết trước khi nghĩ tới MCP.
- MCP server nên là **client mỏng**: ánh xạ 1:1 vào API, không chứa logic nghiệp vụ.
- **Dịch lỗi** thành câu nói rõ cách sửa, đừng ném JSON thô.
- Phạm vi quyền (scope) là guardrail cứng, mạnh hơn lời dặn trong `CLAUDE.md`.
- Cấu hình dùng chung tạo hiệu ứng lan toả: một người cấu hình, cả team hưởng.
- **Ít tool nhưng đúng** — mỗi tool thừa tốn context, tăng nhầm lẫn và tăng rủi ro.

> Chương tiếp: [08 — Hooks & Guardrails](08-hooks-va-guardrails.md)
