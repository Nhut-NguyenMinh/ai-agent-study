# Chương 16 — Lộ trình & Case study

> [← Chương 15](15-chon-cong-cu-ai.md) | [Mục lục](README.md) | [Phụ lục A →](A-phu-luc-cu-phap.md)

---

## 16.1. Bảy cấp độ trưởng thành

Bảy cấp này mô tả quá trình đi từ dùng AI như một hộp thoại sang dùng AI như hạ tầng
vận hành. Không ai nhảy cóc được, nhưng ai cũng có thể đi nhanh nếu biết bậc kế tiếp
là gì.

### Cấp 1 — Hỏi đáp

```
Tôi hỏi → AI trả lời
```

### Cấp 2 — Yêu cầu có cấu trúc

```
Task + Context + Format + Reference → AI
```
→ [Chương 02](02-prompting-va-context.md)

### Cấp 3 — Đối thoại

```
AI trả lời → Đánh giá → Phản hồi → Lặp lại
```
Nhận ra kết quả đầu tiên chỉ là điểm bắt đầu.

### Cấp 4 — Kết hợp nhiều AI

```
AI A tạo phương án → AI B phản biện → AI C nghiên cứu → Con người quyết định
```
→ [Chương 09](09-subagents-va-song-song.md)

### Cấp 5 — Quy trình tự động

```
Trigger → AI → Công cụ → CSDL → Email → Hệ thống
```
→ [Chương 14](14-automation-ngoai-codebase.md)

### Cấp 6 — Agent

```
Mục tiêu → Agent tự lập kế hoạch → dùng công cụ → thực hiện nhiều bước
→ tự đánh giá → hoàn thành
```
→ [Chương 05](05-workflow-plan-execute.md) đến [11](11-verification-feedback-loop.md)

### Cấp 7 — Hệ thống AI riêng

```
Dữ liệu + Model + Agents + Automation + Phần mềm tự xây
```
AI không còn là hộp thoại — nó là **hạ tầng vận hành**.

### Tự xác định vị trí

| Dấu hiệu | Bạn đang ở |
|---|---|
| Hài lòng/thất vọng với câu trả lời đầu tiên | Cấp 1–2 |
| Có phản hồi cụ thể, biết bổ sung ngữ cảnh | Cấp 3 |
| Có `CLAUDE.md`, AI làm việc nhất quán giữa các phiên | Cấp 3–4 |
| Có skill/command tái dùng, có hook | Cấp 5 |
| AI tự chạy test, đọc lỗi, tự sửa, có cổng kiểm chứng | Cấp 6 |
| Có automation chạy khi bạn không ngồi máy | Cấp 7 |

Cấp 6 là nơi lợi ích tăng vọt, và cũng là nơi hầu hết người dùng dừng lại vì bỏ qua
[chương 11](11-verification-feedback-loop.md).

---

## 16.2. Case study — sự cố Stored XSS trong Task Proposal

Đây là một sự cố **có thật**, xảy ra ngày 19/08/2026 trong PM-AGENT. Nó đi qua gần như
toàn bộ nội dung cuốn sách, nên đáng đọc kỹ.

### Bối cảnh

Người dùng báo một lỗi trông rất tầm thường:

> "Task List chỉ hiển thị 2/20 task ở trang 1, trong khi tổng có 75 task."

Nhìn qua thì đây là lỗi phân trang hoặc lỗi truy vấn.

### Bước 1 — Chẩn đoán từ mã nguồn

Đọc code thay vì đoán, tìm ra chuỗi nhân quả:

```
Agent gửi đề xuất task qua MCP  (mô tả là text tự do)
        ↓
approve_task_proposal()  — app/modules/task/service.py:485
        ↓
copy proposal.description THẲNG vào Task.description
        ↓  (KHÔNG đi qua _sanitize_description() đã có sẵn)
list.html:86 render bằng | safe
        ↓
HTML thô không đóng thẻ đúng → vỡ bố cục danh sách
```

Đến đây, kết luận tự nhiên là: "lỗi hiển thị, thêm sanitize là xong".

