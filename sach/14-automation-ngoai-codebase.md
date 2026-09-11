# Chương 14 — Automation ngoài codebase

> [← Chương 13](13-kinh-te-mo-hinh.md) | [Mục lục](README.md) | [Chương 15 →](15-chon-cong-cu-ai.md)

---

## 14.1. Dấu hiệu bạn đang cần automation, không phải prompt tốt hơn

Nếu công việc hằng ngày của bạn có chuỗi này:

```
Copy dữ liệu → dán sang chỗ khác → cập nhật bảng tính → gửi email → cập nhật hệ thống
```

thì vấn đề không nằm ở prompt. Prompt hay đến mấy cũng không giúp gì, vì bạn vẫn phải
ngồi đó thực hiện từng bước.

```
Việc lặp lại thủ công
        ↓
   Hệ thống tự chạy
```

Chương này nói về phần automation nằm **ngoài repo** — nơi AI làm việc với email,
bảng tính, CRM, lịch, thay vì với mã nguồn.

---

## 14.2. Bốn cấp độ tự động hoá

| Cấp | Hình thức | Đặc điểm |
|---|---|---|
| 1 | Chạy tay từng lần | Phụ thuộc bạn nhớ và có mặt |
| 2 | Script chạy tay | Nhanh hơn, vẫn cần bạn gõ lệnh |
| 3 | Lịch chạy trên máy cá nhân | Máy tắt là không chạy |
| 4 | Lịch chạy trên cloud | Chạy độc lập với máy bạn |

Ranh giới giữa cấp 3 và 4 quan trọng hơn vẻ ngoài của nó:

```
Bộ lập lịch trên máy cá nhân:
   Máy bật  → chạy
   Máy tắt  → không chạy      ← và bạn không biết là nó đã không chạy

Chạy trên cloud:
   Máy tắt  → vẫn chạy
```

Cái bẫy của cấp 3 không phải là "không chạy" — mà là **im lặng không chạy**. Bạn tin
rằng báo cáo đã được gửi cho đến khi ai đó hỏi vì sao tuần này không thấy.

---

## 14.3. Routines — công việc định kỳ chạy trên cloud

Routine là công việc lặp lại được chạy trên hạ tầng của nhà cung cấp, không phụ thuộc
máy cá nhân.

Mô hình đáng chú ý: **repository Git trở thành nguồn ngữ cảnh cho routine**.

```
GitHub Repository
 ├── Code
 ├── CLAUDE.md
 ├── Skills
 └── Ngữ cảnh dự án
          ↓
      Routine (chạy trên cloud, hằng tuần)
          ↓
      Đọc dữ liệu → phân tích → tạo báo cáo
```

Điểm mấu chốt: repo không chỉ là nơi lưu code. Nó là **nguồn sự thật** mà các
automation trên cloud lấy về để biết dự án là gì và làm việc theo quy trình nào.

Ví dụ một routine hoàn chỉnh:

```
Chạy mỗi thứ Hai
  → Kết nối kho tài liệu
  → Dùng skill "nghiên cứu đối thủ" và skill "nghiên cứu khách hàng"
  → Kết hợp kết quả
  → Tạo tài liệu báo cáo
```

Cùng một skill dùng trong phiên tương tác và trong routine — không phải viết hai bản.

### Ví dụ thật — automation định kỳ trong PM-AGENT

PM-AGENT có ba lớp automation, mỗi lớp cho một loại việc:

| Lớp | Công cụ | Dùng cho |
|---|---|---|
| Job nền | ARQ (queue trên Redis) | Đồng bộ nguồn, gắn nhãn item, sinh báo cáo |
| Lịch định kỳ | APScheduler | Cron trong ứng dụng |
| Nhắc theo phiên | Hook `Stop` | Nhắc gửi báo cáo khi phiên có sửa code |

Và một skill được thiết kế để chạy **cả thủ công lẫn tự động theo lịch**:
`daily-task-report`. Mô tả của nó ghi rõ điểm khác biệt ở chế độ tự động:

```
chế độ tự động không hỏi người, lọc task theo dấu vết thật:
nhật ký, đổi trạng thái, commit
```

Đây là nguyên tắc thiết kế quan trọng: một quy trình muốn chạy không người trực thì
mọi quyết định của nó phải dựa trên **dấu vết kiểm chứng được**, không dựa trên câu
hỏi cho người dùng. Nếu quy trình cần hỏi mới làm được thì nó chưa sẵn sàng để tự động.

---

## 14.4. Ranh giới quan trọng nhất: deterministic và AI

Đây là bài học lớn nhất của mảng automation, và nó lặp lại nguyên tắc ở
[chương 08](08-hooks-va-guardrails.md) trong bối cảnh mới.

### Workflow xác định (deterministic)

```
NẾU A → LÀM B → RỒI C
```

Ưu điểm: dự đoán được, kiểm tra được, dễ tìm lỗi, ít bất ngờ.

### AI Agent

```
Mục tiêu → tự quyết định các bước
```

Ưu điểm: xử lý được đầu vào phi cấu trúc, hiểu được ý định.
Nhược điểm: không xác định — cùng đầu vào có thể ra kết quả khác nhau.

### Nguyên tắc phân công

```
Rủi ro cao / lặp lại / có luật rõ
            ↓
      Workflow xác định

Chủ quan / ngữ nghĩa / ngôn ngữ tự nhiên
            ↓
         AI Agent
```

Hai loại này **không cạnh tranh nhau**. Chúng bổ sung cho nhau, và hệ thống tốt dùng
cả hai đúng chỗ.

| So sánh | Automation | AI Agent |
|---|---|---|
| Cách vận hành | Theo luật | Theo mục tiêu |
| Luồng | Cố định | Tự quyết định bước |
| Tính dự đoán | Cao | Thấp hơn |
| Phù hợp với | Tác vụ lặp lại | Bài toán nhiều bước, phi cấu trúc |

---

## 14.5. Case study — tự động hoá hộp thư

Đây là ví dụ điển hình cho kiến trúc lai. Bối cảnh: một studio nhỏ, hộp thư lẫn lộn
thư hỏi mua hàng, thư khiếu nại, thư cảm ơn, thư hỏi thông tin chung. Nhân viên xử lý
thủ công.

Mục tiêu:

1. Tự động chào khách
2. Nhớ khách cũ
3. Phân loại thư
4. Tóm tắt nội dung
5. Đưa dữ liệu vào bảng tính
6. Gắn nhãn thư
7. **Nhưng quyền trả lời khách vẫn thuộc về con người**

Điểm 7 là điểm quan trọng nhất của cả hệ thống.

### Bước 1 — Xây phần xác định trước

Phần này **không dùng AI**:

```
Thư mới đến
     ↓
Trích người gửi
     ↓
Tra trong CRM
     ↓
Đã có trong CRM?
  ┌──────┴──────┐
 CÓ            KHÔNG
  ↓              ↓
Cập nhật     Thêm mới
  ↓              ↓
Gửi thư      Gửi thư
"gặp lại"    chào mừng
```

Toàn bộ luồng này viết được bằng `if/else`. Giao cho AI là tự chuốc lấy sự bất định
mà không được lợi gì.

Khi ra lệnh xây, phải nói rõ:

> "...và **không dùng AI** cho workflow này."

### Bước 2 — Thêm AI vào đúng một chỗ

Sau khi phần xác định chạy ổn định, mới thêm AI vào phần nó thật sự cần thiết:

```
Thư
 ↓
AI đọc tiêu đề + nội dung
 ↓
Trả về: category (sales | support | general) + tóm tắt một câu
 ↓
Hệ thống rẽ nhánh theo category
 ↓
Gắn nhãn + ghi vào đúng bảng tính
```

Ranh giới được tuyên bố rõ:

> **AI chỉ phân loại và tóm tắt. AI KHÔNG trả lời khách hàng.**

### Kiến trúc cuối cùng

