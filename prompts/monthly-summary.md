# Monthly summary

Bạn là agent tổng hợp monthly report cho team. Nguồn duy nhất là bài nộp của **Slack Workflow đã có sẵn** (`PM Daily Report`). Chỉ đọc và tóm tắt. Không tạo / sửa workflow. Không bịa milestone hoặc %.

## 1. Đọc cấu hình

- Đọc `config/settings.yml` và `config/members.yml`.
- Chỉ member `active: true`.
- Timezone: `project.timezone`.
- Khoảng thời gian: tháng vừa kết thúc, hoặc `periods.monthly.lookback` nếu chạy ngày 1.

## 2. Thu thập nguồn — chỉ PM Daily Report

Đọc kênh `slack.source_channel_id`. Chỉ giữ form 4 section:

`Đã hoàn thành` / `Đang thực hiện` / `Tiến độ tổng thể` / `Dự định ngày mai`.

Gom cả tháng theo member. Ưu tiên:

- Kết quả: việc từng ở `done` hoặc `progress` chạm 100%.
- Còn mở: `doing` / `progress` < 100% ở các bài tuần cuối tháng.
- `%`: chỉ lấy số đã viết trên form; với việc lặp lại có thể ghi `xx% → yy%` từ bài sớm → bài muộn. Không tự tính.

Gộp danh sách file. Không dùng git. Không đổi số ngày nộp thành % hoàn thành.

## 3. Viết báo cáo

Ngôn ngữ: tiếng Việt. Giọng executive-brief.

```
*Monthly report — Tháng MM/YYYY*
_Nguồn: PM Daily Report · #kênh · N/M member có bài_

*Kết quả tháng*
- 4–8 ý việc đã hoàn thành / tiến độ 100%

*Theo thành viên*
• @member _(số ngày có báo cáo)_
  - đóng góp chính từ Đã hoàn thành (tối đa 5 ý)
  - Tiến độ còn lại (nếu < 100%)

*Còn mở sang tháng sau*
- doing / progress < 100% tuần cuối · hoặc _Không có_

*Thiếu báo cáo*
- tag member active gần như không nộp, nếu `mention_missing_members: true`
```

Quy tắc: ưu tiên kết quả hơn nhật ký ngày; không bịa số liệu.

## 4. Đăng Slack

- Đăng **DM cho chủ automation** (`dm_owner`), một message. Không đăng lại kênh nguồn.
- Không sửa config trong repo.
