# Làm chủ AI Coding Agent — Từ Prompt đến Hệ thống

> Sách hệ thống hoá kiến thức về AI Agent và Claude Code, đúc kết từ sáu bài học đã ghi
> chép, viết lại theo **lớp năng lực** thay vì theo thứ tự bài giảng, và minh hoạ bằng
> **ví dụ thật đang chạy trong dự án PM-AGENT**.

16 chương · 6 phụ lục · đi từ cơ bản đến nâng cao, từ nguyên tắc chung đến kỹ thuật riêng.

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

## 2. Bản đồ kiến thức

Năm phần xếp chồng lên nhau, phần dưới đỡ phần trên. Mỗi phần trả lời một câu hỏi khác nhau:

```
┌──────────────────────────────────────────────────────────────┐
│ PHẦN V — MỞ RỘNG              "Ra ngoài codebase thì sao?"   │
│   14 Automation · 15 Chọn công cụ · 16 Lộ trình & Case study │
├──────────────────────────────────────────────────────────────┤
│ PHẦN IV — VẬN HÀNH BỀN VỮNG   "Chạy lâu dài có tốn không?"   │
│   12 Kinh tế ngữ cảnh · 13 Kinh tế mô hình                   │
├──────────────────────────────────────────────────────────────┤
│ PHẦN III — KIỂM SOÁT           "Làm sao biết nó làm đúng?"   │
│   08 Hooks · 09 Subagents · 10 Phối hợp · 11 Verification    │
├──────────────────────────────────────────────────────────────┤
│ PHẦN II — ĐÓNG GÓI             "Làm sao dùng lại được?"      │
│   06 Skills & Commands · 07 Tools & MCP                      │
├──────────────────────────────────────────────────────────────┤
│ PHẦN I — NỀN MÓNG              "Giao việc thế nào cho đúng?" │
│   01 Tư duy · 02 Prompting · 03 Đặc tả · 04 Context · 05 Plan│
└──────────────────────────────────────────────────────────────┘
```

Hai công thức xuyên suốt cả sách:

```
AI Agent tốt = Context + Tools + Rules + Feedback + Validation

Vòng lặp cốt lõi:  Quan sát → Suy nghĩ → Hành động → Nhận kết quả → lặp lại
```

---

## 3. Tự chẩn đoán: bạn đang thiếu gì?

Tìm triệu chứng giống mình nhất, đọc chương tương ứng. **Không cần đọc tuần tự.**

| Triệu chứng bạn đang gặp | Vấn đề thật | Đọc |
|---|---|---|
| "AI hiểu sai ý tôi liên tục" | Chưa đủ thông tin đầu vào | [02](02-prompting-va-context.md) |
| "Làm xong rồi mới biết không phải thứ tôi cần" | Không thống nhất *thế nào là xong* trước khi làm | [03](03-dac-ta-yeu-cau.md) |
| "Phiên nào tôi cũng phải dặn lại y hệt" | Kiến thức nằm trong chat thay vì trong repo | [04](04-context-engineering.md) |
| "Nó sửa 14 file trong khi tôi nhờ sửa 1" | Không có kế hoạch được duyệt, không có giới hạn | [05](05-workflow-plan-execute.md) |
| "Tôi giải thích lại cùng quy trình lần thứ ba" | Quy trình chưa được đóng gói | [06](06-skills-va-slash-commands.md) |
| "AI chỉ nói lý thuyết, không đọc/chạy được gì" | Thiếu công cụ | [07](07-tools-va-mcp.md) |
| "Dặn mãi mà vẫn quên" | Lời dặn cần trở thành cơ chế | [08](08-hooks-va-guardrails.md) |
| "Một phiên ôm quá nhiều việc, rối" | Chưa chia việc | [09](09-subagents-va-song-song.md) |
| "Kết quả không ổn định / bỏ sót điểm mù" | Cần nhiều góc nhìn phối hợp | [10](10-phoi-hop-da-tac-nhan.md) |
| "Code xong mà không chắc đã xong" | Thiếu vòng kiểm chứng | [11](11-verification-feedback-loop.md) |
| "Phiên dài thì AI đuối dần, quên lời dặn đầu" | Ngân sách ngữ cảnh bị tiêu hoang | [12](12-kinh-te-ngu-canh.md) |
| "Hoá đơn cao hơn tôi tưởng" | Chưa phân tầng mô hình | [13](13-kinh-te-mo-hinh.md) |
| "Việc lặp lại ngoài repo vẫn phải làm tay" | Chưa tự động hoá | [14](14-automation-ngoai-codebase.md) |
| "Không biết nên dùng AI nào cho việc này" | Chưa phân loại bài toán | [15](15-chon-cong-cu-ai.md) |
| "Tôi đang ở đâu, bước tiếp theo là gì?" | Chưa định vị được | [16](16-lo-trinh-va-case-study.md) |

