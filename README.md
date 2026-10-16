# EduHub – Chương 2: Refactor SOLID & Design Patterns

> Học phần **IN4211 – Phát triển phần mềm hướng đối tượng**
> Sinh viên: **Võ Ngọc Hân** – MSSV: **0024418442** – Lớp: DHCNTT24A-IT
> Nhánh làm bài: `feature/solid-refactor` (Pull Request #3)

## 1. Mục tiêu

Bài tập cung cấp sẵn `ClubManager` – một **God class** cố tình viết sai (quản lý CLB, sự kiện, báo cáo, ghi log, gửi thông báo trong cùng một class). Nhiệm vụ: phân tích các vi phạm SOLID, tách class, áp dụng design pattern và viết unit test để code dễ mở rộng, dễ kiểm thử.

## 2. Cấu trúc dự án

```
.
├── Ch02.Refactor.sln
├── Ch02.Refactor/            # Mã nguồn chính
│   ├── Domain/               # Entity, enum (Club, Event, NotificationChannel, ...)
│   ├── Services/             # ClubService, EventService, ReportService
│   ├── Repositories/         # IClubRepository, InMemory + LoggingClubRepository (Decorator)
│   ├── Notifications/        # INotificationService, Factory, Email/Log notifier
│   ├── Reports/              # IReportStrategy, Console/CSV strategy
│   ├── Logging/              # IAppLogger
│   └── Program.cs            # Demo console
├── Ch02.Refactor.Tests/      # Unit test (AAA)
└── docs/solid-analysis.md    # Phân tích vi phạm SOLID (Nhiệm vụ 1)
```

> Tên thư mục con có thể khác chút tùy bạn tổ chức – chỉnh lại cho khớp repo.

## 3. Nhiệm vụ & kết quả

| # | Nhiệm vụ | Kết quả |
|---|----------|---------|
| 1 | Phân tích ≥ 4 vi phạm SOLID | Ghi tại `docs/solid-analysis.md` (dòng code, nguyên tắc vi phạm, hậu quả) |
| 2 | Tách God class thành các class/interface | `ClubService`, `IClubRepository`, `EventService`, `ReportService`, `IAppLogger`, `INotificationService` |
| 3 | Áp dụng 4 design pattern | Factory, Decorator, Strategy, Observer (xem mục 5) |
| 4 | Hoàn thiện Domain và unit test | Test theo mẫu Arrange – Act – Assert trong `Ch02.Refactor.Tests` |
| 5 | Quy trình Git | Làm trên `feature/solid-refactor`, Conventional Commits, rebase `main` trước khi mở PR |

## 4. Áp dụng SOLID

| Nguyên tắc | Cách áp dụng |
|-----------|--------------|
| **S** – Single Responsibility | Mỗi class một nhiệm vụ: `ClubService` (nghiệp vụ CLB), `EventService` (sự kiện), `ReportService` (báo cáo) |
| **O** – Open/Closed | Thêm kênh thông báo hoặc định dạng báo cáo mới bằng class mới, không sửa code cũ |
| **L** – Liskov Substitution | Mọi implementation (`InMemory…`, `Logging…Repository`, các strategy) thay thế được cho interface của nó |
| **I** – Interface Segregation | Interface nhỏ, chuyên biệt (`IAppLogger`, `INotificationService`, `IReportStrategy`, `IEventObserver`) |
| **D** – Dependency Inversion | Service chỉ phụ thuộc interface, nhận qua **constructor injection** |
## 5. Design patterns

| Pattern | Thành phần | Thay thế cho |
|---------|-----------|--------------|
| **Factory** | `NotificationFactory` tạo `INotificationService` theo `NotificationChannel` | Chuỗi `if/else` chọn kênh thông báo |
| **Decorator** | `LoggingClubRepository` bọc `IClubRepository` để ghi log mỗi thao tác | Log trộn lẫn trong logic nghiệp vụ |
| **Strategy** | `IReportStrategy` với `ConsoleReportStrategy`, `CsvReportStrategy` | Phương thức `PrintClubReport` cứng |
| **Observer** | `IEventObserver`; khi tạo sự kiện, `EventService` báo cho `EmailNotifier`, `LogNotifier` | Gọi thẳng từng bên nhận trong `ClubManager` |

## 6. Chạy dự án

Yêu cầu: **.NET SDK** (phiên bản theo `TargetFramework` trong file `.csproj`).

```bash
# Clone và chuyển sang nhánh bài làm
git clone https://github.com/LeeLamm12/PTPMHDT-IN421107-DHCNTT24A-IT.git
cd PTPMHDT-IN421107-DHCNTT24A-IT
git checkout feature/solid-refactor

# Build
dotnet build Ch02.Refactor.sln

# Chạy demo console
dotnet run --project Ch02.Refactor

# Chạy unit test
dotnet test Ch02.Refactor.sln
```

## 7. Quy ước Git

- Nhánh: `feature/solid-refactor`
- Commit theo [Conventional Commits](https://www.conventionalcommits.org/), ví dụ:
  - `refactor: apply SOLID and patterns`
  - `test: add unit tests for ClubService`
  - `docs: add solid-analysis`
- Rebase lên `main` trước khi mở Pull Request:

```bash
git fetch origin
git rebase origin/main
git push --force-with-lease
```

## 8. Tài liệu liên quan

- [`docs/solid-analysis.md`](docs/solid-analysis.md) – phân tích vi phạm SOLID
- Pull Request: #3