### Bước 2 — Vòng phản biện đổi hoàn toàn mức độ nghiêm trọng

Theo luật `{prefix}-critique-loop`, trước khi đề xuất phương án phải chạy một agent phản biện
độc lập. Agent này kết luận khác hẳn:

> Đây không phải lỗi hiển thị. Đây là **Stored XSS thật, mức Critical**.

Ba mắt xích mà chẩn đoán ban đầu bỏ sót:

1. Màn hình duyệt đề xuất (`proposal_list.html`) **không hiển thị mô tả** trước khi
   duyệt → PM duyệt "mù", không nhìn thấy payload.
2. HTMX 2.0.3 mặc định cho phép thẻ script chạy trong nội dung được swap.
3. Vì vậy script thực thi **cho mọi người xem Task List**, không chỉ người tạo.

> **Bài học 1:** Nếu không có vòng phản biện độc lập, sự cố này đã được vá như một lỗi
> CSS. Lỗ hổng vẫn còn nguyên, và không ai biết.
> → [Chương 05, mục 5.4](05-workflow-plan-execute.md)

### Bước 3 — Tra cứu trước khi làm

Truy vấn CSDL phát hiện: lỗi này **đã được báo trước đó một ngày** ở một task khác, với
báo cáo đầy đủ hơn và đã kèm đề xuất sửa gần giống.

> **Bài học 2:** Kiểm tra trùng lặp trước khi bắt tay. Hai người sửa cùng một lỗi theo
> hai cách là cách tạo ra xung đột và mâu thuẫn.

### Bước 4 — Hỏi đúng hai câu, không hỏi lan man

Hai quyết định thuộc về người dùng, không thuộc về AI:

- **Phạm vi sửa:** chỉ vá đường ghi, hay làm đầy đủ (render markdown đẹp + backfill dữ
  liệu cũ)? → chọn đầy đủ.
- **Hai task trùng:** xử lý thế nào? → cập nhật cả hai sau khi sửa xong.

> **Bài học 3:** Hỏi những câu mà **câu trả lời làm thay đổi công việc**. Không hỏi
> những câu có mặc định hợp lý.

### Bước 5 — Test-first, và kiểm chứng thực nghiệm trước khi viết assertion

Cập nhật spec (thêm business rule và hai tiêu chí nghiệm thu), rồi viết 11 test case
unit **trước khi sửa code**.

Chi tiết đắt giá nhất của cả case study nằm ở đây: trước khi chốt assertion, một script
Python được chạy trong container để xem **thư viện markdown và thư viện sanitize thật
sự trả về gì**.

Kết quả trái với trực giác:

- Heading `##` **không** tạo ra `<h2>` mà thành text trần trong cấu hình đó.
- List cần một dòng trống phía trước mới sinh ra `<li>`; không có thì thành text
  thường nối bằng `<br>`.

Nếu viết assertion theo suy đoán, 11 test case đó đã sai ngay từ đầu — và sai theo kiểu
tệ nhất: chúng sẽ được "sửa cho khớp code" ở bước sau.

> **Bài học 4:** Kiểm chứng hành vi thư viện bằng thực nghiệm trước khi viết assertion.
> → [Chương 02, mục 2.8](02-prompting-va-context.md) và [chương 11](11-verification-feedback-loop.md)

### Bước 6 — Chạy test trên code CŨ trước

11 test case được chạy trên code chưa sửa: **9/11 thất bại đúng như dự đoán**.

> **Bài học 5:** Test phải đỏ *trước khi* sửa. Một test không bao giờ đỏ là một test
> không chứng minh được gì.

### Bước 7 — Sửa, giữ nguyên các quyết định bảo mật cũ

Hàm sửa lỗi dùng danh sách thẻ cho phép riêng: **bằng danh sách của Task, trừ `img`** —
để không mở lại một lỗ hổng SSRF/tracking-pixel đã được chặn trước đó ở một luồng khác.

> **Bài học 6:** Kiểm quyết định cũ trước khi thay đổi. Sửa lỗi này mà mở lại lỗ hổng
> kia là đi lùi.
> → [Chương 05, mục 5.8](05-workflow-plan-execute.md)

