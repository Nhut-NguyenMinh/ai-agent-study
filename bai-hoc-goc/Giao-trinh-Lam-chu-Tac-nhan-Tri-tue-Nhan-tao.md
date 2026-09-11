# GIÁO TRÌNH LÀM CHỦ TÁC NHÂN TRÍ TUỆ NHÂN TẠO
## Từ mô hình ngôn ngữ đến hệ thống tác nhân có khả năng tự thực hiện công việc

> **Mục đích:** Tài liệu này tổng hợp toàn bộ nội dung khóa học thành một bài giảng tiếng Việt hoàn chỉnh, tập trung vào cách tư duy, kiến trúc và phương pháp xây dựng tác nhân trí tuệ nhân tạo có khả năng suy luận, sử dụng công cụ, thực hiện công việc, phối hợp với nhiều tác nhân khác, tự kiểm tra và tối ưu chi phí.
>
> **Quy ước:** Tài liệu được viết **thuần Việt về mặt diễn đạt**. Các thuật ngữ nước ngoài trong bài học được chuyển sang cách gọi tiếng Việt tương ứng để dễ học và dễ ghi nhớ.

---

# MỤC LỤC

1. Tư tưởng nền tảng
2. Bốn thành phần của một tác nhân
3. Vòng lặp hoạt động cốt lõi
4. Tiêu chí hoàn thành
5. Hợp đồng yêu cầu
6. Hỏi ngược để làm rõ yêu cầu
7. Bộ nhớ và quy tắc
8. Kỹ năng và quy trình chuẩn
9. Công cụ và giao tiếp với hệ thống bên ngoài
10. Điều phối nhiều tác nhân
11. Bộ định tuyến công việc
12. Chuyên môn hóa tác nhân
13. Xử lý song song
14. Tự động hóa trình duyệt bằng nhiều tác nhân
15. Biến video thành hành động
16. Đồng thuận từ nhiều tác nhân
17. Phòng tranh luận của các tác nhân
18. Vòng kiểm tra độc lập
19. Mô hình nhiều chuyên gia
20. Quản lý ngữ cảnh
21. Kỹ thuật tảng băng
22. Nạp thông tin có chiến lược
23. Hiển thị thông tin theo nhu cầu
24. Nén ngữ cảnh
25. Tối ưu số lượng đơn vị xử lý
26. Tối ưu chất lượng và chi phí
27. Phân tầng mô hình
28. Xử lý theo lô
29. Kiến trúc tác nhân hoàn chỉnh
30. Bảng quyết định thực chiến
31. Mười nguyên tắc vàng
32. Lộ trình tư duy
33. Kết luận

---

# 1. TƯ TƯỞNG NỀN TẢNG

Sai lầm phổ biến nhất khi bắt đầu với tác nhân trí tuệ nhân tạo là nghĩ:

> **Tác nhân chính là một mô hình ngôn ngữ thật thông minh.**

Thực tế, mô hình ngôn ngữ chỉ là **bộ máy suy luận**.

Một tác nhân hoàn chỉnh được xây dựng xung quanh bộ máy đó.

```text
                         TÁC NHÂN TRÍ TUỆ
                                │
          ┌─────────────────────┼─────────────────────┐
          ↓                     ↓                     ↓
      MÔ HÌNH NGÔN NGỮ       VÒNG LẶP             CÔNG CỤ
          │                     │                     │
      Suy luận               Làm tiếp tục         Đọc tệp
      Hiểu ngôn ngữ          Cho tới khi xong     Chạy mã
      Ra quyết định          Không dừng một lần   Tìm kiếm
                                                    Gọi dịch vụ
                                                    Sửa tệp
                                │
                                ↓
                            BỘ NHỚ
                                │
                         Quy tắc cá nhân
                         Lịch sử trao đổi
                         Kinh nghiệm
                         Kỹ năng
                                │
                                ↓
                    HÀNH ĐỘNG TRONG THỰC TẾ
                         KHÔNG CHỈ VĂN BẢN
```

## Bốn thành phần nền tảng

| Thành phần | Vai trò |
|---|---|
| **Mô hình ngôn ngữ** | Suy luận, hiểu ngôn ngữ và ra quyết định |
| **Vòng lặp** | Cho phép tác nhân tiếp tục làm việc cho tới khi hoàn thành |
| **Công cụ** | Cho phép tác nhân tác động vào thế giới bên ngoài |
| **Bộ nhớ** | Lưu quy tắc, kinh nghiệm, sở thích và quy trình |

### Bài học

> **Tác nhân không phải chỉ là mô hình. Tác nhân là một hệ thống được xây dựng xung quanh mô hình.**

---

# 2. BỐN THÀNH PHẦN CỦA MỘT TÁC NHÂN

## 2.1. Bộ não — mô hình ngôn ngữ

Đây là phần chịu trách nhiệm:

- hiểu yêu cầu;
- suy luận;
- lập kế hoạch;
- đưa ra quyết định;
- tạo ra kết quả.

Nhưng bộ não dù thông minh đến đâu cũng không tự động có khả năng:

- đọc tệp trên máy;
- sửa mã nguồn;
- mở trình duyệt;
- gửi biểu mẫu;
- gọi dịch vụ;
- thao tác phần mềm.

Muốn làm được những việc đó cần có công cụ.

---

## 2.2. Vòng lặp

Một mô hình thông thường có thể hoạt động:

```text
Yêu cầu → Câu trả lời
```

Một tác nhân hoạt động:

```text
Quan sát
   ↓
Suy nghĩ
   ↓
Hành động
   ↓
Nhận kết quả
   ↓
Quan sát lại
   ↓
Suy nghĩ
   ↓
Hành động
   ↓
...
```

Đây chính là điểm biến mô hình từ một hệ thống **trả lời** thành một hệ thống **làm việc**.

---

## 2.3. Công cụ

Công cụ là khả năng để tác nhân hành động:

```text
Đọc tệp
Sửa tệp
Chạy chương trình
Chạy lệnh
Tìm kiếm mạng
Gọi dịch vụ
Điều khiển trình duyệt
Tương tác ứng dụng
```

---

## 2.4. Bộ nhớ

Bộ nhớ giúp tác nhân duy trì:

- quy tắc;
- sở thích;
- kinh nghiệm;
- lịch sử;
- quy trình;
- thông tin quan trọng.

Nhờ vậy, một bài học từ hôm nay có thể ảnh hưởng đến hành vi của tác nhân trong những lần làm việc sau.

---

# 3. VÒNG LẶP HOẠT ĐỘNG CỐT LÕI

Đây là kiến thức quan trọng nhất của toàn bộ khóa học.

```text
┌─────────────────────┐
│      QUAN SÁT       │
│                     │
│ Đọc ngữ cảnh        │
│ Đọc tệp             │
│ Đọc kết quả trước   │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│      SUY NGHĨ       │
│                     │
│ Suy luận             │
│ Lập kế hoạch         │
│ Chọn bước tiếp theo  │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│      HÀNH ĐỘNG      │
│                     │
│ Gọi công cụ          │
│ Sửa tệp              │
│ Chạy lệnh            │
└──────────┬──────────┘
           ↓
        Nhận kết quả
           │
           └──────────────→ QUAN SÁT LẠI
                                  ↓
                               SUY NGHĨ
                                  ↓
                               HÀNH ĐỘNG
                                  ↓
                                  ...
                                  ↓
                         CÔNG VIỆC HOÀN THÀNH
                                  ↓
                           TẠO KẾT QUẢ CUỐI
                                  ↓
                                NGƯỜI DÙNG
```

## 3.1. Quan sát

Tác nhân xem xét:

- yêu cầu;
- quy tắc;
- bộ nhớ;
- kỹ năng;
- tệp;
- dữ liệu;
- kết quả của công cụ;
- thông tin từ mạng;
- dữ liệu hình ảnh, âm thanh hoặc video;
- trạng thái hiện tại của công việc.

## 3.2. Suy nghĩ

Tác nhân tự xác định:

> “Tôi cần làm gì tiếp theo?”

Nó có thể:

- lập kế hoạch;
- chia nhỏ công việc;
- chọn công cụ;
- chọn tác nhân khác;
- đánh giá kết quả;
- thử phương án khác nếu thất bại.

## 3.3. Hành động

Tác nhân thực hiện hành động bằng công cụ.

## 3.4. Phản hồi

Kết quả hành động trở lại ngữ cảnh.

Nếu thành công:

```text
Hành động → Kết quả → Hoàn thành
```

Nếu thất bại:

```text
Hành động → Lỗi
           ↓
        Quan sát
           ↓
        Suy nghĩ
           ↓
      Hành động khác
```

### Nguyên tắc

> **Tác nhân mạnh nhờ khả năng lặp lại chu trình quan sát → suy nghĩ → hành động cho tới khi đạt mục tiêu.**

---

# 4. TIÊU CHÍ HOÀN THÀNH

Một tác nhân phải biết:

> **Khi nào công việc được xem là hoàn thành?**

Nếu yêu cầu là:

> “Hãy làm một trang web thật đẹp.”

thì tác nhân phải tự đoán:

- đẹp là như thế nào;
- dành cho ai;
- có những phần nào;
- trên điện thoại có hoạt động không;
- giới hạn mã nguồn ra sao;
- điều gì được xem là thất bại.

Thay vào đó, hãy xác định:

```text
MỤC TIÊU
+
RÀNG BUỘC
+
ĐẦU RA
+
ĐIỀU KIỆN THẤT BẠI
```

Ví dụ:

```text
MỤC TIÊU
Tạo trang giới thiệu sản phẩm.

RÀNG BUỘC
- Hoạt động tốt trên điện thoại
- Cuộn trang mượt
- Không dùng giao diện tối
- Mã nguồn không quá dài

ĐẦU RA
- Phần mở đầu
- Tính năng
- Đánh giá
- Kêu gọi hành động

ĐIỀU KIỆN THẤT BẠI
- Giao diện chung chung
- Điện thoại bị vỡ bố cục
- Hiệu ứng bị giật
- Mã nguồn quá dài
```

### Bài học

> **Tác nhân không chỉ cần biết phải làm gì; nó cần biết thế nào là làm xong.**

---

# 5. HỢP ĐỒNG YÊU CẦU

Hợp đồng yêu cầu là cách biến yêu cầu mơ hồ thành một bản mô tả có thể thực thi.

```text
Yêu cầu của người dùng
        ↓
   Hợp đồng yêu cầu
        │
        ├── Mục tiêu
        ├── Ràng buộc
        ├── Đầu ra
        └── Điều kiện thất bại
        ↓
   Tác nhân thực hiện
        ↓
       Kiểm tra
```

Nó giống như một bản phạm vi công việc.

## Lợi ích

- giảm hiểu sai;
- giảm việc tác nhân tự đoán;
- dễ kiểm tra;
- dễ đánh giá;
- dễ tái sử dụng;
- tạo tiêu chí hoàn thành rõ ràng.

### Nguyên tắc

