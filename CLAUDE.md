# CLAUDE.md — Hướng dẫn cho AI

> Sửa các mục có `TODO` cho khớp dự án. File này được nạp vào MỌI phiên,
> nên giữ ngắn: chỉ ghi điều AI cần để làm đúng.

## Dự án
- Tên: TODO (vd: QuizKid — app luyện trắc nghiệm cho học sinh tiểu học)
- Spec, thiết kế, task nằm trong `docs/`: brief.md, prd.md, schema.md, tasks.md, adr/.
  Luôn đọc tài liệu liên quan trong `docs/` trước khi làm một task.

## Stack
- Backend: Laravel TODO(11/12), PHP TODO(8.3)
- Frontend: Vue 3 + Inertia, Tailwind
- Test: Pest (`tests/Feature`, `tests/Unit`)
- DB: MySQL 8 (test dùng DB riêng, cấu hình trong phpunit.xml)

## Lệnh
- Chạy toàn bộ test: `php artisan test`
- Chạy 1 file test: `php artisan test tests/Feature/TenFileTest.php`
- Format PHP: `./vendor/bin/pint`
- Kiểm tra format: `./vendor/bin/pint --test`
- Lint frontend: `npm run lint` (TODO: xóa nếu chưa có)
- Build frontend: `npm run build`

## Quy ước kiến trúc
- Business logic nằm trong `app/Actions/` (mỗi class một hành động, method `handle()`).
  Controller chỉ nhận request → gọi Action → trả response.
- Validate bằng FormRequest, không validate trong controller.
- Phân quyền bằng Policy. Mọi route thao tác dữ liệu của user phải `authorize`.
- Eager load quan hệ khi lặp (tránh N+1).
- Tính toán quan trọng (điểm, tiền, quyền) chỉ làm ở server, không tin dữ liệu client gửi.

## Quy ước test
- Mỗi acceptance criteria (AC) trong docs/prd.md có ít nhất 1 Pest feature test.
- Tên test bắt đầu bằng mã AC: `it('US-03 AC1: chấm điểm đúng khi nộp bài', ...)`.
- Test phải kiểm tra dữ liệu thực (DB, props Inertia, JSON), không chỉ `assertOk()`.
- Dùng factory, không hard-code ID. Dùng `RefreshDatabase`.

## Quy trình
- Làm task bằng `/build <mã task>`. Mỗi task: một branch `task/<mã>`, một PR.
- Không mở rộng phạm vi ngoài task. Thấy vấn đề khác → ghi vào mục "Ghi chú"
  cuối `docs/tasks.md`, không sửa luôn.

## Cấm
- Không thêm package composer/npm khi chưa hỏi người dùng.
- Không sửa migration đã tồn tại; tạo migration mới.
- Không đọc, sửa hoặc in nội dung `.env`.
- Không xóa hoặc nới lỏng test để test qua.
- Không push thẳng lên `main`.