### Bước 8 — Backfill dữ liệu cũ, có điều kiện an toàn

Tám task đã tạo trước khi sửa cần được làm sạch. Script backfill chỉ động vào bản ghi
**mà mô tả của task còn giống hệt mô tả trong đề xuất gốc** — tức là PM chưa từng sửa tay.

Quy trình: chạy thử (dry-run) → người dùng xác nhận → mới chạy thật.

> **Bài học 7:** Vá đường ghi không làm sạch dữ liệu đã nhiễm. Và khi động vào dữ liệu,
> điều kiện an toàn phải cụ thể, không phải "chắc là không sao".

### Bước 9 — Browser test, và nó tìm ra lỗi thứ hai

Người dùng yêu cầu thêm browser test (không có trong phạm vi ban đầu) để xác nhận bố
cục thật qua luồng đề xuất → duyệt thật.

Khi chạy lại lần thứ hai để xác nhận (thay vì tin kết quả lần đầu), một lỗi khác lộ ra:
phần dọn dẹp của browser test **không xoá Task được sinh ra từ thao tác "Duyệt" trên
giao diện**. Task mồ côi đó chặn việc xoá project ở lần chuẩn bị dữ liệu tiếp theo,
gây lỗi 500 khó hiểu.

Quá trình gỡ lỗi đi qua hai giả thuyết sai trước khi tìm đúng: nghi dữ liệu thừa (sai),
nghi log lẫn giữa các lần chạy (đúng một phần), và cuối cùng phát hiện browser test dùng
**một CSDL riêng** — nên toàn bộ các lệnh kiểm tra trước đó đều tra nhầm cơ sở dữ liệu.

> **Bài học 8:** Chạy lại để xác nhận, đừng tin kết quả cũ. Và khi gỡ lỗi, hãy kiểm
> **giả định về môi trường** trước khi đi sâu vào logic.
> → [Chương 11, mục 11.7](11-verification-feedback-loop.md)

### Bước 10 — Bốn commit nhỏ, không phải một commit lớn

```
1. Vá đường ghi + cập nhật spec
2. Test unit
3. Test browser
4. Script backfill
```

→ [Chương 05, mục 5.5](05-workflow-plan-execute.md)

### Bước 11 — Đóng đúng quy trình, không tự ý

Sau khi code và test đều xanh, trạng thái task vẫn để ở "đang test". Người dùng hỏi vì
sao chưa đóng. Câu trả lời: quy trình dự án yêu cầu chạy `feature-verify` rồi **người
xác nhận** mới đóng — không tự đóng chỉ vì test xanh.

Và việc chạy `feature-verify` chính thức đã sinh thêm hai commit nữa (đánh dấu tiêu chí
nghiệm thu trong spec, thêm mục CHANGELOG).

> **Bài học 9:** "Test xanh" không phải "đã đóng". Cổng xác nhận của con người tồn tại
> vì hai điều đó khác nhau.
> → [Chương 08, mục 8.9](08-hooks-va-guardrails.md)

### Kết quả

```
35/35 unit test  ✅
6/6 browser test ✅
8 bản ghi cũ đã làm sạch, kiểm chứng trên CSDL
6 commit, đã đẩy lên remote
2 task đã cập nhật trạng thái + ghi chú tổng kết qua MCP
```

### Hai lỗi suýt xảy ra, và cái đã chặn chúng

**Lỗi suýt xảy ra 1 — thao tác Git nguy hiểm.**
Định dùng `git stash` rồi `git stash pop` để so sánh trạng thái gốc. Luật
`{prefix}-git-safety` chặn lại (`stash pop` nằm trong danh sách cấm vì có thể gây xung đột).
Cách an toàn được dùng thay thế: `git log --all -- <file>` và `find` để xác nhận module
đó chưa từng tồn tại — không cần động vào thư mục làm việc.

> **Bài học 10:** Guardrail có giá trị nhất đúng vào lúc bạn đang vội và thấy nó phiền.