> **Yêu cầu tốt không nhất thiết phải dài. Yêu cầu tốt phải rõ.**

---

# 6. HỎI NGƯỢC ĐỂ LÀM RÕ YÊU CẦU

Với công việc phức tạp, đừng bắt tác nhân thực hiện ngay.

Thay vào đó:

```text
Yêu cầu
   ↓
Tác nhân hỏi ngược
   ↓
Câu hỏi làm rõ
   ↓
Người dùng trả lời
   ↓
Hợp đồng yêu cầu
   ↓
Thực hiện
```

Tác nhân có thể hỏi:

- Mục tiêu thực sự là gì?
- Ai sẽ sử dụng?
- Điều gì quan trọng nhất?
- Có phong cách nào mong muốn?
- Điều gì tuyệt đối không được làm?
- Có giới hạn nào?
- Ưu tiên chất lượng hay tốc độ?
- Điều gì được xem là thất bại?

### Bài học

> **Trước khi cố trả lời thật hay, hãy cố hiểu thật đúng.**

---

# 7. BỘ NHỚ VÀ QUY TẮC

Tác nhân có thể học từ phản hồi.

Ví dụ:

```text
Người dùng:
“Đừng tạo giao diện tối nữa.”

Tác nhân ghi:

QUY TẮC:
Không tạo ứng dụng bằng giao diện tối.

LÝ DO:
Người dùng thích giao diện sáng.
```

Lần sau, tác nhân đọc quy tắc này trước khi thực hiện.

## Chu trình học

```text
Trải nghiệm
   ↓
Lỗi / phản hồi
   ↓
Bài học
   ↓
Quy tắc
   ↓
Bộ nhớ
   ↓
Phiên làm việc sau
```

## Phân tầng quy tắc

```text
QUY TẮC TOÀN CỤC
        ↓
QUY TẮC DỰ ÁN
        ↓
KỸ NĂNG
        ↓
YÊU CẦU HIỆN TẠI
        ↓
TÁC NHÂN
```

### Bài học

> **Bộ nhớ biến kinh nghiệm thành hành vi nhất quán.**

---

# 8. KỸ NĂNG VÀ QUY TRÌNH CHUẨN

Một kỹ năng của tác nhân có thể hiểu là:

> **Một quy trình chuẩn để tác nhân thực hiện một loại công việc.**

Ví dụ:

```text
KỸ NĂNG: TẠO ĐỀ XUẤT

1. Phân tích yêu cầu
2. Nghiên cứu khách hàng
3. Lập dàn ý
4. Viết đề xuất
5. Kiểm tra
6. Xác nhận
7. Xuất kết quả
```

Thay vì mỗi lần phải giải thích lại toàn bộ quy trình, chỉ cần yêu cầu tác nhân sử dụng quy trình đó.

## Quá trình chuyển hóa

```text
Kinh nghiệm
   ↓
Kiến thức
   ↓
Quy trình chuẩn
   ↓
Kỹ năng của tác nhân
   ↓
Năng lực có thể tái sử dụng
```

### Phân biệt

**Quy tắc:**

> “Luôn làm X.”

**Kỹ năng:**

> “Khi thực hiện loại công việc Y, hãy làm theo quy trình A → B → C.”

---

# 9. CÔNG CỤ VÀ GIAO TIẾP VỚI HỆ THỐNG BÊN NGOÀI

Có thể hình dung:

```text
Mô hình ngôn ngữ = Bộ não
Công cụ          = Đôi tay
Lớp kết nối      = Cầu nối
Môi trường       = Thế giới
```

Công cụ cho phép tác nhân:

- đọc tệp;
- sửa tệp;
- chạy chương trình;
- chạy lệnh;
- tìm kiếm;
- gọi dịch vụ;
- điều khiển trình duyệt;
- tương tác với ứng dụng.

### Công thức

```text
Bộ não
+
Công cụ
+
Môi trường
=
Khả năng hành động
```

### Bài học

> **Mô hình chỉ suy nghĩ. Tác nhân tạo ra giá trị khi nó có thể biến suy nghĩ thành hành động.**

---

# 10. ĐIỀU PHỐI NHIỀU TÁC NHÂN

Một tác nhân duy nhất có thể làm:

```text
Nghiên cứu
↓
Lập trình
↓
Kiểm tra
↓
Đánh giá
```

Nhưng có thể chia thành:

```text
                 NGƯỜI ĐIỀU PHỐI
                       │
          ┌────────────┼────────────┐
          ↓            ↓            ↓
      Nghiên cứu     Giao diện     Máy chủ
       Tác nhân       Tác nhân     Tác nhân
          │            │            │
          └────────────┼────────────┘
                       ↓
                    Đánh giá
                       ↓
                     Sửa
                       ↓
                    Kiểm tra
```

Người điều phối chịu trách nhiệm:

1. Lập kế hoạch
2. Chia việc
3. Phân công
4. Theo dõi
5. Thu kết quả
6. Đánh giá
7. Ghép kết quả

---

# 11. BỘ ĐỊNH TUYẾN CÔNG VIỆC

Bộ định tuyến quyết định:

> **Công việc này nên giao cho tác nhân hoặc mô hình nào?**

Ví dụ theo cách trình bày trong khóa học:

```text
Video / dữ liệu đa phương thức
        ↓
Mô hình mạnh về hình ảnh và video

Suy luận phức tạp
        ↓
Mô hình mạnh về suy luận

Lập trình / kiểm thử
        ↓
Mô hình mạnh về lập trình

Phân loại đơn giản
        ↓
Mô hình nhanh và rẻ
```

