# Chương 13 — Kinh tế mô hình: phân tầng, phân bổ, xử lý theo lô

> [← Chương 12](12-kinh-te-ngu-canh.md) | [Mục lục](README.md) | [Chương 14 →](14-automation-ngoai-codebase.md)

---

## 13.1. Thông minh nhất không phải lúc nào cũng tốt nhất

Phản xạ mặc định: chọn mô hình mạnh nhất cho mọi việc, vì "chất lượng cao hơn thì tốt hơn".

Phản xạ đó bỏ qua ba thứ:

1. **Nhiều việc không cần trí thông minh cao.** Phân loại một email vào ba nhóm, trích
   tên trường từ JSON, đổi định dạng ngày — mô hình rẻ làm chính xác như mô hình đắt.
2. **Ngân sách là hữu hạn.** Tiền tiêu ở việc dễ là tiền không còn cho việc khó.
3. **Mô hình mạnh thường chậm hơn.** Ở việc chạy hàng loạt, chậm nhân với số lượng
   thành một vấn đề riêng.

Mục tiêu không phải:

> ~~Hệ thống thông minh nhất.~~

Mà là:

> **Hệ thống tạo ra giá trị tốt nhất, cân bằng chất lượng, tốc độ, độ tin cậy và chi phí.**

```
        CHẤT LƯỢNG
            ↑
            │      ● mô hình mạnh
            │
            │   ● mô hình trung cấp
            │
            │ ● mô hình rẻ
            └──────────────────→ CHI PHÍ
```

Đường cong này có đoạn dốc (thêm tiền được thêm nhiều chất lượng) và đoạn phẳng (thêm
tiền gần như không thêm gì). Việc của bạn là biết mỗi loại công việc nằm ở đoạn nào.

---

## 13.2. Phân tầng mô hình

Chia công việc theo mức năng lực thực sự cần:

```
Việc đơn giản, lặp lại      →  Mô hình rẻ, nhanh
Việc trung bình             →  Mô hình trung cấp
Việc khó hoặc quan trọng    →  Mô hình mạnh nhất
```

Bảng tham chiếu theo **loại việc**, không theo tên sản phẩm:

| Loại công việc | Mức cần |
|---|---|
| Phân loại đơn giản | Rẻ / nhanh |
| Trích xuất dữ liệu có cấu trúc | Rẻ / nhanh |
| Thu thập dữ liệu số lượng lớn | Rẻ |
| Nghiên cứu, tổng hợp | Trung cấp |
| Làm giàu dữ liệu | Trung cấp |
| Lập trình thông thường | Trung cấp / mạnh |
| Thiết kế kiến trúc | Mạnh |
| Suy luận nhiều bước | Mạnh |
| Điều phối nhiều tác nhân | Mạnh |
| Kiểm tra việc quan trọng | Mạnh |

> **Dùng trí thông minh đắt tiền ở nơi nó tạo ra nhiều giá trị nhất.**

Hai dòng cuối đáng chú ý: **điều phối và kiểm tra nên dùng mô hình mạnh** dù chúng
không trực tiếp tạo ra sản phẩm. Lý do: một quyết định điều phối sai làm hỏng toàn bộ
các bước sau, và một lượt kiểm tra bỏ sót lỗi khiến mọi khoản tiết kiệm phía trước
thành vô nghĩa.

---

## 13.3. Ví dụ thật — phân tầng trong PM-AGENT

### Cảnh báo mức tối thiểu ở đầu skill

Nhiều skill của PM-AGENT mở đầu bằng:

```
Model tối thiểu: Sonnet. Không chạy skill này bằng Haiku — thực nghiệm 2026-08-17
(cùng token, cùng task, chỉ đổi model) cho kết quả phân tích/thao tác kém rõ rệt.
Đây là cảnh báo, không phải chặn cứng: bạn vẫn chạy được, nhưng nếu kết quả kỳ lạ
thì kiểm model đang dùng trước khi đi tìm bug ở chỗ khác.
```

Đây là phân tầng được **đo bằng thực nghiệm có kiểm soát biến** (cùng token, cùng
task, chỉ đổi mô hình), không phải phỏng đoán. Và nó ghi ngày, nên khi mô hình thay
đổi thì biết cần đo lại.

### Cái đắt chỉ chạy ở khâu cuối

Ghi chú thiết kế trong skill `auto-review`:

```
Codex tốn nhiều token → chỉ dùng làm "double-check cuối cùng".
Team chuyên gia Claude chạy song song = chi phí 0, tốc độ cao.
```

Mô hình chi phí:

```
5 chuyên gia quét rộng (rẻ, song song)
        ↓
     Tổng hợp
        ↓
1 lượt kiểm chéo bằng công cụ đắt (hẹp)
```

> **Cái rẻ chạy rộng, cái đắt chạy hẹp.**

### Dự phòng có phân tầng