**Lỗi suýt xảy ra 2 — YAML nuốt assertion.**
Trong một test case, key `not_contains` bị viết hai lần trong cùng một dict. YAML **âm
thầm chỉ giữ giá trị cuối** — assertion đầu bị bỏ qua, không báo lỗi. Phát hiện trước
khi chạy, nhưng nó minh hoạ một loại lỗi rất khó thấy: khung test chỉ hỗ trợ một giá
trị chuỗi cho mỗi key, không hỗ trợ danh sách.

> **Bài học 11:** Cấu hình sai cú pháp thường **im lặng** chứ không báo lỗi. Đọc kỹ
> ngữ nghĩa của khung test, đừng suy từ trực giác.

### Bản đồ: case study này dùng những chương nào

| Bước | Chương liên quan |
|---|---|
| Chẩn đoán từ mã nguồn, không đoán | 02 |
| Vòng phản biện độc lập | 04, 08 |
| Xác định phạm vi, hỏi đúng câu | 04 |
| Test-first, kiểm chứng thực nghiệm | 09 |
| Giữ quyết định bảo mật cũ | 04 |
| Backfill có điều kiện an toàn | 07 |
| Browser test trong môi trường nhất quán | 09 |
| Chia nhỏ commit | 04 |
| Cổng xác nhận của con người | 07 |
| Guardrail Git | 07 |
| Báo cáo ngược qua MCP | 06 |

---

## 16.3. Mười lỗi thường gặp và cách sửa

| # | Lỗi | Dấu hiệu | Cách sửa |
|---|---|---|---|
| 1 | Prompt khổng lồ một phát ăn ngay | Yêu cầu có 8 mục tiêu | Chia task, duyệt từng bước ([ch.05](05-workflow-plan-execute.md)) |
| 2 | Không có ngữ cảnh dự án | Cùng câu dặn lặp ở mọi phiên | Dựng `CLAUDE.md` ([ch.04](04-context-engineering.md)) |
| 3 | `CLAUDE.md` phình to | Dài > 400 dòng | Tách `docs/` + rules, để lại con trỏ |
| 4 | Bật quá nhiều MCP | Tool cả tháng không dùng | Bật theo dự án ([ch.07](07-tools-va-mcp.md)) |
| 5 | Không có vòng phản hồi | AI nói "xong" mà không chạy gì | Trang bị test/log/ảnh chụp ([ch.11](11-verification-feedback-loop.md)) |
| 6 | Test viết sau code | Test xanh nhưng tính năng hỏng | Test-first ([ch.11](11-verification-feedback-loop.md)) |
| 7 | Lời dặn bị quên | Nhắc mãi vẫn sót | Chuyển thành hook ([ch.08](08-hooks-va-guardrails.md)) |
| 8 | Giao quyết định nghiệp vụ cho AI | Kết quả không ổn định | Viết được `if/else` thì để cho code |
| 9 | Subagent phá quyết định cũ | Nó "cải thiện" thứ cố tình để nguyên | Brief đầy đủ ràng buộc ([ch.09](09-subagents-va-song-song.md)) |
| 10 | Tin lời tự báo cáo | "Đã xong" mà chưa ai chạy | Hỏi "kiểm chứng bằng cách nào?" |

---

## 16.4. Lộ trình 30 ngày

### Tuần 1 — Nền móng

```
Ngày 1–2   Codebase Q&A: hỏi 20 câu về dự án, KHÔNG yêu cầu sửa gì
Ngày 3–4   Viết CLAUDE.md tối thiểu (≤ 80 dòng), kiểm bằng phiên mới
Ngày 5–7   Thực hành Plan Mode cho 3 task cỡ vừa
```

### Tuần 2 — Phản hồi

```
Ngày 8–10   Trang bị lệnh chạy test/build cho AI, ghi vào CLAUDE.md
Ngày 11–12  Làm test-first cho một bug: test đỏ trước, sửa sau
Ngày 13–14  Dựng một cách kiểm chứng giao diện (ảnh chụp màn hình)
```

### Tuần 3 — Đóng gói

```
Ngày 15–17  Tìm prompt lặp lại, viết skill đầu tiên
Ngày 18–19  Tách rule đầu tiên ra file riêng, kèm mục "Lý do"
Ngày 20–21  Viết hook đầu tiên cho lời dặn hay bị quên nhất
```

