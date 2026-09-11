# Chương 10 — Mẫu phối hợp đa tác nhân: định tuyến, đồng thuận, tranh luận

> [← Chương 09](09-subagents-va-song-song.md) | [Mục lục](README.md) | [Chương 11 →](11-verification-feedback-loop.md)

---

## 10.1. Chia việc và phối hợp là hai bài toán khác nhau

Chương 09 trả lời câu hỏi *"làm sao chia một việc lớn thành nhiều phần"*. Chương này
trả lời câu hỏi khác: *"nhiều tác nhân cùng nhìn một vấn đề thì kết hợp kết quả thế nào"*.

Khác biệt cốt lõi:

```
Chương 09 — Chia việc:      một việc → nhiều phần độc lập → ghép lại
Chương 10 — Phối hợp:       một vấn đề → nhiều góc nhìn → chọn/hợp nhất
```

Ở chương 09, hai subagent làm hai việc khác nhau. Ở chương 10, nhiều tác nhân làm
**cùng một việc** — và giá trị nằm ở chỗ chúng không ra kết quả giống nhau.

Bốn mẫu phối hợp, dùng cho bốn tình huống khác nhau:

| Mẫu | Khi nào dùng | Kết quả |
|---|---|---|
| **Định tuyến** | Việc cần đúng loại năng lực | Chọn đúng tác nhân/mô hình cho từng việc |
| **Đồng thuận** | Bài toán có nhiều lời giải hợp lý | Mở rộng không gian tìm kiếm |
| **Tranh luận** | Cần thách thức giả định | Hội tụ sau khi phản biện lẫn nhau |
| **Kiểm tra độc lập** | Kết quả quan trọng, cần nghiệm thu | Người làm ≠ người kiểm |

---

## 10.2. Định tuyến công việc

Sai lầm mặc định: dùng mô hình mạnh nhất cho mọi việc. Nó lãng phí ở việc dễ và vẫn
không tối ưu ở việc cần năng lực chuyên biệt.

Bộ định tuyến trả lời một câu: **việc này nên giao cho ai?**

```
Việc đến
   ↓
Phân loại
   ↓
┌──────────────┬──────────────┬──────────────┐
↓              ↓              ↓              ↓
Phân loại     Nghiên cứu    Lập trình    Suy luận khó
đơn giản                                  / điều phối
↓              ↓              ↓              ↓
Mô hình       Mô hình       Mô hình       Mô hình
rẻ/nhanh      trung cấp     chuyên code   mạnh nhất
```

> **Tên và năng lực của từng mô hình thay đổi liên tục. Điều đáng nhớ là nguyên tắc:
> đúng mô hình cho đúng việc.**

Chi tiết về mặt chi phí nằm ở [chương 13](13-kinh-te-mo-hinh.md). Ở đây chỉ nói về
mặt kiến trúc: **định tuyến là một thành phần của hệ thống, không phải quyết định
ngẫu hứng mỗi lần**.

### Ví dụ thật — AI Router của PM-AGENT

PM-AGENT có một bộ định tuyến thật, và nó minh hoạ đủ ba đặc điểm của một router dùng được:

```python
# Service KHÔNG gọi SDK trực tiếp — luôn đi qua router
response = await ai_router.execute(AIRequest(
    task_type="spec_synthesis",     # ← phân loại việc
    system_prompt=system_prompt,
    user_prompt=user_prompt,
))
# Service không biết Claude hay GPT đang chạy
```

| Đặc điểm | Cách PM-AGENT làm |
|---|---|
| **Luật định tuyến tách khỏi mã** | Rule lấy từ DB, cache Redis 5 phút — đổi luật không cần deploy |
| **Có dự phòng** | Provider lỗi → tự thử rule ưu tiên kế tiếp |
| **Có đo lường** | Mọi request ghi vào bảng `ai_requests` (async, không chặn luồng chính) |

Điểm thiết kế quan trọng nhất là dòng cuối trong đoạn mã: **service không biết mô hình
nào đang chạy**. Nhờ vậy, đổi chiến lược định tuyến không phải sửa một dòng nào trong
business logic.

### Định tuyến theo tài nguyên còn lại

Một dạng định tuyến tinh tế hơn: không chỉ theo *loại việc* mà theo *tài nguyên hiện có*.

Skill `auto-review` của PM-AGENT tự hạ cấp:

| Điều kiện | Chế độ | Lý do |
|---|---|---|
| Codex sẵn sàng + context còn > 50% | `full` — 5 chuyên gia + kiểm chéo | Đủ tài nguyên chạy đội đầy đủ |
| Codex không có hoặc context < 50% | `light` — 3 chuyên gia | Giảm tải |
| Context còn < 20% | `quick` — review trong ngữ cảnh chính | Tiết kiệm |

