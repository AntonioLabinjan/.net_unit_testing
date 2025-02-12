## 📖 **ULTIMATIVNA xUnit BIBLIJA ZA UNIT TESTING U C# .NET-u** 🚀  

🔥 **Ovo je tvoj najbolji šalabahter za pisanje unit testova u C# koristeći xUnit.** Pokriva sve od **osnova, naprednih tehnika, problema i njihovih rješenja, mockinga, testiranja baze, paralelizma, exception handlinga, pa sve do integracije s CI/CD-om!**  

---

## 🔹 **1. Instalacija xUnit frameworka**  
Prvo trebaš instalirati **xUnit** u svoj testni projekt. Možeš to napraviti pomoću .NET CLI-a ili NuGet Package Managera.

### 🛠 **CLI Instalacija**  
```sh
dotnet add package xunit
dotnet add package Microsoft.NET.Test.Sdk
dotnet add package xunit.runner.visualstudio
```

### 🛠 **Kreiranje testnog projekta**  
```sh
dotnet new xunit -o MyApp.Tests
cd MyApp.Tests
dotnet add reference ../MyApp/MyApp.csproj
```

U Visual Studio-u možeš samo **desni klik na Solution** → **Add** → **New Project** → **xUnit Test Project**.

---

## 🔹 **2. Osnovni primjer unit testa**
Svaki test u xUnit-u je obična C# metoda označena `[Fact]` atributom.

```csharp
using Xunit;

public class MathTests
{
    [Fact]
    public void Addition_ShouldReturnCorrectSum()
    {
        // Arrange
        int a = 5, b = 3;

        // Act
        int result = a + b;

        // Assert
        Assert.Equal(8, result);
    }
}
```
📌 **Arrange-Act-Assert (AAA)** pattern:  
- **Arrange** – Postavi testne podatke  
- **Act** – Pozovi metodu  
- **Assert** – Provjeri rezultat  

---

## 🔹 **3. Testiranje različitih vrijednosti (Theory & InlineData)**  
Kad želiš testirati istu metodu s više ulaznih vrijednosti, koristi `[Theory]` + `[InlineData]`.

```csharp
public class MathTests
{
    [Theory]
    [InlineData(2, 3, 5)]
    [InlineData(-1, -1, -2)]
    [InlineData(100, 200, 300)]
    public void Addition_ShouldReturnCorrectSum(int a, int b, int expectedSum)
    {
        int result = a + b;
        Assert.Equal(expectedSum, result);
    }
}
```

👉 **`[InlineData]` šalje različite kombinacije podataka testnoj metodi.**  

---

## 🔹 **4. Testiranje exceptiona**  
Ako metoda baca exception, koristimo `Assert.Throws<T>`.

```csharp
public class MathService
{
    public int Divide(int a, int b)
    {
        if (b == 0)
            throw new DivideByZeroException();
        return a / b;
    }
}

public class MathTests
{
    [Fact]
    public void Divide_ByZero_ShouldThrowException()
    {
        var mathService = new MathService();
        Assert.Throws<DivideByZeroException>(() => mathService.Divide(10, 0));
    }
}
```

---

## 🔹 **5. Grupiranje testova sa `Collection` i `Class Fixtures`**  
Ako više testova treba dijeliti istu instancu klase, koristi **Fixtures**.

```csharp
public class DatabaseFixture
{
    public DatabaseFixture()
    {
        // Simulacija konekcije na bazu
        DbConnection = "Connected to TestDB";
    }

    public string DbConnection { get; }
}

public class DatabaseTests : IClassFixture<DatabaseFixture>
{
    private readonly DatabaseFixture _fixture;

    public DatabaseTests(DatabaseFixture fixture)
    {
        _fixture = fixture;
    }

    [Fact]
    public void TestDatabaseConnection()
    {
        Assert.Equal("Connected to TestDB", _fixture.DbConnection);
    }
}
```

👉 **`IClassFixture<T>` osigurava da testovi dijele istu instancu objekta.**  

---

## 🔹 **6. Mocking ovisnosti sa Moq**  
Za testiranje metoda koje koriste vanjske resurse, koristi **Moq**.

```sh
dotnet add package Moq
```

```csharp
using Moq;

public interface IUserService
{
    string GetUserName(int id);
}

public class UserController
{
    private readonly IUserService _userService;

    public UserController(IUserService userService)
    {
        _userService = userService;
    }

    public string GetUserName(int id)
    {
        return _userService.GetUserName(id);
    }
}

public class UserControllerTests
{
    [Fact]
    public void GetUserName_ShouldReturnCorrectName()
    {
        var mockService = new Mock<IUserService>();
        mockService.Setup(s => s.GetUserName(1)).Returns("Antonio");

        var controller = new UserController(mockService.Object);

        var result = controller.GetUserName(1);

        Assert.Equal("Antonio", result);
    }
}
```

📌 **Moq omogućava simulaciju sučelja i vraćanje kontrolisanih podataka.**  

---

## 🔹 **7. Testiranje baze podataka (EF Core In-Memory DB)**  
Za testiranje baze bez pravog SQL Servera, koristi **In-Memory bazu**.

```sh
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

```csharp
using Microsoft.EntityFrameworkCore;

public class AppDbContext : DbContext
{
    public DbSet<User> Users { get; set; }

    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }
}

public class User
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class DatabaseTests
{
    private AppDbContext GetInMemoryDbContext()
    {
        var options = new DbContextOptionsBuilder<AppDbContext>()
            .UseInMemoryDatabase(databaseName: "TestDB")
            .Options;
        return new AppDbContext(options);
    }

    [Fact]
    public void CanAddUserToDatabase()
    {
        using var context = GetInMemoryDbContext();
        var user = new User { Name = "Antonio" };
        context.Users.Add(user);
        context.SaveChanges();

        Assert.Single(context.Users);
    }
}
```

---

## 🔹 **8. Paralelno izvršavanje testova**  
🔹 **Po defaultu, xUnit pokreće testove paralelno.**  
🔹 Možeš ih isključiti pomoću:  

```csharp
[assembly: CollectionBehavior(DisableTestParallelization = true)]
```

Ili koristi **Collection** da spriječiš paralelno izvršavanje testova koji koriste istu bazu:  

```csharp
[Collection("Database collection")]
public class DatabaseTests { }
```

---

## 🔹 **9. CI/CD - Pokretanje testova na svakom commit-u (GitHub Actions)**  
Dodaj `.github/workflows/test.yml`:

```yaml
name: .NET Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repo
        uses: actions/checkout@v2

      - name: Install .NET
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: '7.0'

      - name: Restore dependencies
        run: dotnet restore

      - name: Run tests
        run: dotnet test --logger "trx;LogFileName=test_results.trx"
```

---

### **🚀 Zaključak**  
✔ **Osnove (Fact, Theory, InlineData, Assert)**  
✔ **Mocking ovisnosti (Moq)**  
✔ **Testiranje baza (EF Core In-Memory)**  
✔ **Grupiranje testova (Fixtures, Collections)**  
✔ **CI/CD integracija (GitHub Actions)**  
✔ **Paralelno izvođenje testova**  

🔥 **Sad znaš SVE o xUnit-u!** 🚀