```
        Hệ thống xác định
                ↓
        Quy trình nghiệp vụ
                ↓
           Tầng AI          ← hiểu ngữ nghĩa
                ↓
     Đầu ra có cấu trúc
                ↓
        Hệ thống xác định   ← kiểm tra và thực thi
                ↓
     Hành động / CSDL / CRM
```

Không nên làm:

```
Thư → AI → AI tự quyết mọi thứ → AI tự gửi thư → AI tự sửa CRM
```

Nên làm:

```
Hệ thống kiểm soát luồng
        +
AI xử lý phần cần hiểu ngôn ngữ
        +
Hệ thống kiểm soát hành động cuối
```

### Bài học từ phần kiểm thử

Trong quá trình thử nghiệm, AI phân loại sai một vài thư. Đây không phải lỗi cài đặt —
đó là bản chất của thành phần không xác định.

> **Kết luận: đừng giao logic nghiệp vụ quan trọng cho AI nếu một workflow theo luật
> có thể xử lý chính xác hơn.**

---

## 14.6. Ép đầu ra có cấu trúc

Khi kết quả của AI đi tiếp vào máy móc, đừng nhận văn bản tự do.

```json
{
  "intent": "create_task",
  "priority": "high",
  "assignee": "team-a",
  "confidence": 0.92
}
```

Rồi:

```
Đầu ra AI
   ↓
Kiểm tra schema      ← sai schema thì dừng ngay, không đoán
   ↓
Áp quy tắc nghiệp vụ
   ↓
Hành động
```

Trường `confidence` mở ra một tầng an toàn rẻ tiền: dưới ngưỡng thì chuyển sang hàng
đợi chờ người xem, thay vì tự động chạy tiếp.

---

## 14.7. Con người vẫn ở trong vòng lặp

Với việc có rủi ro, thiết kế đúng là:

```
AI đọc
  ↓
AI phân loại
  ↓
AI tóm tắt
  ↓
Người xem
  ↓
Người quyết định trả lời
```

AI làm phần tốn thời gian (đọc, phân loại, tóm tắt). Người giữ phần có hậu quả (quyết
định và trả lời). Đây không phải giải pháp tạm thời chờ AI giỏi hơn — đây là thiết kế
đúng cho những việc mà một lần sai là mất khách hàng.

---

## 14.8. Gỡ lỗi là một phần của workflow

Đừng kỳ vọng ra lệnh một lần là có workflow hoàn hảo.

```
Xây
 ↓
Chạy
 ↓
Lỗi
 ↓
Xem lịch sử thực thi     ← nguồn thông tin quan trọng nhất
 ↓
Hiểu lỗi
 ↓
Hỏi AI với dữ liệu lỗi cụ thể
 ↓
Sửa
 ↓
Chạy lại
```

**Kỹ thuật kiểm thử chủ động:** cố tình phá một thứ để xem hệ thống phản ứng ra sao.
Ví dụ đổi tên một cột trong bảng tính rồi chạy lại — workflow phải báo lỗi rõ ràng ở
đúng bước, không được im lặng bỏ qua. Một hệ thống chỉ được kiểm thử ở đường đi đẹp
là một hệ thống chưa được kiểm thử.

---

## 14.9. Checklist xây một automation

### Trước khi xây

```
[ ] Xác định trigger (cái gì kích hoạt)
[ ] Xác định đầu vào
[ ] Xác định đầu ra
[ ] Xác định quy tắc nghiệp vụ
[ ] Xác định phần nào THẬT SỰ cần AI
[ ] Xác định dữ liệu nhạy cảm
[ ] Xác định quyền truy cập cần cấp (tối thiểu)
```

### Khi xây

```
[ ] Xây phần xác định TRƯỚC
[ ] Kiểm từng bước riêng lẻ
[ ] Kiểm thông tin xác thực
[ ] Kiểm ánh xạ dữ liệu giữa các bước
[ ] Chỉ thêm AI sau khi phần cơ bản đã ổn định
[ ] Ép AI trả về đầu ra có cấu trúc
```

### Khi kiểm thử

