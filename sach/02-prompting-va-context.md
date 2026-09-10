# Chương 02 — Prompting & thu thập ngữ cảnh

> [← Chương 01](01-tu-duy-nen-tang.md) | [Mục lục](README.md) | [Chương 03 →](03-context-engineering.md)

---

## 2.1. Prompt Engineering thật ra là gì

Hiểu sai phổ biến:

> "Prompt Engineering là học thuộc những câu lệnh ma thuật."

Hiểu đúng:

> **Prompt Engineering là kỹ năng cung cấp đủ thông tin để AI có cơ sở tạo ra kết quả đúng.**

Không có câu thần chú nào. Chỉ có: AI biết đủ hay không biết đủ.

Một yêu cầu đủ thông tin trả lời được sáu câu:

| Thành phần | Câu hỏi nó trả lời |
|---|---|
| Mục tiêu | Kết quả cuối cùng cần đạt là gì? |
| Bối cảnh | Việc này nằm trong hoàn cảnh nào? |
| Dữ liệu | AI được nhìn thấy những gì? |
| Yêu cầu | Ràng buộc, giới hạn, điều cấm là gì? |
| Định dạng | Kết quả trình bày ra sao? |
| Tiêu chí | Thế nào là làm đúng? |

Thiếu câu nào thì AI tự điền câu đó bằng phỏng đoán. Và phỏng đoán của nó dựa trên
"dự án trung bình trên internet", không dựa trên dự án của bạn.

---

## 2.2. Từ tìm kiếm sang đối thoại

Thói quen cũ được rèn bởi Google:

```
Từ khoá → danh sách kết quả → tự đọc → tự tổng hợp
```

Thói quen này mang sang AI thì lãng phí. Cách làm việc với AI:

```
Vấn đề + bối cảnh + dữ liệu → AI phân tích → đối thoại → kiểm chứng → quyết định
```

**Ví dụ tổng quát — cùng một vấn đề, hai cách hỏi:**

Cách tìm kiếm:

```
lỗi 500 khi upload file
```

Cách đối thoại:

```
API POST /api/reports/upload trả 500 khi file > 5MB, file nhỏ thì bình thường.
Stack: FastAPI + nginx, chạy trong Docker.
Đây là log nginx: [dán log]
Đây là log app: [dán log]
Nginx config hiện tại: [dán đoạn liên quan]

Phân tích nguyên nhân có thể, chỉ rõ bằng chứng nào trong log ủng hộ mỗi giả thuyết,
và nói tôi cần kiểm tra thêm gì để loại trừ.
```

Cách thứ hai không "thông minh hơn". Nó chỉ **đưa dữ liệu vào** thay vì bắt AI đoán.

---

## 2.3. Khung T-R-F-C-R

Một khung dễ nhớ để kiểm nhanh xem yêu cầu đã đủ chưa.

### T — Task (việc cần làm)

Động từ cụ thể, phạm vi rõ.

```
Yếu:  "Xem hộ cái service này."
Tốt:  "Phân tích ReportService.generate() và chỉ ra những đường đi có thể ném exception
       chưa được bắt."
```

### R — Role (vai trò)

Đặt góc nhìn. Không phải trò đóng vai cho vui — nó chọn tiêu chuẩn đánh giá.

```
"Bạn là kỹ sư bảo mật đang review code trước khi merge."
```

Câu trên khiến AI ưu tiên đường đi của dữ liệu chưa tin cậy, thay vì góp ý về đặt tên biến.

### F — Format (định dạng)

```
"Trình bày dạng bảng Markdown: cột Vấn đề | Mức độ | File:dòng | Cách sửa."
"Trả về JSON đúng schema sau: {...}"
"Mỗi ý tối đa 3 câu."
```

Định dạng quan trọng gấp bội khi đầu ra sẽ được máy khác đọc — xem [chương 10](10-automation-ngoai-codebase.md).

### C — Context (bối cảnh)

Phần bị bỏ sót nhiều nhất, và cũng là phần quyết định nhất:

- Dự án làm gì, cho ai dùng?
- Ràng buộc kỹ thuật nào không được phá?
- Quyết định nào trong quá khứ đã chốt?
- Cái gì tuyệt đối không được đụng vào?

### R — Reference (tham chiếu)

Một ví dụ mẫu tiết kiệm được ba đoạn văn mô tả.

```
"Viết adapter mới theo đúng cấu trúc của app/modules/adapters/backlog_adapter.py."
```

Câu đó truyền đạt quy ước đặt tên, cách xử lý lỗi, chữ ký hàm và bố cục file — nhiều
hơn bất kỳ mô tả bằng lời nào.

> **Lưu ý về T-R-F-C-R:** đây là *checklist tự kiểm*, không phải biểu mẫu bắt buộc điền
> đủ năm mục mỗi lần hỏi. Việc nhỏ thì T + C là đủ. Dùng nó khi kết quả trả về lệch
> hướng, để tìm xem mình đã bỏ sót ô nào.

---

## 2.4. Vòng Evaluate → Iterate

Kỳ vọng sai:

```
Prompt → AI → kết quả hoàn hảo
```

Thực tế:

```
Prompt
   ↓
Kết quả lần 1
   ↓
ĐÁNH GIÁ ← đây mới là kỹ năng thật sự
   ↓
Phát hiện chỗ lệch
   ↓
Bổ sung ngữ cảnh / chỉnh yêu cầu
   ↓
Kết quả lần 2
   ↓
Lặp lại
```

> **Kết quả đầu tiên là điểm bắt đầu của cuộc đối thoại, không phải điểm kết thúc.**

Kỹ thuật đánh giá hiệu quả — thay vì nói "sai rồi, làm lại":

| Thay vì | Hãy nói |
|---|---|
| "Sai rồi." | "Chỗ này sai vì X. Dự án đang dùng pattern Y, xem file Z." |
| "Làm lại đi." | "Giữ nguyên phần A, chỉ làm lại phần B theo hướng C." |
| "Không hay lắm." | "Đưa ra thêm 2 phương án khác, so sánh theo tiêu chí hiệu năng và độ dễ đọc." |
| "Chắc đúng chưa?" | "Tự rà lại lập luận trên, chỉ ra điểm nào bạn chưa kiểm chứng được từ mã nguồn." |

Câu cuối đặc biệt hữu ích: nó buộc AI tách bạch **điều đã xác minh** khỏi **điều đang suy đoán**.

---

## 2.5. Kỹ thuật quan trọng nhất cho người mới: Codebase Q&A

Đây là lời khuyên đầu tiên trong workshop của Anthropic, và nó đi ngược trực giác:

> **Đừng bắt đầu bằng việc bảo AI viết code. Hãy bắt đầu bằng việc hỏi nó về codebase.**

Vì sao hiệu quả:

1. Bạn học được AI hiểu dự án đến đâu — trước khi giao việc quan trọng.
2. Bạn phát hiện lỗ hổng ngữ cảnh — chỗ nào nó đoán, chỗ đó cần bổ sung `CLAUDE.md`.
3. Bạn học được chính codebase của mình.
4. Rủi ro bằng không — hỏi thì không sửa gì cả.

### Bộ câu hỏi khám phá

**Hiểu một đoạn code:**

```
Đoạn code này được dùng ở đâu?
Class này được khởi tạo như thế nào và ở những chỗ nào?
Vì sao hàm này có tới 15 tham số, và tham số được đặt tên kiểu này từ bao giờ?
```

**Hiểu kiến trúc:**

```
Module này tương tác với phần còn lại của hệ thống ra sao?
Những gì đang phụ thuộc vào file này?
Nếu tôi đổi chữ ký hàm này thì cái gì sẽ vỡ?
```

**Chuẩn bị thay đổi:**

```
Để thêm tính năng X, những file nào cần sửa?
Rủi ro của thay đổi này là gì?
Nên bổ sung những test nào?
Đề xuất kế hoạch trước, chưa sửa file nào cả.
```

**Kiểm chứng:**

