# Làm chủ AI Coding Agent — Từ Prompt đến Hệ thống

> Sách hệ thống hoá kiến thức về AI Agent và Claude Code, đúc kết từ 5 bài học đã ghi chép,
> viết lại theo **lớp năng lực** thay vì theo thứ tự bài giảng, và minh hoạ bằng **ví dụ thật
> đang chạy trong dự án PM-AGENT**.

---

## 1. Cuốn sách này giải quyết vấn đề gì

Người mới dùng AI để lập trình thường mắc kẹt ở một vòng lặp:

```
Viết prompt dài hơn → kết quả vẫn sai → đổ lỗi cho model → đổi model → vẫn sai
```

Nguyên nhân không nằm ở prompt. Nó nằm ở chỗ AI đang làm việc trong một môi trường
thiếu bốn thứ: **ngữ cảnh, công cụ, luật lệ và vòng phản hồi**. Cuốn sách này đi qua
từng thứ một, chỉ ra cách dựng chúng, và cho thấy khi đủ cả bốn thì cách làm việc
thay đổi ra sao.

Câu chốt của toàn bộ nội dung:

> **Đừng cố trở thành người viết prompt giỏi nhất.
> Hãy trở thành người thiết kế môi trường để AI làm việc được.**

---

## 2. Sách này KHÔNG phải là gì

- Không phải danh mục công cụ AI. Công cụ đổi mỗi quý; nguyên tắc thì không.
- Không phải tuyển tập prompt mẫu để copy.
- Không phải tài liệu tham chiếu API của Claude Code — phần tra cứu nằm ở phụ lục,
  và luôn cần đối chiếu với tài liệu chính thức vì cú pháp có thể đổi.

Sách này là **cách tư duy + bộ khung triển khai**, kèm ví dụ đã chạy thật.

---

## 3. Bản đồ kiến thức

Mười hai chương không rời rạc. Chúng xếp thành bốn tầng, tầng dưới đỡ tầng trên:

```
                    ┌──────────────────────────────────────┐
   TẦNG 4           │  Ch.10 Automation ngoài codebase     │
   MỞ RỘNG          │  Ch.11 Chọn đúng công cụ AI          │
                    │  Ch.12 Lộ trình & Case study          │
                    └──────────────────┬───────────────────┘
                    ┌──────────────────┴───────────────────┐
   TẦNG 3           │  Ch.07 Hooks & Guardrails             │
   KIỂM SOÁT        │  Ch.08 Subagents & chạy song song     │
                    │  Ch.09 Verification & Feedback loop   │
                    └──────────────────┬───────────────────┘
                    ┌──────────────────┴───────────────────┐
   TẦNG 2           │  Ch.05 Skills & Slash Commands        │
   ĐÓNG GÓI         │  Ch.06 Tools & MCP                    │
                    └──────────────────┬───────────────────┘
                    ┌──────────────────┴───────────────────┐
   TẦNG 1           │  Ch.01 Tư duy nền tảng                │
   NỀN MÓNG         │  Ch.02 Prompting & thu thập ngữ cảnh  │
                    │  Ch.03 Context Engineering            │
                    │  Ch.04 Workflow Plan → Execute        │
                    └──────────────────────────────────────┘
```

Công thức xuyên suốt cả sách, xuất hiện lại ở mọi chương:

```
AI Agent tốt = Context + Tools + Rules + Feedback + Validation
```

---

## 4. Mục lục

### Phần I — Nền móng

| Chương | Nội dung | Đọc khi |
|---|---|---|
| [01 — Tư duy nền tảng](01-tu-duy-nen-tang.md) | Khác biệt giữa Prompt Engineering, Context Engineering và Agent Engineering. Bốn thành phần của một agent. Bảy nguyên tắc gốc. | Bắt đầu từ đây, kể cả khi đã dùng Claude Code lâu |
| [02 — Prompting & thu thập ngữ cảnh](02-prompting-va-context.md) | Khung T-R-F-C-R. Vòng Evaluate → Iterate. Kỹ thuật Codebase Q&A. Câu hỏi khám phá tốt. | Khi kết quả AI trả về "gần đúng nhưng không dùng được" |
| [03 — Context Engineering](03-context-engineering.md) | `CLAUDE.md`, rules phân cấp, memory, quản lý ngân sách context. Vì sao context đủ quan trọng hơn context nhiều. | Khi phải lặp lại cùng một lời dặn ở mỗi phiên |
| [04 — Workflow Plan → Execute](04-workflow-plan-execute.md) | Plan Mode. Chuỗi Understand → Explore → Plan → Review → Implement → Verify. Chọn workflow theo độ rủi ro. Small commits. | Trước khi giao một task lớn |

