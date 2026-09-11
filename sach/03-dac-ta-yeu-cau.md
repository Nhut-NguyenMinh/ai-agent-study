# Chương 03 — Đặc tả yêu cầu: hợp đồng, tiêu chí hoàn thành, hỏi ngược

> [← Chương 02](02-prompting-va-context.md) | [Mục lục](README.md) | [Chương 04 →](04-context-engineering.md)

---

## 3.1. Vấn đề: "làm cho tôi một trang web thật đẹp"

Chương 02 nói về việc cung cấp đủ thông tin. Chương này nói về một thứ khác, sâu hơn:
**làm sao để cả bạn và AI cùng biết thế nào là xong**.

Xét yêu cầu:

> "Hãy làm một trang giới thiệu sản phẩm thật đẹp."

AI buộc phải tự đoán sáu điều:

- Đẹp là như thế nào?
- Dành cho ai xem?
- Gồm những phần nào?
- Trên điện thoại có phải hoạt động không?
- Được viết bao nhiêu dòng mã?
- Thế nào thì bị coi là làm hỏng?

Sáu phỏng đoán nhân với nhau ra một không gian kết quả rất rộng. Bạn nhận về một thứ
"chạy được" nhưng không phải thứ bạn cần, và bạn chỉ phát hiện ra điều đó **sau khi**
nó đã được xây xong.

Đây không phải lỗi diễn đạt. Đây là lỗi **thiếu hợp đồng**.

---

## 3.2. Bốn thành phần của một tiêu chí hoàn thành

Một yêu cầu thực thi được luôn trả lời đủ bốn câu:

```
MỤC TIÊU          — Đạt được cái gì?
+
RÀNG BUỘC         — Không được vi phạm điều gì?
+
ĐẦU RA            — Sản phẩm bàn giao gồm những gì?
+
ĐIỀU KIỆN THẤT BẠI — Thế nào thì coi là hỏng?
```

Viết lại yêu cầu ở mục 3.1:

```
MỤC TIÊU
Tạo trang giới thiệu sản phẩm cho khách hàng doanh nghiệp.

RÀNG BUỘC
- Hoạt động tốt trên điện thoại (375px trở lên)
- Cuộn trang mượt, không giật
- Không dùng giao diện tối
- Không quá 400 dòng mã

ĐẦU RA
- Phần mở đầu
- Danh sách tính năng
- Đánh giá của khách hàng
- Nút kêu gọi hành động

ĐIỀU KIỆN THẤT BẠI
- Giao diện chung chung, không phân biệt được với mẫu có sẵn
- Bố cục vỡ trên điện thoại
- Hiệu ứng giật khi cuộn
- Mã vượt 400 dòng
```

### Thành phần bị bỏ quên nhiều nhất: điều kiện thất bại

Ba mục đầu là thứ ai cũng nghĩ tới. Mục thứ tư gần như luôn bị bỏ, và nó là mục có
giá trị cao nhất.

Lý do: **mục tiêu mô tả vùng đúng, điều kiện thất bại cắt bỏ vùng sai**. Hai thứ đó
không đối xứng. "Giao diện đẹp" là một vùng rộng và mơ hồ; "giao diện chung chung,
không phân biệt được với mẫu có sẵn" là một ranh giới sắc, kiểm được.

Đây chính là nguyên tắc đã gặp ở chương 01: **điều cấm mạnh hơn điều khuyên**, vì nó
cắt bỏ hẳn một nhánh hành vi mặc định.

---

## 3.3. Hợp đồng yêu cầu

Ghép bốn thành phần lại thành một văn bản trước khi bắt tay — đó là **hợp đồng yêu cầu**.

```
Yêu cầu thô của người dùng
        ↓
   Hợp đồng yêu cầu
        ├── Mục tiêu
        ├── Ràng buộc
        ├── Đầu ra
        └── Điều kiện thất bại
        ↓
   AI thực hiện
        ↓
   Kiểm chứng đối chiếu hợp đồng
```

Nó đóng vai trò như một bản phạm vi công việc: hai bên ký trước, nghiệm thu theo đúng
cái đã ký.

### Lợi ích

| Lợi ích | Cơ chế |
|---|---|
| Giảm hiểu sai | Mỗi mục mơ hồ đều bị buộc phải viết ra thành câu cụ thể |
| Giảm phỏng đoán | AI không phải tự điền vào chỗ trống |
| Dễ kiểm chứng | Nghiệm thu là đối chiếu từng dòng, không phải cảm giác |
| Dễ tái sử dụng | Hợp đồng của việc tương tự chỉ cần sửa vài dòng |
| Tạo điểm dừng | "Xong" có định nghĩa, không kéo dài vô hạn |

> **Yêu cầu tốt không nhất thiết phải dài. Yêu cầu tốt phải rõ.**

---

