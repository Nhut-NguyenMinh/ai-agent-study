# Chương 01 — Tư duy nền tảng

> [Mục lục](README.md) | [Chương 02 →](02-prompting-va-context.md)

---

## 1.1. Ba thời kỳ của việc dùng AI để lập trình

Cách con người làm việc với AI đã đi qua ba giai đoạn. Nhiều người vẫn đang mắc kẹt ở
giai đoạn một trong khi công cụ đã ở giai đoạn ba.

### Thời kỳ 1 — Autocomplete

```
Người viết dở dòng code → AI đoán nốt phần còn lại
```

AI không biết dự án làm gì. Nó nhìn vài chục dòng quanh con trỏ và đoán. Giá trị thật
nhưng nhỏ: tiết kiệm gõ phím, không tiết kiệm suy nghĩ.

### Thời kỳ 2 — Chatbot sinh code

```
Người mô tả yêu cầu → AI trả về một khối code → người copy vào dự án
```

Giai đoạn này sinh ra niềm tin sai lầm rằng "prompt giỏi thì code đúng". Thực tế AI
không biết dự án có sẵn hàm nào, quy ước đặt tên ra sao, hay thay đổi này sẽ làm vỡ
cái gì. Người dùng bù đắp bằng cách viết prompt dài hơn — và prompt dài không giải
được vấn đề thiếu ngữ cảnh.

### Thời kỳ 3 — Agentic

```
Người nêu mục tiêu
      ↓
AI tự khám phá dự án
      ↓
AI lập kế hoạch
      ↓
Người duyệt kế hoạch
      ↓
AI thực hiện
      ↓
AI tự chạy thử, đọc lỗi
      ↓
AI sửa
      ↓
Kiểm chứng lại
```

Điểm khác biệt không phải model thông minh hơn. Điểm khác biệt là **AI được đặt vào
một môi trường có công cụ để tự tìm hiểu và tự kiểm tra**.

---

## 1.2. Công thức gốc

Toàn bộ cuốn sách xoay quanh một công thức:

```
AI Agent = Context + Tools + Rules + Feedback
```

Thiếu thành phần nào thì hỏng theo kiểu riêng của thành phần đó:

| Thiếu | Triệu chứng |
|---|---|
| **Context** | AI viết code đúng cú pháp nhưng sai kiến trúc, đặt tên lệch quy ước, tạo lại hàm đã có sẵn |
| **Tools** | AI chỉ nói lý thuyết, không đọc được file thật, không chạy được test, mọi khẳng định đều là phỏng đoán |
| **Rules** | AI làm đúng việc được giao rồi tiện tay "cải thiện" thêm ba file khác, hoặc chạy lệnh nguy hiểm |
| **Feedback** | AI tuyên bố "đã xong" mà chưa ai biết có chạy được không |

Bổ sung thành phần thứ năm cho những việc có rủi ro:

```
+ Validation — kiểm tra kết quả có hợp lệ trước khi cho phép hành động thật
```

**Ví dụ tổng quát về việc thiếu từng thành phần:**

Giao việc "thêm tính năng xuất báo cáo CSV" cho một agent:

- Thiếu Context → nó tạo module `export/` mới trong khi dự án đã có `app/modules/report/`.
- Thiếu Tools → nó không đọc được model `Report` nên bịa ra tên cột.
- Thiếu Rules → nó sửa luôn 14 file và commit thẳng lên nhánh chính.
- Thiếu Feedback → nó không chạy test, không biết hàm mới ném lỗi khi danh sách rỗng.
- Thiếu Validation → nó tự xoá file báo cáo cũ vì "dọn dẹp cho gọn".

Năm lỗi trên không sửa được bằng prompt hay hơn. Chúng sửa được bằng cách dựng môi trường.

---

## 1.3. Vòng lặp — thứ biến mô hình thành tác nhân

Công thức ở mục 1.2 nói agent **cần gì**. Mục này nói agent **chạy như thế nào**.

Một mô hình ngôn ngữ thuần tuý hoạt động theo một nhịp:

```
Yêu cầu → Câu trả lời
```

Một tác nhân hoạt động theo vòng:

```
┌─────────────────┐
│    QUAN SÁT     │  Đọc yêu cầu, đọc file, đọc kết quả bước trước
└────────┬────────┘
         ↓
┌─────────────────┐
│    SUY NGHĨ     │  "Tôi cần làm gì tiếp theo?" — lập kế hoạch, chọn công cụ
└────────┬────────┘
         ↓
┌─────────────────┐
│   HÀNH ĐỘNG     │  Gọi công cụ, sửa file, chạy lệnh
└────────┬────────┘
         ↓
    Nhận kết quả
         │
         └──────→ QUAN SÁT LẠI → SUY NGHĨ → HÀNH ĐỘNG → ...
                                                          ↓
                                                    HOÀN THÀNH
```

Đây là kiến thức nền quan trọng nhất của cả cuốn sách, vì nó giải thích **vì sao các
chương sau tồn tại**:

| Bước trong vòng lặp | Chương nói kỹ | Nếu bước này yếu thì |
|---|---|---|
| Quan sát | [02](02-prompting-va-context.md), [04](04-context-engineering.md), [12](12-kinh-te-ngu-canh.md) | AI làm việc trên thông tin sai hoặc thiếu |
| Suy nghĩ | [03](03-dac-ta-yeu-cau.md), [05](05-workflow-plan-execute.md), [10](10-phoi-hop-da-tac-nhan.md) | AI đi sai hướng ngay từ kế hoạch |
| Hành động | [07](07-tools-va-mcp.md), [08](08-hooks-va-guardrails.md) | AI chỉ nói lý thuyết, hoặc làm điều nguy hiểm |
| Nhận kết quả | [11](11-verification-feedback-loop.md) | AI không biết mình vừa làm đúng hay sai |

### Khi thất bại, vòng lặp mới là thứ cứu bạn

```
Thành công:  Hành động → Kết quả → Xong

Thất bại:    Hành động → Lỗi
                          ↓
                      Quan sát lỗi
                          ↓
                       Suy nghĩ
                          ↓
                    Hành động khác
```

Nhánh thứ hai chỉ chạy được nếu AI **nhìn thấy** lỗi. Đó là lý do trang bị phương tiện
tự kiểm chứng ([chương 11](11-verification-feedback-loop.md)) quan trọng hơn nhiều so
với việc cố viết yêu cầu hoàn hảo ngay từ đầu.

> **Tác nhân mạnh không phải vì đúng ngay lần đầu, mà vì lặp được chu trình
> quan sát → suy nghĩ → hành động cho tới khi đạt mục tiêu.**

---

## 1.4. Prompt Engineering, Context Engineering, Agent Engineering

Đây là insight quan trọng nhất của chương này.

| | Prompt Engineering | Context Engineering | Agent Engineering |
|---|---|---|---|
| Câu hỏi trung tâm | Nói với AI thế nào cho đúng? | AI cần biết những gì? | Dựng môi trường nào để AI tự làm việc tốt? |
| Đơn vị làm việc | Một câu lệnh | Một dự án | Một quy trình |
| Đầu ra | Một câu trả lời | Nhiều phiên nhất quán | Hệ thống lặp lại được |
| Đo lường | Câu trả lời có hay không | AI có hiểu dự án không | Kết quả có kiểm chứng được không |
| Khi sai thì sửa gì | Viết lại prompt | Bổ sung tài liệu ngữ cảnh | Thêm rule / hook / test |

Ba mức này không thay thế nhau — chúng chồng lên nhau. Nhưng đầu tư vào mức nào cho
lợi suất cao nhất thì rất rõ: sửa một prompt chỉ cứu được một lượt; sửa ngữ cảnh cứu
được mọi phiên sau đó; dựng một hook cứu được cả những phiên mà bạn không ngồi giám sát.

> **Bẫy — "prompt thần kỳ".**
> Dấu hiệu: bạn có một đoạn prompt dài 40 dòng và dán nó vào đầu mỗi cuộc trò chuyện.
> Đó là ngữ cảnh đang bị nhét sai chỗ. Nó thuộc về `CLAUDE.md` (chương 03) hoặc một
> skill (chương 05), nơi nó được nạp tự động và sửa một lần áp dụng mọi nơi.

