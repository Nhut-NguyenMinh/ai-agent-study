# Chương 07 — Hooks & Guardrails

> [← Chương 06](06-tools-va-mcp.md) | [Mục lục](README.md) | [Chương 08 →](08-subagents-va-song-song.md)

---

## 7.1. Vấn đề: lời dặn bị quên

Bạn viết vào `CLAUDE.md`:

```markdown
Sau khi sửa code, luôn cập nhật tài liệu kỹ thuật tương ứng.
```

Nó hoạt động vài lần. Rồi một phiên dài, ngữ cảnh đầy dần, và dòng đó trôi đi. Không ai
phát hiện. Tài liệu lệch dần khỏi code, và ba tuần sau nó thành cái bẫy cho chính bạn.

Câu hỏi then chốt:

> **Một lời dặn quan trọng mà chỉ tồn tại dưới dạng văn bản trong ngữ cảnh — ai bảo
> đảm nó được thực hiện?**

Câu trả lời: không ai cả. Đó chính là lý do hook tồn tại.

---

## 7.2. Hook là gì và vì sao nó khác rule

Hook là **script do harness chạy** tại một thời điểm xác định trong vòng đời phiên
làm việc. Nó không nằm trong ngữ cảnh, không phụ thuộc trí nhớ model, và không thể bị
model bỏ qua.

| | Rule / `CLAUDE.md` | Hook |
|---|---|---|
| Ai thực thi | Model, dựa trên trí nhớ | Harness, bằng code |
| Có thể bị quên không | **Có** | Không |
| Có tốn context không | Có, mọi phiên | Không |
| Diễn đạt được điều gì | Ý định, quy tắc mềm | Điều kiện cứng, kiểm tra được |
| Khi nào nên dùng | Hướng dẫn chung | Điều **không được phép quên** |

Trích nguyên văn từ hook thật của PM-AGENT — đây là lời giải thích ngắn gọn nhất về sự
khác biệt:

```bash
# Vì sao cần hook chứ không phải một dòng trong CLAUDE.md: rule chỉ là lời dặn,
# Claude quên là không có gì phát hiện ra. Hook do harness chạy, không phụ thuộc
# trí nhớ của model.
```

---

## 7.3. Đăng ký hook

Hook khai báo trong `.claude/settings.json`:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          { "type": "command", "command": "<đường dẫn tuyệt đối>/.claude/hooks/post-edit-doc-reminder.sh" }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          { "type": "command", "command": "<đường dẫn tuyệt đối>/.claude/hooks/task-report-reminder.sh" }
        ]
      }
    ]
  }
}
```

Hai thời điểm dùng nhiều nhất:

| Sự kiện | Chạy khi | Dùng cho |
|---|---|---|
| `PostToolUse` | Ngay sau một tool chạy xong (lọc bằng `matcher`) | Nhắc theo ngữ cảnh, kiểm tra tức thì |
| `Stop` | Khi phiên/lượt kết thúc | Kiểm tra tổng kết, nhắc việc còn thiếu |

Chi tiết cú pháp đầy đủ ở [Phụ lục A](A-phu-luc-cu-phap.md).

---

## 7.4. Ví dụ thật 1 — nhắc đồng bộ tài liệu (`PostToolUse`)

Hook `post-edit-doc-reminder.sh` chạy sau mỗi lần sửa file:

```bash
#!/usr/bin/env bash
# Hook: PostToolUse — nhắc sync doc sau khi edit .py files

INPUT=$(cat)          # harness đưa tool input vào stdin dưới dạng JSON

