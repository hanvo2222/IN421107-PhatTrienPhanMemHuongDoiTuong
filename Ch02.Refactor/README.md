# Ch02: Refactoring EduHub (SOLID và Design Pattern)

Bài tập chương 2, môn Phát triển phần mềm hướng đối tượng (IN4211). Bài làm nằm trên nhánh `feature/solid-refactor`.

Starter có một God class `ClubManager` làm mọi việc: kiểm tra dữ liệu, lưu CLB và thành viên, ghi log ra file, gửi thông báo, in báo cáo. Bài làm tách class này thành các thành phần nhỏ, phụ thuộc qua interface, rồi áp dụng 4 design pattern. Chức năng bên ngoài giữ nguyên.

## Tài liệu

- Phân tích vi phạm SOLID: [`docs/solid-analysis.md`](../docs/solid-analysis.md)
- Sơ đồ lớp sau khi refactor: [`docs/class-diagram.md`](../docs/class-diagram.md)

## Cấu trúc thư mục

```
Ch02.Refactor/
  Domain/          Club, Event, Member (luật nghiệp vụ nằm trong entity)
  Logging/         IAppLogger, ConsoleLogger, FileLogger
  Notifications/   INotificationService, Email/Sms/Slack, Factory
  Repositories/    IClubRepository, IMemberRepository, IEventRepository, bản InMemory, Decorator
  Services/        ClubService, EventService, ReportService
  Reports/         IReportStrategy, ConsoleReportStrategy, CsvReportStrategy
  Observers/       IEventObserver, LogNotifier, EmailNotifier
  Program.cs       Composition root: nơi duy nhất dùng new cho class cụ thể
Ch02.Refactor.Tests/   Test xUnit theo mẫu Arrange-Act-Assert
```

## SOLID

| Nguyên lý | Áp dụng |
|-----------|---------|
| S | `ClubManager` được tách thành `ClubService`, `EventService`, `ReportService`; log và thông báo giao cho `IAppLogger`, `INotificationService` |
| O | Thêm kênh thông báo hay định dạng báo cáo mới là thêm class mới, không sửa code cũ |
| L | `LoggingClubRepository` và `InMemoryClubRepository` thay thế nhau được qua `IClubRepository` |
| I | Interface nhỏ, mỗi cái một vai trò: `IClubRepository`, `IMemberRepository`, `IEventRepository`, `INotificationService`... |
| D | Service nhận interface qua constructor; `Program.cs` mới tạo đối tượng cụ thể |

## Bốn design pattern

| Pattern | Thành phần |
|---------|------------|
| Factory | `NotificationServiceFactory` thay chuỗi if/else chọn kênh thông báo |
| Decorator | `LoggingClubRepository` bọc `IClubRepository` để ghi log |
| Strategy | `IReportStrategy` (`ConsoleReportStrategy`, `CsvReportStrategy`) |
| Observer | `EventService` báo cho `IEventObserver` (`LogNotifier`, `EmailNotifier`) khi tạo sự kiện |

## Cách chạy

Cần .NET SDK 10. Chạy các lệnh dưới đây ở thư mục gốc của repo (nơi có `Ch02.Refactor.sln`):

```
dotnet build Ch02.Refactor.sln
dotnet test Ch02.Refactor.sln
dotnet run --project Ch02.Refactor
```

Chương trình demo tạo CLB, thêm thành viên, tạo sự kiện rồi in báo cáo. Dòng `[repo]` do Decorator ghi, các dòng `[EMAIL]`, `[SLACK]` do các kênh thông báo in ra. Báo cáo CSV được ghi vào file `club-report.csv` trong thư mục đang chạy.

## Kiểm thử

`Ch02.Refactor.Tests/DomainTests.cs` có 12 test xUnit theo mẫu AAA, gồm cả trường hợp lỗi và giá trị biên:

- `Club.Create`: tên hợp lệ, tên rỗng hoặc toàn khoảng trắng, tên dài hơn 100 ký tự
- `Club.HasRoom`: sát giới hạn (49 thành viên) và đúng giới hạn (50 thành viên)
- `Event.Register`: thành viên mới, đăng ký hai lần
- `Event.CheckIn`: chưa đăng ký (báo lỗi), đã đăng ký
- `Event.AttendanceRate`: chưa có ai đăng ký
- `ClubService.CreateClub`: tên rỗng báo lỗi

Kết quả: 12 test đều qua.