---

## 1.5. Ví dụ thật — bốn thành phần trong PM-AGENT

PM-AGENT là dự án middleware quản lý dự án (FastAPI + PostgreSQL + HTMX). Hạ tầng agent
của nó có đủ bốn thành phần, và mỗi thành phần nằm ở một chỗ khác nhau:

```
01-src/
├── .claude/
│   ├── CLAUDE.md              ← CONTEXT: tech stack, patterns, quy ước
│   ├── rules/                 ← RULES: 14 file luật, mỗi file một chủ đề
│   │   ├── {prefix}-git-safety.md
│   │   ├── {prefix}-small-commits.md
│   │   ├── {prefix}-test-first.md
│   │   └── ...
│   ├── skills/                ← năng lực đóng gói (chương 05)
│   ├── commands/              ← workflow gọi bằng /slash (chương 05)
│   ├── hooks/                 ← GUARDRAIL do harness chạy (chương 07)
│   │   ├── post-edit-doc-reminder.sh
│   │   └── task-report-reminder.sh
│   └── settings.json          ← nơi đăng ký hook
├── .mcp.json                  ← TOOLS: MCP server nối vào hệ thống PM-AGENT
├── Makefile                   ← FEEDBACK: make test, make test-browser
└── docs/                      ← CONTEXT dài hạn: spec, ERD, tech stack
```

Đối chiếu với công thức:

| Thành phần | Hiện thân trong PM-AGENT |
|---|---|
| Context | `.claude/CLAUDE.md` + `docs/04-tech-stack.md` (nguồn sự thật cho quyết định kỹ thuật) |
| Tools | MCP server `{prefix}` khai báo trong `.mcp.json`, cộng Bash/Git/pytest sẵn có |
| Rules | 14 file trong `.claude/rules/`, ví dụ `{prefix}-git-safety.md` cấm push/commit khi chưa được yêu cầu rõ |
| Feedback | `make test`, `make test-browser`, coverage threshold trong `{prefix}-test-first.md` |
| Validation | Hook `Stop` nhắc gửi báo cáo; quy trình `feature-verify` chặn việc đóng feature khi còn test đỏ |

Điều đáng chú ý: không thành phần nào trong bảng trên là "prompt". Chúng đều là **file
nằm trong repo**, đọc được, sửa được, review được, và đi cùng dự án khi người khác clone về.

---

## 1.6. Bảy nguyên tắc gốc

Bảy nguyên tắc này là bản rút gọn của cả cuốn sách. Mỗi nguyên tắc có một chương triển khai.

### Nguyên tắc 1 — Hiểu trước, code sau

```
Sai:  Yêu cầu → Code ngay
Đúng: Yêu cầu → Khám phá → Hiểu → Lập kế hoạch → Code
```

Chi tiết ở [chương 05](05-workflow-plan-execute.md).

### Nguyên tắc 2 — Ngữ cảnh quan trọng hơn prompt

Một prompt trung bình trên nền ngữ cảnh tốt cho kết quả tốt hơn một prompt xuất sắc
trên nền ngữ cảnh trống. Chi tiết ở [chương 04](04-context-engineering.md).

### Nguyên tắc 3 — Việc lớn phải có kế hoạch được duyệt

```
Kế hoạch → Người duyệt → Thực thi
```

Duyệt một kế hoạch mất năm phút. Đọc lại 800 dòng code sai hướng mất cả buổi.

### Nguyên tắc 4 — Đừng đòi đúng ngay lần đầu, hãy dựng vòng phản hồi

Câu hỏi sai: *"Làm sao viết prompt để AI đúng ngay lần đầu?"*
Câu hỏi đúng: *"Làm sao để AI tự phát hiện là nó sai?"*

Chi tiết ở [chương 11](11-verification-feedback-loop.md).

### Nguyên tắc 5 — Không giao mọi thứ cho AI quyết định

```
AI          → phần cần hiểu ngôn ngữ, ngữ nghĩa, ý định
Hệ thống    → phần cần chính xác tuyệt đối, lặp lại được, kiểm toán được
```

