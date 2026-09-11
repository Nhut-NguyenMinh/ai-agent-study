# Chương 11 — Verification & Feedback loop

> [← Chương 10](10-phoi-hop-da-tac-nhan.md) | [Mục lục](README.md) | [Chương 12 →](12-kinh-te-ngu-canh.md)

---

## 11.1. Câu hỏi sai và câu hỏi đúng

Câu hỏi sai:

> "Làm sao viết prompt để AI làm đúng ngay lần đầu?"

Câu hỏi đúng:

> **"Làm sao để AI tự phát hiện là nó làm sai?"**

Sự khác biệt giữa hai câu này chính là ranh giới giữa prompt engineering và agent
engineering. Câu thứ nhất theo đuổi một thứ không tồn tại. Câu thứ hai dẫn tới một hệ
thống hoạt động được kể cả khi lần đầu sai.

```
Viết
  ↓
Chạy
  ↓
Lỗi
  ↓
Đọc lỗi        ← chỉ làm được nếu AI thấy được lỗi
  ↓
Phân tích
  ↓
Sửa
  ↓
Chạy lại
  ↓
Xác nhận
```

Vòng lặp này là thứ biến AI từ máy sinh code thành đồng nghiệp làm được việc.

---

## 11.2. Cho AI phương tiện tự kiểm tra

Đây là câu hỏi cần trả lời cho mọi lĩnh vực bạn làm việc:

> **Trong lĩnh vực của tôi, làm sao biết một thay đổi là đúng — và AI có tiếp cận được
> phương tiện đó không?**

| Lĩnh vực | Phương tiện kiểm chứng |
|---|---|
| Logic backend | Unit test, integration test |
| API | Gọi thật rồi kiểm response, schema validation |
| Giao diện | Ảnh chụp màn hình bằng trình duyệt tự động, browser test |
| Truy vấn dữ liệu | Chạy trên CSDL thật, so kết quả với kỳ vọng |
| Hiệu năng | Đo thời gian, đếm số truy vấn |
| Kiểu dữ liệu | Type checker |
| Phong cách | Linter, formatter |

Ví dụ đã kiểm chứng: khi đưa một bản mock giao diện và yêu cầu dựng lại, kết quả lần
đầu thường tạm ổn. Sau 2–3 vòng **tự chụp màn hình, tự so sánh, tự sửa**, kết quả gần
như khớp hoàn toàn. Điều làm nên khác biệt không phải prompt mô tả mock kỹ hơn — mà là
AI *nhìn thấy* được kết quả của chính nó.

---

## 11.3. Test-first: viết test trước khi viết code

Thứ tự thông thường (code trước, test sau) có một khiếm khuyết mang tính cấu trúc:

> **Test viết sau sẽ được viết để "pass" đoạn code vừa viết, chứ không phải để kiểm
> chứng yêu cầu.**

Hệ quả:

- Test xanh nhưng chưa chắc tính năng đúng.
- Trường hợp biên không được nghĩ tới, vì code đã không xử lý nó.
- Không ai biết yêu cầu nào chưa được phủ.

Test-first đảo thứ tự:

```
[1] TÀI LIỆU TEST → [người duyệt] → [2] IMPLEMENT → [3] VERIFY & CLOSE
```

Lợi ích:

1. Buộc nghĩ về **hành vi** trước khi nghĩ về cách hiện thực.
2. Người duyệt test case **không cần biết lập trình** — dễ review hơn code rất nhiều.
3. Lỗ hổng yêu cầu lộ ra sớm, khi sửa còn rẻ.
4. "Xong" có nghĩa xác định: mọi tiêu chí nghiệm thu đều có test và đều xanh.

---

## 11.4. Ví dụ thật — vòng đời test-first của PM-AGENT

Luật `.claude/rules/{prefix}-test-first.md` áp dụng cho **mọi task có thay đổi code**, trừ
sửa lỗi chính tả, đổi tên biến, thêm chú thích và đổi config không đổi logic.

### Pha 1 — Tài liệu test

Test case được viết bằng YAML, đặt trong `docs/feature-specs/<module>/tests/cases/`:

```
ut-<service>.yaml       ← unit test, chạy qua yaml_collector + ut_runner
browser-<screen>.yaml   ← browser test, chạy qua Playwright
```