### Phần II — Đóng gói năng lực

| Chương | Nội dung | Đọc khi |
|---|---|---|
| [05 — Skills & Slash Commands](05-skills-va-slash-commands.md) | Biến prompt lặp lại thành quy trình tái sử dụng. Cấu trúc một skill. Khi nào là rule, khi nào là skill. | Khi thấy mình giải thích lại cùng một quy trình lần thứ ba |
| [06 — Tools & MCP](06-tools-va-mcp.md) | Bash tools, MCP server, chia sẻ cấu hình cho cả team. Vì sao nhiều tool không đồng nghĩa mạnh hơn. | Khi muốn AI đọc/ghi được hệ thống bên ngoài |

### Phần III — Kiểm soát và kiểm chứng

| Chương | Nội dung | Đọc khi |
|---|---|---|
| [07 — Hooks & Guardrails](07-hooks-va-guardrails.md) | Hook do harness chạy chứ không phụ thuộc trí nhớ model. Ranh giới deterministic vs AI. Human-in-the-loop theo mức rủi ro. | Khi một lời dặn quan trọng bị quên lặp đi lặp lại |
| [08 — Subagents & chạy song song](08-subagents-va-song-song.md) | Subagent, agent team, git worktree, nhiều phiên song song. Khi nào KHÔNG nên multi-agent. | Khi một phiên phải ôm quá nhiều việc |
| [09 — Verification & Feedback loop](09-verification-feedback-loop.md) | Test-first, browser test, coverage threshold, layout verify. Cho AI cách tự kiểm tra kết quả. | Khi "code xong" mà vẫn không chắc đã xong |

### Phần IV — Mở rộng ra ngoài

| Chương | Nội dung | Đọc khi |
|---|---|---|
| [10 — Automation ngoài codebase](10-automation-ngoai-codebase.md) | Routines chạy trên cloud, n8n, kiến trúc lai deterministic + AI, structured output. | Khi công việc lặp lại nằm ngoài repo (email, sheet, CRM) |
| [11 — Chọn đúng công cụ AI](11-chon-cong-cu-ai.md) | Phân loại General / Research / Specialized. Open-source & local AI. Vibe Coding. Khung 7 câu hỏi ra quyết định. | Khi phân vân "nên dùng AI nào cho việc này" |
| [12 — Lộ trình & Case study](12-lo-trinh-va-case-study.md) | Bảy cấp độ trưởng thành. Một case study end-to-end có thật trong PM-AGENT. Lỗi thường gặp và cách sửa. | Khi muốn biết mình đang ở đâu và bước kế tiếp là gì |

### Phụ lục tra cứu

| Phụ lục | Nội dung |
|---|---|
| [A — Cú pháp & cấu hình](A-phu-luc-cu-phap.md) | Khung sườn `CLAUDE.md`, frontmatter skill, `settings.json` hooks, `.mcp.json`, biến môi trường hook |
| [B — Phím tắt & dòng lệnh](B-phu-luc-phim-tat-cli.md) | Phím tắt trong phiên, cờ CLI, `claude -p` trong pipeline Unix |
| [C — Checklist](C-phu-luc-checklist.md) | Toàn bộ checklist rải rác trong sách, gom một chỗ để in ra dùng |
| [D — Thuật ngữ](D-phu-luc-thuat-ngu.md) | Giải nghĩa ngắn các thuật ngữ, kèm chương nói kỹ về nó |
| [E — Hướng dẫn viết `CLAUDE.md`](E-huong-dan-viet-claude-md.md) | Bài thực hành sâu: dòng nào thực sự đổi hành vi AI, dòng nào bị bỏ qua, mẫu đầy đủ, cách kiểm chứng và bảo trì |

---

## 5. Đọc sách này thế nào

**Nếu bạn mới bắt đầu:** đọc tuần tự chương 01 → 04, dựng `CLAUDE.md` cho một dự án
thật, rồi mới sang phần II. Đừng đọc hết 12 chương trước khi làm — kiến thức về
agent chỉ đọng lại khi đã vấp.

**Nếu bạn đã dùng Claude Code hằng ngày:** đọc chương 01 để chỉnh lại mô hình tư duy,
rồi nhảy thẳng vào chương thấy đau nhất — thường là 07 (lời dặn bị quên) hoặc
09 (không biết khi nào là xong).

