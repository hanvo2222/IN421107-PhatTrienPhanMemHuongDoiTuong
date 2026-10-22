# Sơ đồ lớp sau khi refactor (Ch02)

Các service chỉ phụ thuộc vào interface. `Program.cs` là nơi duy nhất tạo đối tượng cụ thể rồi đưa vào constructor.

```mermaid
classDiagram
    direction LR

    class ClubService
    class EventService
    class ReportService

    class IClubRepository {
        <<interface>>
        GetAll()
        GetById(id)
        Add(club)
    }
    class IMemberRepository {
        <<interface>>
        GetByClub(clubId)
        GetById(id)
        Add(member)
    }
    class IEventRepository {
        <<interface>>
        GetByClub(clubId)
        GetById(id)
        Add(ev)
    }
    class IAppLogger {
        <<interface>>
        Info(message)
    }
    class INotificationService {
        <<interface>>
        Send(to, message)
    }
    class INotificationServiceFactory {
        <<interface>>
        Create(channel)
    }
    class IReportStrategy {
        <<interface>>
        Write(rows)
    }
    class IEventObserver {
        <<interface>>
        OnEventCreated(ev, club)
    }

    ClubService ..> IClubRepository
    ClubService ..> IMemberRepository
    ClubService ..> IAppLogger
    ClubService ..> INotificationServiceFactory
    EventService ..> IClubRepository
    EventService ..> IEventRepository
    EventService o-- IEventObserver : Subscribe
    ReportService ..> IClubRepository
    ReportService ..> IMemberRepository
    ReportService ..> IAppLogger
    ReportService ..> IReportStrategy

    InMemoryClubRepository ..|> IClubRepository
    LoggingClubRepository ..|> IClubRepository
    LoggingClubRepository --> IClubRepository : wraps
    InMemoryMemberRepository ..|> IMemberRepository
    InMemoryEventRepository ..|> IEventRepository

    ConsoleLogger ..|> IAppLogger
    FileLogger ..|> IAppLogger

    EmailNotificationService ..|> INotificationService
    SmsNotificationService ..|> INotificationService
    SlackNotificationService ..|> INotificationService
    NotificationServiceFactory ..|> INotificationServiceFactory
    NotificationServiceFactory ..> INotificationService : creates

    ConsoleReportStrategy ..|> IReportStrategy
    CsvReportStrategy ..|> IReportStrategy

    LogNotifier ..|> IEventObserver
    EmailNotifier ..|> IEventObserver
```

## Pattern nằm ở đâu

| Pattern | Thành phần |
|---------|------------|
| Factory | `INotificationServiceFactory`, `NotificationServiceFactory` tạo `INotificationService` theo `NotificationChannel` |
| Decorator | `LoggingClubRepository` bọc `IClubRepository` để ghi log |
| Strategy | `IReportStrategy` với `ConsoleReportStrategy` và `CsvReportStrategy`, dùng trong `ReportService.Generate` |
| Observer | `EventService` báo cho các `IEventObserver` (`LogNotifier`, `EmailNotifier`) khi tạo sự kiện |