**Chú thích:** Tên và khả năng của các mô hình thay đổi theo thời gian. Điều cần ghi nhớ là nguyên tắc:

> **Đúng mô hình → đúng công việc.**

Không nên dùng mô hình mạnh và đắt nhất cho mọi nhiệm vụ.

---

# 12. CHUYÊN MÔN HÓA TÁC NHÂN

Thay vì một tác nhân phải giỏi tất cả:

```text
Tác nhân A → Nghiên cứu
Tác nhân B → Giao diện
Tác nhân C → Lập trình
Tác nhân D → Kiểm thử
```

Mỗi tác nhân tập trung vào phần mình phù hợp nhất.

Điều này tạo ra:

- ít ngữ cảnh thừa;
- nhiệm vụ rõ hơn;
- dễ kiểm tra;
- dễ mở rộng;
- có thể dùng mô hình khác nhau.

---

# 13. XỬ LÝ SONG SONG

Nếu các nhiệm vụ độc lập, không nên xử lý tuần tự.

## Tuần tự

```text
A → 2 phút
B → 2 phút
C → 2 phút
D → 2 phút

≈ 8 phút
```

## Song song

```text
A ─┐
B ─┤
C ─┼──→ cùng xử lý
D ─┘

≈ 2 phút
```

### Tư duy quan trọng

> **Không chỉ tìm cách làm một việc nhanh hơn. Hãy thay đổi cách chia việc để làm nhiều việc cùng lúc.**

Đây là một trong những lợi ích kinh tế lớn nhất của hệ thống nhiều tác nhân.

---

# 14. TỰ ĐỘNG HÓA TRÌNH DUYỆT BẰNG NHIỀU TÁC NHÂN

Ví dụ tìm kiếm thông tin trên nhiều trang web.

### Cách tuần tự

```text
Trình duyệt
 ↓
Trang A
 ↓
Trang B
 ↓
Trang C
 ↓
Trang D
```

### Cách song song

```text
                    Người điều phối
                         │
        ┌────────────────┼────────────────┐
        ↓                ↓                ↓
    Trình duyệt 1    Trình duyệt 2    Trình duyệt 3
        │                │                │
      Trang A          Trang B          Trang C
        │                │                │
        └────────────────┼────────────────┘
                         ↓
                     Tổng hợp
                         ↓
                       Lọc
                         ↓
                   Kết quả cuối
```

Mỗi tác nhân có thể:

- mở trang;
- nhấn nút;
- áp dụng bộ lọc;
- tìm kiếm;
- đọc kết quả;
- thu thập dữ liệu.

### Bài học tổng quát

Đây không chỉ là kỹ thuật dành cho trình duyệt.

Đây là mô hình:

> **Chia không gian công việc thành nhiều phần độc lập rồi cho nhiều tác nhân xử lý đồng thời.**

---

# 15. BIẾN VIDEO THÀNH HÀNH ĐỘNG

Một quy trình rất đáng chú ý:

```text
Video
  ↓
Phân tích hình ảnh và nội dung
  ↓
Trích xuất các bước
  ↓
Chuyển thành hướng dẫn có cấu trúc
  ↓
Tác nhân thực hiện
  ↓
Công cụ
  ↓
Kiểm tra
```

Ví dụ:

```text
Video hướng dẫn phần mềm
        ↓
Phân tích video
        ↓
Trích xuất quy trình
        ↓
Tác nhân tái tạo quy trình
        ↓
Kiểm tra kết quả
```

### Bài học

> **Video có thể trở thành nguồn hướng dẫn cho tác nhân, không chỉ là nội dung để con người xem.**

Quy trình trích xuất được còn có thể lưu thành kỹ năng để dùng lại.

---

# 16. ĐỒNG THUẬN TỪ NHIỀU TÁC NHÂN

Mô hình ngôn ngữ có tính biến thiên.

Cùng một câu hỏi:

```text
Tác nhân 1 → A
Tác nhân 2 → B
Tác nhân 3 → A
Tác nhân 4 → C
Tác nhân 5 → D
Tác nhân 6 → A
```

Thay vì coi sự khác biệt là vấn đề, ta có thể khai thác nó.

```text
                    Vấn đề
                       ↓
       ┌─────────┬─────────┬─────────┐
       ↓         ↓         ↓         ↓
    Tác nhân 1 Tác nhân 2 Tác nhân 3 ... N
       ↓         ↓         ↓         ↓
       └─────────┴─────────┴─────────┘
                       ↓
                    Tổng hợp
                       ↓
             Đồng thuận / khác biệt
                       ↓
                    Quyết định
```

## Ba loại kết quả

### Đồng thuận

Nhiều tác nhân cùng đưa ra một ý tưởng.

### Khác biệt

Các tác nhân đưa ra những hướng khác nhau.

### Ngoại lệ

Một tác nhân đưa ra ý tưởng rất khác biệt.

Ý tưởng ngoại lệ có thể:

- cực kỳ sáng tạo;
- hoặc hoàn toàn sai.

Vì vậy:

> **Ý tưởng ngoại lệ cần được kiểm chứng.**

### Bài học

> **Nhiều tác nhân giúp mở rộng không gian tìm kiếm giải pháp.**

---

# 17. PHÒNG TRANH LUẬN CỦA CÁC TÁC NHÂN

Một phương pháp khác là cho mỗi tác nhân một vai trò:

```text
Người nhìn hệ thống
Người thực tế
Người phản biện
Người tìm lỗi biên
Người đại diện người dùng
```

Sau đó cho chúng tranh luận:

```text
Tác nhân A
    ↕
Tác nhân B
    ↕
Tác nhân C
    ↕
Tác nhân D
    ↕
Tác nhân E
    ↓
Hội tụ
```

Mục tiêu:

- thách thức giả định;
- phát hiện điểm mù;
- tìm trường hợp đặc biệt;
- nhìn vấn đề từ nhiều góc.

## Đồng thuận và tranh luận khác nhau thế nào?

### Đồng thuận

```text
Suy nghĩ độc lập
      ↓
Tổng hợp
```

### Tranh luận

```text
Tác nhân A
   ↕
Tác nhân B
   ↕
Tác nhân C
   ↕
Phản biện
   ↓
Tinh chỉnh
```

---

# 18. VÒNG KIỂM TRA ĐỘC LẬP

Một tác nhân tự làm rồi tự kiểm tra dễ bị ảnh hưởng bởi chính cách giải quyết của mình.

Có thể dùng:

```text
TÁC NHÂN THỰC HIỆN
        ↓
      KẾT QUẢ
        ↓
TÁC NHÂN ĐÁNH GIÁ MỚI
        ↓
    ┌───┴────┐
    ↓        ↓
  ĐẠT      CÓ LỖI
    ↓        ↓
  Kiểm tra  Tác nhân sửa
               ↓
             Kiểm tra
               ↓
           Đã xác nhận
```

Tác nhân đánh giá kiểm tra:

- tính đúng;
- trường hợp đặc biệt;
- lỗi;
- bảo mật;
- yêu cầu;
- khả năng đơn giản hóa.

### Nguyên tắc

> **Người thực hiện và người kiểm tra nên có góc nhìn càng độc lập càng tốt.**

---

# 19. MÔ HÌNH NHIỀU CHUYÊN GIA

Các kỹ thuật:

- nhiều tác nhân;
- đồng thuận;
- tranh luận;
- kiểm tra độc lập;
- suy luận song song;

đều dựa trên một ý tưởng:

> **Nhiều quá trình suy luận độc lập có thể tạo ra kết quả tốt hơn nếu được tổng hợp đúng cách.**

```text
                 Vấn đề
                    ↓
       ┌────────────┼────────────┐
       ↓            ↓            ↓
    Chuyên gia A  Chuyên gia B  Chuyên gia C
       ↓            ↓            ↓
       └────────────┼────────────┘
                    ↓
                 Tổng hợp
                    ↓
                  Kết quả
```

---

# 20. QUẢN LÝ NGỮ CẢNH

Ngữ cảnh không chỉ là cuộc trò chuyện.

Nó có thể gồm:

```text
Hướng dẫn hệ thống
Quy tắc
Bộ nhớ
Kỹ năng
Công cụ
Kết nối hệ thống
Tệp
Lịch sử trao đổi
Kết quả công cụ
Nghiên cứu
Dữ liệu đa phương thức
```

Tất cả đều sử dụng đơn vị xử lý của mô hình.

### Điều quan trọng

Một phiên làm việc có thể đã sử dụng đáng kể ngữ cảnh **trước khi người dùng gửi nhiều câu hỏi**, bởi vì quy tắc, bộ nhớ, kỹ năng và mô tả công cụ đã chiếm chỗ.

### Bài học

> **Ngữ cảnh là tài nguyên hữu hạn.**

---

# 21. KỸ THUẬT TẢNG BĂNG

Không nên:

```text
Nạp toàn bộ mã nguồn
Nạp toàn bộ tệp
Nạp toàn bộ lịch sử
Nạp toàn bộ dữ liệu mạng
Nạp toàn bộ kỹ năng
```

Thay vào đó:

```text
                NGỮ CẢNH
                   │
       ┌───────────┼───────────┐
       ↓           ↓           ↓
     Quy tắc     Bộ nhớ    Công việc hiện tại
                                  │
                                  ↓
                         Truy xuất khi cần
                                  │
                    ┌─────────────┼────────────┐
                    ↓             ↓            ↓
                  Đọc           Tìm kiếm      Mạng
```

Ý tưởng:

> **Không cần đưa toàn bộ thông tin vào ngữ cảnh. Chỉ cần cho tác nhân khả năng lấy thông tin khi cần.**

Đó là tư duy “tảng băng”: phần nhìn thấy rất nhỏ, phần còn lại nằm phía dưới và chỉ được truy cập khi cần.

---

# 22. NẠP THÔNG TIN CÓ CHIẾN LƯỢC

## Cách cũ

```text
Toàn bộ mã nguồn
      ↓
Ngữ cảnh
      ↓
Mô hình
```

## Cách tốt hơn

```text
Nhiệm vụ
   ↓
Tìm kiếm
   ↓
Tệp liên quan
   ↓
Đoạn liên quan
   ↓
Đọc
   ↓
Suy luận
```

Ví dụ:

```text
10.000 đơn vị xử lý
       ↓
Tìm kiếm
       ↓
2.000 đơn vị liên quan
```

→ ít dữ liệu hơn.

→ ít nhiễu hơn.

→ tập trung hơn.

### Nguyên tắc

> **Không phải thông tin nào có sẵn cũng cần được nạp vào đầu tác nhân.**

---

# 23. HIỂN THỊ THÔNG TIN THEO NHU CẦU

Một kỹ năng có thể rất dài.

Không cần nạp toàn bộ ngay từ đầu.

Có thể chia:

```text
Kỹ năng
│
├── Tên
├── Mô tả
│
└── Hướng dẫn đầy đủ
```

Ban đầu chỉ cần:

```text
Tên + Mô tả
```

Khi tác nhân xác định:

> “Tôi cần kỹ năng này.”