Nếu logic viết được thành `if / else` thì nó thuộc về code, không thuộc về AI.
Chi tiết ở [chương 08](08-hooks-va-guardrails.md) và [chương 14](14-automation-ngoai-codebase.md).

### Nguyên tắc 6 — Hành động rủi ro phải qua cổng kiểm soát

```
AI → Đầu ra có cấu trúc → Kiểm tra hợp lệ → (Người duyệt nếu cần) → Hành động → Ghi log
```

### Nguyên tắc 7 — Nghĩ theo hệ sinh thái, không theo từng phiên

Một phiên chat là công cụ. Nhiều phiên + skills + hooks + MCP + Git + test + automation
là **hệ thống làm việc**. Mục tiêu là cái thứ hai.

---

## 1.7. Ba câu hỏi tự kiểm trước mỗi task

Trước khi gõ yêu cầu đầu tiên cho AI, tự trả lời ba câu:

**1. AI có đủ thông tin để làm đúng không?**
Nếu câu trả lời là "nó phải đoán chỗ này" → dừng lại, bổ sung ngữ cảnh trước.

**2. AI có cách nào tự biết là nó làm sai không?**
Nếu không có test, không có lệnh chạy thử, không có ảnh chụp màn hình → bạn sẽ là
người duy nhất phát hiện lỗi, và bạn sẽ phát hiện muộn.

**3. Nếu AI làm sai ở bước này, hậu quả có hoàn tác được không?**
Hoàn tác được → cứ để nó chạy. Không hoàn tác được → cần cổng duyệt.

Ba câu này ánh xạ đúng vào Context, Feedback và Validation.

---

## 1.8. Bẫy thường gặp ở giai đoạn đầu

| Bẫy | Dấu hiệu nhận biết | Chương xử lý |
|---|---|---|
| Prompt khổng lồ một phát ăn ngay | "Xây cho tôi CRM có auth, database, API, dashboard, email, deploy" | 04 |
| Nhét mọi thứ vào ngữ cảnh | `CLAUDE.md` dài 900 dòng, phiên nào cũng gần đầy context | 03 |
| Cài mọi MCP server tìm thấy | Danh sách tool dài, model chọn nhầm tool liên tục | 06 |
| Tin lời "đã xong" | Không ai chạy test, lỗi lộ ra ở môi trường thật | 09 |
| Dặn bằng lời thay vì bằng cơ chế | Cùng một lời nhắc lặp lại ở mọi phiên và vẫn bị quên | 07 |
| Dùng multi-agent cho việc đơn giản | Ba agent cho một task sửa CSS | 08 |

---

## 1.9. Bài tập

**Bài 1 — Chấm điểm môi trường hiện tại.**
Với dự án bạn đang làm, điền bảng sau. Ô nào trống chính là chương bạn nên đọc trước.

| Thành phần | Dự án của tôi có gì? |
|---|---|
| Context | |
| Tools | |
| Rules | |
| Feedback | |
| Validation | |

**Bài 2 — Tìm prompt lặp lại.**
Mở lại lịch sử ba phiên gần nhất. Tìm câu dặn nào bạn đã viết ở cả ba phiên. Đó là
ứng viên đầu tiên để đưa vào `CLAUDE.md` ở chương 03.

**Bài 3 — Xác định việc không hoàn tác được.**
Liệt kê ba hành động trong dự án mà nếu AI làm sai thì không hoàn tác được
(ví dụ: push lên nhánh chính, xoá dữ liệu, gửi email cho khách). Giữ danh sách này lại,
chương 07 sẽ dùng đến.

---

## Tóm tắt chương

```
Autocomplete  →  Chatbot sinh code  →  Agentic
                                          ↑
                          Context + Tools + Rules + Feedback (+ Validation)
```

- Prompt cứu được một lượt; ngữ cảnh cứu được mọi phiên; hạ tầng cứu được cả phiên
  không ai giám sát.
- Nếu logic viết được thành `if/else` thì đừng giao cho AI.
- Cả bốn thành phần đều là **file trong repo**, không phải câu chữ trong khung chat.

> Chương tiếp: [02 — Prompting & thu thập ngữ cảnh](02-prompting-va-context.md)
