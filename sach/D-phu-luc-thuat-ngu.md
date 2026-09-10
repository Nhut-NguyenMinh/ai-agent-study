# Phụ lục D — Thuật ngữ

> [← Phụ lục C](C-phu-luc-checklist.md) | [Mục lục](README.md) | [Phụ lục E →](E-huong-dan-viet-claude-md.md)

> Mỗi mục: định nghĩa ngắn, vì sao nó quan trọng, và chương nói kỹ về nó.
> Sắp theo bảng chữ cái.

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

→ [Chương 01, mục 1.3](01-tu-duy-nen-tang.md)

---

### Agent Team

Nhiều agent phối hợp và trao đổi với nhau trên một bài toán phức tạp. Tốn tài nguyên
hơn subagent độc lập — chỉ dùng khi các nhánh việc thật sự cần trao đổi.

→ [Chương 08, mục 8.9](08-subagents-va-song-song.md)

---

### `CLAUDE.md`

File ngữ cảnh dự án, được nạp tự động mọi phiên. Chứa: dự án là gì, tech stack, kiến
trúc, quy ước, lệnh thường dùng, điều cấm.

> Nguyên tắc: lời dặn lặp lại ở nhiều phiên thì thuộc về đây, không thuộc khung chat.

→ [Chương 03](03-context-engineering.md), [Phụ lục A.2](A-phu-luc-cu-phap.md), [Phụ lục E](E-huong-dan-viet-claude-md.md)

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

→ [Chương 03](03-context-engineering.md)

---

### Context Engineering

Kỹ năng đưa kiến thức quan trọng vào **môi trường làm việc** của AI thay vì nhắc lại
bằng lời ở mỗi phiên.

→ [Chương 03](03-context-engineering.md)

---

### Deterministic (Xác định)

Tính chất của một quy trình luôn cho cùng kết quả với cùng đầu vào. Đối lập với AI —
vốn không xác định.

> Quy tắc: viết được thành `if/else` thì thuộc về code, không thuộc về AI.

→ [Chương 07, mục 7.7](07-hooks-va-guardrails.md), [Chương 10, mục 10.4](10-automation-ngoai-codebase.md)

---

### Feedback loop (Vòng phản hồi)

Chu trình cho phép AI tự phát hiện và sửa lỗi:

```
Viết → Chạy → Lỗi → Đọc lỗi → Phân tích → Sửa → Chạy lại → Xác nhận
```

Chỉ hoạt động khi AI **thấy được** kết quả: test, log, ảnh chụp màn hình.

→ [Chương 09](09-verification-feedback-loop.md)

---

### Git worktree

Cơ chế Git cho phép nhiều thư mục làm việc dùng chung một kho, mỗi thư mục một nhánh.
Dùng để chạy nhiều phiên AI song song mà không giẫm chân nhau.

→ [Chương 08, mục 8.6](08-subagents-va-song-song.md)

---

### Guardrail

Cơ chế giữ agent trong ranh giới an toàn: luật, hook, phạm vi quyền, cổng phê duyệt.

> Guardrail tốt vừa chặn cái nguy hiểm vừa **mở đường cho cái an toàn** — nếu không,
> người dùng sẽ tắt hết.

→ [Chương 07](07-hooks-va-guardrails.md)

---

### Hook

Script do **harness** chạy tại một thời điểm xác định trong vòng đời phiên. Khác rule
ở chỗ nó không phụ thuộc trí nhớ model và không thể bị bỏ qua.

Ba yêu cầu: điều kiện hẹp và kiểm chứng được, cơ chế chống lặp, thông điệp nói rõ hậu quả.

→ [Chương 07](07-hooks-va-guardrails.md), [Phụ lục A.6–A.7](A-phu-luc-cu-phap.md)

---

### Human-in-the-loop

Thiết kế giữ con người ở điểm quyết định của những hành động có rủi ro.

```
Rủi ro thấp    → tự động hoàn toàn
Rủi ro vừa     → tự động + kiểm tra hợp lệ + log
Rủi ro cao     → người phê duyệt trước khi thực thi
```

→ [Chương 07, mục 7.9](07-hooks-va-guardrails.md)

---

### MCP (Model Context Protocol)

Chuẩn để AI kết nối với hệ thống bên ngoài. MCP server khai báo danh sách tool kèm
schema; AI gọi được như tool có sẵn.

> Nhiều MCP không làm agent mạnh hơn — mỗi tool thừa tốn context, tăng nhầm lẫn, tăng
> rủi ro.

→ [Chương 06](06-tools-va-mcp.md), [Phụ lục A.8](A-phu-luc-cu-phap.md)

---

### Memory

Kiến thức đọng lại từ những lần vấp, ghi thành file để dùng ở phiên sau. Khác
`CLAUDE.md` ở chỗ nó là bài học, không phải quy tắc chủ động đặt ra.