FILE_PATH=$(echo "$INPUT" | python3 -c "
import json, sys
data = json.load(sys.stdin)
print(data.get('tool_input', {}).get('file_path', ''))
" 2>/dev/null)

# Chỉ nhắc khi sửa .py trong app/ — không nhắc doc, test, migration
if [[ "$FILE_PATH" == */app/*.py ]] && \
   [[ "$FILE_PATH" != */test* ]] && \
   [[ "$FILE_PATH" != */alembic/* ]] && \
   [[ "$FILE_PATH" != */__init__.py ]]; then

  echo "DOC SYNC REMINDER"
  echo "File đã sửa: $FILE_PATH"
  echo "Sau khi hoàn thành implementation, chạy /doc-sync-code"
fi
```

Ba điểm thiết kế đáng học:

**1. Hook đọc dữ liệu tool từ stdin.** Nó biết file nào vừa bị sửa, nên nhắc được đúng
ngữ cảnh thay vì nhắc chung chung.

**2. Điều kiện hẹp.** Bốn mệnh đề loại trừ: không nhắc khi sửa test, migration, hay
`__init__.py`. Nhắc sai chỗ là tiếng ồn, và tiếng ồn làm người ta bỏ qua cả lúc cần đọc.

**3. Nó nhắc, không tự làm.** Hook là shell script — nó không biết thay đổi vừa rồi có
ý nghĩa gì. Việc viết tài liệu vẫn thuộc về AI, thứ duy nhất còn nhớ ngữ cảnh.

---

## 7.5. Ví dụ thật 2 — nhắc gửi báo cáo (`Stop`)

Hook này tinh vi hơn, và ba quyết định thiết kế trong nó áp dụng được cho mọi hook.

```bash
#!/usr/bin/env bash
# Hook: Stop — nhắc gửi báo cáo task khi phiên đã sửa code mà chưa báo cáo.
#
# Hook KHÔNG tự gửi báo cáo: nó là shell script, nó không biết phiên vừa làm gì.
# Nó chỉ nhắc, việc viết nội dung vẫn là của Claude — người duy nhất còn nhớ.
#
# Điều kiện nhắc: working tree có thay đổi trong app/ hoặc tests/. Chỉ sửa tài liệu
# thì không nhắc — báo cáo cho mỗi lần sửa chữ là nhiễu, và nhiễu thì người ta tắt hook.

set -u
REPO_ROOT="$(cd "$(dirname "${BASH_SOURCE[0]}")/../.." && pwd)"
cd "$REPO_ROOT" || exit 0

# (1) Chỉ nhắc khi thật sự có động vào code
SO_FILE=$(git status --porcelain -- app tests 2>/dev/null | grep -cE '\.(py|html)$')
[ "${SO_FILE:-0}" -eq 0 ] && exit 0

# (2) Mỗi phiên chỉ nhắc một lần — nhắc sau MỖI lượt sẽ thành tiếng ồn,
#     và tiếng ồn làm người ta bỏ qua đúng lúc cần đọc.
DAU_MOC="${TMPDIR:-/tmp}/{prefix}-report-reminded-$(date +%F)-${CLAUDE_SESSION_ID:-$PPID}"
[ -f "$DAU_MOC" ] && exit 0
touch "$DAU_MOC" 2>/dev/null

# (3) Thông điệp nói rõ HẬU QUẢ và ĐƯỜNG ĐI TIẾP
cat <<'MSG'
NHẮC GỬI BÁO CÁO TASK

Phiên này đã sửa code nhưng chưa thấy báo cáo task nào được gửi.

Báo cáo task (`task_reports`) là nguồn DUY NHẤT mà Báo cáo PM đọc — không gửi
thì việc hôm nay không xuất hiện ở đâu cả, kể cả khi code đã chạy.

Gửi ngay lúc còn nhớ việc:  /task-report
Chốt cả ngày (nhiều task):  /daily-task-report  rồi  /personal-report-review
MSG
```

### Ba nguyên tắc rút ra

**Nguyên tắc 1 — Điều kiện kích hoạt phải hẹp và kiểm chứng được.**

Hook không hỏi "phiên này có quan trọng không" (không kiểm chứng được). Nó hỏi
"working tree có thay đổi `.py` hoặc `.html` trong `app/` hay `tests/` không" — một
câu hỏi có câu trả lời chính xác.

**Nguyên tắc 2 — Chống lặp.**

Dòng đánh dấu theo ngày + phiên đảm bảo mỗi phiên chỉ nhắc một lần. Đây là chi tiết
quyết định hook sống hay chết: một hook nhắc mười lần sẽ bị tắt trong tuần đầu tiên.

> **Tiếng ồn là kẻ giết hook.** Một hook nhắc đúng lúc thì được đọc; một hook nhắc mọi
> lúc thì bị bỏ qua — kể cả lần nó đúng.

**Nguyên tắc 3 — Thông điệp nói hậu quả, không chỉ nói việc.**

So sánh:

```
Kém:  "Nhớ gửi báo cáo task."
Tốt:  "Báo cáo task là nguồn DUY NHẤT mà Báo cáo PM đọc — không gửi thì việc
       hôm nay không xuất hiện ở đâu cả, kể cả khi code đã chạy.
       Gửi ngay: /task-report"
```

Bản tốt trả lời được câu "nếu bỏ qua thì sao" và chỉ luôn lệnh cần gõ. Một lời nhắc
không nói hậu quả sẽ bị coi là nghi thức.

---

## 7.6. Guardrail bằng luật — ví dụ an toàn Git

Không phải guardrail nào cũng là script. Có những ranh giới diễn đạt bằng luật thì
đủ, miễn là luật đủ cụ thể.

**Ví dụ thật — `.claude/rules/{prefix}-git-safety.md`** phân ba tầng:

### Tầng 1 — Cấm nếu chưa có lệnh rõ ràng từ người dùng

```
git push (mọi biến thể, kể cả --force-with-lease)
git commit (kể cả --amend)
git merge / git rebase / git tag
git branch -d / -D
git reset (mọi chế độ)
git checkout . / git restore .
git clean -f / -fd
git stash drop / clear / pop
```

Đặc điểm chung: **mất việc không hoàn tác được**. Không phải "nguy hiểm về lý thuyết"
mà là "gõ nhầm là mất buổi làm".

### Tầng 2 — Cấm trên nhánh chính, kể cả khi được yêu cầu

```
git push --force lên main / master / develop / staging / production
git reset --hard trên các nhánh đó
```

Ở đây luật ghi rõ: nếu người dùng yêu cầu thì **cảnh báo và đề xuất phương án khác**,
chứ không im lặng làm theo.

### Tầng 3 — Cấm thao tác trên remote, kể cả khi được yêu cầu

```
gh pr merge / glab mr merge
gh pr review --approve
```

Lý do được ghi rõ trong luật: **merge vào remote là trách nhiệm của chủ repository, không
phải của developer**. Đây không phải hạn chế kỹ thuật mà là ranh giới tổ chức — và
ranh giới tổ chức cũng cần được viết ra, nếu không AI sẽ tưởng "có quyền tức là được phép".

### Danh sách được phép — quan trọng ngang danh sách cấm

```
git status / diff / log / show / blame / shortlog
git branch (liệt kê) / remote -v
git fetch (chỉ tải về, KHÔNG đổi working tree)
git stash list / rev-parse / describe
```

Có danh sách trắng thì AI không phải hỏi xin phép cho từng lệnh chỉ-đọc. Guardrail
tốt vừa chặn cái nguy hiểm vừa **mở đường cho cái an toàn** — nếu không, người dùng
sẽ tắt hết để đỡ phiền.

---

## 7.7. Ranh giới: cái gì cho AI, cái gì cho hệ thống

Đây là nguyên tắc kiến trúc quan trọng nhất của chương, và nó áp dụng cho cả code lẫn
automation.

```
Đầu vào phi cấu trúc
        ↓
      AI  ← hiểu ngữ nghĩa, ý định, ngôn ngữ
        ↓
Đầu ra có cấu trúc
        ↓
   Kiểm tra hợp lệ  ← hệ thống
        ↓
  Logic xác định     ← hệ thống
        ↓
     Hành động
```

Bảng phân công:

| Loại việc | Giao cho | Vì sao |
|---|---|---|
| Đọc email và đoán ý khách hàng | AI | Không viết được thành `if/else` |
| Phân loại nội dung tự do | AI | Cần hiểu ngôn ngữ |
| Tóm tắt, trích thông tin | AI | Cần hiểu ngữ cảnh |
| "Số tiền > 10 triệu thì cần duyệt" | Hệ thống | Quy tắc chính xác, phải lặp lại đúng 100% |
| "Khách này đã có trong CSDL chưa" | Hệ thống | Tra cứu, không phải phán đoán |
| Gửi email, ghi CSDL, xoá dữ liệu | Hệ thống | Hành động có hậu quả |

> **Quy tắc kiểm tra nhanh: nếu logic viết được thành `if/else` thì nó thuộc về code,
> không thuộc về AI.**

Lý do không phải vì AI làm sai — mà vì AI **không xác định**. Cùng một đầu vào có thể
cho kết quả khác nhau. Với việc phân loại email thì chấp nhận được; với việc quyết
định có duyệt chi 10 triệu hay không thì không.

---

## 7.8. Đầu ra có cấu trúc

Khi kết quả của AI sẽ được máy khác dùng, đừng nhận văn bản tự do.

```
Kém:  "Email này có vẻ là yêu cầu hỗ trợ, khá gấp."

Tốt:  {
        "category": "support",
        "priority": "high",
        "summary": "Khách yêu cầu hoàn tiền do sản phẩm lỗi",
        "confidence": 0.92
      }
```

Rồi hệ thống xử lý tiếp:

```
Đầu ra AI → Kiểm tra schema → Áp quy tắc nghiệp vụ → Hành động
```

Trường `confidence` cho phép thêm một tầng an toàn: dưới ngưỡng thì chuyển cho người
xem thay vì tự động chạy tiếp.

---

## 7.9. Human-in-the-loop theo mức rủi ro

Không phải việc gì cũng nên tự động hoàn toàn. Chia theo mức độ khó hoàn tác:

| Mức rủi ro | Ví dụ | Cơ chế |
|---|---|---|
| **Thấp** | Gắn nhãn, ghi log, tạo bản nháp | Tự động hoàn toàn |
| **Trung bình** | Ghi CSDL, cập nhật bản ghi | Tự động + kiểm tra hợp lệ + ghi log |
| **Cao** | Gửi email cho khách, đổi production, xoá dữ liệu, giao dịch tiền, đổi quyền | **Người phê duyệt trước khi thực thi** |

Với mức cao, luồng đúng là:

```
AI chuẩn bị hành động
      ↓
Trình bày cho người xem
      ↓
Người phê duyệt
      ↓
Hệ thống thực thi
      ↓
Ghi log kiểm toán
```

Điểm cốt lõi: **AI chuẩn bị, hệ thống thực thi, người quyết định**. AI không tự bấm nút.

### Ví dụ thật — hai cổng dừng của PM-AGENT

Quy trình phát triển của PM-AGENT có đúng hai cổng cần người, đặt ở hai chỗ đắt nhất
nếu sai:

```
test-doc-first  → [NGƯỜI DUYỆT TÀI LIỆU TEST]  → implement
feature-verify  → [NGƯỜI XÁC NHẬN ĐÓNG]        → doc-sync
```

Và một guardrail bằng luật ở tầng khác: skill `qa-filter` quy định AI phải hỏi người
đang ngồi cùng phiên **trước**, chỉ khi họ xác nhận không trả lời được thì mới đẩy câu
hỏi lên hệ thống cho PM. Mục đích: lọc bớt câu hỏi rác — không phải mọi thắc mắc của
AI đều cần làm phiền PM.

---

## 7.10. Khi nào leo thang từ rule lên hook

Không phải luật nào cũng cần thành hook. Thang leo:

```
1. Viết vào CLAUDE.md / rule
        ↓ vẫn bị quên
2. Đưa vào skill của loại việc đó
        ↓ vẫn bị quên
3. Dựng hook
        ↓ vẫn bị vi phạm
4. Chặn cứng ở tầng hệ thống (scope quyền, quyền file, CI gác cổng)
```

Tiêu chí leo lên bậc tiếp theo:

| Dấu hiệu | Hành động |
|---|---|
| Quên một lần | Không làm gì, có thể là ngẫu nhiên |
| Quên hai lần trở lên | Chuyển thành hook |
| Vi phạm gây hậu quả không hoàn tác được | Chặn cứng ở tầng hệ thống, đừng dừng ở hook |

Ví dụ bậc 4 trong PM-AGENT: quyền gán task không được kiểm bằng lời dặn mà bằng **scope
của token** — thiếu `task:assign` thì server trả 403, không cách nào lách.

---

## 7.11. Bẫy thường gặp

> **Bẫy 1 — Hook quá ồn.**
> Nhắc sau mỗi lượt, mỗi file. **Cách sửa:** thu hẹp điều kiện + thêm dấu mốc chống lặp.

> **Bẫy 2 — Hook cố làm việc của AI.**
> Script tự viết báo cáo hoặc tự sửa code. Nó không có ngữ cảnh nên sẽ làm sai.
> **Cách sửa:** hook phát hiện và nhắc; AI làm nội dung.

> **Bẫy 3 — Hook thất bại âm thầm.**
> Script lỗi cú pháp, không ai biết, và mọi người tưởng guardrail đang chạy.
> **Cách sửa:** chạy thử script bằng tay sau khi viết; dùng `set -u`; thoát bằng
> `exit 0` ở nhánh không áp dụng.

> **Bẫy 4 — Nhầm guardrail với ma sát.**
> Bắt xin phép cho cả `git status`. Người dùng sẽ tắt hết. **Cách sửa:** có danh sách
> trắng cho thao tác chỉ-đọc.

> **Bẫy 5 — Giao quyết định nghiệp vụ quan trọng cho AI.**
> "AI xem đơn này có nên duyệt không." **Cách sửa:** AI trích thông tin và đánh giá;
> quy tắc duyệt nằm trong code.

---

## 7.12. Bài tập

**Bài 1 — Tìm lời dặn hay bị quên.**
Nhớ lại một điều bạn đã viết vào `CLAUDE.md` mà vẫn bị bỏ sót nhiều lần. Viết một hook
`Stop` nhắc điều đó, với điều kiện kích hoạt hẹp và dấu mốc chống lặp.

**Bài 2 — Phân loại rủi ro.**
Lấy danh sách ba hành động không hoàn tác được từ bài tập chương 01. Với mỗi hành động,
quyết định: tự động, tự động + kiểm tra, hay cần người duyệt. Ghi vào `.claude/rules/`.

**Bài 3 — Vẽ ranh giới AI/hệ thống.**
Chọn một quy trình trong dự án. Vẽ sơ đồ và tô hai màu: phần AI làm, phần code làm.
Nếu có ô nào bạn phân vân, thử viết nó thành `if/else` — viết được thì nó thuộc về code.

---

## Tóm tắt chương

- Rule là lời dặn — **có thể bị quên**. Hook do harness chạy — **không thể**.
- Hook cần: điều kiện hẹp và kiểm chứng được, cơ chế chống lặp, thông điệp nói rõ
  hậu quả và bước tiếp theo.
- Hook **nhắc**, không tự làm — nó không có ngữ cảnh để làm đúng.
- Guardrail tốt vừa chặn cái nguy hiểm vừa mở đường cho cái an toàn.
- Ranh giới: **AI hiểu, hệ thống quyết định và thực thi**. Viết được `if/else` thì
  đừng giao cho AI.
- Đầu ra có cấu trúc + kiểm tra hợp lệ + phê duyệt theo mức rủi ro.
- Thang leo thang: rule → skill → hook → chặn cứng ở tầng hệ thống.

> Chương tiếp: [08 — Subagents & chạy song song](08-subagents-va-song-song.md)
