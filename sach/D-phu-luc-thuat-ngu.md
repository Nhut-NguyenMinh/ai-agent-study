# Phụ lục D — Thuật ngữ

> [← Phụ lục C](C-phu-luc-checklist.md) | [Mục lục](README.md) | [Phụ lục E →](E-huong-dan-viet-claude-md.md)

> Mỗi mục: định nghĩa ngắn, vì sao nó quan trọng, và chương nói kỹ về nó.
> Sắp theo bảng chữ cái.

---

### `CLAUDE.md`

File ngữ cảnh dự án, được nạp tự động mọi phiên. Chứa: dự án là gì, tech stack, kiến
trúc, quy ước, lệnh thường dùng, điều cấm.

> Nguyên tắc: lời dặn lặp lại ở nhiều phiên thì thuộc về đây, không thuộc khung chat.

→ [Chương 04](04-context-engineering.md), [Phụ lục A.2](A-phu-luc-cu-phap.md), [Phụ lục E](E-huong-dan-viet-claude-md.md)

---

### Agent

Hệ thống AI có khả năng tự khám phá, lập kế hoạch, dùng công cụ, thực hiện nhiều bước
và tự đánh giá kết quả — khác với chatbot chỉ trả lời một lượt.

```
Agent = Context + Tools + Rules + Feedback (+ Validation)
```

→ [Chương 01](01-tu-duy-nen-tang.md)

---

### Agent Engineering

Kỹ năng **dựng môi trường** để AI làm việc tốt, thay vì kỹ năng viết câu lệnh hay.
Đối tượng làm việc là quy trình, không phải một câu hỏi.

→ [Chương 01, mục 1.4](01-tu-duy-nen-tang.md)

---

### Agent Team

Nhiều agent phối hợp và trao đổi với nhau trên một bài toán phức tạp. Tốn tài nguyên
hơn subagent độc lập — chỉ dùng khi các nhánh việc thật sự cần trao đổi.

→ [Chương 09, mục 9.9](09-subagents-va-song-song.md)

---

### Codebase Q&A

Kỹ thuật bắt đầu bằng việc **hỏi AI về codebase** thay vì yêu cầu nó viết code. Giúp
đánh giá AI hiểu dự án đến đâu, phát hiện lỗ hổng ngữ cảnh, và học chính codebase của mình.

Rút ngắn onboarding kỹ thuật từ 2–3 tuần xuống 2–3 ngày trong ví dụ thực tế tại Anthropic.

→ [Chương 02, mục 2.5](02-prompting-va-context.md)

---

### Context (Ngữ cảnh)

Toàn bộ thông tin AI có khi làm việc: system prompt, `CLAUDE.md`, rules, định nghĩa
tool, file đã đọc, lịch sử hội thoại.

> **Context phải đủ, không phải càng nhiều càng tốt.**

→ [Chương 04](04-context-engineering.md)

---

### Context Engineering

Kỹ năng đưa kiến thức quan trọng vào **môi trường làm việc** của AI thay vì nhắc lại
bằng lời ở mỗi phiên.

→ [Chương 04](04-context-engineering.md)

---

### Deterministic (Xác định)

Tính chất của một quy trình luôn cho cùng kết quả với cùng đầu vào. Đối lập với AI —
vốn không xác định.

> Quy tắc: viết được thành `if/else` thì thuộc về code, không thuộc về AI.

→ [Chương 08, mục 8.7](08-hooks-va-guardrails.md), [Chương 14, mục 14.4](14-automation-ngoai-codebase.md)

---

### Feedback loop (Vòng phản hồi)

Chu trình cho phép AI tự phát hiện và sửa lỗi:

```
Viết → Chạy → Lỗi → Đọc lỗi → Phân tích → Sửa → Chạy lại → Xác nhận
```

Chỉ hoạt động khi AI **thấy được** kết quả: test, log, ảnh chụp màn hình.

→ [Chương 11](11-verification-feedback-loop.md)

---

### Git worktree

Cơ chế Git cho phép nhiều thư mục làm việc dùng chung một kho, mỗi thư mục một nhánh.
Dùng để chạy nhiều phiên AI song song mà không giẫm chân nhau.

→ [Chương 09, mục 9.6](09-subagents-va-song-song.md)

---

### Guardrail

Cơ chế giữ agent trong ranh giới an toàn: luật, hook, phạm vi quyền, cổng phê duyệt.

> Guardrail tốt vừa chặn cái nguy hiểm vừa **mở đường cho cái an toàn** — nếu không,
> người dùng sẽ tắt hết.

→ [Chương 08](08-hooks-va-guardrails.md)