Yêu cầu với test case:

- Mỗi tiêu chí nghiệm thu (AC) có **ít nhất 1 happy path + 1 negative case**.
- Dữ liệu đầu vào **cụ thể**, không trừu tượng — `admin@example.test`, không phải
  "một email hợp lệ".
- Có browser test cho mọi màn hình quan trọng.
- Có danh sách GAP: tính năng chưa làm → test case sẽ skip, ghi rõ lý do.

Sau đó **dừng lại chờ người duyệt**. Không viết code trước khi test doc được duyệt.

### Mức độ tài liệu test theo loại việc

| Loại | Mức tài liệu |
|---|---|
| Feature mới (≥ 3 AC) | Đầy đủ — mỗi AC ít nhất 2 test case |
| Sửa lỗi | Tối thiểu 1 TC tái hiện lỗi + 1 TC xác nhận đã sửa |
| Đổi hành vi | TC cho hành vi mới + TC hồi quy cho hành vi cũ |
| Refactor | Xác nhận test hiện có đủ phủ — thiếu thì bổ sung |
| Đổi schema DB | TC cho migration dữ liệu + TC kiểm ràng buộc mới |
| Sửa lỗ hổng bảo mật | TC cho vector tấn công vừa vá + TC hồi quy |

Dòng cuối đáng chú ý: sửa lỗ hổng mà không có test cho chính vector đó thì lần
refactor sau sẽ mở lại nó, và không ai biết.

### Pha 3 — Verify & Close

Ngưỡng coverage theo loại code:

| Loại code | Ngưỡng |
|---|---|
| Service / business logic | ≥ 80% |
| Router / API endpoint | ≥ 70% |
| Repository / tầng DB | ≥ 60% |
| Code nhạy cảm bảo mật (auth, mã hoá) | ≥ 90% |
| Utils / helpers | ≥ 70% |

Điều kiện để gọi là "feature đã đóng":

```
[ ] 0 unit test thất bại
[ ] 0 browser test thất bại (nếu có TC browser)
[ ] Coverage đạt ngưỡng của module
[ ] Mọi AC có trạng thái đã kiểm chứng
[ ] Test doc đã cập nhật trạng thái từng TC
[ ] CHANGELOG đã có mục đóng
```

Sáu điều kiện, và không điều nào là "tôi thấy nó chạy được".

---

## 11.5. Chạy test ngay sau mỗi commit

Luật này chống lại thói quen dồn test đến cuối:

> **Mỗi commit code phải chạy test ngay sau khi commit — không đợi đến cuối.**

| Loại commit | Test bắt buộc |
|---|---|
| Service / logic backend | `make test-unit` |
| API router / endpoint | `make test-unit` |
| Template / frontend | `make test-browser` cho màn hình đó |
| DB migration | `make test-unit-run` (integration) |
| Sửa lỗi bảo mật | Test cho vector tấn công |

Khi test đỏ sau một commit:

1. Sửa ngay, trước khi sang commit tiếp theo.
2. **Không tích luỹ lỗi đỏ** — "đỏ khi nào, sửa khi đó".
3. Nếu đỏ vì tính năng chưa làm (GAP) → đánh dấu skip **kèm lý do tường minh**.

Lý do của điều 2: khi có năm test đỏ cùng lúc, không ai biết cái nào gây ra cái nào, và
cả nhóm bắt đầu bỏ qua màu đỏ. Đó là lúc bộ test chết.

---

## 11.6. Ví dụ thật — kiểm chứng giao diện bằng ảnh chụp

Test logic không bắt được lỗi bố cục. PM-AGENT có luật riêng
(`.claude/rules/{prefix}-frontend-layout-verify.md`) cho việc này:

```
Sửa template → commit → [KIỂM TRA BỐ CỤC] → đạt? → tiếp
                              ↓ không đạt
                          Sửa lại → chụp lại
```

### Quy trình

1. **Xác định màn hình bị ảnh hưởng** — đọc frontend spec, lấy danh sách Screen ID.
2. **Chụp ảnh thực tế** bằng Playwright trong container:

```python
page = browser.new_page(viewport={'width': 1280, 'height': 800})
page.goto('http://app:<PORT>/<URL>')
page.screenshot(path='docs/screenshots/<screen-id>-actual.png', full_page=True)
```

