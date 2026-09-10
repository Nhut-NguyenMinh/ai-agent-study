# Chương 04 — Workflow: Plan → Review → Execute → Verify

> [← Chương 03](03-context-engineering.md) | [Mục lục](README.md) | [Chương 05 →](05-skills-va-slash-commands.md)

---

## 4.1. Sai lầm kinh điển: một prompt khổng lồ

```
"Xây cho tôi một CRM có: đăng nhập, phân quyền, database, REST API,
dashboard, gửi email, xuất báo cáo, và deploy lên production."
```

Yêu cầu này hỏng vì bốn lý do độc lập nhau:

1. **Ngữ cảnh quá tải** — tám mục tiêu cùng lúc, không mục nào được suy nghĩ kỹ.
2. **Không review được** — kết quả trả về là hàng nghìn dòng, không ai đọc nổi.
3. **Sai một chỗ hỏng cả khối** — model dữ liệu sai ở bước hai kéo theo sáu bước sau.
4. **Không có điểm dừng** — không biết khi nào thì gọi là xong.

Đôi khi AI vẫn làm được và code chạy. Nhưng "chạy được" và "đúng thứ bạn cần" là hai
chuyện khác nhau — và bạn chỉ phát hiện ra điều đó sau khi đã có 3.000 dòng code.

---

## 4.2. Chuỗi bảy bước

```
1. Understand   — hiểu yêu cầu thực sự là gì
2. Explore      — đọc code hiện tại, tìm ràng buộc
3. Plan         — lập kế hoạch cụ thể
4. Review       — NGƯỜI duyệt kế hoạch          ← cổng chặn quan trọng nhất
5. Implement    — thực hiện theo kế hoạch
6. Test         — chạy thử, đọc lỗi
7. Iterate      — sửa và chạy lại
```

Bước 4 là nơi tiết kiệm nhiều thời gian nhất trong cả chuỗi. Duyệt một kế hoạch 20 dòng
mất năm phút. Đọc lại 800 dòng code đi sai hướng mất cả buổi chiều, và thường kết thúc
bằng việc vứt đi làm lại.

### Bước 1 — Understand

Trước khi khám phá, phải biết đang tìm gì. Câu hỏi cần trả lời:

- Kết quả cuối cùng người dùng nhìn thấy là gì?
- Ai dùng tính năng này, trong tình huống nào?
- Thế nào là làm xong?

**Ví dụ thật — PM-AGENT có luật riêng cho bước này** (`.claude/rules/{prefix}-require-context.md`):
mọi yêu cầu phải xác định được **ít nhất một** trong ba thứ:

| # | Thông tin | Ví dụ |
|---|---|---|
| 1 | URL màn hình/endpoint | `/projects/123/tasks` |
| 2 | Tên màn hình/tính năng | "Task List", "modal tạo project" |
| 3 | File hoặc hàm cụ thể | `sync_service.py`, `TaggingPromptBuilder.build_input()` |

Không có cái nào → **dừng và hỏi lại**, không được đoán phạm vi.

Đối chiếu:

| Yêu cầu | Đủ hay thiếu |
|---|---|
| "Fix lỗi ở `sync_service.py` hàm `upsert_item`" | Đủ — file + hàm |
| "Màn hình Task List lỗi render khi không có data" | Đủ — màn hình |
| "Có lỗi khi tạo task" | **Thiếu** — không rõ màn hình nào, endpoint nào |
| "Cải thiện performance" | **Thiếu** — không xác định được component |

Lý do rất cụ thể: yêu cầu mơ hồ dẫn tới sửa nhầm file, và sửa nhầm file thì phá vỡ
những thứ không liên quan. Hỏi lại một lần ở đầu rẻ hơn nhiều.

### Bước 2 — Explore

Cho AI đọc trước khi cho nó sửa. Câu hỏi hiệu quả đã liệt kê ở [mục 2.5](02-prompting-va-context.md).
Điều cần nói rõ ở bước này:

```
Chỉ đọc và phân tích. Chưa sửa file nào.
```

### Bước 3 — Plan

Một kế hoạch dùng được phải có đủ bốn phần:

```markdown
## Phương án

### Thay đổi
- File X: [làm gì cụ thể]
- File Y: [làm gì cụ thể]

### Giả định
- [Điều gì đang được giả định là đúng]

### Rủi ro đã biết
- [Điều gì có thể sai]

### Cách kiểm chứng
- [Chạy lệnh nào, xem cái gì để biết là đúng]
```

Mục **Giả định** là mục hay bị bỏ nhất và có giá trị cao nhất — nó là nơi những hiểu
lầm lộ ra trước khi thành code.

### Bước 4 — Review

Người đọc kế hoạch và tìm ba thứ:

- Kế hoạch có hiểu đúng yêu cầu không?
- Có động vào thứ không nên động không?
- Có bỏ sót trường hợp nào không?

Kế hoạch sai → sửa kế hoạch → mới cho code.

### Bước 5–7 — Implement, Test, Iterate

Chi tiết ở [chương 09](09-verification-feedback-loop.md).

---

## 4.3. Không có một workflow duy nhất

Ép mọi việc vào cùng một quy trình là lãng phí. Chọn quy trình theo độ phức tạp và
rủi ro:

| Loại việc | Quy trình |
|---|---|
| **Nhỏ** (sửa typo, đổi màu, đổi text) | Hỏi → Làm → Kiểm |
| **Vừa** (một hàm, một endpoint) | Hiểu → Kế hoạch → Làm → Test |
| **Lớn** (nhiều module) | Khám phá → Kế hoạch → **Người duyệt** → Làm → Test → Lặp → Review → Merge |
| **Nguy hiểm** (production, dữ liệu, tiền, quyền) | Hiểu → Kế hoạch → Kiểm tra hợp lệ → **Người phê duyệt** → Thực thi → Ghi log kiểm toán |

> **Quy tắc:** việc càng phức tạp và càng khó hoàn tác thì càng phải lập kế hoạch trước.

Ngược lại cũng đúng: bắt một task sửa typo đi qua bảy bước là nghi thức vô nghĩa, và
nghi thức vô nghĩa khiến người ta bỏ quy trình luôn ở những lúc thật sự cần.

---

## 4.4. Ví dụ thật — phân loại độ khó và vòng phản biện

PM-AGENT có luật `.claude/rules/{prefix}-critique-loop.md` biến ý "chọn workflow theo độ khó"
thành bảng tra cứu:

| Mức | Tiêu chí | Số vòng phản biện |
|---|---|---|
| **Khó / Lớn** | ≥ 3 module bị ảnh hưởng; đổi kiến trúc; liên quan bảo mật/auth; diff > 300 dòng | 3 |
| **Khá** | 2 module; đổi schema DB kèm migration; feature mới có AI call hoặc async job | 2 |
| **Trung bình** | 1 module; bug có nhiều nguyên nhân khả dĩ; refactor một service | 1 |
| **Dễ** | 1 hàm; đổi config; spec đã chốt và người dùng đã chỉ định cách làm | 0 |

Và bắt buộc thông báo trước khi bắt đầu:

> "Phân loại: [Khó/Khá/Trung bình/Dễ] — sẽ chạy [N] vòng phản biện."

### Vòng phản biện là gì

Với việc từ Trung bình trở lên, kế hoạch không đi thẳng tới người dùng. Nó đi qua một
agent phản biện độc lập trước — agent này không mang theo lý do đã chọn phương án ban
đầu, nên nhìn ra được điểm mù:

```
Đề xuất phương án
      ↓
Agent phản biện độc lập  ← 5 góc: kỹ thuật, bảo mật, hiệu năng, nhất quán, tái sử dụng
      ↓
Verdict: "Cần sửa" hoặc "Đủ tốt để thực hiện"
      ↓
Nếu cần sửa → cập nhật phương án → lặp (tối đa N vòng)
      ↓
MỚI trình bày cho người dùng
```

Điểm thiết kế quan trọng: **các vòng phản biện chạy nội bộ, không hỏi người dùng ở
giữa**. Người dùng chỉ thấy phương án sau khi đã qua hết vòng — họ không bị lôi vào
việc chấm điểm từng bản nháp.

### Vì sao cần agent thứ hai thay vì tự rà lại

Tự rà lại phương án của chính mình có một điểm yếu cố hữu: bạn đã bị neo vào lý do
chọn nó. Agent phản biện đọc phương án như đọc của người lạ, không biết bạn đã cân
nhắc gì, nên nó hỏi những câu bạn đã bỏ qua từ đầu.

> **Bẫy thật đã xảy ra** (ghi trong `{prefix}-adversarial-hard-stop.md`): người dùng nêu
> đúng giả thuyết deadlock từ sớm. Giả thuyết bị bác bỏ dựa trên một chỉ số đọc sai
> đơn vị, rồi các phương án khác được đề xuất mà không qua phản biện. Công cụ giám sát
> sau đó xác nhận: đúng là deadlock, đúng như giả thuyết ban đầu. Bài học rút ra thành
> luật: **điểm trôi dạt nguy hiểm nhất là lúc chuyển từ phân tích sang đề xuất** — và
> đó chính là chỗ phải chặn cứng.

---

## 4.5. Chia nhỏ: giới hạn kích thước mỗi bước

