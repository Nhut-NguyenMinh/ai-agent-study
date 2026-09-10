# Chương 11 — Chọn đúng công cụ AI

> [← Chương 10](10-automation-ngoai-codebase.md) | [Mục lục](README.md) | [Chương 12 →](12-lo-trinh-va-case-study.md)

---

## 11.1. Sai lầm: chạy theo công cụ

Mỗi tuần có công cụ AI mới. Phản xạ thường thấy:

```
Công cụ mới → học → công cụ mới → học → công cụ mới → học
```

Kết quả: biết tên nhiều công cụ, không giải quyết được vấn đề nào trọn vẹn.

Hai câu hỏi phân biệt cách tiếp cận:

| Câu hỏi sai | Câu hỏi đúng |
|---|---|
| "AI nào tốt nhất?" | "Bài toán của tôi thuộc loại nào?" |
| "Công cụ nào đang hot?" | "Công cụ nào được thiết kế cho loại bài toán này?" |

> **Không có AI tốt nhất cho mọi việc. Chỉ có AI phù hợp nhất với từng bài toán.**

Học theo **bài toán**, không học theo danh sách công cụ. Danh sách đổi mỗi quý; cách
phân loại bài toán thì bền.

---

## 11.2. Năm lớp năng lực

Thay vì học từng công cụ, nắm năm lớp năng lực. Bất kỳ công cụ mới nào cũng rơi vào
một trong năm lớp này.

```
1. PROMPTING            → biết giao tiếp với AI
2. AI TOOL CATEGORIES   → biết chọn đúng loại AI
3. AUTOMATION + AGENTS  → biết biến công việc thành quy trình
4. OPEN SOURCE AI       → biết làm chủ dữ liệu và hạ tầng
5. VIBE CODING          → biết biến ý tưởng thành phần mềm
```

Chúng không rời rạc:

```
              VẤN ĐỀ / MỤC TIÊU
                      ↓
                 PROMPTING
                      ↓
                 CHỌN CÔNG CỤ
             (General / Research / Chuyên biệt)
                      ↓
        ┌─────────────┴─────────────┐
        ↓                           ↓
  AUTOMATION / AGENT          OPEN SOURCE / LOCAL
        └─────────────┬─────────────┘
                      ↓
                VIBE CODING
             (tự xây khi không có công cụ phù hợp)
```

Lớp 1 đã nói ở [chương 02](02-prompting-va-context.md); lớp 3 ở
[chương 07](07-hooks-va-guardrails.md) và [chương 10](10-automation-ngoai-codebase.md).
Chương này nói lớp 2, 4, 5.

---

## 11.3. Ba nhóm công cụ

### Nhóm A — General / Reasoning

Ví dụ: Claude, ChatGPT, Gemini.

Phù hợp với: tư duy logic, viết nội dung, lập trình, phân tích, tóm tắt, brainstorm,
chiến lược, giải quyết vấn đề tổng quát.

**Dùng nhiều model như một hội đồng:**

```
Model A → tạo phương án
   ↓
Model B → phản biện
   ↓
Con người → quyết định
```

Không cần trung thành với một model. Model thứ hai đọc phương án mà không mang theo lý
do đã chọn nó — cùng cơ chế với subagent phản biện ở [chương 08](08-subagents-va-song-song.md).

### Nhóm B — Research

Ví dụ: Perplexity, NotebookLM, Consensus.

Phù hợp khi cần: nghiên cứu, học thuật, khảo sát thị trường, kiểm chứng thông tin,
truy nguồn, tổng hợp nhiều tài liệu.

> Khi **độ chính xác và khả năng truy nguồn** quan trọng hơn sự sáng tạo, hãy ưu tiên
> công cụ nghiên cứu thay vì model tổng quát.

### Nhóm C — Chuyên biệt

Mỗi loại đầu ra có công cụ được thiết kế riêng: tạo hình ảnh, giọng nói, video, hoạt
hình, thiết kế, lập trình trong IDE...

> **Danh sách tên sản phẩm thay đổi rất nhanh. Điều cần nhớ là cách phân loại, không
> phải danh sách.**

### Quy tắc chọn

```
Đừng hỏi:  "Công cụ nào nổi tiếng nhất?"
Hãy hỏi:   "Công cụ nào được thiết kế cho bài toán này?"
```

---

## 11.4. Kết nối AI với tri thức nội bộ

Một model tổng quát không biết gì về doanh nghiệp của bạn. Câu trả lời của nó sẽ đúng
về mặt chung chung và vô dụng về mặt cụ thể.

