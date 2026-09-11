# Chương 12 — Kinh tế ngữ cảnh: nạp có chiến lược, tảng băng, nén

> [← Chương 11](11-verification-feedback-loop.md) | [Mục lục](README.md) | [Chương 13 →](13-kinh-te-mo-hinh.md)

---

## 12.1. Vì sao ngữ cảnh là bài toán kinh tế

Chương 04 đã nói ngữ cảnh là tài nguyên hữu hạn. Chương này nói về **cách chi tiêu tài
nguyên đó**.

Có hai lý do độc lập buộc phải quản lý ngữ cảnh, và chúng đẩy về cùng một hướng:

```
LÝ DO 1 — CHẤT LƯỢNG              LÝ DO 2 — CHI PHÍ

Ngữ cảnh tăng                     Ngữ cảnh tăng
   ↓                                 ↓
Nhiễu tăng                        Số token tăng
   ↓                                 ↓
Mật độ thông tin hữu ích giảm     Tiền tăng
   ↓
Chất lượng giảm
```

Lý do 1 đi ngược trực giác nhiều người: **đưa thêm thông tin không phải lúc nào cũng
giúp**. Một phiên làm việc dài, ngữ cảnh đầy dần, thì lời dặn quan trọng ở đầu phiên
bị pha loãng giữa hàng nghìn dòng đã đọc.

Vì vậy mục tiêu không phải:

> ~~Dùng càng nhiều ngữ cảnh càng tốt.~~

Mà là:

> **Dùng lượng ngữ cảnh hữu ích tối thiểu.**

---

## 12.2. Ngữ cảnh gồm những gì

Nhiều người nghĩ ngữ cảnh chỉ là cuộc trò chuyện. Thực tế nó gồm ít nhất mười thứ,
tất cả cùng tranh nhau chỗ:

```
Hướng dẫn hệ thống
+ CLAUDE.md và rules (mọi tầng)
+ Memory
+ Định nghĩa skill đang nạp
+ Định nghĩa tool (mọi MCP server đang bật)
+ File đã đọc trong phiên
+ Kết quả lệnh đã chạy
+ Dữ liệu tìm kiếm/nghiên cứu
+ Dữ liệu đa phương tiện (ảnh, tài liệu)
+ Lịch sử hội thoại
──────────────────────────────
= Ngân sách context
```

Điều đáng chú ý: **một phiên có thể đã tiêu đáng kể ngữ cảnh trước khi bạn gõ câu đầu
tiên**, vì rules, memory, skill và mô tả tool đã chiếm chỗ sẵn.

### Đo trước khi tối ưu

Đừng đoán. Đo.

Claude Code có lệnh xem phân bổ ngữ cảnh hiện tại (thường là `/context` — kiểm bằng
`/help` cho phiên bản bạn đang dùng). Nó cho thấy từng mục đang tiêu bao nhiêu: hướng
dẫn hệ thống, memory, skill, tool, hội thoại.

Chạy nó **ngay khi mở phiên mới, trước khi gõ gì cả**. Con số đó là mức sàn bạn phải
trả ở mọi phiên — và thường lớn hơn người ta tưởng.

---

## 12.3. Kỹ thuật tảng băng

Nguyên tắc nền tảng của cả chương:

```
Không nạp toàn bộ mã nguồn
Không nạp toàn bộ tài liệu
Không nạp toàn bộ lịch sử
Không nạp toàn bộ dữ liệu
```

Thay vào đó:

```
              NGỮ CẢNH (phần nổi — nhỏ)
                     │
       ┌─────────────┼─────────────┐
       ↓             ↓             ↓
    Quy tắc       Memory     Việc đang làm
                                   │
                                   ↓
                        ┌── Truy xuất KHI CẦN ──┐
                        ↓          ↓            ↓
                      Đọc       Tìm kiếm      Mạng
              (phần chìm — lớn, chỉ chạm khi cần)
```

> **Không cần đưa toàn bộ thông tin vào ngữ cảnh. Chỉ cần cho AI khả năng lấy thông
> tin khi cần.**

Đây là lý do [chương 07 (Tools & MCP)](07-tools-va-mcp.md) quan trọng với chương này:
công cụ tìm kiếm tốt cho phép giữ phần nổi nhỏ mà không mất thông tin.

---

## 12.4. Nạp thông tin có chiến lược