Kế hoạch tốt vẫn hỏng nếu mỗi bước quá lớn. PM-AGENT đặt giới hạn cứng
(`.claude/rules/{prefix}-small-commits.md`):

| Giới hạn | Ngưỡng |
|---|---|
| Số file thay đổi | Tối đa **5** (lý tưởng ≤ 3) |
| Số dòng thay đổi mỗi file | Tối đa **100** |
| Tổng diff | Tối đa **300** dòng |

Vượt ngưỡng → **bắt buộc chia nhỏ thành subtask trước khi bắt tay làm**.

### Quy tắc chia subtask

- Mỗi subtask độc lập — commit được mà không làm vỡ cái khác.
- Subtask sau có thể phụ thuộc subtask trước, nhưng không được phụ thuộc ngược.
- Thứ tự ưu tiên: **schema/model → service/logic → API/router → test**.

Thứ tự này không tuỳ tiện: tầng dưới ổn định trước thì tầng trên không phải sửa lại.

### Ví dụ tổng quát — chia một feature

```
Yêu cầu: "Thêm tính năng đánh dấu task là khẩn cấp"

Subtask 1 — Schema
  Files: app/models/task.py, alembic/versions/xxxx_add_is_urgent.py
  Diff ước tính: ~30 dòng

Subtask 2 — Service
  Files: app/modules/task/service.py, app/modules/task/repository.py
  Diff ước tính: ~60 dòng

Subtask 3 — API
  Files: app/modules/task/router.py, app/schemas/task.py
  Diff ước tính: ~40 dòng

Subtask 4 — UI
  Files: app/templates/partials/task_row.html
  Diff ước tính: ~25 dòng
```

Bốn commit thay vì một. Mỗi commit review được trong hai phút, revert được độc lập, và
`git bisect` khoanh vùng được chính xác khi có lỗi sau này.

### Ngoại lệ hợp lệ

| Trường hợp | Xử lý |
|---|---|
| File migration lớn | Không tính vào giới hạn, nhưng phải là file duy nhất trong commit đó |
| Rename/move hàng loạt | Cho phép nếu là rename thuần, không trộn với thay đổi logic |
| Code sinh tự động | Không tính, phải ghi rõ trong thông điệp commit |

---

## 4.6. Ví dụ thật — vòng đời đầy đủ của một feature trong PM-AGENT

Ghép tất cả lại, đây là quy trình bắt buộc cho mọi thay đổi code trong PM-AGENT:

```
[1] TÀI LIỆU TEST  →  [2] IMPLEMENT  →  [3] VERIFY & CLOSE
    /test-doc-first        (code)          /feature-verify
    + người duyệt                          + người xác nhận
```

Triển khai đầy đủ cho một feature mới:

```
new-feature (spec + plan)
      ↓
test-doc-first  → tạo YAML test case  → [NGƯỜI DUYỆT]
      ↓
implement (small commits, test sau mỗi commit)
      ↓
feature-verify (chạy test + coverage) → [NGƯỜI XÁC NHẬN ĐÓNG]
      ↓
doc-sync-code (cập nhật tài liệu)
      ↓
feature-report (báo cáo tổng hợp)
```

Với việc sửa lỗi thì rút gọn nhưng giữ nguyên xương sống:

```
chẩn đoán
  ↓
test-doc-first (tối thiểu: 1 TC tái hiện lỗi + 1 TC xác nhận đã sửa) → [DUYỆT]
  ↓
fix
  ↓
feature-verify → [XÁC NHẬN ĐÓNG]
```

Điểm cốt lõi: **test case được viết và duyệt TRƯỚC khi code**. Chi tiết vì sao ở
[chương 09](09-verification-feedback-loop.md).

---

## 4.7. Hai cổng dừng bắt buộc

Trong quy trình trên có hai chỗ AI phải dừng và chờ người:

| Cổng | Ở đâu | Vì sao |
|---|---|---|
| **Duyệt tài liệu test** | Sau khi viết test case, trước khi code | Test case là bản dịch của yêu cầu. Duyệt test dễ hơn duyệt code, và không cần biết lập trình |
| **Xác nhận đóng** | Sau khi test chạy xong | "Test xanh" và "đúng thứ tôi cần" là hai chuyện khác nhau |

Cổng thứ nhất đặc biệt đáng chú ý: người duyệt không cần đọc code. Họ đọc câu
*"Khi người dùng bấm Lưu mà chưa nhập tiêu đề thì hiện lỗi 'Tiêu đề bắt buộc' ngay
dưới ô nhập"* và biết ngay đúng hay sai. Đó là chỗ AI và người hiểu nhau rẻ nhất.

---

## 4.8. Kiểm quyết định cũ trước khi thay đổi

