# Phụ lục C — Checklist

> [← Phụ lục B](B-phu-luc-phim-tat-cli.md) | [Mục lục](README.md) | [Phụ lục D →](D-phu-luc-thuat-ngu.md)

> Gom toàn bộ checklist rải rác trong sách về một chỗ. Phần này thiết kế để in ra hoặc
> dán lên tường — mỗi mục có link về chương giải thích.

---

## C.1. Trước khi giao một task cho AI

```
NGỮ CẢNH
[ ] AI đã hiểu mục tiêu chưa?
[ ] Có CLAUDE.md chưa?
[ ] Đã đưa dữ liệu thật (log, response, schema, ảnh) thay vì mô tả chưa?
[ ] Đã chỉ ra file/module liên quan chưa?

PHẠM VI
[ ] Xác định được ít nhất 1 trong 3: URL / màn hình / file-hàm?
[ ] Đã nói rõ cái gì KHÔNG được đụng vào chưa?
[ ] Có quyết định nào đã chốt trong quá khứ về phần này không?

YÊU CẦU
[ ] Input/output đã rõ chưa?
[ ] Tiêu chí nghiệm thu đã rõ chưa?
[ ] Định dạng đầu ra mong muốn là gì?

THỰC THI
[ ] Task này có cần lập kế hoạch trước không?
[ ] Có thể chia nhỏ không?
[ ] Phần nào chạy song song được?

AN TOÀN
[ ] Hành động nào ở đây không hoàn tác được?
[ ] Có cần cổng phê duyệt không?
[ ] Có test/cách kiểm chứng không?
```

→ [Chương 02](02-prompting-va-context.md), [04](04-workflow-plan-execute.md)

---

## C.2. Chia nhỏ commit

```
[ ] Diff ≤ 5 file?
[ ] Không file nào > 100 dòng thay đổi?
[ ] Tổng diff ≤ 300 dòng?
[ ] Subtask này đứng độc lập được không (không vỡ nếu subtask sau chưa xong)?
[ ] Thông điệp commit mô tả đúng CÁI GÌ và TẠI SAO?
```

Thứ tự subtask: **schema/model → service/logic → API/router → test**

→ [Chương 04, mục 4.5](04-workflow-plan-execute.md)

---

## C.3. Tài liệu test (trước khi code)

```
[ ] Mỗi tiêu chí nghiệm thu có ≥ 1 happy path + 1 negative case?
[ ] Dữ liệu đầu vào CỤ THỂ (không phải "một email hợp lệ")?
[ ] Có test cho màn hình quan trọng (browser test)?
[ ] Có danh sách GAP (tính năng chưa làm → test skip, ghi rõ lý do)?
[ ] Mỗi nhánh của hàm có ≥ 1 test case?
[ ] Đã trình bày cho người duyệt và CHỜ duyệt xong chưa?
```

→ [Chương 09](09-verification-feedback-loop.md)

---

## C.4. Verify & đóng feature

```
[ ] 0 unit test thất bại
[ ] 0 browser test thất bại (nếu có TC browser)
[ ] Coverage đạt ngưỡng của module
[ ] Mọi tiêu chí nghiệm thu ở trạng thái đã kiểm chứng
[ ] Test doc đã cập nhật trạng thái từng TC
[ ] CHANGELOG đã có mục đóng
[ ] Người dùng đã XÁC NHẬN đóng (không tự đóng vì test xanh)
```

Ngưỡng coverage tham khảo:

| Loại code | Ngưỡng |
|---|---|
| Service / business logic | ≥ 80% |
| Router / API | ≥ 70% |
| Repository / DB | ≥ 60% |
| Bảo mật (auth, mã hoá) | ≥ 90% |
| Utils / helpers | ≥ 70% |

→ [Chương 09, mục 9.4](09-verification-feedback-loop.md)

---

## C.5. Màn hình giao diện

```
KỸ THUẬT
[ ] Dùng macro chung (button, form field, badge) — không tự viết HTML
[ ] Cancel bên trái, Primary bên phải; không có 2 primary button
[ ] Form có CSRF token + hx-post/get + hx-target
[ ] Dùng toast, không dùng alert()

TRẢI NGHIỆM
[ ] Mọi nút submit có trạng thái đang tải
[ ] Hành động thành công có phản hồi nhìn thấy được
[ ] Thông báo lỗi nói rõ vấn đề VÀ cách sửa, đặt gần chỗ lỗi
[ ] Danh sách rỗng có empty state (icon + text + hành động)
[ ] Hành động xoá/vô hiệu hoá qua modal xác nhận
[ ] Xem thử trên mobile — không tràn ngang

ĐỐI CHIẾU BỐ CỤC
[ ] Spec có mục "Tiêu chí UI/UX" cho màn hình này
[ ] Đã chụp ảnh màn hình sau khi implement
[ ] Đã đối chiếu từng tiêu chí — tất cả đạt
[ ] Có tiêu chí không đạt → đã sửa và chụp lại
```

→ [Chương 09, mục 9.6](09-verification-feedback-loop.md)

---

## C.6. Xây một automation