Không ghi vào memory những gì repo đã có — memory chép lại code sẽ lỗi thời và thành
nguồn sai.

→ [Chương 03, mục 3.6](03-context-engineering.md)

---

### Plan Mode

Chế độ làm việc trong đó AI khám phá và lập kế hoạch **trước**, chỉ thực hiện sau khi
người duyệt.

```
Mục tiêu → Khám phá → Kế hoạch → Người duyệt → Thực hiện → Kiểm chứng
```

→ [Chương 04](04-workflow-plan-execute.md)

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

→ [Chương 10, mục 10.3](10-automation-ngoai-codebase.md)

---

### Rule

File luật trong `.claude/rules/`, nạp mọi phiên. Nên tách theo chủ đề, mỗi file có mục
**Lý do**.

Luật không có lý do sẽ bị lách ngay khi bất tiện.

→ [Chương 03, mục 3.5](03-context-engineering.md), [Phụ lục A.3](A-phu-luc-cu-phap.md)

---

### Scope (Phạm vi quyền)

Danh sách quyền gắn vào token của agent (`task:read`, `report:write`...). Là guardrail
**ở tầng hệ thống** — mạnh hơn lời dặn vì không thể bị quên hay thuyết phục.

→ [Chương 06, mục 6.4](06-tools-va-mcp.md)

---

### Skill

Quy trình đóng gói để AI dùng lại. Gồm: khi nào dùng, điều kiện tiên quyết, nguyên tắc,
các bước, đầu ra.

> Prompt lặp lại lần thứ ba → đóng thành skill.

Phần `description` quyết định skill có được gọi đúng lúc — nên chứa cả câu người dùng
hay nói và câu tự phân biệt với skill gần giống.

→ [Chương 05](05-skills-va-slash-commands.md), [Phụ lục A.4](A-phu-luc-cu-phap.md)

---

### Slash command

Lối vào ngắn cho một quy trình (`/new-feature`, `/task-report`). Khác skill ở chỗ
**người chủ động gọi**, thay vì model tự chọn.

→ [Chương 05, mục 5.5](05-skills-va-slash-commands.md)

---

### Structured output (Đầu ra có cấu trúc)

Buộc AI trả về dữ liệu theo schema (thường là JSON) thay vì văn bản tự do, để hệ thống
kiểm tra và xử lý tiếp được.

Trường `confidence` cho phép chuyển sang người xem khi độ tin cậy dưới ngưỡng.

→ [Chương 07, mục 7.8](07-hooks-va-guardrails.md), [Chương 10, mục 10.6](10-automation-ngoai-codebase.md)

---

### Subagent

Agent phụ được giao một việc độc lập, chạy với ngữ cảnh riêng và **chỉ trả về kết luận**.
Dùng để cô lập việc tốn ngữ cảnh và để có góc nhìn độc lập.

> Subagent **không biết** quyết định đã chốt trong dự án — phải brief rõ, nếu không nó
> sẽ phá.

→ [Chương 08](08-subagents-va-song-song.md)

---

### Test-first

Viết và duyệt test case **trước** khi viết code.

> Test viết sau chỉ chứng minh code chạy như code đã viết — không chứng minh yêu cầu
> được đáp ứng.

→ [Chương 09, mục 9.3](09-verification-feedback-loop.md)

---

### T-R-F-C-R

Khung tự kiểm một yêu cầu: **T**ask (việc cần làm), **R**ole (vai trò), **F**ormat
(định dạng), **C**ontext (bối cảnh), **R**eference (tham chiếu).

Là checklist để tìm chỗ bỏ sót khi kết quả lệch hướng — không phải biểu mẫu bắt buộc
điền đủ mỗi lần.

→ [Chương 02, mục 2.3](02-prompting-va-context.md)

---

### Validation (Kiểm tra hợp lệ)

Bước kiểm tra đầu ra của AI trước khi cho phép hành động thật: kiểm schema, kiểm quy
tắc nghiệp vụ, kiểm ngưỡng tin cậy.

→ [Chương 07](07-hooks-va-guardrails.md)

---

### Verification (Kiểm chứng)

Xác nhận kết quả bằng bằng chứng chạy được, không bằng lời tự báo cáo.

> "Đã xong" không phải bằng chứng. Đầu ra của lệnh mới là.

→ [Chương 09](09-verification-feedback-loop.md)

---

### Vibe Coding

Cách xây phần mềm trong đó AI viết phần lớn code, người tập trung vào mô tả yêu cầu,
đánh giá và kiểm thử.

Không có nghĩa "AI code xong là xong" — vẫn cần hiểu đủ để kiểm soát sản phẩm.

→ [Chương 11, mục 11.6](11-chon-cong-cu-ai.md)

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

> Về [Mục lục](README.md)