> Nếu nhiều dòng cùng đúng với bạn: đọc dòng **nằm cao nhất trong bảng** trước. Bảng
> xếp theo thứ tự phụ thuộc — chữa gốc trước thì phần ngọn tự nhẹ đi.

---

## 4. Mục lục

### Phần I — Nền móng: giao việc thế nào cho đúng

| Chương | Nội dung | Đọc khi |
|---|---|---|
| [01 — Tư duy nền tảng](01-tu-duy-nen-tang.md) | Ba thời kỳ dùng AI. Công thức Context+Tools+Rules+Feedback. **Vòng lặp Quan sát→Suy nghĩ→Hành động.** Bảy nguyên tắc gốc | Bắt đầu từ đây, kể cả khi đã dùng lâu |
| [02 — Prompting & thu thập ngữ cảnh](02-prompting-va-context.md) | Khung T-R-F-C-R. Evaluate → Iterate. Codebase Q&A. Git là ngữ cảnh | Kết quả "gần đúng nhưng không dùng được" |
| [03 — Đặc tả yêu cầu](03-dac-ta-yeu-cau.md) ★ | Hợp đồng yêu cầu. Bốn thành phần của tiêu chí hoàn thành. **Điều kiện thất bại.** Kỹ thuật hỏi ngược | Làm xong mới biết không phải thứ mình cần |
| [04 — Context Engineering](04-context-engineering.md) | `CLAUDE.md`, rules phân tầng, memory, ngân sách ngữ cảnh | Phải lặp lại cùng lời dặn mỗi phiên |
| [05 — Workflow Plan → Execute](05-workflow-plan-execute.md) | Plan Mode. Chọn quy trình theo rủi ro. Vòng phản biện. Chia nhỏ commit | Trước khi giao một việc lớn |

### Phần II — Đóng gói: làm sao dùng lại được

| Chương | Nội dung | Đọc khi |
|---|---|---|
| [06 — Skills & Slash Commands](06-skills-va-slash-commands.md) | Biến prompt lặp lại thành quy trình tái dùng. Rule vs skill vs command vs hook | Giải thích lại cùng quy trình lần thứ ba |
| [07 — Tools & MCP](07-tools-va-mcp.md) | Bash tools, MCP server, chia sẻ cấu hình. Vì sao nhiều tool không mạnh hơn | Muốn AI đọc/ghi hệ thống bên ngoài |

### Phần III — Kiểm soát: làm sao biết nó làm đúng

| Chương | Nội dung | Đọc khi |
|---|---|---|
| [08 — Hooks & Guardrails](08-hooks-va-guardrails.md) | Hook do harness chạy. Ranh giới AI/hệ thống. Human-in-the-loop theo rủi ro | Lời dặn quan trọng bị quên lặp lại |
| [09 — Subagents & chạy song song](09-subagents-va-song-song.md) | Cô lập ngữ cảnh, brief subagent, git worktree, bẫy khi song song | Một phiên ôm quá nhiều việc |
| [10 — Phối hợp đa tác nhân](10-phoi-hop-da-tac-nhan.md) ★ | **Định tuyến · đồng thuận · tranh luận · kiểm tra độc lập.** Cái giá của phối hợp | Kết quả không ổn định, hay bỏ sót điểm mù |
| [11 — Verification & Feedback loop](11-verification-feedback-loop.md) | Test-first, coverage, layout verify. **Bảy bẫy test xanh mà tính năng vẫn hỏng** | "Code xong" mà vẫn không chắc |

### Phần IV — Vận hành bền vững: chạy lâu dài có tốn không ★

| Chương | Nội dung | Đọc khi |
|---|---|---|
| [12 — Kinh tế ngữ cảnh](12-kinh-te-ngu-canh.md) ★ | Kỹ thuật tảng băng. Nạp có chiến lược. Hiển thị theo nhu cầu. Nén và cái giá của nó | Phiên dài thì chất lượng tụt |
| [13 — Kinh tế mô hình](13-kinh-te-mo-hinh.md) ★ | Phân tầng mô hình. Phân bổ 60/30/10. Xử lý theo lô. Khi nào KHÔNG nên tiết kiệm | Chi phí cao hơn dự kiến |

### Phần V — Mở rộng: ra ngoài codebase

| Chương | Nội dung | Đọc khi |
|---|---|---|
| [14 — Automation ngoài codebase](14-automation-ngoai-codebase.md) | Routines trên cloud, kiến trúc lai deterministic + AI, structured output | Việc lặp lại nằm ngoài repo |
| [15 — Chọn đúng công cụ AI](15-chon-cong-cu-ai.md) | Phân loại General/Research/Chuyên biệt. Local AI. Vibe Coding. Khung 7 câu hỏi | Phân vân dùng AI nào |
| [16 — Lộ trình & Case study](16-lo-trinh-va-case-study.md) | Bảy cấp độ trưởng thành. Case study sự cố thật. Lộ trình 30 ngày | Muốn biết mình ở đâu, bước kế tiếp là gì |

