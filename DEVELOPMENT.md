# NalburCore ERP

## Hızlı Başlangıç

### 1. Bağlantı dizesini ayarla
`src/NalburCore.WebAPI/appsettings.Development.json` dosyasını aç,
`DefaultConnection` değerini SQL Server sunucuna göre güncelle:

```json
"DefaultConnection": "Server=YOUR_SERVER_NAME;Database=NalburCoreDb;Trusted_Connection=True;TrustServerCertificate=True"
```

### 2. Migration oluştur (ilk kez)
```powershell
# Visual Studio — Package Manager Console (Startup: WebAPI, Default: Infrastructure)
Add-Migration InitialCreate -Project NalburCore.Infrastructure -StartupProject NalburCore.WebAPI

# VEYA dotnet CLI (çözüm kökünden)
dotnet ef migrations add InitialCreate `
  --project src/NalburCore.Infrastructure `
  --startup-project src/NalburCore.WebAPI
```

### 3. Veritabanını güncelle
```powershell
# Package Manager Console
Update-Database -Project NalburCore.Infrastructure -StartupProject NalburCore.WebAPI

# VEYA dotnet CLI
dotnet ef database update `
  --project src/NalburCore.Infrastructure `
  --startup-project src/NalburCore.WebAPI
```

### 4. Projeyi çalıştır
```powershell
cd src/NalburCore.WebAPI
dotnet run
# Swagger Arayüzü: Proje çalıştırıldıktan sonra terminalde çıkan URL üzerinden erişilebilir.
```

---

## Proje Referans Zinciri

```
NalburCore.WebAPI
    └── NalburCore.Infrastructure
            └── NalburCore.Core
```

WebAPI hiçbir zaman doğrudan Core'a referans vermez.
Bağımlılık her zaman dıştan içe akar.

---

## Klasör Yapısı

```
NalburCore/
├── NalburCore.sln
└── src/
    ├── NalburCore.Core/                        ← Sıfır dış bağımlılık
    │   ├── NalburCore.Core.csproj
    │   ├── Domain/
    │   │   ├── Common/
    │   │   │   └── Result.cs                   ← Result<T>, Error
    │   │   ├── Entities/
    │   │   │   └── Entities.cs                 ← Tüm entity sınıfları
    │   │   └── Enums/
    │   │       └── Enums.cs
    │   └── Application/
    │       ├── Contracts/
    │       │   ├── Repositories/
    │       │   │   └── IRepositories.cs        ← IUnitOfWork + tüm repo arayüzleri
    │       │   └── Services/
    │       │       └── IServices.cs            ← IUnitConversionService, IStockReportService
    │       └── Features/
    │           └── Sales/
    │               └── SaleService.cs          ← Atomik satış iş mantığı
    │
    ├── NalburCore.Infrastructure/              ← EF Core, Dapper, SQL Server
    │   ├── NalburCore.Infrastructure.csproj
    │   ├── Migrations/                         ← "Add-Migration" çıktıları buraya yazılır
    │   ├── Persistence/
    │   │   ├── NalburCoreDbContext.cs
    │   │   ├── Configurations/
    │   │   │   └── EntityConfigurations.cs     ← Fluent API konfigürasyonları
    │   │   └── Repositories/
    │   │       └── Repositories.cs             ← EF + Dapper implementasyonları + UoW
    │   └── Services/
    │       ├── UnitConversionService.cs
    │       └── StockReportService.cs           ← Dapper raporlama
    │
    └── NalburCore.WebAPI/                      ← Giriş noktası
        ├── NalburCore.WebAPI.csproj
        ├── Program.cs                          ← DI kayıtları
        ├── appsettings.json
        ├── appsettings.Development.json
        ├── Properties/
        │   └── launchSettings.json
        ├── Controllers/
        │   └── SalesController.cs
        └── Middleware/
            └── ExceptionHandlerMiddleware.cs
```
© 2026 NalburCore ERP. Tüm Hakları Saklıdır.