```
Làm sao xác nhận thay đổi này chạy đúng?
Chạy các test liên quan.
Có chỗ nào bạn chưa kiểm chứng được không?
```

Câu cuối cùng nên hỏi ở mọi task quan trọng.

### Kết quả thật tại Anthropic

Onboarding kỹ thuật cho người mới trước đây mất khoảng **2–3 tuần**. Khi dùng
Codebase Q&A làm bước đầu, con số này rút xuống khoảng **2–3 ngày**. Cơ chế rất đơn
giản: người mới hỏi AI thay vì xếp hàng chờ hỏi đồng nghiệp, và AI trả lời dựa trên
mã nguồn thật chứ không phải trí nhớ của ai đó.

---

## 2.6. Ví dụ thật — Codebase Q&A trên PM-AGENT

PM-AGENT có quy ước: mọi adapter phải tuân theo `SourceAdapter` Protocol, và
`SyncService` không được biết adapter cụ thể nào. Một người mới không biết điều đó.

**Phiên Q&A trước khi viết dòng code nào:**

```
Hỏi 1: Dự án này thêm một nguồn dữ liệu mới (ví dụ Jira) thì cần đụng vào những file nào?
       Đọc app/modules/adapters/ rồi trả lời, đừng đoán.

Hỏi 2: AdapterRegistry đăng ký adapter ở đâu, và ai gọi nó?

Hỏi 3: Nếu tôi thêm một adapter mà không đăng ký vào registry thì chuyện gì xảy ra?

Hỏi 4: Xem git log của app/modules/adapters/ — vì sao dự án chọn typing.Protocol
       thay vì abstract base class?
```

Câu hỏi 4 minh hoạ một nguồn ngữ cảnh bị bỏ quên: **lịch sử Git**.

---

## 2.7. Lịch sử Git là nguồn ngữ cảnh mạnh nhất mà ít người dùng

Code cho biết hệ thống *đang* làm gì. Git cho biết *vì sao* nó thành ra như vậy.

Một agent có quyền chạy `git` có thể truy ra:

- Tham số này được thêm vào lúc nào, trong commit nào.
- Ai thêm, và thông điệp commit nói gì.
- Commit đó gắn với issue nào.
- Đoạn code trông kỳ quặc này là kết quả của bản vá cho lỗi nào.

**Ví dụ tổng quát:**

```
Hàm ReportService.build_summary() có một nhánh if trông thừa, xử lý trường hợp
project_id là None. Xem git log/blame của dòng đó, tìm commit đưa nó vào và
giải thích vì sao nhánh này tồn tại. Nếu nó thật sự thừa thì nói rõ bằng chứng.
```

Không có Git, AI sẽ trả lời "trông có vẻ thừa, xoá được" — một câu trả lời tự tin và
nguy hiểm. Có Git, nó tìm ra commit `fix(report): guard against orphan report rows`
và kết luận ngược lại.

**Ứng dụng hằng tuần:**

```
Tuần này tôi đã ship những gì? Xem git log, lọc theo commit của tôi, tổng hợp
thành danh sách gọn để dán vào báo cáo.
```

---

## 2.8. Cung cấp dữ liệu thay vì mô tả dữ liệu

Một bẫy đã thực sự xảy ra trong PM-AGENT: viết 31 test case cho một vấn đề không tồn
tại, vì dữ liệu được mô tả theo tưởng tượng thay vì mở dữ liệu thật ra xem.

> **Bẫy — mô tả thay vì đưa dữ liệu.**
> Dấu hiệu: trong yêu cầu có những cụm như "dữ liệu kiểu như", "đại loại là",
> "thường thì trường này sẽ có". Mỗi cụm đó là một chỗ AI sắp phỏng đoán.

Cách sửa: đưa mẫu thật.

| Thay vì | Hãy làm |
|---|---|
| "Response API trả về danh sách task" | Dán một response thật (đã che dữ liệu nhạy cảm) |
| "Bảng này có mấy cột" | Dán kết quả `\d+ tasks` hoặc file model |
| "Log báo lỗi kết nối" | Dán nguyên đoạn log kèm timestamp |
| "Giao diện bị vỡ" | Đưa ảnh chụp màn hình |