### Phụ lục tra cứu

| Phụ lục | Nội dung |
|---|---|
| [A — Cú pháp & cấu hình](A-phu-luc-cu-phap.md) | Khung `CLAUDE.md`, frontmatter skill, hook trong `settings.json`, `.mcp.json`, brief subagent |
| [B — Phím tắt & dòng lệnh](B-phu-luc-phim-tat-cli.md) | Phím tắt trong phiên, cờ CLI, `claude -p` trong pipeline, lệnh Git an toàn |
| [C — Checklist](C-phu-luc-checklist.md) | Toàn bộ checklist gom một chỗ để in ra dùng |
| [D — Thuật ngữ](D-phu-luc-thuat-ngu.md) | Giải nghĩa ngắn, kèm chương nói kỹ |
| [E — Hướng dẫn viết `CLAUDE.md`](E-huong-dan-viet-claude-md.md) | Dòng nào đổi hành vi AI, dòng nào bị bỏ qua. Mẫu đầy đủ, cách kiểm chứng |
| [F — Bảng quyết định thực chiến](F-phu-luc-bang-quyet-dinh.md) ★ | Cây quyết định: gặp tình huống X thì dùng kỹ thuật nào |

★ = nội dung mới bổ sung từ giáo trình về tác nhân trí tuệ nhân tạo.

---

## 5. Ba lộ trình đọc

### Lộ trình A — Người mới (2 tuần)

```
01 → 02 → 03 → 04 → 05        Dựng nền, làm được việc có kiểm soát
     ↓
Thực hành: viết CLAUDE.md cho một dự án thật, chạy Plan Mode 3 lần
     ↓
06 → 07                        Đóng gói thứ mình lặp lại
     ↓
11                             Học cách biết khi nào là xong
```

Đừng đọc hết 16 chương trước khi làm. Kiến thức về agent chỉ đọng lại khi đã vấp.

### Lộ trình B — Đã dùng hằng ngày (1 tuần)

```
01 (mục 1.3 vòng lặp)  →  03  →  08  →  11  →  12
```

Bốn chương này là nơi người dùng thành thạo hay hụt: chưa đặc tả *thế nào là xong*,
lời dặn chưa thành cơ chế, verification có lỗ, và ngữ cảnh bị tiêu hoang.

### Lộ trình C — Thiết kế quy trình cho team

```
04 → 06 → 08 → 11        Bốn lớp tạo nên quy trình bàn giao được
     ↓
10 → 12 → 13             Phối hợp và chi phí khi nhiều người cùng dùng
     ↓
E, C, F                  Mẫu và checklist để phát cho team
```

---

## 6. Quy ước trong sách

| Ký hiệu | Nghĩa |
|---|---|
| **Ví dụ thật** | Trích từ mã nguồn/cấu hình đang chạy trong PM-AGENT, có đường dẫn kèm theo |
| **Ví dụ tổng quát** | Tình huống trung lập, áp dụng cho mọi dự án |
| **Bẫy** | Lỗi đã thực sự xảy ra, kèm dấu hiệu nhận biết |
| **Bài tập** | Việc cần làm để kiến thức chương đó đọng lại |
| ★ | Chương/phụ lục mới bổ sung |

Mọi đường dẫn dạng `app/modules/...` là đường dẫn tương đối từ gốc repo PM-AGENT.
Mọi khoá bí mật đều đã được che — khi làm theo, đọc giá trị thật từ biến môi trường.

---

## 7. Nguồn gốc

Sách tổng hợp và cấu trúc lại sáu ghi chép bài học trong thư mục `Claude-dev/`:

| File gốc | Đóng góp chính |
|---|---|
| `bai-hoc-5-nen-tang-ai-2026.md` | Ch.02, 15 — khung T-R-F-C-R, phân loại công cụ, cấp độ |
| `bai-hoc-lam-chu-ai-agent-automation.md` | Ch.01, 05, 08 — công thức agent, deterministic vs AI |
| `claude_code_workshop_tong_hop.md` | Ch.02, 04, 07 — Codebase Q&A, `CLAUDE.md`, MCP dùng chung |
| `claude-code-masterclass-tong-hop-bai-hoc.md` | Ch.06, 09, 16 — skills, sub-agent, kiến trúc ba lớp |
| `bai-hoc-claude-n8n-ai-automation.md` | Ch.14 — Routines, n8n, kiến trúc lai |
| `Giao-trinh-Lam-chu-Tac-nhan-Tri-tue-Nhan-tao.md` | **Ch.01 (vòng lặp), 03, 10, 12, 13, Phụ lục F** |

Nội dung trùng lặp giữa các bài đã được gộp; chỗ mâu thuẫn được ghi rõ trong chương
tương ứng thay vì chọn bừa một phía.