thì mới đọc hướng dẫn đầy đủ.

### Tư duy

> **Chỉ mở rộng thông tin khi có nhu cầu thực sự.**

Điều này giúp tiết kiệm ngữ cảnh.

---

# 24. NÉN NGỮ CẢNH

Khi ngữ cảnh gần đầy:

```text
Ngữ cảnh rất lớn
      ↓
Nén
      ↓
Ngữ cảnh cô đọng
```

Mục đích:

- giảm kích thước;
- tăng mật độ thông tin;
- tiếp tục phiên làm việc.

Nhưng có đánh đổi:

```text
Nén
 ↓
Ít đơn vị xử lý hơn
 ↓
Có thể mất một số chi tiết
```

Một số:

- kết quả công cụ;
- lịch sử;
- chi tiết nhỏ;

có thể bị loại bỏ.

### Nguyên tắc

> **Nén ngữ cảnh giúp kéo dài phiên làm việc nhưng không nên được xem là giải pháp duy nhất để bảo vệ thông tin quan trọng.**

---

# 25. TỐI ƯU SỐ LƯỢNG ĐƠN VỊ XỬ LÝ

Có hai lý do phải quản lý ngữ cảnh.

## Chất lượng

```text
Ngữ cảnh tăng
   ↓
Nhiễu tăng
   ↓
Mật độ thông tin hữu ích giảm
   ↓
Chất lượng có thể giảm
```

## Chi phí

```text
Ngữ cảnh tăng
   ↓
Số đơn vị xử lý tăng
   ↓
Chi phí tăng
```

Vì vậy mục tiêu không phải:

> **Dùng càng nhiều ngữ cảnh càng tốt.**

Mà là:

> **Dùng lượng ngữ cảnh hữu ích tối thiểu.**

---

# 26. TỐI ƯU CHẤT LƯỢNG VÀ CHI PHÍ

Không phải lúc nào:

> Mô hình mạnh nhất = giải pháp tốt nhất.

Có thể hình dung:

```text
                  CHẤT LƯỢNG
                      ↑
                      │
                ●     │
                      │
          ●           │
                      │
      ●               │
                      └──────────→ CHI PHÍ
```

Mục tiêu là tìm điểm cân bằng:

> **Chất lượng đủ cao với chi phí hợp lý.**

Nếu một nhiệm vụ đơn giản chỉ cần mô hình rẻ, dùng mô hình cao cấp hơn có thể không đem lại đủ giá trị bổ sung.

---

# 27. PHÂN TẦNG MÔ HÌNH

Một hệ thống hiệu quả có thể chia:

```text
Nhiệm vụ đơn giản
        ↓
Mô hình nhanh và rẻ

Nhiệm vụ trung bình
        ↓
Mô hình trung cấp

Nhiệm vụ khó / quan trọng
        ↓
Mô hình mạnh
```

Ví dụ:

| Loại nhiệm vụ | Mức mô hình |
|---|---|
| Phân loại đơn giản | Rẻ / nhanh |
| Trích xuất dữ liệu | Rẻ / nhanh |
| Thu thập dữ liệu lớn | Rẻ |
| Nghiên cứu | Trung cấp |
| Làm giàu dữ liệu | Trung cấp |
| Lập trình thông thường | Trung cấp / mạnh |
| Kiến trúc hệ thống | Mạnh |
| Suy luận phức tạp | Mạnh |
| Điều phối | Mạnh |
| Kiểm tra quan trọng | Mạnh |

### Nguyên tắc

> **Dùng trí thông minh đắt tiền ở nơi nó tạo ra nhiều giá trị nhất.**

---

# 28. CHIẾN LƯỢC 60 / 30 / 10

Khóa học đưa ra một cách phân bổ mang tính gợi ý:

```text
60%
Mô hình rẻ
↓
Công việc đơn giản / lặp lại

30%
Mô hình trung cấp
↓
Nghiên cứu / suy luận thông thường

10%
Mô hình mạnh
↓
Suy luận quan trọng / điều phối / quyết định khó
```

Ví dụ toán học trong bài:

```text
100 triệu đơn vị × 5 đô la
= 500 đô la
```

Nếu chia:

```text
60 triệu × 1 đô la = 60 đô la
30 triệu × 3 đô la = 90 đô la
10 triệu × 5 đô la = 50 đô la

Tổng = 200 đô la
```

→ Trong ví dụ này, chi phí giảm khoảng 60%.

**Chú thích:** Đây là ví dụ minh họa và tỷ lệ gợi ý của tác giả. Không nên xem đây là quy tắc cứng hoặc bảng giá hiện hành.

---

# 29. XỬ LÝ THEO LÔ

Nếu một nhiệm vụ không cần kết quả ngay:

```text
Xử lý tức thời
→ trả kết quả ngay
```

hoặc:

```text
Xử lý theo lô
→ gom nhiều yêu cầu
→ xử lý sau
→ có thể giảm chi phí
```

Phù hợp với:

- phân loại hàng loạt;
- trích xuất hàng loạt;
- làm giàu dữ liệu;
- phân tích dữ liệu;
- các công việc ngoại tuyến.

### Nguyên tắc

> **Không phải mọi yêu cầu đều cần được xử lý ngay lập tức.**

---

# 30. KIẾN TRÚC TÁC NHÂN HOÀN CHỈNH

Khi ghép tất cả kiến thức:

```text
                         NGƯỜI DÙNG
                              │
                              ↓
                     HỎI NGƯỢC LÀM RÕ
                              │
                              ↓
                       HỢP ĐỒNG YÊU CẦU
                              │
                              ↓
                         ĐIỀU PHỐI
                              │
                       ┌──────┴──────┐
                       ↓             ↓
                    LẬP KẾ HOẠCH   ĐỊNH TUYẾN
                                     │
                ┌────────────────────┼────────────────────┐
                ↓                    ↓                    ↓
             Tác nhân A           Tác nhân B           Tác nhân C
             Nghiên cứu           Lập trình             Kiểm tra
                ↓                    ↓                    ↓
                └────────────────────┼────────────────────┘
                                     ↓
                              CÔNG CỤ / KẾT NỐI
                                     ↓
                                  HÀNH ĐỘNG
                                     ↓
                               NHẬN KẾT QUẢ
                                     ↓
                           KIỂM TRA / TRANH LUẬN
                                     ↓
                                  SỬA LỖI
                                     ↓
                                  KIỂM THỬ
                                     ↓
                            TIÊU CHÍ HOÀN THÀNH
                                     ↓
                              KẾT QUẢ ĐÃ XÁC NHẬN
```

Bao quanh toàn bộ hệ thống là:

```text
┌────────────────────────────────────────────────────┐
│                 QUẢN LÝ NGỮ CẢNH                   │
│                                                    │
│ Quy tắc                                             │
│ Bộ nhớ                                              │
│ Kỹ năng                                             │
│ Truy xuất có chọn lọc                               │
│ Nạp thông tin theo nhu cầu                         │
│ Nén ngữ cảnh                                        │
│ Tối ưu đơn vị xử lý                                │
└────────────────────────────────────────────────────┘
```

---

# 31. BẢNG QUYẾT ĐỊNH THỰC CHIẾN

```text
Công việc?
 │
 ├─ Đơn giản?
 │    └─ Dùng mô hình rẻ / nhanh
 │
 ├─ Lặp lại?
 │    └─ Tạo kỹ năng
 │
 ├─ Cần tác động ra bên ngoài?
 │    └─ Dùng công cụ / lớp kết nối
 │
 ├─ Có nhiều phần độc lập?
 │    └─ Dùng nhiều tác nhân song song
 │
 ├─ Có nhiều câu trả lời hợp lý?
 │    └─ Dùng đồng thuận
 │
 ├─ Cần phản biện?
 │    └─ Dùng tranh luận
 │
 ├─ Kết quả quan trọng?
 │    └─ Dùng tác nhân kiểm tra độc lập
 │
 ├─ Yêu cầu chưa rõ?
 │    └─ Hỏi ngược
 │
 ├─ Ngữ cảnh quá lớn?
 │    └─ Truy xuất có chiến lược
 │
 ├─ Phiên làm việc quá dài?
 │    └─ Nén và cô đọng
 │
 └─ Chi phí cao?
      └─ Phân tầng mô hình / xử lý theo lô
```

---

# 32. MƯỜI NGUYÊN TẮC VÀNG

## 1. Ngữ cảnh trước yêu cầu

Trước khi nghĩ:

> “Viết yêu cầu thế nào cho hay?”

hãy hỏi:

> **“Tác nhân cần biết những gì?”**

---

## 2. Tiêu chí hoàn thành trước khi thực hiện

Tác nhân phải biết:

> **Thế nào là xong?**

---

## 3. Hỏi ngược trước công việc phức tạp

Nếu yêu cầu chưa rõ:

> **Hỏi trước, làm sau.**

---

## 4. Yêu cầu lặp lại → kỹ năng

Nếu phải giải thích cùng một quy trình nhiều lần:

> **Biến quy trình thành kỹ năng.**

---

## 5. Lỗi lặp lại → quy tắc

Nếu tác nhân thường xuyên mắc cùng một lỗi:

> **Biến lỗi thành quy tắc trong bộ nhớ.**

---

## 6. Công việc độc lập → xử lý song song

Nếu nhiều phần có thể làm đồng thời:

> **Đừng xử lý tuần tự.**

---

## 7. Người thực hiện khác người kiểm tra

> **Tác nhân làm và tác nhân kiểm tra nên độc lập.**

---

## 8. Dùng ngữ cảnh tối thiểu nhưng đủ dùng

Không đưa tất cả mọi thứ vào ngữ cảnh chỉ vì có thể.

> **Chỉ nạp thứ cần thiết.**

---

## 9. Đúng mô hình cho đúng nhiệm vụ

Không dùng mô hình mạnh nhất cho mọi công việc.

---

## 10. Tối ưu giá trị, không chỉ tối ưu trí thông minh

Mục tiêu không phải:

> **Tác nhân thông minh nhất.**

Mà là:

> **Hệ thống tạo ra giá trị tốt nhất với chất lượng, tốc độ, độ tin cậy và chi phí phù hợp.**

---

# 33. CÔNG THỨC TƯ DUY VỀ TÁC NHÂN

Có thể cô đọng nền tảng thành:

```text
TÁC NHÂN TỐT
=
MÔ HÌNH TỐT
×
NGỮ CẢNH TỐT
×
CÔNG CỤ TỐT
×
QUY TRÌNH TỐT
×
KIỂM TRA TỐT
```

Để mở rộng:

```text
TÁC NHÂN
+
KỸ NĂNG
+
CÔNG CỤ
+
ĐIỀU PHỐI
+
ĐỊNH TUYẾN
+
NHIỀU TÁC NHÂN
+
KIỂM TRA
```

Để tạo thành hệ thống có hiệu quả kinh tế:

```text
HỆ THỐNG TÁC NHÂN
+
QUẢN LÝ NGỮ CẢNH
+
PHÂN TẦNG MÔ HÌNH
+
XỬ LÝ SONG SONG
+
TỐI ƯU CHI PHÍ
+
TỰ ĐỘNG HÓA
```

---

# 34. TỪ “DÙNG AI” ĐẾN “XÂY HỆ THỐNG AI”

## Cấp độ 1 — Người sử dụng AI

```text
Tôi hỏi AI
    ↓
AI trả lời
```

## Cấp độ 2 — Người vận hành AI

```text
Tôi giao việc
    ↓
AI dùng công cụ
    ↓
AI thực hiện
```

## Cấp độ 3 — Người xây dựng quy trình

```text
Quy tắc
+
Bộ nhớ
+
Kỹ năng
+
Công cụ
```

## Cấp độ 4 — Người xây dựng tác nhân

```text
Vòng lặp
+
Ngữ cảnh
+
Công cụ
+
Kiểm tra
```

## Cấp độ 5 — Người xây dựng hệ thống nhiều tác nhân

```text
Điều phối
+
Định tuyến
+
Song song
+
Đồng thuận
+
Tranh luận
+
Kiểm tra
```

## Cấp độ 6 — Kiến trúc sư hệ thống tác nhân

Tối ưu đồng thời:

```text
Chất lượng
+
Độ tin cậy
+
Tốc độ
+
Ngữ cảnh
+
Chi phí
```

---

# 35. MÔ HÌNH TƯ DUY CUỐI CÙNG

Nếu chỉ nhớ một sơ đồ:

```text
                         TÁC NHÂN
                            │
          ┌─────────────────┼─────────────────┐
          ↓                 ↓                 ↓
        BỘ NÃO            BỘ NHỚ           ĐÔI TAY
     Mô hình ngôn ngữ   Quy tắc / Kỹ năng   Công cụ / Kết nối
          │                 │                 │
          └─────────────────┼─────────────────┘
                            ↓
                       VÒNG LẶP
                            │
                  Quan sát → Suy nghĩ → Hành động
                            │
                            ↓
                       LÀM CÔNG VIỆC
                            │
                            ↓
                     NHIỀU TÁC NHÂN
                            │
             ┌──────────────┼──────────────┐
             ↓              ↓              ↓
          Song song       Đồng thuận     Tranh luận
             │              │              │
             └──────────────┼──────────────┘
                            ↓
                       KIỂM TRA ĐỘC LẬP
                            ↓
                           SỬA
                            ↓
                         KIỂM THỬ
                            ↓
                   KẾT QUẢ ĐÃ XÁC NHẬN
                            │
                            ↓
                 TỐI ƯU NGỮ CẢNH / CHI PHÍ
```

---

# 36. BÀI HỌC LỚN NHẤT CỦA TOÀN KHÓA

Trước khi học:

```text
AI
 ↓
Yêu cầu
 ↓
Câu trả lời
```

Sau khi học:

```text
                    MỤC TIÊU KINH DOANH
                            ↓
                       XÁC ĐỊNH VIỆC
                            ↓
                      HỎI NGƯỢC
                            ↓
                    HỢP ĐỒNG YÊU CẦU
                            ↓
                         ĐIỀU PHỐI
                            ↓
                        ĐỊNH TUYẾN
                            ↓
             ┌──────────────┼──────────────┐
             ↓              ↓              ↓
          Tác nhân A     Tác nhân B     Tác nhân C
             ↓              ↓              ↓
             └──────────────┼──────────────┘
                            ↓
                      CÔNG CỤ / KẾT NỐI
                            ↓
                         HÀNH ĐỘNG
                            ↓
                      NHẬN KẾT QUẢ
                            ↓
                  KIỂM TRA / TRANH LUẬN
                            ↓
                           SỬA
                            ↓
                         KIỂM THỬ
                            ↓
                   TIÊU CHÍ HOÀN THÀNH
                            ↓
                    KẾT QUẢ ĐÃ XÁC NHẬN
                            ↓
                     BÀI HỌC / BỘ NHỚ
                            ↓
                         KỸ NĂNG
                            ↓
                    HỆ THỐNG TÁI SỬ DỤNG
```

Tất cả được tối ưu bằng:

```text
QUẢN LÝ NGỮ CẢNH
+
PHÂN TẦNG MÔ HÌNH
+
XỬ LÝ SONG SONG
+
TỐI ƯU ĐƠN VỊ XỬ LÝ
+
TỐI ƯU CHI PHÍ
```

---

# 37. KẾT LUẬN

Chuyển đổi tư duy quan trọng nhất là:

> **Đừng chỉ học cách sử dụng trí tuệ nhân tạo. Hãy học cách thiết kế hệ thống trí tuệ nhân tạo.**

Một tác nhân tốt không chỉ:

- biết suy nghĩ;
- biết trả lời;

mà còn:

- biết quan sát;
- biết lập kế hoạch;
- biết sử dụng công cụ;
- biết hành động;
- biết lặp lại;
- biết phối hợp;
- biết tự kiểm tra;
- biết truy xuất thông tin khi cần;
- biết chọn mô hình phù hợp;
- biết quản lý ngữ cảnh;
- biết tối ưu chi phí.

## Câu chốt

> **Đừng chỉ hỏi: “Mô hình nào thông minh nhất?”**
>
> Hãy hỏi:
>
> **“Tôi có thể thiết kế hệ thống tác nhân nào để biến trí thông minh thành hành động, hành động thành quy trình, quy trình thành tự động hóa, và tự động hóa thành giá trị?”**

Đó chính là tư duy **làm chủ tác nhân trí tuệ nhân tạo**.