AI Router định tuyến theo rule lấy từ DB và **tự thử rule ưu tiên kế tiếp khi provider
lỗi**. Đây là phân tầng ở chiều khác: không phải theo độ khó mà theo tình trạng sẵn sàng.

---

## 13.4. Chiến lược phân bổ 60 / 30 / 10

Một cách phân bổ mang tính gợi ý để bắt đầu:

```
60%  →  Mô hình rẻ       →  Việc đơn giản, lặp lại
30%  →  Mô hình trung    →  Nghiên cứu, suy luận thông thường
10%  →  Mô hình mạnh     →  Suy luận quan trọng, điều phối, quyết định khó
```

Minh hoạ bằng số (đơn giá giả định để thấy cơ chế):

```
Dùng một mức cho tất cả:
  100 triệu token × 5 đơn vị giá = 500

Phân tầng 60/30/10:
   60 triệu × 1 = 60
   30 triệu × 3 = 90
   10 triệu × 5 = 50
                ────
                 200      → giảm khoảng 60%
```

> **Lưu ý:** đây là ví dụ minh hoạ cơ chế và tỷ lệ gợi ý, **không phải bảng giá** và
> không phải quy tắc cứng. Tỷ lệ đúng cho bạn phụ thuộc loại việc — một đội làm nghiên
> cứu sẽ khác một đội làm dữ liệu hàng loạt. Hãy đo rồi điều chỉnh.

### Cách áp dụng cho công việc lập trình

Tỷ lệ trên sinh ra từ bối cảnh xử lý dữ liệu quy mô lớn. Với công việc lập trình hằng
ngày, phân bổ thực tế thường nghiêng về mức cao hơn — vì phần lớn thời gian là suy
luận trên mã nguồn, không phải xử lý hàng loạt.

Điều giữ nguyên giá trị là **nguyên tắc**, không phải con số: tách rõ ba nhóm việc và
đừng để nhóm thứ nhất tiêu tiền của nhóm thứ ba.

Ứng viên nhóm rẻ trong một dự án phần mềm:

- Đổi định dạng, sinh dữ liệu mẫu
- Trích thông tin từ log theo mẫu cố định
- Phân loại/gắn nhãn hàng loạt
- Tóm tắt từng file riêng lẻ trong một khảo sát rộng
- Kiểm tra cú pháp, lint, format

---

## 13.5. Xử lý theo lô

Không phải yêu cầu nào cũng cần trả lời ngay.

```
Xử lý tức thời        →  trả kết quả ngay, đắt hơn
Xử lý theo lô         →  gom nhiều yêu cầu, xử lý sau, rẻ hơn
```

Phù hợp với: phân loại hàng loạt, trích xuất hàng loạt, làm giàu dữ liệu, phân tích
định kỳ, mọi việc chạy ngoại tuyến.

> **Không phải mọi yêu cầu đều cần được xử lý ngay lập tức.**

### Ví dụ thật — hàng đợi trong PM-AGENT

PM-AGENT đã có sẵn hạ tầng cho mô hình này: **7 ARQ job** chạy nền trên hàng đợi Redis
(đồng bộ nguồn, gắn nhãn item, sinh báo cáo), cộng APScheduler cho việc định kỳ.

Điểm đáng chú ý về mặt kiến trúc: những việc này **vốn đã không cần chạy tức thời**.
Chúng chạy nền, không ai ngồi đợi. Đó chính là nhóm việc nên xét xử lý theo lô và dùng
mô hình rẻ — một quyết định kinh tế có sẵn chỗ để áp dụng.

---

## 13.6. Đo trước khi tối ưu

Mọi lời khuyên trong chương này đều vô dụng nếu không biết tiền đang đi đâu.

Tối thiểu cần đo ba thứ:

| Đo gì | Cách đo | Dùng để |
|---|---|---|
| Token mỗi phiên, theo hoạt động | Bảng tổng kết cuối phiên | Biết khâu nào ngốn nhất |
| Token mỗi loại việc | Ghi log theo `task_type` | Biết loại việc nào đáng hạ tầng mô hình |
| Tỷ lệ subagent trên tổng | Ghi lại token của subagent | Biết phối hợp có đang quá tay không |

PM-AGENT đã làm điều này ở hai chỗ: bảng token cuối mỗi phiên (rule `{prefix}-session-wrap`)
và bảng `ai_requests` ghi mọi lời gọi qua AI Router.

Nhìn lại bảng đo ở [mục 12.8](12-kinh-te-ngu-canh.md): một subagent chiếm 49% toàn
phiên. Nếu không đo, con số đó vô hình — và bạn sẽ không biết mình vừa tiêu nửa ngân
sách vào một quyết định.

---

## 13.7. Khi nào KHÔNG nên tiết kiệm

Tối ưu chi phí có thể đi quá đà. Bốn chỗ không nên tiết kiệm:

| Tình huống | Vì sao |
|---|---|
| **Kiểm tra việc quan trọng** | Bỏ sót một lỗi bảo mật đắt hơn mọi khoản tiết kiệm cộng lại |
| **Điều phối nhiều bước** | Quyết định sai ở đầu làm hỏng toàn bộ phía sau |
| **Việc chỉ làm một lần** | Tiết kiệm trên một lần chạy là tiết kiệm không đáng kể |
| **Khi đang gỡ lỗi khó** | Thời gian của bạn đắt hơn chênh lệch token |

Nguyên tắc chung: **tiết kiệm ở việc lặp lại nhiều, đừng tiết kiệm ở việc quyết định**.

Đây cũng là lý do câu chuyện "hạ mô hình cho rẻ" hay phản tác dụng: người ta hạ ở đúng
chỗ cần suy luận, rồi mất nhiều giờ đi tìm lỗi mà nguyên nhân là chính quyết định đó.
Cảnh báo *"kiểm model đang dùng trước khi đi tìm bug ở chỗ khác"* trong skill PM-AGENT
sinh ra từ tình huống này.

---

## 13.8. Bảng quyết định nhanh

```
Việc này lặp lại nhiều lần, mỗi lần đơn giản?
   → Mô hình rẻ + cân nhắc xử lý theo lô

Việc cần suy luận nhiều bước hoặc quyết định kiến trúc?
   → Mô hình mạnh, đừng tiếc

Việc là kiểm tra/nghiệm thu kết quả quan trọng?
   → Mô hình mạnh, và phải là tác nhân độc lập (ch.10)

Việc cần quét rộng rồi chốt hẹp?
   → Rẻ chạy rộng → đắt kiểm chéo phần cuối

Không rõ cần mức nào?
   → Chạy thử ở mức thấp trước; kém thì nâng, và GHI LẠI kết quả so sánh
```

Dòng cuối là cách duy nhất để bảng phân tầng của bạn không thành phỏng đoán: mỗi lần
phải nâng mức, ghi lại loại việc đó — sau vài lần bạn có bảng riêng, đo bằng thực tế.

---

## 13.9. Bẫy thường gặp

> **Bẫy 1 — Dùng mô hình mạnh nhất cho mọi thứ "cho chắc".**
> **Cách sửa:** phân tầng theo loại việc; bắt đầu thấp ở việc lặp lại.

> **Bẫy 2 — Hạ mô hình ở chỗ cần suy luận.**
> Tiết kiệm vài đồng, mất vài giờ gỡ lỗi. **Cách sửa:** không hạ ở khâu quyết định,
> điều phối, kiểm tra.

> **Bẫy 3 — Tối ưu mà không đo.**
> Đổi mô hình theo cảm giác, không biết có rẻ hơn thật không. **Cách sửa:** ghi log
> theo loại việc trước khi đổi.

> **Bẫy 4 — Áp cứng tỷ lệ 60/30/10.**
> Đó là gợi ý từ một bối cảnh cụ thể. **Cách sửa:** dùng nguyên tắc phân tầng, tự đo
> tỷ lệ của mình.

> **Bẫy 5 — Quên rằng mô hình thay đổi.**
> Bảng phân tầng viết một năm trước có thể đã sai. **Cách sửa:** ghi ngày đo, đo lại
> định kỳ — như cách PM-AGENT ghi *"thực nghiệm 2026-08-17"*.

---

## 13.10. Bài tập

**Bài 1 — Phân loại công việc của bạn.**
Liệt kê 10 loại việc bạn hay giao cho AI. Xếp vào ba nhóm rẻ/trung/mạnh. Có bao nhiêu
việc đang chạy ở mức cao hơn mức cần?

**Bài 2 — Thử hạ một bậc.**
Chọn một loại việc lặp lại ở nhóm "rẻ". Chạy thử ở mức thấp hơn 5 lần. Ghi lại: chất
lượng có tụt không, tụt ở điểm nào.

**Bài 3 — Tìm ứng viên xử lý theo lô.**
Trong dự án của bạn, việc nào đang chạy tức thời mà thực ra không ai ngồi đợi kết quả?

---

## Tóm tắt chương

- Mục tiêu là **giá trị tốt nhất**, không phải trí thông minh cao nhất.
- **Phân tầng theo loại việc**, không theo tên sản phẩm — tên đổi, nguyên tắc thì không.
- **Điều phối và kiểm tra nên dùng mức cao** dù không trực tiếp tạo sản phẩm.
- **Cái rẻ chạy rộng, cái đắt chạy hẹp.**
- 60/30/10 là **gợi ý để bắt đầu**, không phải quy tắc — hãy đo rồi chỉnh.
- **Xử lý theo lô** cho mọi việc không ai ngồi đợi.
- **Không đo thì đừng tối ưu.** Ghi token theo loại việc trước khi đổi gì.
- Tiết kiệm ở việc **lặp lại nhiều**, đừng tiết kiệm ở việc **quyết định**.
- Mọi kết luận về mô hình đều có hạn dùng — **ghi ngày đo, đo lại định kỳ**.

> Chương tiếp: [14 — Automation ngoài codebase](14-automation-ngoai-codebase.md)
