# Phụ lục A — Cú pháp & cấu hình

> [← Chương 12](12-lo-trinh-va-case-study.md) | [Mục lục](README.md) | [Phụ lục B →](B-phu-luc-phim-tat-cli.md)

> **Lưu ý:** cú pháp cấu hình của công cụ thay đổi theo phiên bản. Các mẫu dưới đây
> lấy từ cấu hình đang chạy thật trong PM-AGENT tại thời điểm viết. Khi áp dụng, hãy đối
> chiếu với tài liệu chính thức của phiên bản bạn đang dùng.

---

## A.1. Cây thư mục cấu hình

```
<repo>/
├── .claude/
│   ├── CLAUDE.md              # ngữ cảnh dự án — nạp mọi phiên
│   ├── settings.json          # cấu hình chia sẻ (commit vào repo)
│   ├── settings.local.json    # cấu hình cá nhân (KHÔNG commit)
│   ├── rules/*.md             # luật, tách theo chủ đề
│   ├── skills/<tên>/SKILL.md  # năng lực đóng gói
│   ├── commands/<tên>.md      # workflow gọi bằng /<tên>
│   ├── hooks/*.sh             # script guardrail
│   └── knowledge/**/*.md      # kiến thức tham chiếu theo chủ đề
├── .mcp.json                  # khai báo MCP server
└── docs/                      # ngữ cảnh sâu, nạp khi cần

~/.claude/                     # cấu hình cá nhân, áp dụng mọi dự án
├── CLAUDE.md
├── rules/*.md
└── skills/
```

---

## A.2. Khung sườn `CLAUDE.md`

```markdown
# <Tên dự án> — Quy tắc làm việc

## Dự án
<1–3 câu: làm gì, cho ai, quy mô>

**Tài liệu quan trọng:**
- `docs/01-overview.md` — tổng quan, phạm vi
- `docs/04-tech-stack.md` — NGUỒN SỰ THẬT cho quyết định kỹ thuật

## Tech Stack (CHỐT — không đổi nếu không hỏi)
Language / Framework / ORM / Database / Cache / Frontend / Auth / Deploy

## Kiến trúc & Patterns bắt buộc
### 1. <Tên pattern>
<ví dụ code ngắn>
- KHÔNG <điều cấm cụ thể>

## Quy ước đặt tên
| Loại | Kiểu | Ví dụ |

## Lệnh thường dùng
| Việc | Lệnh |
| Chạy dev | make dev |
| Chạy test | make test |
| Tạo migration | make migrate-create |

## Điều KHÔNG được làm
- <liệt kê cụ thể, kèm lý do ngắn>

## Checklist trước khi tạo file mới
- [ ] ...
```

**Quy tắc độ dài:** dưới 400 dòng. Vượt thì tách sang `docs/` và để lại con trỏ.

---

## A.3. Khung sườn một rule

```markdown
# <Tên quy tắc> (Bắt buộc / Khuyến nghị)

Áp dụng cho: <phạm vi cụ thể>
Không áp dụng khi: <ngoại lệ>

---

## Nguyên tắc
> <một câu cô đọng>

## Quy trình
### Bước 1 — ...
### Bước 2 — ...

## Bảng tra cứu
| Tình huống | Xử lý |

## Red flags — từ chối merge nếu thấy
| Dấu hiệu | Vấn đề | Cách sửa |

---

## Lý do
<Sự cố thật đã xảy ra, hoặc chi phí cụ thể nếu không tuân thủ>
```

Mục **Lý do** là bắt buộc. Luật không có lý do sẽ bị lách ngay khi bất tiện.

---

## A.4. Frontmatter của skill