Cách lãng phí nhất, và cũng phổ biến nhất:

```
Cách cũ:
Toàn bộ mã nguồn → Ngữ cảnh → Mô hình
```

Cách đúng:

```
Nhiệm vụ
   ↓
Tìm kiếm (grep/glob)         ← rẻ
   ↓
Danh sách file liên quan
   ↓
Đọc ĐÚNG đoạn cần            ← chỉ trả tiền cho phần dùng
   ↓
Suy luận
```

Con số minh hoạ:

```
Đọc cả file:      10.000 token
Tìm rồi đọc đoạn:  2.000 token
                  ────────────
                  ít dữ liệu hơn, ít nhiễu hơn, tập trung hơn
```

> **Không phải thông tin nào có sẵn cũng cần được nạp vào đầu AI.**

### Ví dụ thật — trích một trường thay vì đọc cả file

Skill `get-task` của PM-AGENT cần đúng một trường cấu hình trong `.mcp.json`. Chỉ dẫn
trong skill:

```
Chỉ trích đúng 1 field, KHÔNG bao giờ đọc nguyên .mcp.json
```

```bash
python3 -c "import json; d=json.load(open('.mcp.json')); \
print(d.get('mcpServers',{}).get('{prefix}',{}).get('env',{}).get('{PREFIX}_GET_TASK_EXCLUDE_STATUSES',''))"
```

Luật này ra đời vì lý do bảo mật (file chứa token thật — xem [mục 4.8](04-context-engineering.md)),
nhưng nó **đồng thời là kỹ thuật tiết kiệm ngữ cảnh**. Hai mục tiêu trùng nhau: thứ gì
không cần vào ngữ cảnh thì đừng đưa vào, vừa an toàn hơn vừa rẻ hơn.

### Ví dụ thật — gộp lời gọi hay đi cùng nhau

Tool `{prefix}_get_task_context` trả **task + Q&A đã có + taxonomy trong một lượt**, thay
vì ba tool riêng.

Ba lời gọi riêng tốn ba vòng và ba lần trả phí ngữ cảnh cho phần khung. Nguyên tắc
thiết kế MCP: **gộp những lời gọi luôn đi cùng nhau**.

---

## 12.5. Hiển thị theo nhu cầu

Một skill viết tốt có thể dài hàng trăm dòng. Nạp toàn bộ mọi skill vào mọi phiên là
lãng phí lớn — phần lớn skill không được dùng trong phiên đó.

Giải pháp là chia hai tầng:

```
Skill
├── Tên + mô tả        ← nạp sẵn, vài dòng
└── Hướng dẫn đầy đủ   ← chỉ nạp khi AI quyết định "tôi cần skill này"
```

> **Chỉ mở rộng thông tin khi có nhu cầu thực sự.**

### Vì sao `description` của skill lại quan trọng đến thế

Điều này giải thích một chi tiết ở [chương 06](06-skills-va-slash-commands.md) mà lúc
đó chưa nói hết lý do: `description` là **thứ duy nhất được nạp sẵn**. Toàn bộ quy
trình chi tiết nằm ở phần chìm.

Hệ quả thực tế:

| Nếu `description` | Thì |
|---|---|
| Mơ hồ | Skill không bao giờ được gọi — phần chìm vô dụng |
| Quá dài | Trả phí ở mọi phiên cho thứ hiếm khi dùng |
| Vừa đủ và cụ thể | Trả phí nhỏ, mở rộng khi cần |

Cùng một cơ chế áp dụng cho tài liệu dự án: `CLAUDE.md` giữ con trỏ, `docs/` giữ chi
tiết. PM-AGENT làm đúng vậy:

```markdown
docs/04-tech-stack.md — NGUỒN TRUTH cho tech decisions (đọc trước khi code)
```

Một dòng trong phần nổi, thay cho toàn bộ tài liệu ở phần chìm.

---

## 12.6. Subagent như một cơ chế tiết kiệm ngữ cảnh

[Chương 09](09-subagents-va-song-song.md) giới thiệu subagent để chia việc. Ở góc nhìn
kinh tế, nó còn là **bộ lọc ngữ cảnh**:

```
Không có subagent:
  Agent chính đọc 15 file → 15 file nằm trong ngữ cảnh đến hết phiên

Có subagent:
  Subagent đọc 15 file → trả về 20 dòng kết luận
  Agent chính chỉ mang theo 20 dòng đó
```

