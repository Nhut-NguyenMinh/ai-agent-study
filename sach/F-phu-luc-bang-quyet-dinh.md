# Phụ lục F — Bảng quyết định thực chiến

> [← Phụ lục E](E-huong-dan-viet-claude-md.md) | [Mục lục](README.md)

> Gặp một tình huống, tra ngay kỹ thuật cần dùng. Mỗi dòng dẫn về chương nói kỹ.
> Đây là phụ lục để mở ra lúc đang làm việc, không phải để đọc từ đầu đến cuối.

---

## F.1. Cây quyết định tổng

```
CÔNG VIỆC ĐẾN
     │
     ├─ Yêu cầu chưa rõ, hoặc dùng từ định tính ("đẹp", "nhanh")?
     │     └─→ HỎI NGƯỢC + viết HỢP ĐỒNG YÊU CẦU          → ch.03
     │
     ├─ Chưa biết dự án/module này hoạt động ra sao?
     │     └─→ CODEBASE Q&A trước, chưa sửa gì            → ch.02
     │
     ├─ Việc lớn, nhiều bước, khó hoàn tác?
     │     └─→ PLAN MODE: kế hoạch → người duyệt → làm    → ch.05
     │
     ├─ Việc này tôi đã mô tả quy trình ≥ 3 lần?
     │     └─→ ĐÓNG GÓI THÀNH SKILL                       → ch.06
     │
     ├─ AI cần đọc/ghi hệ thống bên ngoài?
     │     └─→ BASH CLI trước; không có thì MCP           → ch.07
     │
     ├─ Lời dặn này đã bị quên ≥ 2 lần?
     │     └─→ LEO THANG THÀNH HOOK                       → ch.08
     │
     ├─ Việc cần đọc rất nhiều file nhưng kết luận ngắn?
     │     └─→ SUBAGENT                                   → ch.09, 12
     │
     ├─ Có nhiều phần độc lập, làm song song được?
     │     └─→ KIỂM PHẠM VI FILE TRÙNG rồi mới song song  → ch.09
     │
     ├─ Bài toán có nhiều lời giải hợp lý?
     │     └─→ ĐỒNG THUẬN (N tác nhân độc lập)            → ch.10
     │
     ├─ Cần thách thức giả định, tìm điểm mù?
     │     └─→ TRANH LUẬN (các tác nhân có vai)           → ch.10
     │
     ├─ Kết quả quan trọng, cần nghiệm thu?
     │     └─→ KIỂM TRA ĐỘC LẬP (người làm ≠ người kiểm)  → ch.10, 11
     │
     ├─ Không chắc "đã xong" nghĩa là gì?
     │     └─→ TEST-FIRST + tiêu chí nghiệm thu           → ch.03, 11
     │
     ├─ Phiên dài, chất lượng tụt dần?
     │     └─→ ĐO NGỮ CẢNH → nạp có chiến lược → chia phiên → ch.12
     │
     ├─ Chi phí cao hơn dự kiến?
     │     └─→ PHÂN TẦNG MÔ HÌNH + xử lý theo lô          → ch.13
     │
     ├─ Việc lặp lại nằm ngoài repo?
     │     └─→ AUTOMATION: xác định trước, AI sau         → ch.14
     │
     └─ Không rõ nên dùng công cụ AI nào?
           └─→ KHUNG 7 CÂU HỎI                            → ch.15
```

---

## F.2. Tra theo triệu chứng

| Triệu chứng | Kỹ thuật | Chương |
|---|---|---|
| AI hiểu sai ý liên tục | Đưa dữ liệu thật thay vì mô tả; T-R-F-C-R | [02](02-prompting-va-context.md) |
| Làm xong mới biết sai hướng | Hợp đồng yêu cầu + điều kiện thất bại | [03](03-dac-ta-yeu-cau.md) |
| Phải dặn lại y hệt mỗi phiên | Đưa vào `CLAUDE.md`/rules | [04](04-context-engineering.md) |
| Code mới vẫn theo kiểu cũ | Nói rõ "code cũ là nợ kỹ thuật" | [E](E-huong-dan-viet-claude-md.md) |
| Sửa 14 file khi chỉ nhờ sửa 1 | Kế hoạch được duyệt + giới hạn commit | [05](05-workflow-plan-execute.md) |
| Giải thích lại quy trình lần 3 | Đóng gói skill | [06](06-skills-va-slash-commands.md) |
| Skill không bao giờ được gọi | Sửa `description`: thêm câu kích hoạt thật | [06](06-skills-va-slash-commands.md) |
| AI chỉ nói lý thuyết | Trang bị công cụ | [07](07-tools-va-mcp.md) |
| Model chọn nhầm tool liên tục | Tắt MCP không dùng | [07](07-tools-va-mcp.md), [12](12-kinh-te-ngu-canh.md) |
| Dặn mãi vẫn quên | Leo thang thành hook | [08](08-hooks-va-guardrails.md) |
| Hook bị mọi người tắt | Thu hẹp điều kiện + chống lặp | [08](08-hooks-va-guardrails.md) |
| Một phiên ôm quá nhiều việc | Subagent / chia phiên | [09](09-subagents-va-song-song.md), [12](12-kinh-te-ngu-canh.md) |
| Subagent phá quyết định cũ | Brief đủ ràng buộc đã chốt | [09](09-subagents-va-song-song.md) |
| Kết quả không ổn định | Đồng thuận nhiều tác nhân | [10](10-phoi-hop-da-tac-nhan.md) |
| Bỏ sót điểm mù | Tranh luận có vai | [10](10-phoi-hop-da-tac-nhan.md) |
| Test xanh mà tính năng vẫn hỏng | Smoke test đường thật | [11](11-verification-feedback-loop.md) §11.8 |
| "Xong" mà không ai dám đóng | Điều kiện đóng + người xác nhận | [11](11-verification-feedback-loop.md) |
| Phiên dài thì AI quên lời dặn đầu | Đo ngữ cảnh, rút gọn, chia phiên | [12](12-kinh-te-ngu-canh.md) |
| Hoá đơn cao bất ngờ | Phân tầng mô hình, đo theo loại việc | [13](13-kinh-te-mo-hinh.md) |
| Automation im lặng không chạy | Thêm cách biết khi nó thất bại | [14](14-automation-ngoai-codebase.md) |
| Lỗi 401/403 lặp lại | Kiểm biến môi trường container trước | [07](07-tools-va-mcp.md), [B](B-phu-luc-phim-tat-cli.md) |