```markdown
---
name: ten-skill-kebab-case
description: >
  [LÀM GÌ] Mô tả ngắn gọn skill làm gì.
  [KHI NÀO] Dùng khi <tình huống>, hoặc khi người dùng nói "<câu thật 1>",
  "<câu thật 2>", "<câu thật 3>".
  [PHÂN BIỆT] KHÁC với <skill gần giống> vốn <điểm khác>.
  [ĐIỀU KIỆN] Yêu cầu <điều kiện tiên quyết>.
---

# <Tiêu đề>

<Một câu nêu ranh giới: skill này làm gì và KHÔNG làm gì>

## Nguyên tắc quan trọng
- <điều bắt buộc>
- <điều cấm + hậu quả cụ thể nếu vi phạm>

## Điều kiện tiên quyết
- <cần gì> — kiểm bằng <cách kiểm>

## Quy trình
### Bước 1 — ...
### Bước 2 — ...

## Đầu ra
<Định dạng cụ thể, thế nào là xong>
```

Bốn thành phần `[LÀM GÌ] [KHI NÀO] [PHÂN BIỆT] [ĐIỀU KIỆN]` trong `description` quyết
định skill có được gọi đúng lúc hay không.

---

## A.5. Frontmatter của command

```markdown
---
name: "Tên hiển thị"
description: "Việc nó làm.
  TRIGGER when: user nói '<câu 1>', '<câu 2>'.
  SKIP: <khi nào KHÔNG dùng>."
command: "ten-command"
arguments: "(--tuỳ chọn) [tham số]"
user_invocable: true
---

# Skill: <tên>

## Input
/<ten-command> <tham số>

## Workflow N bước
### Bước 1 — <tên> *(auto)*
### Bước 5 — <tên> **[PAUSE]**   ← dừng chờ người dùng
```

Đánh dấu `[PAUSE]` cho mọi bước cần người xác nhận — đừng để AI tự đoán chỗ nên dừng.

---

## A.6. Đăng ký hook trong `settings.json`

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "/duong/dan/tuyet/doi/.claude/hooks/ten-hook.sh"
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "/duong/dan/tuyet/doi/.claude/hooks/hook-khac.sh"
          }
        ]
      }
    ]
  }
}
```

| Trường | Ý nghĩa |
|---|---|
| Khoá ngoài (`PostToolUse`, `Stop`) | Thời điểm kích hoạt |
| `matcher` | Biểu thức chính quy lọc tên tool (chỉ dùng với `PostToolUse`) |
| `type: "command"` | Chạy một lệnh shell |
| `command` | **Đường dẫn tuyệt đối** tới script |

---

## A.7. Khung sườn một hook

```bash
#!/usr/bin/env bash
# Hook: <sự kiện> — <mục đích một dòng>
#
# Vì sao là hook chứ không phải một dòng trong CLAUDE.md:
# <lý do — rule có thể bị quên, hook thì không>

set -u

REPO_ROOT="$(cd "$(dirname "${BASH_SOURCE[0]}")/../.." && pwd)"
cd "$REPO_ROOT" || exit 0

# (1) ĐIỀU KIỆN HẸP — thoát sớm nếu không áp dụng
<kiểm tra cụ thể, kiểm chứng được>
[ <không thoả> ] && exit 0

# (2) CHỐNG LẶP — mỗi phiên chỉ nhắc một lần
DAU_MOC="${TMPDIR:-/tmp}/<ten>-$(date +%F)-${CLAUDE_SESSION_ID:-$PPID}"
[ -f "$DAU_MOC" ] && exit 0
touch "$DAU_MOC" 2>/dev/null

# (3) THÔNG ĐIỆP — nói rõ hậu quả và bước tiếp theo
cat <<'MSG'
<TIÊU ĐỀ NGẮN>

<Chuyện gì đang xảy ra>
<Nếu bỏ qua thì hậu quả là gì>