```
[ ] Đường đi đẹp
[ ] Trường hợp mới / trường hợp cũ
[ ] Từng nhánh phân loại
[ ] Dữ liệu sai định dạng
[ ] Dữ liệu thiếu trường
[ ] Xem lịch sử thực thi, kiểm cả lần thất bại
```

### Trước khi bật chạy thật

```
[ ] Không còn dữ liệu thử / thông tin xác thực thừa
[ ] Không commit dữ liệu riêng tư
[ ] Kiểm lại quyền — AI không có quyền vượt mức cần thiết
[ ] Có ghi log
[ ] Có cách biết khi nó thất bại   ← thường bị quên nhất
```

Dòng cuối đáng nhấn mạnh. Một automation thất bại **âm thầm** còn tệ hơn không có
automation: bạn tin rằng việc đã được làm.

---

## 14.10. Đừng đưa dữ liệu riêng tư lên nơi công khai

Khi đưa dự án lên kho công khai hoặc gửi cho dịch vụ ngoài:

| Loại dữ liệu | Xử lý |
|---|---|
| Thông tin khách hàng | Không commit, không gửi |
| Dữ liệu bán hàng, nội bộ | Tách khỏi repo công khai |
| Khoá, token, mật khẩu | Biến môi trường; đã lỡ commit thì **thu hồi khoá** |
| Ảnh chụp màn hình có dữ liệu thật | Che trước khi đưa vào tài liệu |

Nguyên tắc: **gửi ra dịch vụ ngoài là công bố**. Nó có thể được lưu đệm hoặc lập chỉ
mục kể cả khi sau đó bạn xoá đi.

---

## 14.11. Ba công thức đáng nhớ

**Công thức 1 — Xây tính năng**

```
Yêu cầu → Kế hoạch → Duyệt → Thực hiện → Kiểm thử → Triển khai
```

**Công thức 2 — Xây automation**

```
Trigger → Logic xác định → AI nếu cần → Kiểm tra hợp lệ → Hành động → Log → Giám sát
```

**Công thức 3 — Xử lý đầu vào phi cấu trúc**

```
Đầu vào phi cấu trúc → AI hiểu → Đầu ra có cấu trúc → Kiểm tra → Hành động xác định
```

---

## 14.12. Bài tập

**Bài 1 — Tìm việc lặp lại.**
Ghi lại một tuần làm việc. Đánh dấu mọi việc bạn làm nhiều hơn ba lần. Chọn việc tốn
thời gian nhất trong số đó.

**Bài 2 — Vẽ ranh giới.**
Với việc đó, vẽ luồng và tô hai màu: phần viết được thành `if/else`, phần cần hiểu
ngôn ngữ. Xây phần thứ nhất trước.

**Bài 3 — Kiểm thử phá hoại.**
Sau khi có automation chạy được, cố tình phá một thứ (đổi tên trường, bỏ trống dữ liệu).
Kiểm: nó có báo lỗi rõ ràng không, hay im lặng cho qua?

---

## Tóm tắt chương

- Copy-paste lặp lại là dấu hiệu cần automation, không phải cần prompt tốt hơn.
- Lịch chạy trên máy cá nhân thất bại **âm thầm** — cloud giải quyết điều đó.
- Repo có thể là **nguồn ngữ cảnh** cho automation trên cloud.
- Quy trình muốn chạy tự động thì mọi quyết định phải dựa trên **dấu vết kiểm chứng
  được**, không dựa trên câu hỏi cho người dùng.
- **Xây phần xác định trước, thêm AI sau** — và chỉ ở chỗ thật sự cần hiểu ngôn ngữ.
- AI hiểu; hệ thống kiểm tra và thực thi; người quyết định việc có hậu quả.
- Ép đầu ra có cấu trúc, kiểm tra schema, dùng ngưỡng tin cậy.
- Gỡ lỗi là một phần của quy trình — kiểm thử cả đường thất bại.
- Automation thất bại âm thầm còn tệ hơn không có automation.

> Chương tiếp: [15 — Chọn đúng công cụ AI](15-chon-cong-cu-ai.md)