Đây là điều nhiều hệ thống bỏ qua: **luôn chạy chế độ nặng nhất rồi cạn context giữa
chừng** còn tệ hơn chủ động chạy chế độ nhẹ từ đầu.

---

## 10.3. Đồng thuận — khai thác tính biến thiên

Mô hình ngôn ngữ không xác định: cùng một câu hỏi, chạy nhiều lần ra kết quả khác nhau.
Phản xạ thông thường coi đó là nhược điểm. Mẫu đồng thuận coi đó là **tài nguyên**.

```
                    Vấn đề
                       ↓
       ┌─────────┬─────────┬─────────┐
       ↓         ↓         ↓         ↓
   Tác nhân 1  Tác nhân 2  Tác nhân 3  ... N
   (độc lập)   (độc lập)   (độc lập)
       ↓         ↓         ↓         ↓
       └─────────┴─────────┴─────────┘
                       ↓
                   Tổng hợp
                       ↓
          Đồng thuận / Khác biệt / Ngoại lệ
                       ↓
                  Con người quyết
```

Điểm mấu chốt: các tác nhân **không nhìn thấy kết quả của nhau**. Nếu thấy, chúng sẽ
hội tụ sớm và bạn mất đúng thứ mình cần.

### Ba loại kết quả và cách đọc

| Kết quả | Nghĩa là gì | Hành động |
|---|---|---|
| **Đồng thuận** — đa số ra cùng một hướng | Hướng đó có cơ sở vững, hoặc là lối mòn phổ biến | Tin tưởng cao hơn, nhưng cảnh giác nếu vấn đề cần sáng tạo |
| **Khác biệt** — mỗi tác nhân một hướng | Bài toán có nhiều lời giải hợp lý, hoặc đề bài chưa đủ ràng buộc | Xem lại hợp đồng yêu cầu ([ch.03](03-dac-ta-yeu-cau.md)) |
| **Ngoại lệ** — một tác nhân ra hướng rất khác | Hoặc rất sáng tạo, hoặc hoàn toàn sai | **Bắt buộc kiểm chứng** — đừng vứt, cũng đừng tin ngay |

Ô "ngoại lệ" là ô đáng tiền nhất và cũng nguy hiểm nhất. Đây chính là chỗ mà một góc
nhìn mà cả nhóm bỏ sót có thể xuất hiện — nhưng nó đứng cùng chỗ với những câu trả lời
sai tự tin.

> **Nhiều tác nhân giúp mở rộng không gian tìm kiếm giải pháp — không giúp xác nhận
> giải pháp nào đúng.** Việc xác nhận vẫn cần bằng chứng.

### Khi nào dùng đồng thuận

Phù hợp: đặt tên kiến trúc, chọn hướng thiết kế, tìm nguyên nhân có thể của một sự cố,
liệt kê rủi ro, sinh ý tưởng.

Không phù hợp: việc chỉ có một đáp án đúng (tính toán, tra cứu), việc đã có luật rõ
ràng, việc cần chạy nhanh.

---

## 10.4. Tranh luận — thách thức giả định

Đồng thuận cho các tác nhân nghĩ độc lập rồi tổng hợp. Tranh luận cho chúng **tương tác**.

```
Đồng thuận:            Tranh luận:

Nghĩ độc lập           Tác nhân A
     ↓                     ↕
  Tổng hợp             Tác nhân B
                           ↕
                       Tác nhân C
                           ↓
                       Hội tụ
```

Mỗi tác nhân nhận một vai, và vai quyết định nó tìm gì:

| Vai | Tìm gì |
|---|---|
| Người nhìn hệ thống | Ảnh hưởng lan sang module khác, ranh giới bị phá |
| Người thực tế | Chi phí triển khai, thời gian, việc phát sinh |
| Người phản biện | Giả định chưa được kiểm chứng |
| Người tìm lỗi biên | Dữ liệu rỗng, giá trị null, đồng thời, giới hạn |
| Người đại diện người dùng | Trải nghiệm, thông báo lỗi, đường đi khi sai |

Mục tiêu không phải tìm người thắng. Mục tiêu là **lộ ra điểm mù** — thứ mà một góc
nhìn đơn lẻ không thấy được.

### Ví dụ thật — vòng phản biện cứu một lỗ hổng Critical

PM-AGENT có luật `{prefix}-critique-loop` bắt buộc chạy phản biện trước khi đề xuất phương án,
số vòng theo độ khó (xem [chương 05](05-workflow-plan-execute.md)).

Trong sự cố XSS ngày 19/08:

```
Chẩn đoán ban đầu (từ mã nguồn):
  → "Lỗi hiển thị: description thô làm vỡ bố cục Task List. Thêm sanitize là xong."

Agent phản biện độc lập:
  → "Đây là Stored XSS thật, mức Critical."

Ba mắt xích chẩn đoán ban đầu bỏ sót:
  1. Màn hình duyệt không hiển thị mô tả → PM duyệt "mù"
  2. HTMX 2.0.3 mặc định cho phép thẻ script chạy trong nội dung swap
  3. Script thực thi cho MỌI người xem Task List
```

Không có vòng phản biện, lỗ hổng này đã được vá như một lỗi CSS — và vẫn còn nguyên.

Điểm đáng học về mặt thiết kế: **các vòng phản biện chạy nội bộ, không hỏi người dùng
ở giữa**. Người dùng chỉ thấy phương án sau khi đã qua hết vòng; họ không bị lôi vào
việc chấm điểm từng bản nháp.

### Vì sao phải là tác nhân khác, không phải tự rà lại

Tự rà lại phương án của chính mình có một điểm yếu cố hữu: bạn đã bị neo vào lý do đã
chọn nó. Agent phản biện đọc phương án như đọc của người lạ — không biết bạn đã cân
nhắc gì, nên nó hỏi những câu bạn đã bỏ qua từ đầu.

Điều kiện để nó hiệu quả: **ngữ cảnh phải sạch**. Nếu bạn đưa cả lý luận của mình vào
prompt cho nó, bạn đã neo nó theo.

---

## 10.5. Kiểm tra độc lập — người làm khác người nghiệm thu

Mẫu thứ tư, và là mẫu nên dùng thường xuyên nhất.

```
TÁC NHÂN THỰC HIỆN
        ↓
      KẾT QUẢ
        ↓
TÁC NHÂN KIỂM TRA (ngữ cảnh sạch)
        ↓
    ┌───┴────┐
   ĐẠT     CÓ LỖI
    ↓        ↓
  Nghiệm   Tác nhân sửa
   thu        ↓
            Kiểm lại
```

Tác nhân kiểm tra soi sáu thứ: tính đúng, trường hợp biên, lỗi tiềm ẩn, bảo mật, có
đáp ứng yêu cầu không, có đơn giản hoá được không.

> **Người thực hiện và người kiểm tra nên có góc nhìn càng độc lập càng tốt.**

### Ví dụ thật — đội chuyên gia của `/auto-review`

```
full:  5 chuyên gia (song song) → tổng hợp → Codex kiểm chéo
light: 3 chuyên gia (song song) → tổng hợp
quick: review trong ngữ cảnh chính
```

Ghi chú thiết kế nằm trong chính skill:

```
Codex tốn nhiều token → chỉ dùng làm "double-check cuối cùng".
Team chuyên gia Claude chạy song song = chi phí 0, tốc độ cao.
```

Nguyên tắc rút ra áp dụng được cho mọi hệ thống nhiều tác nhân:

> **Cái rẻ chạy rộng, cái đắt chạy hẹp.** Năm tác nhân quét toàn bộ; công cụ đắt tiền
> chỉ xác nhận kết luận cuối.

Và quy mô review được chọn theo quy mô thay đổi, không phải lúc nào cũng dùng đội đầy đủ:

| Thay đổi | Cách review |
|---|---|
| ≥ 3 file, hoặc đụng security/auth | `/auto-review` full hoặc light |
| 1–2 file, code thông thường | `/{prefix}-code-review` — nhanh, đủ |
| Chỉ quan tâm bảo mật | `/{prefix}-security-audit` — chuyên sâu OWASP |

---

## 10.6. Chọn mẫu nào — bảng quyết định

```
Việc cần đúng loại năng lực?              → ĐỊNH TUYẾN
Bài toán có nhiều lời giải hợp lý?        → ĐỒNG THUẬN
Cần thách thức giả định, tìm điểm mù?     → TRANH LUẬN
Kết quả quan trọng, cần nghiệm thu?       → KIỂM TRA ĐỘC LẬP
Việc đơn giản, một đáp án đúng?           → MỘT TÁC NHÂN, đừng phức tạp hoá
```

Kết hợp được, và thường nên kết hợp:

```
Định tuyến (chọn ai làm)
    ↓
Thực hiện
    ↓
Kiểm tra độc lập (nghiệm thu)
    ↓
  Còn nghi ngờ?
    ↓
Tranh luận (lộ điểm mù)
```

---

## 10.7. Cái giá của phối hợp

Mọi mẫu ở chương này đều tốn thêm — token, thời gian, độ phức tạp. Bảng đánh đổi:

| Mẫu | Chi phí tương đối | Đáng dùng khi |
|---|---|---|
| Định tuyến | Thấp (một lần quyết định) | Luôn luôn — nó *tiết kiệm* chi phí |
| Kiểm tra độc lập | Trung bình (thêm 1 lượt) | Hầu hết việc quan trọng |
| Tranh luận | Cao (nhiều vòng tương tác) | Quyết định khó hoàn tác |
| Đồng thuận N tác nhân | Cao (N lần chi phí) | Bài toán mở, giá trị cao |

> **Đừng dùng nhiều tác nhân chỉ vì nó "trông rất AI".** Ba agent cho một task sửa CSS
> tốn nhiều hơn tiết kiệm.

Quy tắc thực hành: bắt đầu bằng một tác nhân. Thêm mẫu phối hợp khi có **triệu chứng
cụ thể** — kết quả không ổn định (→ đồng thuận), bỏ sót điểm mù (→ tranh luận), lỗi lọt
lưới (→ kiểm tra độc lập), chi phí cao ở việc dễ (→ định tuyến).

---

## 10.8. Bẫy thường gặp

> **Bẫy 1 — Đồng thuận giả.**
> Các tác nhân nhìn thấy kết quả của nhau nên hội tụ sớm. **Cách sửa:** chạy độc lập
> hoàn toàn, chỉ tổng hợp ở cuối.

> **Bẫy 2 — Neo agent phản biện.**
> Đưa cả lý luận của mình vào prompt cho nó. **Cách sửa:** chỉ đưa phương án và ngữ
> cảnh kỹ thuật, không đưa lý do bạn chọn.

> **Bẫy 3 — Đếm phiếu thay vì cân bằng chứng.**
> 4/5 tác nhân nói A nên chọn A. Nhưng chúng có thể cùng sai theo một lối mòn phổ biến.
> **Cách sửa:** đồng thuận là tín hiệu, không phải bằng chứng.

> **Bẫy 4 — Vứt ý kiến ngoại lệ.**
> Nó khác đa số nên bị bỏ. **Cách sửa:** kiểm chứng nó bằng dữ kiện, rồi mới quyết.

> **Bẫy 5 — Tranh luận không có điểm dừng.**
> Các tác nhân cãi nhau vô hạn. **Cách sửa:** đặt số vòng tối đa và điều kiện thoát
> sớm — PM-AGENT dùng "tối đa 2 vòng mỗi task, sau Approve hoặc Escalate thì dừng".

> **Bẫy 6 — Phối hợp thay cho suy nghĩ.**
> Gọi 5 tác nhân vì không muốn tự nghĩ. **Cách sửa:** bạn vẫn là người quyết; các mẫu
> này mở rộng đầu vào cho quyết định đó, không thay thế nó.

---

## 10.9. Bài tập

**Bài 1 — Đồng thuận trên một quyết định thật.**
Lấy một quyết định thiết kế đang phân vân. Hỏi cùng câu đó ở 3 phiên độc lập. Phân loại
kết quả thành đồng thuận / khác biệt / ngoại lệ.

**Bài 2 — Tranh luận có vai.**
Với một phương án đã có, giao cho 3 tác nhân ba vai ở mục 10.4. So sánh với những gì
bạn tự tìm ra.

**Bài 3 — Vẽ bảng định tuyến của bạn.**
Liệt kê các loại việc bạn hay giao cho AI. Với mỗi loại, ghi mức năng lực thực sự cần.
Đây là đầu vào cho [chương 13](13-kinh-te-mo-hinh.md).

---

## Tóm tắt chương

- Chia việc (ch.09) và phối hợp (ch.10) là hai bài toán khác nhau.
- Bốn mẫu: **định tuyến** (đúng năng lực), **đồng thuận** (mở rộng lời giải),
  **tranh luận** (lộ điểm mù), **kiểm tra độc lập** (nghiệm thu).
- Định tuyến nên là **thành phần của hệ thống**, luật tách khỏi mã, có dự phòng và đo lường.
- Đồng thuận chỉ hoạt động khi các tác nhân **không thấy kết quả của nhau**.
- Ý kiến **ngoại lệ** đáng tiền nhất và nguy hiểm nhất — bắt buộc kiểm chứng.
- Agent phản biện cần **ngữ cảnh sạch**; đưa lý luận của bạn vào là neo nó theo.
- **Cái rẻ chạy rộng, cái đắt chạy hẹp.**
- Mọi mẫu đều tốn thêm — thêm khi có triệu chứng cụ thể, không thêm vì trông hiện đại.

> Chương tiếp: [11 — Verification & Feedback loop](11-verification-feedback-loop.md)