## 3.4. Ví dụ thật — tài liệu test chính là hợp đồng yêu cầu

PM-AGENT không gọi nó là "hợp đồng", nhưng quy trình `test-doc-first`
(`.claude/rules/{prefix}-test-first.md`) thực hiện **đúng ý tưởng đó**, chỉ là ở dạng chặt
chẽ hơn.

Đối chiếu:

| Thành phần hợp đồng | Hiện thân trong PM-AGENT |
|---|---|
| Mục tiêu | Tiêu chí nghiệm thu (AC-*) trong `01-backend-spec.md` |
| Ràng buộc | Business rules (BR-*) + ngưỡng coverage + giới hạn commit |
| Đầu ra | Danh sách file cần tạo/sửa trong `06-commit-plan.md` |
| Điều kiện thất bại | **Negative test case** — mỗi AC bắt buộc có ít nhất 1 |

Dòng cuối là điểm đáng chú ý nhất. Luật ghi rõ:

```
Mỗi AC-* trong backend spec → ít nhất 1 happy path + 1 negative case
```

**Negative case chính là điều kiện thất bại được viết dưới dạng chạy được.** Thay vì
mô tả bằng lời "không được cho qua khi thiếu tiêu đề", nó thành một test case cụ thể
có dữ liệu vào và kết quả mong đợi.

Và quan trọng nhất: hợp đồng này **được người duyệt trước khi code**.

```
test-doc-first → [NGƯỜI DUYỆT] → implement → feature-verify → [NGƯỜI XÁC NHẬN ĐÓNG]
```

Người duyệt không cần biết lập trình. Họ đọc câu *"Khi người dùng bấm Lưu mà chưa nhập
tiêu đề thì hiện lỗi 'Tiêu đề bắt buộc' ngay dưới ô nhập"* và biết ngay đúng hay sai.

---

## 3.5. Hỏi ngược — để AI làm rõ trước khi làm

Hợp đồng tốt đòi hỏi thông tin mà người giao việc nhiều khi **chưa nghĩ tới**. Cách
lấy thông tin đó ra không phải bắt họ viết nhiều hơn, mà là **để AI hỏi ngược**.

```
Yêu cầu thô
   ↓
AI hỏi ngược       ← bước hay bị bỏ
   ↓
Người trả lời
   ↓
Hợp đồng yêu cầu
   ↓
Thực hiện
```

### Tám câu hỏi ngược hiệu quả

```
1. Mục tiêu thực sự đằng sau yêu cầu này là gì?
2. Ai sẽ dùng kết quả, trong tình huống nào?
3. Điều gì quan trọng nhất — nếu phải hy sinh thì hy sinh cái gì?
4. Có phong cách/mẫu nào bạn muốn làm theo không?
5. Điều gì tuyệt đối không được làm?
6. Có giới hạn nào về thời gian, phạm vi, công nghệ?
7. Ưu tiên chất lượng hay tốc độ?
8. Thế nào thì bạn coi là thất bại?
```

Câu 3 và câu 8 hay lộ ra nhiều thông tin nhất. Câu 3 buộc người giao việc xếp thứ tự
ưu tiên — thứ họ thường chưa làm. Câu 8 lấy ra chính thành phần bị bỏ quên ở mục 3.2.

> **Trước khi cố trả lời thật hay, hãy cố hiểu thật đúng.**

### Khi nào cần hỏi ngược

| Tình huống | Có nên hỏi ngược |
|---|---|
| Việc lớn, nhiều bước, khó hoàn tác | **Có** — bắt buộc |
| Yêu cầu dùng từ định tính ("đẹp", "nhanh", "tốt hơn") | **Có** |
| Hai cách hiểu dẫn tới hai kết quả khác hẳn | **Có** |
| Việc nhỏ, có mặc định hợp lý | Không — cứ làm rồi báo giả định đã dùng |
| Người giao đã nêu đủ bốn thành phần | Không |

Hỏi ngược không có nghĩa hỏi mọi thứ. Hỏi những câu mà **câu trả lời làm thay đổi công
việc** — câu nào có mặc định hợp lý thì tự quyết và nói rõ giả định.

---

## 3.6. Ví dụ thật — lọc câu hỏi trước khi làm phiền người khác

PM-AGENT đi xa hơn một bước, và đây là chi tiết đáng học: hỏi ngược thì tốt, nhưng
**hỏi ai** cũng là một quyết định.

Skill `qa-filter` quy định:

```
Luôn hỏi người vận hành trong phiên hiện tại TRƯỚC.
Chỉ gửi câu hỏi lên hệ thống (cho PM/BrSE) khi người vận hành xác nhận
không trả lời được.

Mục đích: lọc bớt câu hỏi rác — không phải mọi thắc mắc của AI
đều cần làm phiền PM.
```

Và skill `get-task` bổ sung một luật chống lãng phí khác:

```
Yêu cầu chưa rõ → phải hỏi trước, không suy đoán.
Nhưng dùng {prefix}_list_qa kiểm câu hỏi đã có TRƯỚC
— tránh hỏi lại câu người khác đã trả lời.
```

Ba tầng lọc: **tự tra cứu → hỏi người ngồi cùng → mới hỏi lên trên**. Không có ba tầng
này, cơ chế hỏi ngược sẽ tự giết mình — người bị hỏi quá nhiều sẽ ngừng trả lời.

---

## 3.7. Hỏi ngược ở chiều ngược lại: khi bạn là người bị hỏi

Một kỹ thuật ít người dùng: yêu cầu AI **tự tạo hợp đồng rồi đưa bạn duyệt**, thay vì
bạn phải viết hợp đồng.

```
Tôi muốn <mô tả thô>.

Trước khi làm, hãy viết cho tôi một hợp đồng yêu cầu gồm 4 mục:
mục tiêu, ràng buộc, đầu ra, điều kiện thất bại.
Chỗ nào bạn phải suy đoán thì đánh dấu rõ và nêu câu hỏi.
Chưa sửa file nào.
```

Cách này đảo ngược gánh nặng: AI làm bản nháp, bạn chỉ sửa. Và phần **"chỗ nào phải
suy đoán"** cho bạn thấy chính xác những chỗ yêu cầu còn mơ hồ — thường là những chỗ
bạn cũng chưa nghĩ tới.

---

## 3.8. Bẫy thường gặp

> **Bẫy 1 — Hợp đồng chỉ có mục tiêu.**
> Ba mục còn lại bị bỏ vì "hiển nhiên". Không có gì hiển nhiên với người chưa biết dự
> án. **Cách sửa:** bắt buộc điền điều kiện thất bại, kể cả khi chỉ một dòng.

> **Bẫy 2 — Ràng buộc viết bằng từ định tính.**
> "Phải nhanh", "phải gọn". **Cách sửa:** đổi thành số đo được — "dưới 400 dòng",
> "phản hồi dưới 300ms".

> **Bẫy 3 — Hỏi ngược thành thẩm vấn.**
> AI hỏi 15 câu cho một việc sửa nhãn nút. **Cách sửa:** chỉ hỏi câu mà câu trả lời
> làm đổi công việc; còn lại tự quyết và nêu giả định.

> **Bẫy 4 — Hợp đồng ký xong rồi sửa ngầm.**
> Giữa chừng phát hiện cần thêm việc và tự làm luôn. **Cách sửa:** dừng, cập nhật hợp
> đồng, báo, rồi mới tiếp.

> **Bẫy 5 — Nghiệm thu không đối chiếu hợp đồng.**
> Có hợp đồng nhưng lúc nhận việc lại đánh giá bằng cảm giác. **Cách sửa:** nghiệm thu
> là đi từng dòng của mục Đầu ra và Điều kiện thất bại.

---

## 3.9. Bài tập

**Bài 1 — Viết hợp đồng cho việc sắp làm.**
Lấy việc tiếp theo trong danh sách của bạn. Viết đủ bốn mục. Đo thời gian: thường mất
5–10 phút, và tiết kiệm nhiều hơn thế.

**Bài 2 — Để AI viết hợp đồng.**
Dùng mẫu ở mục 3.7 cho cùng việc đó. So sánh với bản bạn tự viết — chỗ nào AI hỏi mà
bạn chưa nghĩ tới?

**Bài 3 — Tìm điều kiện thất bại đã xảy ra.**
Nhớ lại một lần kết quả "đúng yêu cầu nhưng không dùng được". Viết ra điều kiện thất
bại mà lẽ ra phải nêu từ đầu. Thêm nó vào mẫu hợp đồng của bạn.

---

## Tóm tắt chương

- Yêu cầu mơ hồ không phải lỗi diễn đạt — đó là **thiếu hợp đồng**.
- Tiêu chí hoàn thành có bốn thành phần: **mục tiêu, ràng buộc, đầu ra, điều kiện thất bại**.
- **Điều kiện thất bại** bị bỏ quên nhiều nhất và có giá trị cao nhất: mục tiêu mô tả
  vùng đúng, điều kiện thất bại cắt bỏ vùng sai.
- Hợp đồng phải được **duyệt trước khi làm** — và người duyệt không cần biết lập trình.
- **Negative test case chính là điều kiện thất bại ở dạng chạy được.**
- **Hỏi ngược trước việc phức tạp**, nhưng chỉ hỏi câu mà câu trả lời làm đổi công việc.
- Lọc ba tầng: tự tra cứu → hỏi người ngồi cùng → mới hỏi lên trên.

> Chương tiếp: [04 — Context Engineering](04-context-engineering.md) — đưa những gì
> vừa thống nhất vào file trong repo, để không phải thoả thuận lại ở phiên sau.