### Tuần 4 — Mở rộng

```
Ngày 22–24  Dùng subagent cho một việc tìm kiếm rộng và một việc review
Ngày 25–26  Thử git worktree với hai việc song song
Ngày 27–28  Tự động hoá một việc lặp lại ngoài repo
Ngày 29–30  Rà lại toàn bộ: cái gì đang dùng, cái gì bỏ, cái gì thiếu
```

---

## 16.5. Mô hình tư duy cuối cùng

```
        MÔI TRƯỜNG LÀM VIỆC CỦA AI

     ┌──────────────────────────────┐
     │          NGỮ CẢNH            │
     │  CLAUDE.md / Rules / Memory  │
     └──────────────┬───────────────┘
                    ↓
     ┌──────────────────────────────┐
     │           AGENT              │
     │  Hiểu → Lập kế hoạch → Làm   │
     └──────────────┬───────────────┘
                    ↓
     ┌──────────────────────────────┐
     │          CÔNG CỤ             │
     │  Git / MCP / Terminal / API  │
     └──────────────┬───────────────┘
                    ↓
     ┌──────────────────────────────┐
     │         KIỂM CHỨNG           │
     │  Test / Build / Log / Ảnh    │
     └──────────────┬───────────────┘
                    ↓
     ┌──────────────────────────────┐
     │          PHẢN HỒI            │
     │  Sửa / Cải thiện / Lặp lại   │
     └──────────────────────────────┘
```

Hoặc dạng chuỗi:

```
Mục tiêu → Ngữ cảnh → Spec → Kế hoạch → Thực hiện → Kiểm chứng
→ Tự động hoá → Triển khai → Cải thiện
```

---

## 16.6. Ba câu chốt của cả cuốn sách

> **1.** Đừng chỉ bảo AI viết code. Hãy cho nó ngữ cảnh, công cụ, luật lệ và vòng phản
> hồi để nó tự khám phá, lập kế hoạch, thực hiện, kiểm tra và cải thiện.

> **2.** Đừng hỏi "AI làm được tất cả không?". Hãy hỏi: phần nào nên là luật cố định,
> phần nào cần AI, và phần nào vẫn cần con người kiểm soát?

> **3.** Đừng cố trở thành người biết nhiều công cụ AI nhất. Hãy trở thành người biến
> được một vấn đề thành một hệ thống giải quyết vấn đề.

---

## 16.7. Bài tập cuối

**Bài 1 — Case study của chính bạn.**
Chọn một sự cố đã xảy ra trong dự án của bạn. Viết lại theo khung của mục 12.2: bối
cảnh → chẩn đoán → điều bị bỏ sót → cách sửa → bài học. Lưu vào memory.

**Bài 2 — Chấm điểm lại.**
Mở bảng ở [bài tập chương 01](01-tu-duy-nen-tang.md) và điền lại. So sánh với lần đầu.

**Bài 3 — Chọn bậc kế tiếp.**
Xác định bạn đang ở cấp nào trong bảy cấp. Chọn **một** việc để lên bậc tiếp theo, và
làm nó trong tuần này.

---

## Tóm tắt chương

- Bảy cấp: hỏi đáp → yêu cầu có cấu trúc → đối thoại → nhiều AI → automation → agent
  → hệ thống riêng.
- Cấp 6 là nơi lợi ích tăng vọt, và là nơi phần lớn người dùng dừng lại vì bỏ qua
  kiểm chứng.
- Case study có thật cho thấy: **vòng phản biện độc lập** đã biến một "lỗi CSS" thành
  một lỗ hổng Critical được vá đúng.
- Kiểm chứng thực nghiệm trước khi viết assertion; test phải đỏ trước khi sửa.
- Vá đường ghi không làm sạch dữ liệu đã nhiễm.
- "Test xanh" ≠ "đã đóng".
- Guardrail có giá trị nhất đúng vào lúc nó gây phiền.

> Phần tra cứu: [Phụ lục A — Cú pháp & cấu hình](A-phu-luc-cu-phap.md)
