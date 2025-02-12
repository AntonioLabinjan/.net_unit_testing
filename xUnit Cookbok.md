---  

```md
# xUnit Cookbook - Primjeri Koda

Ovdje se nalaze konkretni primjeri testiranja u **xUnit** frameworku za **.NET**.

## 1. Osnovni test sa `[Fact]`
```csharp
using Xunit;

public class MathTests
{
    [Fact]
    public void Addition_ShouldReturnCorrectSum()
    {
        int result = 2 + 3;
        Assert.Equal(5, result);
    }
}
```

## 2. `[Theory]` + `[InlineData]` za različite ulaze
```csharp
public class MathTests
{
    [Theory]
    [InlineData(2, 3, 5)]
    [InlineData(-1, -1, -2)]
    [InlineData(0, 0, 0)]
    public void Addition_ShouldWork(int a, int b, int expected)
    {
        Assert.Equal(expected, a + b);
    }
}
```

## 3. Testiranje exceptiona
```csharp
public class Calculator
{
    public int Divide(int a, int b)
    {
        if (b == 0) throw new DivideByZeroException();
        return a / b;
    }
}

public class CalculatorTests
{
    [Fact]
    public void Divide_ByZero_ThrowsException()
    {
        var calc = new Calculator();
        Assert.Throws<DivideByZeroException>(() => calc.Divide(10, 0));
    }
}
```

## 4. `[IDisposable]` za setup i cleanup
```csharp
public class ResourceTests : IDisposable
{
    private readonly SomeResource _resource;

    public ResourceTests()
    {
        _resource = new SomeResource();
    }

    [Fact]
    public void TestResource()
    {
        Assert.NotNull(_resource);
    }

    public void Dispose()
    {
        _resource.Dispose();
    }
}
```

## 5. Testiranje **async** metoda
```csharp
public class AsyncTests
{
    public async Task<int> GetValueAsync() => await Task.FromResult(42);

    [Fact]
    public async Task AsyncMethod_ShouldReturnCorrectValue()
    {
        int result = await GetValueAsync();
        Assert.Equal(42, result);
    }
}
```

## 6. Mocking sa **Moq**
```csharp
using Moq;

public interface IUserService { string GetUser(int id); }

public class UserServiceTests
{
    [Fact]
    public void GetUser_ShouldReturnMockedUser()
    {
        var mock = new Mock<IUserService>();
        mock.Setup(s => s.GetUser(1)).Returns("Antonio");
        
        var result = mock.Object.GetUser(1);
        Assert.Equal("Antonio", result);
    }
}
```

## 7. Grupiranje testova sa `[CollectionFixture]`
```csharp
public class SharedDatabaseFixture
{
    public string DbConnection { get; } = "Connected";
}

[CollectionDefinition("Database Collection")]
public class DatabaseCollection : ICollectionFixture<SharedDatabaseFixture> { }

[Collection("Database Collection")]
public class DatabaseTests
{
    private readonly SharedDatabaseFixture _fixture;

    public DatabaseTests(SharedDatabaseFixture fixture) { _fixture = fixture; }

    [Fact]
    public void Database_Connection_ShouldBeShared()
    {
        Assert.Equal("Connected", _fixture.DbConnection);
    }
}
```

## 8. Testiranje baze sa **In-Memory EF Core**
```csharp
using Microsoft.EntityFrameworkCore;

public class User { public int Id { get; set; } public string Name { get; set; } }

public class AppDbContext : DbContext
{
    public DbSet<User> Users { get; set; }
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }
}

public class DbTests
{
    private AppDbContext GetDbContext()
    {
        var options = new DbContextOptionsBuilder<AppDbContext>()
            .UseInMemoryDatabase("TestDB").Options;
        return new AppDbContext(options);
    }

    [Fact]
    public void CanAddUser()
    {
        using var db = GetDbContext();
        db.Users.Add(new User { Name = "Marko" });
        db.SaveChanges();

        Assert.Single(db.Users);
    }
}
```

## 9. Singleton servis sa `[IClassFixture]`
```csharp
public class SingletonFixture
{
    public int Counter { get; private set; } = 0;
    public void Increment() => Counter++;
}

public class SingletonTests : IClassFixture<SingletonFixture>
{
    private readonly SingletonFixture _fixture;

    public SingletonTests(SingletonFixture fixture) { _fixture = fixture; }

    [Fact]
    public void Counter_ShouldBeShared()
    {
        _fixture.Increment();
        Assert.True(_fixture.Counter > 0);
    }
}
```

## 10. Pokretanje testova u **CI/CD (GitHub Actions)**
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

## 11. Mocking HTTP requesta sa **HttpClient** i **Moq**
```csharp
using Moq;
using System.Net.Http;
using System.Threading.Tasks;

public class ApiClient
{
    private readonly HttpClient _httpClient;

    public ApiClient(HttpClient httpClient) { _httpClient = httpClient; }

    public async Task<string> GetDataAsync()
    {
        return await _httpClient.GetStringAsync("https://api.example.com/data");
    }
}

public class ApiClientTests
{
    [Fact]
    public async Task GetDataAsync_ShouldReturnMockedData()
    {
        var handlerMock = new Mock<HttpMessageHandler>();
        handlerMock
            .Setup(m => m.Send(It.IsAny<HttpRequestMessage>()))
            .Returns(new HttpResponseMessage { Content = new StringContent("Mocked Data") });

        var client = new HttpClient(handlerMock.Object);
        var apiClient = new ApiClient(client);

        string result = await apiClient.GetDataAsync();

        Assert.Equal("Mocked Data", result);
    }
}
```

---


```
