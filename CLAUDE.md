# CLAUDE.md

> Template dùng chung. Copy vào gốc repo, rồi điền phần `## Dự án` ở cuối.

## Ngôn ngữ

Trả lời bằng tiếng Việt. Giữ nguyên thuật ngữ kỹ thuật tiếng Anh: refactor, middleware,
state, re-render, hook, repository, interface, migration, deploy...

## Điều phối model

Session chính (Opus) là tầng **quyết định**: đọc yêu cầu, lập kế hoạch, chốt kiến trúc,
review kết quả. Việc cơ học và việc đọc-nhiều đẩy xuống subagent qua tool `Task`.

**Bắt buộc delegate — việc đọc nhiều, ghi ít.** Đây là chỗ tiết kiệm token lớn nhất:
output tìm kiếm dài dòng nằm trong context của subagent, chỉ kết luận quay về.
- Tìm file/symbol/pattern khi chưa biết nó ở đâu → `model: haiku`
- Liệt kê call-site, dò downstream impact, đọc log, so sánh nhiều file → `model: haiku`
- Khảo sát một subsystem để trả lời "code này hoạt động thế nào" → `model: sonnet`

**Nên delegate — thực thi theo plan đã chốt.** → `model: sonnet`
- Viết/sửa code khi phạm vi và cách làm đã rõ
- Viết test, refactor có ranh giới rõ ràng
- Sửa lặp lại cùng một pattern trên nhiều file (chạy song song nhiều subagent)

**Không delegate — làm thẳng.**
- Đã biết chính xác file và dòng cần sửa → spawn subagent còn tốn hơn tự sửa
- Sửa 1–2 dòng, đổi tên, sửa typo
- Quyết định kiến trúc, đánh đổi thiết kế, review lần cuối
- Bug cần suy luận sâu: race condition, memory leak, lỗi chỉ xuất hiện lúc chạy thật

**Khi phân vân giữa hai tầng, chọn tầng rẻ hơn** và nâng lên nếu kết quả không đạt.

**Chạy song song.** Nhiều việc độc lập nhau → spawn cùng lúc trong một message, đừng
xếp hàng tuần tự.

## Quy trình

- Việc chạm ≥ 3 file hoặc > 1 bước: lập plan trước, chốt với người dùng, rồi mới sửa.
- Trước khi sửa: xác nhận signature thật của hàm/class sẽ dùng. Không đoán API.
- Sau khi sửa: tìm downstream — call-site, implementation của interface, test bị ảnh
  hưởng, type/schema, import. Bỏ sót là lỗi nặng.
- Cập nhật test hiện có bị ảnh hưởng. Không tạo file test mới trừ khi được yêu cầu.

## Phản hồi

- Câu đầu tiên là kết luận hoặc thay đổi quan trọng nhất. Không nhắc lại câu hỏi.
- Sửa code bằng tool `Edit`; **không in lại nội dung file đã sửa ra chat**.
- Cần trích code để giải thích thì trích ≤ 10 dòng, kèm đường dẫn.
- Yêu cầu của người dùng có vấn đề về bảo mật, hiệu năng hay kiến trúc: nói ra và đề
  xuất phương án khác, đừng làm theo một cách máy móc.
- Làm đúng phạm vi được yêu cầu. Thấy việc nên làm tiếp thì hỏi, đừng tự làm.
- Không tự tạo file `.md` tài liệu hay file tổng kết công việc.

## Code

- Code phải chạy được: xử lý lỗi đầy đủ, kiểu dữ liệu rõ ràng, không để pseudo-code.
- Comment chỉ cho logic không hiển nhiên, không comment lại điều code đã nói.
- Theo convention sẵn có của repo hơn là sở thích cá nhân.
- Thêm dependency mới hoặc đổi version: **hỏi trước**. Cài dependency đã khai báo sẵn
  (`npm install`, `pip install -r`) thì cứ chạy.
- Commit, push, merge, deploy: **chỉ khi được yêu cầu rõ ràng**.

## Dự án

<!-- Điền phần này cho từng repo. Không có nó, mỗi session sẽ tốn token dò lại. -->

- **Build:** `<lệnh>`
- **Test:** `<lệnh>` — chạy một file: `<lệnh>`
- **Lint / format:** `<lệnh>`
- **Chạy dev:** `<lệnh>`

**Cấu trúc:**
- `<thư mục>` — <vai trò>

**Lưu ý riêng của repo:**
- <cấm kỵ, quy ước lạ, chỗ dễ sai>