```
Tài liệu nội bộ
      ↓
Cơ sở tri thức
      ↓
   AI Agent
      ↓
Nhân viên / Khách hàng
```

Những gì đáng đưa vào cơ sở tri thức: chính sách nhân sự, quy trình onboarding, FAQ nội
bộ, quy trình xử lý khiếu nại, chính sách hoàn tiền, quy trình chăm sóc khách hàng.

```
AI không có ngữ cảnh
    → chatbot chung chung

AI + dữ liệu doanh nghiệp
    → trợ lý hiểu doanh nghiệp
```

Đây chính là [Context Engineering](03-context-engineering.md) áp dụng ngoài phạm vi
lập trình. Cùng một nguyên tắc, khác bối cảnh.

---

## 11.5. Open source và local AI

Hai cách tiếp cận:

```
Cloud AI       → gọi API của nhà cung cấp
Local / Open   → chạy model trên hạ tầng của mình
```

> **Lưu ý về giấy phép:** "open source", "open weights" và "được dùng thương mại" là
> ba chuyện khác nhau. Phải kiểm giấy phép của từng model và từng phiên bản trước khi
> triển khai — đừng suy ra từ tên gọi.

### Bốn lý do cân nhắc local

**1. Quyền riêng tư và bảo mật.** Dữ liệu được xử lý trên hạ tầng kiểm soát được thay
vì gửi ra ngoài. Đáng cân nhắc với dữ liệu khách hàng, tài liệu nội bộ, thông tin nhạy cảm.

**2. Kiểm soát.** Giảm phụ thuộc vào thay đổi chính sách, thay đổi API, thay đổi giá,
giới hạn dịch vụ.

**3. Chi phí và độ trễ.** Với một số khối lượng công việc, chạy local giảm chi phí về
lâu dài và giảm độ trễ. Nhưng phải tính đủ: GPU, điện, máy chủ, bảo trì, vận hành.

> **Đừng mặc định local rẻ hơn cloud.** Chi phí ẩn của vận hành thường lớn hơn hoá đơn
> API cho tới một quy mô khá lớn.

**4. Tuỳ biến.** Fine-tune, xây model chuyên biệt, tối ưu model nhỏ cho một nhiệm vụ.

> Không phải bài toán nào cũng cần model lớn nhất. Một model nhỏ được tối ưu đúng nhiệm
> vụ nhiều khi phù hợp hơn.

---

## 11.6. Vibe Coding

Thay đổi trong cách xây phần mềm:

```
Trước:  Ý tưởng → học lập trình → học framework → viết code → gỡ lỗi → triển khai
Nay:    Ý tưởng → mô tả yêu cầu → AI viết code → chạy thử → phát hiện lỗi
        → yêu cầu sửa → kiểm thử → lặp lại
```

### Vibe Coding không có nghĩa là "AI code xong là xong"

Người dùng vẫn phải biết:

- Mình muốn xây gì, cho ai?
- Logic nghiệp vụ là gì?
- Hệ thống gồm những thành phần nào?
- Dữ liệu lưu ở đâu?
- Kết quả có đúng không?
- Có lỗ hổng bảo mật không?

Năng lực cần thiết dịch chuyển:

```
Từ:   Kỹ năng viết code thuần tuý

Sang: Giải quyết vấn đề
      + Tư duy sản phẩm
      + Tư duy hệ thống
      + Viết yêu cầu
      + Đánh giá kết quả
```

### Kiến thức lập trình vẫn cần

Không cần trở thành lập trình viên chuyên nghiệp, nhưng cần hiểu đủ để **kiểm soát**:
logic lập trình, API, frontend, backend, database, Git, xác thực, triển khai, kiến trúc
hệ thống.

> Mục tiêu: đọc và hiểu ở mức đủ để kiểm soát sản phẩm do AI tạo ra.

Không có mức đó, bạn không phải người dùng AI — bạn là người chuyển tiếp đầu ra của AI
mà không biết nó đúng hay sai.

### Bẫy lớn nhất — Vibe Coding mà không kiểm thử

```
AI viết code ≠ code đúng
```

Luôn:

```
Xây → Chạy → Kiểm thử → Soi kết quả → Sửa → Kiểm thử lại
```

Xem chi tiết ở [chương 09](09-verification-feedback-loop.md), đặc biệt bảy bẫy "test
xanh nhưng tính năng vẫn hỏng".

---

## 11.7. Khung 7 câu hỏi ra quyết định

Gặp bài toán mới, đi qua bảy câu này theo thứ tự:

**1. Tôi muốn đạt kết quả gì?**
Định nghĩa kết quả trước khi chọn công cụ. Bước này bị bỏ qua nhiều nhất.