---

### Hiển thị theo nhu cầu (Progressive disclosure)

Chia thông tin hai tầng: phần tóm tắt nạp sẵn, phần chi tiết chỉ nạp khi được gọi.
Đây là lý do `description` của skill quyết định skill có được dùng hay không — nó là
**thứ duy nhất được nạp sẵn**.

→ [Chương 12, mục 12.5](12-kinh-te-ngu-canh.md)

---

### Hỏi ngược

Để AI đặt câu hỏi làm rõ **trước khi** bắt tay, thay vì đoán. Chỉ hỏi những câu mà câu
trả lời làm **thay đổi công việc**; câu có mặc định hợp lý thì tự quyết và nêu giả định.

Lọc ba tầng: tự tra cứu → hỏi người ngồi cùng → mới hỏi lên trên.

→ [Chương 03, mục 3.5](03-dac-ta-yeu-cau.md)

---

### Hook

Script do **harness** chạy tại một thời điểm xác định trong vòng đời phiên. Khác rule
ở chỗ nó không phụ thuộc trí nhớ model và không thể bị bỏ qua.

Ba yêu cầu: điều kiện hẹp và kiểm chứng được, cơ chế chống lặp, thông điệp nói rõ hậu quả.

→ [Chương 08](08-hooks-va-guardrails.md), [Phụ lục A.6–A.7](A-phu-luc-cu-phap.md)

---

### Hợp đồng yêu cầu

Bản mô tả công việc gồm bốn phần — **mục tiêu, ràng buộc, đầu ra, điều kiện thất bại** —
thống nhất trước khi làm và dùng làm cơ sở nghiệm thu.

Trong PM-AGENT, tài liệu test (`test-doc-first`) đóng đúng vai trò này, với negative test
case chính là điều kiện thất bại ở dạng chạy được.

→ [Chương 03](03-dac-ta-yeu-cau.md)

---

### Human-in-the-loop

Thiết kế giữ con người ở điểm quyết định của những hành động có rủi ro.

```
Rủi ro thấp    → tự động hoàn toàn
Rủi ro vừa     → tự động + kiểm tra hợp lệ + log
Rủi ro cao     → người phê duyệt trước khi thực thi
```

→ [Chương 08, mục 8.9](08-hooks-va-guardrails.md)

---

### Kỹ thuật tảng băng

Giữ phần nổi của ngữ cảnh thật nhỏ (quy tắc, bộ nhớ, việc đang làm), phần chìm (mã
nguồn, tài liệu, dữ liệu) chỉ chạm tới khi cần qua công cụ tìm kiếm.

> Không cần đưa toàn bộ thông tin vào ngữ cảnh — chỉ cần cho AI khả năng lấy khi cần.

→ [Chương 12, mục 12.3](12-kinh-te-ngu-canh.md)

---

### MCP (Model Context Protocol)

Chuẩn để AI kết nối với hệ thống bên ngoài. MCP server khai báo danh sách tool kèm
schema; AI gọi được như tool có sẵn.

> Nhiều MCP không làm agent mạnh hơn — mỗi tool thừa tốn context, tăng nhầm lẫn, tăng
> rủi ro.

→ [Chương 07](07-tools-va-mcp.md), [Phụ lục A.8](A-phu-luc-cu-phap.md)

---

### Memory

Kiến thức đọng lại từ những lần vấp, ghi thành file để dùng ở phiên sau. Khác
`CLAUDE.md` ở chỗ nó là bài học, không phải quy tắc chủ động đặt ra.

Không ghi vào memory những gì repo đã có — memory chép lại code sẽ lỗi thời và thành
nguồn sai.

→ [Chương 04, mục 4.6](04-context-engineering.md)

---

### Nén ngữ cảnh (Compaction)

Tóm tắt lịch sử phiên thành bản cô đọng để tiếp tục làm việc khi ngữ cảnh gần đầy.
Là **đánh đổi, không phải phép màu** — chi tiết có thể mất. Thông tin quan trọng phải
được ghi ra file, không chỉ nằm trong hội thoại.

→ [Chương 12, mục 12.7](12-kinh-te-ngu-canh.md)

---

### Phân tầng mô hình

Chia công việc theo mức năng lực thực sự cần (rẻ / trung cấp / mạnh) thay vì dùng mô
hình mạnh nhất cho mọi việc.

Nguyên tắc: **cái rẻ chạy rộng, cái đắt chạy hẹp**. Không tiết kiệm ở khâu điều phối,
kiểm tra việc quan trọng, hoặc lúc đang gỡ lỗi khó.

→ [Chương 13](13-kinh-te-mo-hinh.md)

