# PTPMHDT-IN421107-DHCNTT24A-IT

Repo nộp bài tập môn **Phát triển phần mềm hướng đối tượng** (IN4211), Học kỳ 1 năm học 2026–2027.

- **Trường:** Đại học Đồng Tháp, Khoa Công nghệ và Kỹ thuật
- **Lớp:** ĐHCNTT24A-IT (IN421107)
- **Giảng viên:** ThS. Nguyễn Tuấn Kiệt
- **Sinh viên:** Sơn Lê Lam - 0024418698

Xuyên suốt các bài tập là hệ thống **EduHub**, quản lý Câu lạc bộ và Sự kiện sinh viên.

## Cách tổ chức repo

- Nhánh `main` chỉ chứa README này và `.gitignore`, không chứa code bài tập.
- Mỗi chương nằm trên **một nhánh riêng** và được nộp bằng **Pull Request** vào `main`.
- Commit viết bằng tiếng Anh theo Conventional Commits (`feat:`, `refactor:`, `docs:`, `test:`...).

## Danh sách bài tập

| Chương | Chủ đề | Nhánh | Pull Request |
|--------|--------|-------|--------------|
| Ch01 | Phân tích yêu cầu và domain modeling EduHub (user story, sơ đồ lớp, 5 domain class, console demo) | [`ch01/0024418698`](https://github.com/LeeLamm12/PTPMHDT-IN421107-DHCNTT24A-IT/tree/ch01/0024418698) | [#1](https://github.com/LeeLamm12/PTPMHDT-IN421107-DHCNTT24A-IT/pull/1) |
| Ch02 | Refactor theo SOLID và design pattern (Factory, Decorator, Strategy, Observer) | [`feature/solid-refactor`](https://github.com/LeeLamm12/PTPMHDT-IN421107-DHCNTT24A-IT/tree/feature/solid-refactor) | [#3](https://github.com/LeeLamm12/PTPMHDT-IN421107-DHCNTT24A-IT/pull/3) |

## Cách xem bài của từng chương

1. Bấm nút chọn nhánh ở góc trên bên trái trang Code (đang ghi `main`).
2. Chọn nhánh của chương cần xem, hoặc bấm vào tên nhánh trong bảng trên.
3. Mô tả chi tiết, cách chạy và kết quả của từng chương nằm trong README và thư mục `docs/` trên nhánh đó.

## Công nghệ

C# trên .NET 10 (Console Application), kiểm thử bằng xUnit.