3. **Đối chiếu từng tiêu chí UI/UX trong spec:**

```markdown
| Tiêu chí | Kết quả |
|----------|---------|
| Cancel bên trái, Primary bên phải | ✅ |
| Có empty state khi danh sách rỗng | ✅ |
| Bảng không tràn ngang trên mobile | ❌ |
```

4. Còn ô ❌ → **không được sang bước tiếp theo**.

### Điều kiện tiên quyết: spec phải có tiêu chí kiểm được

Luật ghi rõ: *"không có tiêu chí = không thể verify"*. Trước khi implement, mỗi màn
hình phải có wireframe + mục "Tiêu chí UI/UX" trong spec.

Đây là điểm cốt lõi và áp dụng được cho mọi loại kiểm chứng:

> **Kiểm chứng chỉ có ý nghĩa khi có tiêu chí viết trước.** Không có tiêu chí thì
> "đối chiếu" chỉ là cảm giác đúng, và cảm giác thì không lặp lại được.

---

## 11.7. Môi trường kiểm chứng phải nhất quán

Luật PM-AGENT: **browser test bắt buộc chạy trong Docker**, không chạy trực tiếp trên
máy cá nhân.

Lý do ghi trong luật: môi trường mỗi máy dev một khác → kết quả không nhất quán.
Container Playwright đảm bảo cùng phiên bản trình duyệt, cùng chế độ headless.

```bash
make dev-d          # app phải healthy trước
make test-browser   # chạy trong container
make test-all       # cả unit + browser
```

Bài học tổng quát: một bộ test cho kết quả khác nhau trên máy khác nhau thì **tệ hơn
không có test** — nó tạo tranh cãi "máy tôi chạy được" và làm mất niềm tin vào toàn bộ
bộ test.

---

## 11.8. Bẫy: test xanh nhưng tính năng vẫn hỏng

Đây là phần giá trị nhất của chương — những lỗi đã thực sự xảy ra trong PM-AGENT, nơi
test xanh mà tính năng vẫn không dùng được.

> **Bẫy 1 — Mock được dựng theo cách code *nghĩ*, không theo dữ liệu thật.**
> Fixture mô phỏng dữ liệu theo giả định của code. Test xanh vĩnh viễn, tính năng
> không bao giờ chạy được với dữ liệu thật.
> **Cách phòng:** mở dữ liệu thật ra xem trước khi dựng mock.

> **Bẫy 2 — Mock không chặn việc gán thuộc tính không tồn tại.**
> Fixture gán một phương thức không có trong lớp thật. Unit test xanh giả, chỉ lộ khi
> chạy đường thật.
> **Cách phòng:** đừng chỉ tin unit test — luôn có ít nhất một đường chạy thật.

> **Bẫy 3 — Quan hệ lazy-load ném lỗi khi chạy thật.**
> Đọc `task.children` trong async session ném `MissingGreenlet` → lỗi 500. Mock luôn
> xanh vì mock trả về list bình thường.
> **Cách phòng:** browser test hoặc gọi API thật cho mọi đường có truy cập quan hệ.

> **Bẫy 4 — Assert kiểu dict "pass mù".**
> Trong khung test YAML, viết `fields: {dict}` khiến runner coi đó là assert lồng nhau
> và **bỏ qua key lạ một cách im lặng**. Test xanh mà chẳng kiểm gì.
> **Cách phòng:** đọc kỹ ngữ nghĩa assert của khung test; bọc đúng cấu trúc
> (`returns:`) thay vì viết dict trần.

> **Bẫy 5 — Route tự dựng dict thiếu key mới.**
> Template nhận dict thiếu một khoá → Jinja coi là `Undefined` → hiểu nhầm thành "bật".
> Unit test xanh nhưng giao diện sai.
> **Cách phòng:** test render template thật, không chỉ test hàm dựng dict.

> **Bẫy 6 — Coi ERROR là "lỗi có sẵn, không liên quan".**
> Trạng thái ERROR trong pytest thường là **fixture lỗi thời sau refactor**, không phải
> lỗi nền vô hại.
> **Cách phòng:** mở ra xem trước khi dán nhãn "không liên quan".