---

### Plan Mode

Chế độ làm việc trong đó AI khám phá và lập kế hoạch **trước**, chỉ thực hiện sau khi
người duyệt.

```
Mục tiêu → Khám phá → Kế hoạch → Người duyệt → Thực hiện → Kiểm chứng
```

→ [Chương 05](05-workflow-plan-execute.md)

---

### Prompt Engineering

Kỹ năng cung cấp đủ thông tin để AI có cơ sở tạo kết quả đúng. Không phải học thuộc
câu lệnh ma thuật.

Khung tự kiểm: **T-R-F-C-R** (Task, Role, Format, Context, Reference).

→ [Chương 02](02-prompting-va-context.md)

---

### Routine

Công việc định kỳ chạy trên hạ tầng cloud, không phụ thuộc máy cá nhân. Repo Git có thể
làm nguồn ngữ cảnh cho routine.

> Lịch chạy trên máy cá nhân thất bại **âm thầm** — đó mới là vấn đề, không phải việc
> nó không chạy.

→ [Chương 14, mục 14.3](14-automation-ngoai-codebase.md)

---

### Rule

File luật trong `.claude/rules/`, nạp mọi phiên. Nên tách theo chủ đề, mỗi file có mục
**Lý do**.

Luật không có lý do sẽ bị lách ngay khi bất tiện.

→ [Chương 04, mục 4.5](04-context-engineering.md), [Phụ lục A.3](A-phu-luc-cu-phap.md)

---

### Scope (Phạm vi quyền)

Danh sách quyền gắn vào token của agent (`task:read`, `report:write`...). Là guardrail
**ở tầng hệ thống** — mạnh hơn lời dặn vì không thể bị quên hay thuyết phục.

→ [Chương 07, mục 7.4](07-tools-va-mcp.md)

---

### Skill

Quy trình đóng gói để AI dùng lại. Gồm: khi nào dùng, điều kiện tiên quyết, nguyên tắc,
các bước, đầu ra.

> Prompt lặp lại lần thứ ba → đóng thành skill.

Phần `description` quyết định skill có được gọi đúng lúc — nên chứa cả câu người dùng
hay nói và câu tự phân biệt với skill gần giống.

→ [Chương 06](06-skills-va-slash-commands.md), [Phụ lục A.4](A-phu-luc-cu-phap.md)

---

### Slash command

Lối vào ngắn cho một quy trình (`/new-feature`, `/task-report`). Khác skill ở chỗ
**người chủ động gọi**, thay vì model tự chọn.

→ [Chương 06, mục 6.5](06-skills-va-slash-commands.md)

---

### Structured output (Đầu ra có cấu trúc)

Buộc AI trả về dữ liệu theo schema (thường là JSON) thay vì văn bản tự do, để hệ thống
kiểm tra và xử lý tiếp được.

Trường `confidence` cho phép chuyển sang người xem khi độ tin cậy dưới ngưỡng.

→ [Chương 08, mục 8.8](08-hooks-va-guardrails.md), [Chương 14, mục 14.6](14-automation-ngoai-codebase.md)

---

### Subagent

Agent phụ được giao một việc độc lập, chạy với ngữ cảnh riêng và **chỉ trả về kết luận**.
Dùng để cô lập việc tốn ngữ cảnh và để có góc nhìn độc lập.

> Subagent **không biết** quyết định đã chốt trong dự án — phải brief rõ, nếu không nó
> sẽ phá.

→ [Chương 09](09-subagents-va-song-song.md)

---

### T-R-F-C-R

Khung tự kiểm một yêu cầu: **T**ask (việc cần làm), **R**ole (vai trò), **F**ormat
(định dạng), **C**ontext (bối cảnh), **R**eference (tham chiếu).

Là checklist để tìm chỗ bỏ sót khi kết quả lệch hướng — không phải biểu mẫu bắt buộc
điền đủ mỗi lần.

→ [Chương 02, mục 2.3](02-prompting-va-context.md)

---

### Test-first

Viết và duyệt test case **trước** khi viết code.

> Test viết sau chỉ chứng minh code chạy như code đã viết — không chứng minh yêu cầu
> được đáp ứng.

→ [Chương 11, mục 11.3](11-verification-feedback-loop.md)

---

### Tranh luận (Debate)

Cho nhiều tác nhân có **vai khác nhau** phản biện lẫn nhau để lộ điểm mù, khác với
đồng thuận (nghĩ độc lập rồi tổng hợp). Cần đặt số vòng tối đa và điều kiện thoát sớm.

→ [Chương 10, mục 10.4](10-phoi-hop-da-tac-nhan.md)