Đây là lý do subagent hiệu quả nhất ở đúng loại việc: **đọc nhiều, kết luận ngắn** —
tìm kiếm rộng, khảo sát module, review.

Ngược lại, subagent **lỗ** khi bạn đã biết chính xác file cần đọc: chi phí khởi tạo và
brief lớn hơn phần tiết kiệm được.

---

## 12.7. Nén ngữ cảnh — và cái giá của nó

Khi ngữ cảnh gần đầy, có thể nén: tóm tắt lịch sử thành bản cô đọng rồi tiếp tục.

```
Ngữ cảnh rất lớn → Nén → Ngữ cảnh cô đọng → Tiếp tục làm việc
```

Nhưng đây là đánh đổi, không phải phép màu:

```
Nén
 ↓
Ít token hơn
 ↓
CÓ THỂ MẤT CHI TIẾT
```

Thứ hay bị mất: kết quả lệnh đã chạy, chi tiết nhỏ trong file đã đọc, các nhánh thảo
luận đã bị loại.

> **Nén giúp kéo dài phiên làm việc, nhưng đừng coi nó là cách bảo vệ thông tin quan trọng.**

### Cách phòng thủ đúng

Thông tin quan trọng không nên chỉ tồn tại trong hội thoại. Nó nên được **ghi ra file**:

| Thông tin | Ghi vào đâu |
|---|---|
| Quyết định thiết kế | Spec hoặc `docs/` |
| Bài học từ lỗi | Memory ([mục 4.6](04-context-engineering.md)) |
| Trạng thái công việc dở | File bàn giao (HANDOFF) |
| Kế hoạch đang theo | File kế hoạch, không phải trong đầu phiên |

Khi ngữ cảnh bị nén, file vẫn còn. Đây là phòng thủ thật sự — không phải hy vọng bản
tóm tắt giữ đúng thứ mình cần.

---

## 12.8. Ví dụ thật — đo chi tiêu của một phiên

Session xử lý sự cố XSS của PM-AGENT (19/08) có bảng đo thật:

| Hoạt động | Token ước tính | % |
|---|---|---|
| Đọc ngữ cảnh nền (CLAUDE.md + ~10 rule) | ~15K | 7% |
| Truy vấn dữ liệu (≈10 lượt psql) | ~8K | 4% |
| Phân tích mã nguồn (~20 file, ~2.500 dòng) | ~45K | 20% |
| **1 subagent phản biện** | **~109K** | **49%** |
| Sửa file (~15 lượt) | ~20K | 9% |
| Chạy test/kiểm chứng (~15 lượt) | ~15K | 7% |
| Ghi chú + gọi MCP | ~6K | 3% |
| Hỏi người dùng (3 vòng) | ~3K | 1% |
| **Tổng** | **~221K** | |

Ba điều đọc được từ bảng này:

**1. Một subagent chiếm gần một nửa toàn phiên.** Không phải nó lãng phí — nó là thứ
phát hiện ra lỗ hổng Critical, đáng từng token. Nhưng nếu không đo, bạn sẽ không biết
điều đó và có thể vô tư gọi năm cái tương tự cho một task nhỏ.

**2. Ngữ cảnh nền tốn 15K trước khi làm gì.** Đây chính là chi phí của `CLAUDE.md`
508 dòng và 14 rule — trả ở **mọi** phiên, kể cả phiên sửa một dòng CSS.

**3. Phần tạo ra giá trị trực tiếp (sửa file) chỉ chiếm 9%.** Phần còn lại là hiểu,
kiểm chứng và phối hợp. Đây là phân bổ bình thường cho việc khó, không phải dấu hiệu
lãng phí — nhưng nó cho thấy tối ưu nên nhắm vào đâu.

> **Không đo thì không biết mình đang chi tiêu vào đâu.** Mỗi phiên quan trọng nên có
> một bảng như trên.

---

## 12.9. Bảy kỹ thuật, xếp theo hiệu quả

