# slack-task-reporter

Tổng hợp report của các member theo **daily / weekly / monthly**, rồi đăng lên Slack qua Cursor Automation.

```
config/
  members.yml          # danh sách member + Slack ID
  settings.yml         # kênh, timezone, lịch chạy
prompts/
  daily-summary.md
  weekly-summary.md
  monthly-summary.md
README.md
        │
        ▼
 Cursor Automation     (3 lịch: ngày / tuần / tháng)
        │
        ▼
      Slack            (kênh report)
```

## Cách hoạt động

1. Member nộp **Slack Workflow có sẵn** (`PM Daily Report`, người khác đã tạo) — bài đăng vào `#project-management-vn`.
2. Reporter **không** tạo hay sửa workflow đó; chỉ đọc bài đã đăng.
3. Đúng lịch, Cursor Automation checkout repo, tách 4 section form, đối chiếu `members.yml`.
4. Agent gửi bản **summary** vào **DM của chủ automation** (không đăng lại kênh nguồn).

Automation **không** tự sửa file trong repo. Đổi member / kênh / giờ thì sửa YAML rồi commit lên branch mà automation checkout.

## Cấu hình trước khi chạy

### 1. Member — `config/members.yml`

Thay các dòng example bằng người thật:

| Field      | Bắt buộc | Ý nghĩa                                      |
|------------|----------|----------------------------------------------|
| `id`       | có       | khóa nội bộ, không dấu, ổn định              |
| `name`     | có       | tên hiển thị trong report                    |
| `slack_id` | có       | ID Slack dạng `U…` (Profile → Copy member ID)|
| `github`   | không    | username GitHub, dùng khi đối chiếu PR       |
| `role`     | không    | `lead` / `engineer` / `qa` / …               |
| `active`   | có       | `false` = bỏ qua khi tổng hợp                |

### 2. Kênh và lịch — `config/settings.yml`

Điền 4 giá trị Slack (ID dạng `C…` hoặc `G…`, không dùng `U…`):

- `slack.post_to: owner_dm` — **đăng** summary vào DM của bạn (`post_channel_id: null`)
- `slack.read_only` — **chỉ đọc** `#project-management-vn`. Không dùng làm đích gửi
- `source.workflow_name` — tên workflow đã có (`PM Daily Report`). Không tạo workflow mới
- `source.fields` — section trên form PM Daily Report: `Đã hoàn thành` / `Đang thực hiện` / `Tiến độ tổng thể` / `Dự định ngày mai`

Lịch mặc định theo `Asia/Ho_Chi_Minh` (cron phía dưới là UTC):

| Kỳ      | Khi chạy (VN)        | Cron UTC          |
|---------|----------------------|-------------------|
| Daily   | 17:30 Thứ 2–6        | `30 10 * * 1-5`   |
| Weekly  | 11:00 Thứ 3          | `0 4 * * 2`       |
| Monthly | 09:00 ngày 1         | `0 2 1 * *`       |

Tắt một kỳ: `periods.<kỳ>.enabled: false`.

## Tạo Cursor Automation

Cần **3 automation** (một prompt / một lịch). Tạo trong **Agents Window** — phiên chat thường không mở được Automations editor.

Mỗi automation:

| Mục            | Giá trị                                                                 |
|----------------|-------------------------------------------------------------------------|
| Trigger        | Đúng lịch (cron) của kỳ đó                                              |
| Tools          | Đọc Slack + Đăng Slack                                                  |
| Repo / branch  | Repo này, branch đã commit `config/` và `prompts/`                      |
| Kênh đọc       | `slack.read_only.channel_id`                                            |
| Đích đăng      | DM chủ automation (`dm_owner`)                                          |
| Instructions   | Nội dung file `prompts/daily-summary.md` / `weekly-summary.md` / `monthly-summary.md` |

Commit và push `config/` + `prompts/` trước khi bật automation. Agent cloud chỉ thấy file đã có trên branch.

## Checklist lần đầu

- [ ] Thay example trong `members.yml` bằng Slack ID thật
- [ ] Điền channel ID nguồn / đích trong `settings.yml`
- [ ] Commit + push lên branch mặc định
- [ ] Mở Agents Window và tạo 3 automation daily / weekly / monthly
- [ ] Chạy thử 1 lần (manual) rồi kiểm tra kênh report
