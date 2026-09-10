# Phụ lục B — Phím tắt & dòng lệnh

> [← Phụ lục A](A-phu-luc-cu-phap.md) | [Mục lục](README.md) | [Phụ lục C →](C-phu-luc-checklist.md)

> **Lưu ý:** phím tắt và cờ dòng lệnh thay đổi theo phiên bản. Bảng dưới đây tổng hợp
> từ tài liệu bài học. **Luôn xác nhận bằng `/help` trong phiên** hoặc tài liệu chính
> thức của phiên bản bạn đang chạy trước khi ghi vào quy trình của nhóm.

---

## B.1. Phím tắt trong phiên

| Phím | Tác dụng | Khi nào dùng |
|---|---|---|
| `Shift + Tab` | Chuyển chế độ tự động chấp nhận chỉnh sửa file | Khi AI đang đi đúng hướng và bạn không muốn duyệt từng file — lệnh shell vẫn cần duyệt |
| `#` | Ghi một điều cần nhớ vào bộ nhớ | Khi phát hiện AI dùng sai một công cụ hoặc quy ước |
| `!` | Chuyển sang chế độ shell | Chạy lệnh và **đưa đầu ra vào ngữ cảnh** cho lượt sau |
| `Esc` | Dừng AI đang chạy | Khi thấy nó đi sai hướng — an toàn, không hỏng phiên |
| `Esc` `Esc` | Quay lại lịch sử | Sửa lại yêu cầu trước |
| `Ctrl + R` | Xem đầy đủ đầu ra | Khi output bị rút gọn |

### Về `Shift + Tab`

Chế độ tự động chấp nhận chỉ áp dụng cho **sửa file**, không áp dụng cho lệnh shell.
Phù hợp khi: AI đang lặp lại một vòng test, hoặc đang viết nhiều file test tương tự.
Vẫn có thể yêu cầu hoàn tác sau đó.

### Về `!`

Điểm giá trị không phải là chạy lệnh nhanh hơn — mà là **đầu ra của lệnh được đưa vào
ngữ cảnh**. Dùng cho lệnh chạy lâu, hoặc lệnh bạn biết chính xác cần gõ:

```
!git status
!make test
!docker compose logs app --tail 100
```

### Về `Esc`

Dừng giữa chừng là an toàn kể cả khi AI đang sửa file hoặc chạy một quy trình. Nếu nó
vừa đề xuất một hướng gần đúng, hãy dừng và nắn lại hướng thay vì để nó chạy hết rồi
sửa sau.

---

## B.2. Khởi động và tiếp tục phiên

| Lệnh | Tác dụng |
|---|---|
| `claude` | Mở phiên mới |
| `claude --continue` | Tiếp tục phiên gần nhất |
| `claude --resume` | Chọn một phiên trước đó để tiếp tục |
| `claude -p "<prompt>"` | Chạy một lần rồi thoát (chế độ không tương tác) |

---

## B.3. `claude -p` — dùng như một tiện ích Unix

Chế độ `-p` biến công cụ thành một mắt xích trong pipeline:

```bash
claude -p "<prompt>"
```

Các tuỳ chọn thường dùng (kiểm bằng `claude --help`):

- Giới hạn công cụ được phép dùng
- Cho phép một số lệnh shell cụ thể
- Chọn định dạng đầu ra (văn bản, JSON, JSON streaming)

### Kết hợp với pipeline

```bash
# Phân tích trạng thái repo
git status | claude -p "Tóm tắt các thay đổi và cảnh báo file nào không nên commit"

# Đọc log lớn
docker compose logs app --tail 2000 | claude -p "Tìm lỗi lặp lại nhiều nhất, trả về JSON"

# Nối tiếp với công cụ khác
cat error.log | claude -p "Trích các lỗi khác nhau, trả về JSON mảng" | jq '.[].message'
```

Có thể hình dung nó như **một tiện ích Unix rất thông minh**: đưa dữ liệu vào, nhận
JSON ra, rồi nối tiếp sang công cụ khác.

Ứng dụng: pipeline CI, xử lý sự cố, công cụ nội bộ, tự động hoá tuỳ biến.

---

## B.4. Chạy nhiều phiên song song

```bash
# Tạo worktree cho từng nhánh việc
git worktree add ../repo-feature-a feature/a
git worktree add ../repo-feature-b feature/b

# Mỗi thư mục một phiên
cd ../repo-feature-a && claude
cd ../repo-feature-b && claude
```

Kết hợp với `tmux` hoặc nhiều cửa sổ terminal. Xem
[chương 08](08-subagents-va-song-song.md) về các bẫy khi làm song song.

```bash
# Dọn dẹp khi xong
git worktree remove ../repo-feature-a
git worktree list
```

---

## B.5. Lệnh Git an toàn cho agent

