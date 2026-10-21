# Bộ khung Claude Code cho dự án Laravel + Vue

Quy trình: **ý tưởng → PRD → thiết kế → chia task → build từng task → PR**, có cổng duyệt của người ở các điểm quan trọng.

```
/prd "ý tưởng"  →  🚦 duyệt brief + PRD
/design         →  🚦 duyệt schema + ADR
/tasks          →  docs/tasks.md (tùy chọn: /tasks --issues)
/build T1       →  🚦 duyệt kế hoạch → test → code → review → 🚦 cho phép push/PR
```

## Nội dung

| File | Vai trò |
|---|---|
| `CLAUDE.md` | Quy ước dự án, AI đọc mỗi phiên. **Sửa các mục TODO.** |
| `.claude/skills/prd/` | `/prd` — phỏng vấn bạn, viết brief + user stories + AC |
| `.claude/skills/design/` | `/design` — schema, route, ADR |
| `.claude/skills/tasks/` | `/tasks` — chia task lát dọc, tùy chọn tạo GitHub Issues |
| `.claude/skills/build/` | `/build T4` — vòng lặp test-first đến PR |
| `.claude/agents/spec-critic.md` | Subagent phản biện spec/thiết kế |
| `.claude/agents/code-reviewer.md` | Subagent review code độc lập |
| `.claude/settings.json` | Quyền (allow/deny) + đăng ký hooks |
| `.claude/hooks/protect-files.sh` | Chặn sửa `.env`, lock file, migration đã commit |
| `.claude/hooks/format-php.sh` | Tự chạy Pint sau mỗi lần sửa file PHP |
| `.claude/hooks/require-green-tests.sh` | Test đỏ thì không cho AI kết thúc (tối đa 3 lần ép sửa) |
| `docs/` | Mẫu brief, PRD, schema, tasks, ADR |

## Cài đặt

```bash
# 1. Copy vào gốc repo Laravel
cp -r laravel-vue-claude-kit/{CLAUDE.md,.claude,docs} /duong-dan/toi/repo/
cd /duong-dan/toi/repo

# 2. Cho phép chạy hook scripts (macOS/Linux)
chmod +x .claude/hooks/*.sh

# 3. Không commit file trạng thái cục bộ
printf '\n.claude/.state/\n.claude/settings.local.json\n' >> .gitignore

# 4. Sửa các mục TODO trong CLAUDE.md, rồi mở Claude Code
claude
```

Kiểm tra trong Claude Code:
- `/hooks` → thấy 3 hook (PreToolUse, PostToolUse, Stop)
- `/agents` → thấy `code-reviewer`, `spec-critic`
- Gõ `/` → thấy `prd`, `design`, `tasks`, `build`

## Yêu cầu
- Repo là git repo, đã `composer install` (hook cần `vendor/bin/pint`, `php artisan test`).
- Pest đã cài. Nếu dự án dùng PHPUnit thuần, sửa chữ "Pest" trong CLAUDE.md và skill `build`.
- `gh` CLI (chỉ khi dùng `/tasks --issues` và mở PR tự động).
- Hook scripts dùng `bash` và `php` để đọc JSON, không cần `jq`.
  Trên Windows, chạy Claude Code trong WSL hoặc Git Bash.

## Tùy chỉnh thường gặp
- **Test suite chạy chậm** → trong `require-green-tests.sh`, thay `php artisan test` bằng
  `php artisan test --parallel` (cần cài `brianium/paratest`) hoặc chỉ chạy `tests/Feature`.
- **Muốn nới/chặt số lần tự sửa** → đổi `MAX_ATTEMPTS` trong `require-green-tests.sh`.
- **Thêm file cần bảo vệ** → thêm vào mảng `PROTECTED` trong `protect-files.sh`.
- **Thư mục backend khác** (vd: `modules/`) → thêm vào `WATCH_PATHS` trong `require-green-tests.sh`.

## Lời khuyên khi bắt đầu
1. Chạy `/build` thủ công 5–10 task đầu, ngồi cạnh quan sát.
2. Mỗi lần AI sai một kiểu lặp lại → thêm 1 dòng vào CLAUDE.md hoặc checklist của `code-reviewer`.
3. Chỉ khi quy trình ổn định mới chuyển sang tự động hơn (GitHub Actions, chạy song song bằng worktrees).
4. Mỗi task nên mở một phiên Claude Code mới (`/clear`) để context sạch.

## Tài liệu tham khảo
- Skills: https://code.claude.com/docs/en/skills
- Subagents: https://code.claude.com/docs/en/sub-agents
- Hooks: https://code.claude.com/docs/en/hooks-guide
- Best practices: https://code.claude.com/docs/en/best-practices