---

### Validation (Kiểm tra hợp lệ)

Bước kiểm tra đầu ra của AI trước khi cho phép hành động thật: kiểm schema, kiểm quy
tắc nghiệp vụ, kiểm ngưỡng tin cậy.

→ [Chương 08](08-hooks-va-guardrails.md)

---

### Verification (Kiểm chứng)

Xác nhận kết quả bằng bằng chứng chạy được, không bằng lời tự báo cáo.

> "Đã xong" không phải bằng chứng. Đầu ra của lệnh mới là.

→ [Chương 11](11-verification-feedback-loop.md)

---

### Vibe Coding

Cách xây phần mềm trong đó AI viết phần lớn code, người tập trung vào mô tả yêu cầu,
đánh giá và kiểm thử.

Không có nghĩa "AI code xong là xong" — vẫn cần hiểu đủ để kiểm soát sản phẩm.

→ [Chương 15, mục 15.6](15-chon-cong-cu-ai.md)

---

### Vòng lặp (Agent loop)

Chu trình **Quan sát → Suy nghĩ → Hành động → Nhận kết quả → lặp lại** — thứ biến một
mô hình trả lời thành một tác nhân làm việc. Khi thất bại, chính vòng lặp này cho phép
đọc lỗi và thử hướng khác.

→ [Chương 01, mục 1.3](01-tu-duy-nen-tang.md)

---

### Xử lý theo lô (Batch)

Gom nhiều yêu cầu không cần kết quả tức thời rồi xử lý sau, thường rẻ hơn. Phù hợp với
phân loại/trích xuất hàng loạt và mọi việc chạy nền không ai ngồi đợi.

→ [Chương 13, mục 13.5](13-kinh-te-mo-hinh.md)

---

### Điều kiện thất bại

Thành phần thứ tư của tiêu chí hoàn thành, và là thành phần bị bỏ quên nhiều nhất:
**thế nào thì coi là hỏng**.

Mục tiêu mô tả vùng đúng; điều kiện thất bại cắt bỏ vùng sai. Hai thứ không đối xứng —
ranh giới sắc bao giờ cũng kiểm được dễ hơn mô tả mơ hồ.

→ [Chương 03, mục 3.2](03-dac-ta-yeu-cau.md)

---

### Định tuyến công việc (Routing)

Quyết định việc nào giao cho tác nhân/mô hình nào. Nên là **thành phần của hệ thống**
(luật tách khỏi mã, có dự phòng, có đo lường), không phải quyết định ngẫu hứng mỗi lần.

→ [Chương 10, mục 10.2](10-phoi-hop-da-tac-nhan.md), [Chương 13](13-kinh-te-mo-hinh.md)

---

### Đồng thuận (Consensus)

Cho nhiều tác nhân giải **cùng một** bài toán một cách **độc lập**, rồi tổng hợp. Khai
thác tính biến thiên của mô hình để mở rộng không gian lời giải.

Ba loại kết quả: đồng thuận (đa số cùng hướng), khác biệt (mỗi bên một hướng), ngoại lệ
(một bên rất khác — đáng tiền nhất và nguy hiểm nhất, bắt buộc kiểm chứng).

> Chỉ hoạt động khi các tác nhân **không thấy kết quả của nhau**.

→ [Chương 10, mục 10.3](10-phoi-hop-da-tac-nhan.md)

---

## Bảng đối chiếu thuật ngữ Anh–Việt

| Tiếng Anh | Dùng trong sách |
|---|---|
| Context | Ngữ cảnh |
| Context window | Cửa sổ ngữ cảnh / ngân sách context |
| Deterministic | Xác định |
| Feedback loop | Vòng phản hồi |
| Guardrail | Rào chắn / cơ chế bảo vệ (giữ nguyên "guardrail") |
| Happy path | Đường đi đẹp |
| Human-in-the-loop | Con người trong vòng lặp |
| Prompt | Câu lệnh / yêu cầu (giữ nguyên "prompt") |
| Scope | Phạm vi quyền |
| Structured output | Đầu ra có cấu trúc |
| Trigger | Điều kiện kích hoạt |
| Validation | Kiểm tra hợp lệ |
| Verification | Kiểm chứng |
| Workflow | Quy trình |
| Consensus | Đồng thuận |
| Debate | Tranh luận |
| Routing | Định tuyến |
| Progressive disclosure | Hiển thị theo nhu cầu |
| Compaction | Nén ngữ cảnh |
| Batch processing | Xử lý theo lô |
| Definition of Done | Tiêu chí hoàn thành |

> Về [Mục lục](README.md)