> **Bẫy 7 — Chạy test phạm vi hẹp mà bỏ bớt cờ.**
> Bỏ một cờ trong lệnh gốc khiến fixture dọn dẹp xoá nhầm điểm mount, và **mọi** test
> lỗi ở giai đoạn setup — trông như hỏng toàn hệ thống.
> **Cách phòng:** giữ nguyên cờ của lệnh gốc, chỉ đổi đường dẫn phạm vi.

Mẫu số chung của bảy bẫy: **test xanh không phải bằng chứng tính năng chạy được.**
Nó chỉ là bằng chứng những gì được kiểm thì đúng.

---

## 11.9. Phân loại nguyên nhân khi test đỏ

Khi có test thất bại, phân loại trước rồi mới xử lý:

| Nguyên nhân | Xử lý |
|---|---|
| **Lỗi code** | Sửa code → chạy lại (không cần duyệt lại test doc) |
| **Test sai** (thiết kế TC lỗi) | Sửa TC, nhưng **báo người dùng trước khi sửa** |
| **Spec mâu thuẫn** | Dừng, báo, làm rõ spec trước khi động vào cái gì |

Quy tắc quan trọng: **không được làm xanh bằng cách skip test mà không có lý do hợp lệ.**

Ranh giới giữa loại 1 và loại 2 rất dễ bị lạm dụng. Khi AI (hoặc người) sửa test cho
khớp code thay vì sửa code cho khớp yêu cầu, bộ test mất hết giá trị. Đó là lý do luật
bắt buộc **báo trước khi sửa test case**.

---

## 11.10. Nghi ngờ kết quả tự báo cáo

Nguyên tắc cuối, và có lẽ quan trọng nhất:

> **AI báo "đã xong" không phải bằng chứng đã xong. Kết quả lệnh chạy mới là bằng chứng.**

Câu hỏi nên hỏi ở mọi task quan trọng:

```
Bạn đã kiểm chứng điều này bằng cách nào?
Chạy lệnh nào, đầu ra ra sao?
Có phần nào bạn chưa kiểm chứng được không?
```

Câu cuối đặc biệt giá trị. Nó cho phép AI thừa nhận giới hạn thay vì lấp đầy khoảng
trống bằng sự tự tin — và một danh sách "chưa kiểm chứng" trung thực đáng giá hơn
nhiều so với một câu "đã xong" trơn tru.

---

## 11.11. Bài tập

**Bài 1 — Kiểm kê phương tiện phản hồi.**
Liệt kê mọi cách AI có thể tự kiểm tra kết quả trong dự án của bạn. Cái nào chưa có mà
đáng có? Dựng cái rẻ nhất trước.

**Bài 2 — Test-first cho một bug.**
Lần sửa lỗi tới, viết trước hai test: một tái hiện lỗi (phải đỏ), một xác nhận đã sửa.
Chỉ sau đó mới sửa code.

**Bài 3 — Săn test xanh vô nghĩa.**
Chọn một test đang xanh. Cố tình phá hàm nó kiểm. Nếu test vẫn xanh → nó không kiểm gì
cả. Đối chiếu với bảy bẫy ở mục 9.8.

---

## Tóm tắt chương

- Câu hỏi đúng không phải "làm sao AI đúng ngay lần đầu" mà **"làm sao AI tự biết là
  nó sai"**.
- Trang bị phương tiện tự kiểm: test, log, ảnh chụp màn hình, type check, linter.
- **Test-first**: test viết sau chỉ chứng minh code chạy như code đã viết, không chứng
  minh yêu cầu được đáp ứng.
- Test case cần dữ liệu cụ thể, và người duyệt test không cần biết lập trình.
- Chạy test **ngay sau mỗi commit**; không tích luỹ lỗi đỏ.
- Kiểm chứng chỉ có nghĩa khi **tiêu chí được viết trước**.
- Môi trường kiểm chứng phải nhất quán, nếu không bộ test tự huỷ giá trị.
- **Test xanh ≠ tính năng chạy được** — xem bảy bẫy ở mục 9.8.
- "Đã xong" không phải bằng chứng; đầu ra của lệnh mới là.

> Chương tiếp: [12 — Kinh tế ngữ cảnh: nạp có chiến lược, tảng băng, nén](12-kinh-te-ngu-canh.md)