---

## F.3. Chọn cơ chế: rule, skill, command hay hook

```
Luôn đúng, mọi phiên, mọi loại việc?
   → RULE  (.claude/rules/)

Quy trình cho một loại việc, AI tự nhận biết khi cần?
   → SKILL  (.claude/skills/)

Quy trình người chủ động gọi?
   → COMMAND  (.claude/commands/)

Lời dặn hay bị quên dù đã viết?
   → HOOK  (.claude/hooks/ + settings.json)

Vi phạm gây hậu quả không hoàn tác được?
   → CHẶN CỨNG Ở TẦNG HỆ THỐNG  (scope quyền, quyền file, CI gác cổng)
```

Thang leo thang khi một luật liên tục thất bại:

```
CLAUDE.md → skill → hook → chặn cứng
```

→ [ch.06](06-skills-va-slash-commands.md), [ch.08](08-hooks-va-guardrails.md)

---

## F.4. Chọn mẫu phối hợp đa tác nhân

| Câu hỏi | Mẫu | Chi phí |
|---|---|---|
| Việc cần đúng loại năng lực? | **Định tuyến** | Thấp — thực ra *tiết kiệm* |
| Kết quả quan trọng, cần nghiệm thu? | **Kiểm tra độc lập** | Trung bình |
| Cần thách thức giả định? | **Tranh luận** | Cao |
| Nhiều lời giải hợp lý, cần mở rộng? | **Đồng thuận** | Cao (N lần) |
| Việc đơn giản, một đáp án đúng? | **Một tác nhân** | — |

→ [ch.10](10-phoi-hop-da-tac-nhan.md)

---

## F.5. Chọn mức mô hình

```
Việc lặp lại nhiều, mỗi lần đơn giản?     → mức rẻ + cân nhắc xử lý theo lô
Suy luận nhiều bước, quyết kiến trúc?     → mức mạnh, đừng tiếc
Kiểm tra/nghiệm thu việc quan trọng?      → mức mạnh + tác nhân độc lập
Quét rộng rồi chốt hẹp?                   → rẻ chạy rộng, đắt kiểm chéo cuối
Không rõ cần mức nào?                     → thử mức thấp trước, ghi lại kết quả
```

**Không tiết kiệm ở:** kiểm tra việc quan trọng · điều phối nhiều bước · việc chỉ làm
một lần · khi đang gỡ lỗi khó.

→ [ch.13](13-kinh-te-mo-hinh.md)

---

## F.6. Chọn quy trình theo mức rủi ro

| Loại việc | Quy trình |
|---|---|
| Nhỏ (typo, đổi màu) | Hỏi → Làm → Kiểm |
| Vừa (một hàm, một endpoint) | Hiểu → Kế hoạch → Làm → Test |
| Lớn (nhiều module) | Khám phá → Kế hoạch → **Người duyệt** → Làm → Test → Lặp |
| Nguy hiểm (production, dữ liệu, tiền, quyền) | Hiểu → Kế hoạch → Kiểm tra hợp lệ → **Người phê duyệt** → Thực thi → Ghi log |

→ [ch.05](05-workflow-plan-execute.md), [ch.08](08-hooks-va-guardrails.md)

---

## F.7. Ba câu hỏi khi bí

Khi không biết bắt đầu từ đâu, hỏi ba câu này theo thứ tự:

```
1. AI có đủ thông tin để làm đúng không?           → Context   → ch.02, 03, 04
2. AI có cách nào tự biết là nó làm sai không?     → Feedback  → ch.11
3. Nếu nó làm sai, hậu quả có hoàn tác được không? → Validation → ch.08
```

Câu nào trả lời "không" thì đó là việc cần làm trước.

→ [ch.01 §1.7](01-tu-duy-nen-tang.md)

---

> Về [Mục lục](README.md)