---

## 2.9. Không suy đoán — quy tắc bắt buộc khi bàn về spec và code

PM-AGENT có một luật riêng cho việc này (`.claude/rules/{prefix}-no-speculation.md`), và nó
đáng áp dụng cho mọi dự án:

> Khi bàn về spec hoặc code, **không được viết suy đoán như thể là sự thật**.

Những từ bị coi là cờ đỏ khi dùng để khẳng định: *"có lẽ", "chắc là", "hình như",
"tôi nghĩ rằng", "probably", "should be"*.

Thay vào đó:

- Điều xác minh được từ mã nguồn → **phải kiểm tra trước khi viết**, và dẫn `file:dòng`.
- Điều không xác minh được → ghi rõ "chưa xác nhận" và tạo việc để kiểm tra.

Lý do rất thực tế: một suy đoán được viết như sự thật sẽ đi tiếp — người khác đọc,
tin, chuyển cho khách hàng, và ba tuần sau nó thành mâu thuẫn spec mà không ai truy
được nguồn gốc. Kiểm chứng trước khi phát ngôn thì rẻ; sửa sau khi đã lan thì đắt.

**Áp dụng khi làm việc với AI:** yêu cầu AI đánh dấu rõ ranh giới.

```
Với mỗi khẳng định trong phần phân tích, ghi kèm file:dòng làm bằng chứng.
Khẳng định nào không dẫn được bằng chứng thì xếp riêng vào mục "Chưa xác minh".
```

---

## 2.10. Checklist trước khi gửi một yêu cầu quan trọng

```
[ ] Đã nói rõ kết quả cuối cùng cần đạt chưa?
[ ] Đã đưa dữ liệu thật (log, response, schema, ảnh) thay vì mô tả chưa?
[ ] Đã chỉ ra file/module liên quan chưa?
[ ] Đã nói rõ cái gì KHÔNG được đụng vào chưa?
[ ] Đã nêu định dạng đầu ra mong muốn chưa?
[ ] Đã nói thế nào là làm đúng (tiêu chí nghiệm thu) chưa?
[ ] Với việc lớn: đã yêu cầu lập kế hoạch trước khi sửa file chưa?
```

---

## 2.11. Bài tập

**Bài 1 — Phiên Q&A đầu tiên.**
Chọn một module bạn chưa hiểu rõ trong dự án của mình. Hỏi năm câu khám phá ở mục 2.5.
Ghi lại chỗ nào AI đoán sai — đó chính là ngữ cảnh còn thiếu.

**Bài 2 — Khảo cổ Git.**
Tìm một đoạn code trông vô lý trong dự án. Yêu cầu AI dùng `git log`/`git blame` để
giải thích nguồn gốc. So sánh câu trả lời khi có và không có quyền chạy Git.

**Bài 3 — Viết lại một yêu cầu.**
Lấy một yêu cầu bạn từng gửi cho AI và nhận kết quả kém. Viết lại theo T-R-F-C-R.
Chạy lại và so sánh.

---

## Tóm tắt chương

- Prompt không phải câu thần chú — nó là phương tiện chuyển thông tin.
- T-R-F-C-R là checklist tự kiểm, không phải biểu mẫu.
- Kết quả lần đầu là điểm bắt đầu; **Evaluate → Iterate** mới là kỹ năng.
- Người mới nên bắt đầu bằng **Codebase Q&A**, không phải bằng yêu cầu viết code.
- **Git là ngữ cảnh**: code nói *cái gì*, Git nói *vì sao*.
- Đưa dữ liệu thật thay vì mô tả dữ liệu.
- Không viết suy đoán như thể sự thật — khẳng định phải dẫn được `file:dòng`.

> Chương tiếp: [03 — Context Engineering](03-context-engineering.md) — biến những gì
> vừa học được ở đây thành file nằm trong repo, để không phải nói lại ở phiên sau.
