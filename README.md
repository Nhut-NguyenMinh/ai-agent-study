# AI Agent Study

Ghi chép và hệ thống hoá kiến thức về **AI Coding Agent** — từ cách viết prompt đến cách
dựng một môi trường để AI làm việc được trong dự án thật.

Nội dung viết bằng tiếng Việt, thuật ngữ kỹ thuật giữ nguyên tiếng Anh.

---

## Nội dung

### 📘 [Sách: Làm chủ AI Coding Agent — Từ Prompt đến Hệ thống](sach/README.md)

Sách hoàn chỉnh **16 chương + 6 phụ lục** (~9.000 dòng), tổ chức theo **lớp năng lực** thay
vì theo thứ tự bài giảng. Bắt đầu đọc từ [`sach/README.md`](sach/README.md) — có bản đồ
kiến thức, mục lục kèm cột "đọc khi nào", và ba lộ trình đọc theo trình độ.

| Phần | Chương |
|---|---|
| **I — Nền móng** | Tư duy nền tảng · Prompting & ngữ cảnh · **Đặc tả yêu cầu** · Context Engineering · Workflow Plan → Execute |
| **II — Đóng gói** | Skills & Slash Commands · Tools & MCP |
| **III — Kiểm soát** | Hooks & Guardrails · Subagents & song song · **Phối hợp đa tác nhân** · Verification & Feedback loop |
| **IV — Vận hành bền vững** | **Kinh tế ngữ cảnh** · **Kinh tế mô hình** |
| **V — Mở rộng** | Automation ngoài codebase · Chọn đúng công cụ AI · Lộ trình & Case study |
| **Phụ lục** | Cú pháp · Phím tắt & CLI · Checklist · Thuật ngữ · Hướng dẫn viết `CLAUDE.md` · **Bảng quyết định** |

### 📚 [Bài học gốc](bai-hoc-goc/)

Sáu ghi chép gốc trước khi được gộp và cấu trúc lại thành sách:

- [`bai-hoc-5-nen-tang-ai-2026.md`](bai-hoc-goc/bai-hoc-5-nen-tang-ai-2026.md) — 5 nền tảng dùng AI hiệu quả
- [`bai-hoc-lam-chu-ai-agent-automation.md`](bai-hoc-goc/bai-hoc-lam-chu-ai-agent-automation.md) — làm chủ AI agent & automation
- [`claude_code_workshop_tong_hop.md`](bai-hoc-goc/claude_code_workshop_tong_hop.md) — tổng hợp workshop Claude Code
- [`claude-code-masterclass-tong-hop-bai-hoc.md`](bai-hoc-goc/claude-code-masterclass-tong-hop-bai-hoc.md) — masterclass: build & sell
- [`bai-hoc-claude-n8n-ai-automation.md`](bai-hoc-goc/bai-hoc-claude-n8n-ai-automation.md) — Claude + n8n + AI agent
- [`Giao-trinh-Lam-chu-Tac-nhan-Tri-tue-Nhan-tao.md`](bai-hoc-goc/Giao-trinh-Lam-chu-Tac-nhan-Tri-tue-Nhan-tao.md) — giáo trình tác nhân: vòng lặp, phối hợp đa tác nhân, kinh tế ngữ cảnh & mô hình

---

## Bắt đầu từ đâu

| Bạn là | Đọc |
|---|---|
| Mới dùng AI để lập trình | [Chương 01](sach/01-tu-duy-nen-tang.md) → [05](sach/05-workflow-plan-execute.md) theo thứ tự |
| Đã dùng Claude Code hằng ngày | [01 §1.3 vòng lặp](sach/01-tu-duy-nen-tang.md) → [03](sach/03-dac-ta-yeu-cau.md) → [08](sach/08-hooks-va-guardrails.md) → [11](sach/11-verification-feedback-loop.md) → [12](sach/12-kinh-te-ngu-canh.md) |
| Đang thiết kế quy trình cho team | [04](sach/04-context-engineering.md) → [06](sach/06-skills-va-slash-commands.md) → [08](sach/08-hooks-va-guardrails.md) → [11](sach/11-verification-feedback-loop.md) |
| Lo chi phí token tăng | [12](sach/12-kinh-te-ngu-canh.md) → [13](sach/13-kinh-te-mo-hinh.md) |
| Chỉ cần viết `CLAUDE.md` cho tốt | [Phụ lục E](sach/E-huong-dan-viet-claude-md.md) — có mẫu đầy đủ copy dùng được |
| Không biết mình thiếu gì | [Bảng tự chẩn đoán trong mục lục sách](sach/README.md) |

---

## Ý chính

```
AI Agent tốt = Context + Tools + Rules + Feedback + Validation

Vòng lặp cốt lõi:  Quan sát → Suy nghĩ → Hành động → Nhận kết quả → lặp lại
```

Ba câu chốt của cả cuốn sách:

> Đừng chỉ bảo AI viết code. Hãy cho nó ngữ cảnh, công cụ, luật lệ và vòng phản hồi để
> nó tự khám phá, lập kế hoạch, thực hiện, kiểm tra và cải thiện.

> Đừng hỏi "AI làm được tất cả không?". Hãy hỏi: phần nào nên là luật cố định, phần nào
> cần AI, và phần nào vẫn cần con người kiểm soát?

> Đừng cố trở thành người biết nhiều công cụ AI nhất. Hãy trở thành người biến được một
> vấn đề thành một hệ thống giải quyết vấn đề.

---

## Quy ước `{prefix}`

Ví dụ trong sách lấy từ một dự án thật. Mọi tiền tố định danh của dự án đó được thay
bằng `{prefix}` để tách rõ **phần là quy ước riêng** khỏi **phần đáng học**:

```
{prefix}-git-safety.md      ← tên file rule
{prefix}_get_task           ← tên tool MCP
{PREFIX}_AGENT_TOKEN        ← biến môi trường
```

Dự án của bạn tên `acme` thì đọc thành `acme-git-safety.md`, `acme_get_task`,
`ACME_AGENT_TOKEN`. Trong khối JSON/YAML mẫu, `{prefix}` là chỗ điền — thay bằng giá
trị thật trước khi dùng.

---

## Về ví dụ trong sách

Ví dụ minh hoạ lấy từ một dự án nội bộ có thật, được ẩn danh dưới tên **PM-AGENT**:
cấu hình rules/skills/hooks đang chạy, kiến trúc MCP server, và một case study sự cố
bảo mật đã xảy ra thật ([chương 16](sach/16-lo-trinh-va-case-study.md)).

Mọi khoá, token và thông tin định danh đã được che hoặc thay thế bằng `{prefix}`. Cú pháp cấu hình có
thể đổi theo phiên bản công cụ — luôn đối chiếu với tài liệu chính thức trước khi áp
dụng vào quy trình của nhóm.