```
TRƯỚC KHI XÂY
[ ] Xác định trigger
[ ] Xác định đầu vào / đầu ra
[ ] Xác định quy tắc nghiệp vụ
[ ] Xác định phần nào THẬT SỰ cần AI
[ ] Xác định dữ liệu nhạy cảm
[ ] Xác định quyền truy cập tối thiểu cần cấp

KHI XÂY
[ ] Xây phần xác định TRƯỚC
[ ] Kiểm từng bước riêng lẻ
[ ] Kiểm thông tin xác thực
[ ] Kiểm ánh xạ dữ liệu giữa các bước
[ ] Chỉ thêm AI sau khi phần cơ bản ổn định
[ ] Ép AI trả về đầu ra có cấu trúc

KHI KIỂM THỬ
[ ] Đường đi đẹp
[ ] Từng nhánh rẽ
[ ] Dữ liệu sai định dạng / thiếu trường
[ ] Xem lịch sử thực thi, kiểm cả lần thất bại

TRƯỚC KHI BẬT THẬT
[ ] Không còn dữ liệu thử / thông tin xác thực thừa
[ ] Không commit dữ liệu riêng tư
[ ] AI không có quyền vượt mức cần thiết
[ ] Có ghi log
[ ] CÓ CÁCH BIẾT KHI NÓ THẤT BẠI      ← hay bị quên nhất
```

→ [Chương 10](10-automation-ngoai-codebase.md)

---

## C.7. Giao việc cho subagent

```
[ ] Nhiệm vụ cụ thể, có điểm kết thúc rõ?
[ ] Đã ghi ngữ cảnh tối thiểu (dự án, stack, file liên quan)?
[ ] Đã LIỆT KÊ RÀNG BUỘC ĐÃ CHỐT không được phá?
[ ] Đã nói rõ được làm gì / KHÔNG được làm gì?
[ ] Đã nêu định dạng đầu ra mong đợi?
[ ] Người lạ đọc brief này có làm được không mà không hỏi thêm?
```

→ [Chương 08, mục 8.4](08-subagents-va-song-song.md)

---

## C.8. Trước khi chạy hai việc song song

```
[ ] Đã liệt kê tập file dự kiến của từng việc?
[ ] Hai tập có giao nhau không? (giao nhiều → chạy nối tiếp)
[ ] Có file dùng chung (fixture, config, migration) không?
[ ] Việc B có phụ thuộc kết quả việc A không?
[ ] Có môi trường dùng chung nào bị đổi cấu hình khi chạy không?
[ ] Mỗi việc vẫn giữ commit riêng / test riêng / vòng verify riêng?
[ ] Đã git log TRƯỚC khi bắt đầu (để so sau)?
```

→ [Chương 08, mục 8.7–8.8](08-subagents-va-song-song.md)

---

## C.9. Rà soát hạ tầng agent (định kỳ, mỗi quý)

```
NGỮ CẢNH
[ ] CLAUDE.md còn dưới 400 dòng không?
[ ] Có luật nào không còn đúng cần xoá không?
[ ] Có lệnh/đường dẫn nào đã lỗi thời không?
[ ] Có mâu thuẫn giữa các tầng ngữ cảnh không?

CÔNG CỤ
[ ] MCP nào tháng qua không dùng lần nào? → tắt
[ ] Token nào cấp quyền rộng hơn mức cần? → thu hẹp
[ ] Có token nào lỡ commit không? → thu hồi

QUY TRÌNH
[ ] Skill nào chưa bao giờ được gọi? → sửa description hoặc bỏ
[ ] Hook nào đang gây ồn? → thu hẹp điều kiện
[ ] Lời dặn nào vẫn bị quên dù đã viết? → leo thang thành hook

KIỂM CHỨNG
[ ] Bộ test có test nào xanh mà không kiểm gì không?
[ ] Có test nào bị skip mà không còn lý do hợp lệ không?
[ ] Coverage có tụt dưới ngưỡng ở module nào không?
```

→ [Chương 03](03-context-engineering.md), [06](06-tools-va-mcp.md),
[07](07-hooks-va-guardrails.md), [09](09-verification-feedback-loop.md)

---

## C.10. Khung 7 câu hỏi chọn công cụ

```
1. Tôi muốn đạt kết quả gì?
2. AI cần biết những gì?
3. Đây là loại công việc gì? (reasoning / nghiên cứu / chuyên biệt)
4. Công việc có lặp lại không?         → có: automation
5. Có cần AI tự quyết định nhiều bước? → có: agent
6. Dữ liệu có nhạy cảm / cần kiểm soát hạ tầng? → có: local model
7. Không có công cụ phù hợp?           → tự xây
```

→ [Chương 11, mục 11.7](11-chon-cong-cu-ai.md)

---

## C.11. Ba câu hỏi tự kiểm nhanh

Khi không có thời gian cho checklist dài, hỏi ba câu:

```
1. AI có đủ thông tin để làm đúng không?          → Context
2. AI có cách nào tự biết là nó làm sai không?    → Feedback
3. Nếu nó làm sai, hậu quả có hoàn tác được không? → Validation
```

→ [Chương 01, mục 1.6](01-tu-duy-nen-tang.md)

> Tiếp: [Phụ lục D — Thuật ngữ](D-phu-luc-thuat-ngu.md)