Danh sách này lấy từ `.claude/rules/{prefix}-git-safety.md` của PM-AGENT.

### Chỉ đọc — cho phép tự do

```bash
git status / diff / log / show / blame / shortlog
git branch          # liệt kê
git remote -v
git fetch           # chỉ tải về, KHÔNG đổi thư mục làm việc
git stash list
git rev-parse / describe
```

### Cần lệnh rõ ràng từ người dùng

```bash
git push (mọi biến thể)      git commit (kể cả --amend)
git merge / rebase / tag     git branch -d / -D
git reset (mọi chế độ)       git checkout . / git restore .
git clean -f / -fd           git stash drop / clear / pop
```

### Không bao giờ làm, kể cả khi được yêu cầu

```bash
git push --force lên main / master / develop / staging / production
git reset --hard trên các nhánh đó
gh pr merge / glab mr merge
gh pr review --approve
```

### Quy tắc bổ sung

- Không dùng `--no-verify` để bỏ qua hook.
- Không đổi cấu hình Git (`user.name`, `user.email`).
- Gặp xung đột merge: hướng dẫn người dùng giải quyết, **không tự chọn phiên bản**.
- Gặp file khoá (`.lock`): tìm nguyên nhân, **không xoá**.

---

## B.6. Lệnh dự án — mẫu Makefile

Bọc lệnh dài thành mục tiêu ngắn, ổn định, rồi ghi tên mục tiêu vào `CLAUDE.md`.

```make
dev / dev-d / down / prod        # vòng đời môi trường
migrate / migrate-create         # migration
seed                             # dữ liệu mẫu
test / test-unit / test-unit-run # test
test-db-setup                    # chuẩn bị CSDL test
test-browser / test-all          # browser test
logs / logs-app / shell / ps     # chẩn đoán
backup / clean
```

Lợi ích không phải tiết kiệm gõ phím mà là **giao diện ổn định**: `make test` luôn
đúng kể cả khi cờ pytest bên dưới đổi. Ghi câu lệnh pytest đầy đủ vào `CLAUDE.md` thì
sẽ lỗi thời; ghi `make test` thì không.

---

## B.7. Câu hỏi mẫu — dùng lại được

### Khám phá codebase

```
Đoạn code này được dùng ở đâu?
Class này được khởi tạo ở những chỗ nào?
Module này tương tác với phần còn lại của hệ thống ra sao?
Những gì đang phụ thuộc vào file này?
Nếu tôi đổi chữ ký hàm này thì cái gì sẽ vỡ?
```

### Khảo cổ Git

```
Xem git log/blame của dòng này — vì sao nó tồn tại?
Đoạn code trông thừa này được thêm trong commit nào, để sửa lỗi gì?
Tuần này tôi đã ship những gì? Xem git log và tổng hợp.
```

### Chuẩn bị thay đổi

```
Để thêm tính năng X, những file nào cần sửa?
Rủi ro của thay đổi này là gì?
Nên bổ sung những test nào?
Đề xuất kế hoạch trước, chưa sửa file nào cả.
```

### Kiểm chứng

```
Làm sao xác nhận thay đổi này chạy đúng?
Chạy các test liên quan và cho tôi xem đầu ra.
Bạn đã kiểm chứng điều này bằng cách nào?
Có phần nào bạn chưa kiểm chứng được không?
```

### Tự phản biện

```
Tự rà lại lập luận trên, chỉ ra điểm nào chưa kiểm chứng được từ mã nguồn.
Với mỗi khẳng định, ghi kèm file:dòng làm bằng chứng.
Khẳng định nào không dẫn được bằng chứng thì xếp riêng vào mục "Chưa xác minh".
```

Câu cuối cùng đáng dùng ở mọi task quan trọng.

---

## B.8. Chẩn đoán nhanh khi có gì đó không ổn

| Triệu chứng | Kiểm trước tiên |
|---|---|
| Lỗi xác thực (401/403) lặp lại | Biến môi trường của container — có đang trỏ đúng môi trường không? |
| MCP tool không xuất hiện | `.mcp.json` đúng cú pháp chưa; server có khởi động được không |
| Hook không chạy | Đường dẫn trong `settings.json` có **tuyệt đối** không; script có quyền chạy không |
| Test "hỏng toàn bộ" ở giai đoạn setup | Có bỏ bớt cờ nào của lệnh gốc không |
| Test kiểm CSDL nhưng không thấy dữ liệu | Có đang tra **đúng CSDL** không (test dùng DB riêng) |
| AI quên lời dặn giữa phiên dài | Ngân sách context — xem [mục 3.7](03-context-engineering.md) |
| Kết quả kém bất thường | Model đang dùng có đúng mức tối thiểu của quy trình không |

> Tiếp: [Phụ lục C — Checklist](C-phu-luc-checklist.md)