**Nếu bạn đang thiết kế quy trình cho cả team:** đọc 03 → 05 → 07 → 09 theo thứ tự đó.
Đấy là bốn lớp tạo nên một quy trình có thể bàn giao được cho người khác.

---

## 6. Quy ước trong sách

| Ký hiệu | Nghĩa |
|---|---|
| **Ví dụ thật** | Trích từ mã nguồn/cấu hình đang chạy trong PM-AGENT, có đường dẫn file kèm theo |
| **Ví dụ tổng quát** | Tình huống trung lập, áp dụng được cho mọi dự án |
| **Bẫy** | Lỗi đã thực sự xảy ra, kèm dấu hiệu nhận biết |
| **Bài tập** | Việc cần làm để kiến thức chương đó đọng lại |
| `{prefix}` | Chỗ điền tên viết tắt của dự án bạn — xem giải thích ngay dưới |

### Ký hiệu `{prefix}`

Ví dụ trong sách lấy từ một dự án thật, nên tên file rule, tên tool MCP và biến môi
trường đều mang một tiền tố định danh của dự án đó. Tiền tố ấy được thay bằng
`{prefix}` để bạn thấy ngay **phần nào là quy ước riêng, phần nào là bắt buộc**:

| Trong sách | Ý nghĩa | Nếu dự án bạn tên `acme` thì thành |
|---|---|---|
| `{prefix}-git-safety.md` | Tên file rule | `acme-git-safety.md` |
| `{prefix}_get_task` | Tên tool MCP | `acme_get_task` |
| `{PREFIX}_AGENT_TOKEN` | Biến môi trường | `ACME_AGENT_TOKEN` |
| `"{prefix}": { ... }` | Tên MCP server trong `.mcp.json` | `"acme": { ... }` |

Phần sau dấu gạch nối (`-git-safety`, `_get_task`) là nội dung đáng học; phần `{prefix}`
chỉ là quy ước đặt tên — bạn thay bằng gì cũng được, miễn nhất quán trong dự án.

> Lưu ý: trong các khối JSON/YAML mẫu, `{prefix}` là chỗ điền — thay bằng giá trị thật
> trước khi dùng, đừng chép nguyên.

Mọi đường dẫn file dạng `app/modules/...` là đường dẫn tương đối từ gốc repo PM-AGENT.
PM-AGENT là tên ẩn danh của một dự án nội bộ có thật — mọi ví dụ đều lấy từ cấu hình
và sự cố đã xảy ra thật, chỉ đổi tên định danh.
Mọi khoá bí mật trong sách đều đã được che thành `***` — khi làm theo, hãy đọc giá trị
thật từ biến môi trường, đừng chép từ sách ra.

---

## 7. Nguồn gốc

Sách tổng hợp và cấu trúc lại năm ghi chép bài học, bản gốc nằm ở [`../bai-hoc-goc/`](../bai-hoc-goc/):

| File gốc | Đóng góp chính vào sách |
|---|---|
| [`bai-hoc-5-nen-tang-ai-2026.md`](../bai-hoc-goc/bai-hoc-5-nen-tang-ai-2026.md) | Chương 02, 11 — khung T-R-F-C-R, phân loại công cụ, 7 cấp độ |
| [`bai-hoc-lam-chu-ai-agent-automation.md`](../bai-hoc-goc/bai-hoc-lam-chu-ai-agent-automation.md) | Chương 01, 04, 07 — công thức agent, deterministic vs AI |
| [`claude_code_workshop_tong_hop.md`](../bai-hoc-goc/claude_code_workshop_tong_hop.md) | Chương 02, 03, 06 — Codebase Q&A, `CLAUDE.md`, MCP dùng chung |
| [`claude-code-masterclass-tong-hop-bai-hoc.md`](../bai-hoc-goc/claude-code-masterclass-tong-hop-bai-hoc.md) | Chương 05, 08, 12 — skills, sub-agent, kiến trúc ba lớp |
| [`bai-hoc-claude-n8n-ai-automation.md`](../bai-hoc-goc/bai-hoc-claude-n8n-ai-automation.md) | Chương 10 — Routines, n8n, kiến trúc lai |

Nội dung trùng lặp giữa các bài đã được gộp; chỗ mâu thuẫn đã được ghi rõ trong chương
tương ứng thay vì chọn bừa một phía.