**2. AI cần biết những gì?**
Ngữ cảnh, dữ liệu, tài liệu tham chiếu, ràng buộc.

**3. Đây là loại công việc gì?**
Reasoning tổng quát? Nghiên cứu? Hình ảnh? Video? Giọng nói? Lập trình? Thiết kế?

**4. Công việc có lặp lại không?**
→ Có: cân nhắc automation ([chương 10](10-automation-ngoai-codebase.md)).

**5. Có cần AI tự quyết định nhiều bước không?**
→ Có: cân nhắc agent. → Không: một workflow theo luật sẽ ổn định hơn.

**6. Dữ liệu có nhạy cảm hoặc cần kiểm soát hạ tầng không?**
→ Có: cân nhắc local/open model.

**7. Không có công cụ nào phù hợp?**
→ Cân nhắc tự xây bằng Vibe Coding.

---

## 11.8. Năm sai lầm phổ biến

> **Sai lầm 1 — Chạy theo công cụ.**
> Học liên tục nhưng không tạo ra kết quả. **Sửa:** học theo bài toán.

> **Sai lầm 2 — Một AI làm tất cả.**
> Dùng một công cụ cho mọi loại việc. **Sửa:** phân loại trước khi chọn.

> **Sai lầm 3 — Prompt một lần rồi bỏ.**
> Kết quả đầu chưa đạt thì kết luận "AI không làm được". **Sửa:** Evaluate → Iterate.

> **Sai lầm 4 — Tin AI tuyệt đối.**
> AI có thể sai, thiếu ngữ cảnh, hoặc bịa thông tin nghe rất hợp lý.
> **Sửa:** con người giữ vai trò **người đánh giá và người quyết định**.

> **Sai lầm 5 — Vibe Coding mà không kiểm thử.**
> **Sửa:** xây → chạy → kiểm thử → soi → sửa → kiểm thử lại.

---

## 11.9. AI không thay thế "bộ não chính"

AI làm được: phân tích, viết, lập trình, nghiên cứu, đề xuất, tạo phương án.

Con người vẫn phải: xác định mục tiêu, quyết định hướng đi, đánh giá chất lượng, kiểm
chứng thông tin, chịu trách nhiệm cho quyết định cuối.

### Câu hỏi ở tầng cao hơn

Đừng chỉ hỏi:

> "AI trả lời gì?"

Hãy hỏi:

> **"AI đang giúp tôi suy nghĩ tốt hơn như thế nào?"**

AI có thể đóng vai: người phản biện, người đưa góc nhìn thứ hai, người brainstorm,
người mô phỏng chuyên gia, người kiểm tra logic. Những vai đó tạo giá trị lớn hơn nhiều
so với vai "người trả lời".

---

## 11.10. Bài tập

**Bài 1 — Phân loại công việc.**
Liệt kê mười việc bạn dùng AI trong tháng qua. Xếp từng việc vào nhóm A/B/C. Có việc
nào đang dùng nhầm nhóm không?

**Bài 2 — Áp khung 7 câu hỏi.**
Chọn một bài toán đang vướng. Trả lời bảy câu ở mục 11.7. Câu nào bạn chưa trả lời được
chính là chỗ cần làm rõ trước khi chọn công cụ.

**Bài 3 — Hội đồng hai model.**
Lấy một quyết định kỹ thuật đang phân vân. Hỏi model A đề xuất phương án, đưa phương án
đó cho model B phản biện, rồi tự quyết định. So sánh với việc chỉ hỏi một model.

---

## Tóm tắt chương

- Không có AI tốt nhất cho mọi việc — chỉ có AI phù hợp nhất với từng bài toán.
- Học theo **bài toán**, không theo danh sách công cụ.
- Năm lớp năng lực: Prompting → Chọn công cụ → Automation/Agent → Open source →
  Vibe Coding.
- Ba nhóm công cụ: General (reasoning), Research (truy nguồn), Chuyên biệt (một loại
  đầu ra).
- AI + dữ liệu nội bộ = trợ lý hiểu doanh nghiệp; AI không ngữ cảnh = chatbot chung chung.
- Local AI đáng cân nhắc vì riêng tư, kiểm soát, chi phí, tuỳ biến — nhưng **đừng mặc
  định là rẻ hơn**, và phải kiểm giấy phép.
- Vibe Coding chuyển trọng tâm từ viết code sang **giải quyết vấn đề + đánh giá kết quả**.
- Con người giữ vai trò **người đánh giá và người quyết định** — đó là vai không giao được.

> Chương tiếp: [12 — Lộ trình & Case study](12-lo-trinh-va-case-study.md)