Làm ngay: /<lệnh cụ thể>
MSG
```

### Đọc dữ liệu tool trong hook `PostToolUse`

Harness đưa thông tin tool vào **stdin** dưới dạng JSON:

```bash
INPUT=$(cat)
FILE_PATH=$(echo "$INPUT" | python3 -c "
import json, sys
print(json.load(sys.stdin).get('tool_input', {}).get('file_path', ''))
" 2>/dev/null)
```

### Ba quy tắc bắt buộc

1. **Điều kiện hẹp và kiểm chứng được** — không hỏi câu không có câu trả lời chính xác.
2. **Chống lặp** — hook ồn là hook bị tắt.
3. **Luôn `exit 0` ở nhánh không áp dụng** — hook lỗi không được chặn công việc.

---

## A.8. Khai báo MCP server (`.mcp.json`)

```json
{
  "mcpServers": {
    "ten-server": {
      "command": "uv",
      "args": ["run", "--with", "mcp", "--with", "httpx",
               "python", "tools/<duong-dan>/mcp_server.py"],
      "env": {
        "BASE_URL": "http://localhost:8099",
        "AGENT_TOKEN": "***",
        "TUY_CHON": ""
      }
    }
  }
}
```

**Bảo mật:**

- File chứa token thật **không commit**. Commit `.mcp.json.example` với giá trị rỗng.
- **Không đọc nguyên file này vào ngữ cảnh.** Trích đúng trường cần:

```bash
python3 -c "import json; d=json.load(open('.mcp.json')); \
print(d.get('mcpServers',{}).get('<ten>',{}).get('env',{}).get('<TEN_BIEN>',''))" 2>/dev/null
```

- Lỡ commit token → **thu hồi token đó**, đừng chỉ xoá khỏi file.

---

## A.9. Khung sườn brief giao việc cho subagent

```markdown
## Nhiệm vụ
<Một việc cụ thể, có điểm kết thúc rõ>

## Ngữ cảnh
- Dự án: <một dòng>
- Stack: <một dòng>
- File/module liên quan: <danh sách>

## Ràng buộc đã chốt — KHÔNG được phá
- <quyết định 1> — lý do: <...>
- <quyết định 2> — lý do: <...>

## Phạm vi
- Được làm: <...>
- KHÔNG được làm: <...>

## Đầu ra mong đợi
<Định dạng cụ thể>
```

Mục "Ràng buộc đã chốt" là mục quyết định — subagent không có lịch sử dự án.

---

## A.10. Khung sườn tài liệu bàn giao

```markdown
# Brief: <Tên feature>

**Issue:** <id>   **Ngày:** YYYY-MM-DD   **Spec:** docs/feature-specs/<module>/

## Task
<1–2 câu. KHÔNG lặp lại spec — người nhận sẽ tự đọc.>

## Spec files (đọc theo thứ tự)
1. 01-backend-spec.md — business rules, tiêu chí nghiệm thu
2. 02-api-design.md — endpoints, schemas
3. 03-database-mapping.md — bảng, model
4. 04-frontend-spec.md — bố cục, form  ← bỏ nếu không có UI

## Files cần tạo/sửa
| File | Action | Ghi chú |

## Test commands
<COPY CHÍNH XÁC từ tài liệu test — không viết lại theo trí nhớ>

## Definition of Done
- [ ] ...
```

---

## A.11. Cấu trúc thư mục workspace agent (tham khảo)

Một cách tổ chức khi công việc AI vượt ra ngoài một repo code:

```
AI-WORKSPACE/
├── CLAUDE.md          # blueprint tổng
├── directives/        # SOP chi tiết: yêu cầu, quy tắc UI, quy tắc code
├── skills/            # năng lực đóng gói
├── agents/            # định nghĩa agent chuyên môn
├── references/        # tài liệu tham chiếu
├── execution/         # nơi làm việc
└── outputs/           # kết quả
```

Đây không phải cấu trúc bắt buộc, mà là một cách tư duy: **tách ngữ cảnh — luật — năng
lực — agent — thực thi — đầu ra**.

> Tiếp: [Phụ lục B — Phím tắt & dòng lệnh](B-phu-luc-phim-tat-cli.md)