| # | Kỹ thuật | Tiết kiệm | Công sức |
|---|---|---|---|
| 1 | Tắt MCP không dùng trong dự án này | Cao — định nghĩa tool nằm trong mọi lượt | Rất thấp |
| 2 | Rút gọn `CLAUDE.md`, đẩy chi tiết sang `docs/` + con trỏ | Cao — trả ở mọi phiên | Thấp |
| 3 | Tìm kiếm rồi đọc đoạn, thay vì đọc cả file | Cao | Thấp |
| 4 | Dùng subagent cho việc đọc-nhiều-kết-luận-ngắn | Cao | Trung bình |
| 5 | Gộp các lời gọi tool luôn đi cùng nhau | Trung bình | Trung bình |
| 6 | Ghi thông tin quan trọng ra file thay vì giữ trong hội thoại | Trung bình (và chống mất khi nén) | Thấp |
| 7 | Chia phiên theo việc, đừng kéo một phiên qua nhiều việc khác nhau | Trung bình | Rất thấp |

Kỹ thuật 7 đơn giản nhưng hay bị bỏ: một phiên đã đọc 30 file cho việc A thì mang toàn
bộ 30 file đó sang việc B. **Mở phiên mới cho việc mới** rẻ hơn nhiều so với tiếp tục.

---

## 12.10. Bẫy thường gặp

> **Bẫy 1 — Tối ưu ngữ cảnh bằng cách cắt thông tin cần thiết.**
> Cắt đến mức AI phải đoán. **Cách sửa:** mục tiêu là *hữu ích tối thiểu*, không phải
> *tối thiểu*. Cắt phần suy ra được từ repo, giữ phần đi ngược mặc định.

> **Bẫy 2 — Coi nén là giải pháp.**
> Để phiên chạy đến đầy rồi nén, lặp lại nhiều lần. Mỗi lần nén mất thêm chi tiết.
> **Cách sửa:** ghi ra file trước khi nén; chia phiên sớm hơn.

> **Bẫy 3 — Đọc cả file vì tiện.**
> `cat` một file 2000 dòng để xem một hàm. **Cách sửa:** tìm rồi đọc đoạn.

> **Bẫy 4 — Bật mọi MCP "cho chắc".**
> Mỗi server bật là định nghĩa tool nằm trong mọi lượt trò chuyện. **Cách sửa:** bật
> theo dự án ([mục 7.6](07-tools-va-mcp.md)).

> **Bẫy 5 — Không bao giờ đo.**
> Tối ưu theo cảm giác. **Cách sửa:** chạy lệnh xem phân bổ ngữ cảnh ở đầu phiên và
> khi thấy chất lượng tụt.

---

## 12.11. Bài tập

**Bài 1 — Đo mức sàn.**
Mở phiên mới, chưa gõ gì, xem phân bổ ngữ cảnh. Ghi lại con số. Đó là thuế bạn trả ở
mọi phiên.

**Bài 2 — Cắt 30%.**
Từ con số trên, tìm cách giảm 30%: tắt MCP không dùng, rút `CLAUDE.md`, bỏ skill không
còn dùng. Đo lại.

**Bài 3 — So sánh hai cách đọc.**
Cùng một câu hỏi về codebase, làm hai lần: (a) đọc cả file rồi trả lời, (b) tìm kiếm
rồi đọc đoạn. So sánh ngữ cảnh tiêu tốn và chất lượng câu trả lời.

---

## Tóm tắt chương

- Quản lý ngữ cảnh có **hai lý do độc lập**: chất lượng giảm khi nhiễu tăng, và chi phí.
- Mục tiêu là **lượng ngữ cảnh hữu ích tối thiểu**, không phải tối đa cũng không phải
  tối thiểu.
- **Kỹ thuật tảng băng**: giữ phần nổi nhỏ, cho AI khả năng lấy phần chìm khi cần.
- **Nạp có chiến lược**: tìm kiếm → file liên quan → đọc đúng đoạn.
- **Hiển thị theo nhu cầu**: skill nạp sẵn tên + mô tả, chi tiết chỉ khi được gọi —
  đây là lý do `description` quyết định skill có được dùng hay không.
- **Subagent là bộ lọc ngữ cảnh**: đọc nhiều, trả về kết luận ngắn.
- **Nén là đánh đổi, không phải phép màu** — thông tin quan trọng phải nằm trong file.
- **Không đo thì không biết chi tiêu vào đâu.**

> Chương tiếp: [13 — Kinh tế mô hình: phân tầng, phân bổ, xử lý theo lô](13-kinh-te-mo-hinh.md)