Một luật ít gặp nhưng cứu được nhiều lần (`.claude/rules/{prefix}-verify-before-change.md`):

> Trước khi đổi spec, schema, hay code — **bắt buộc kiểm xem đã có quyết định nào chốt
> về nó trong quá khứ chưa**.

Bốn câu cần trả lời:

- Item này đã có quyết định thiết kế đã chốt chưa?
- Có đang vi phạm một quy tắc đã quyết rõ (kiểu "không bỏ cột này") không?
- Có đang vượt ranh giới phạm vi Phase 1/Phase 2 không?
- Có đang hồi sinh một quyết định đã bị đảo trong quá khứ không?

Các sự cố thật dẫn tới luật này:

1. Một ràng buộc đã chốt là "xử lý ở tầng ứng dụng" bị hồi sinh thành ràng buộc DB.
2. Một cột được ghi chú "không bỏ ở Phase 1" bị đánh dấu deprecated.
3. Thay đổi DB được thực hiện mà không liệt kê các spec bị ảnh hưởng.

Cả ba đều cùng một nguyên nhân: bỏ qua bước kiểm quyết định cũ.

**Khi giao việc cho subagent, luật này còn quan trọng hơn** — subagent không có lịch
sử dự án, nên những gì đã chốt phải được viết thẳng vào prompt giao việc, nếu không nó
sẽ vô tư phá vỡ. Chi tiết ở [chương 08](08-subagents-va-song-song.md).

---

## 4.9. Bẫy thường gặp

> **Bẫy 1 — Kế hoạch quá mơ hồ để duyệt.**
> "Bước 1: sửa service. Bước 2: cập nhật API." Kế hoạch này không duyệt được vì nó
> không nói sửa gì. **Cách sửa:** yêu cầu nêu tên file, tên hàm, và ước tính số dòng.

> **Bẫy 2 — Duyệt kế hoạch cho có.**
> Đọc lướt rồi bấm đồng ý. **Cách sửa:** đọc kỹ mục "Giả định" — đó là nơi hiểu lầm nằm.

> **Bẫy 3 — Kế hoạch đổi giữa chừng mà không báo.**
> AI phát hiện cần thêm thay đổi ngoài kế hoạch và tự làm luôn. **Cách sửa:** luật rõ
> ràng — phát hiện việc ngoài kế hoạch thì **dừng, cập nhật kế hoạch, rồi mới tiếp**.

> **Bẫy 4 — Nghi thức hoá cho việc nhỏ.**
> Bắt task sửa typo đi qua bảy bước. **Cách sửa:** phân loại độ khó trước; mức "Dễ" thì
> làm luôn và ghi rõ "(Dễ — bỏ qua phản biện)".

---

## 4.10. Bài tập

**Bài 1 — Plan Mode lần đầu.**
Chọn một tính năng cỡ vừa. Yêu cầu: *"Khám phá dự án, hiểu kiến trúc hiện tại, lập kế
hoạch triển khai, liệt kê rủi ro và test cần có. Chưa sửa file nào."* Đọc kế hoạch và
đếm xem có bao nhiêu chỗ bạn phải chỉnh — mỗi chỗ đó là một lần bạn vừa tiết kiệm được
một vòng code lại.

**Bài 2 — Chia nhỏ một việc lớn.**
Lấy một việc ước tính > 300 dòng diff. Chia thành các subtask ≤ 5 file / ≤ 300 dòng,
theo thứ tự schema → service → API → test.

**Bài 3 — Phản biện độc lập.**
Với một kế hoạch đã có, mở một phiên mới (không mang ngữ cảnh cũ) và yêu cầu nó đóng
vai kỹ sư review độc lập tìm điểm yếu. So sánh với những gì bạn tự tìm ra.

---

## Tóm tắt chương

```
Understand → Explore → Plan → [NGƯỜI DUYỆT] → Implement → Test → Iterate
```

- Prompt khổng lồ hỏng vì quá tải, không review được, và không có điểm dừng.
- Xác định phạm vi trước: **URL, màn hình, hoặc file/hàm** — thiếu cả ba thì hỏi lại.
- Chọn quy trình theo độ khó và độ rủi ro; đừng ép mọi việc vào một khuôn.
- Việc từ trung bình trở lên nên qua **phản biện độc lập** trước khi trình bày.
- Giới hạn cứng mỗi bước: ≤ 5 file, ≤ 300 dòng — vượt thì chia nhỏ.
- Hai cổng dừng: **duyệt tài liệu test** và **xác nhận đóng**.
- Kiểm quyết định cũ trước khi thay đổi; khi giao cho subagent thì phải viết ra.

> Chương tiếp: [05 — Skills & Slash Commands](05-skills-va-slash-commands.md)
