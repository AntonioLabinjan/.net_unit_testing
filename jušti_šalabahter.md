
---

### **FluentAssertions - Šalabahter**

**Osnovna sintaksa**  
FluentAssertions koristi **fluid API** koji omogućava izražajnu sintaksu za asercije u testovima. Svaka asercija koristi lančanje metoda.

#### **1. Osnovne asercije**
- **Jednakost (Equality)**  
```csharp
actual.Should().Be(expected);
```
Provjerava da su vrijednosti jednake.

- **Nejednakost (Inequality)**  
```csharp
actual.Should().NotBe(unexpected);
```
Provjerava da nisu jednake.

- **Jednakost sa nulom (Null checking)**  
```csharp
actual.Should().BeNull();
actual.Should().NotBeNull();
```

- **True/False**
```csharp
actual.Should().BeTrue();
actual.Should().BeFalse();
```

#### **2. Provjera tipova i obujma**
- **Provjera tipa**  
```csharp
actual.Should().BeOfType<SomeType>();
```

- **Provjera broja elemenata u kolekciji**  
```csharp
collection.Should().HaveCount(3);
```

#### **3. Provjera stringova**
- **Jednakost stringova**  
```csharp
actualString.Should().Be("expectedString");
```

- **Sadrži (Contains)**  
```csharp
actualString.Should().Contain("substring");
```

- **Početak (StartWith) / Kraj (EndWith)**  
```csharp
actualString.Should().StartWith("start");
actualString.Should().EndWith("end");
```

#### **4. Kolekcije i sekvence**
- **Sadrži elemente (Contains)**  
```csharp
collection.Should().Contain(element);
```

- **Biti u redoslijedu (BeInAscendingOrder / BeInDescendingOrder)**  
```csharp
collection.Should().BeInAscendingOrder();
collection.Should().BeInDescendingOrder();
```

- **Biti prazno (BeEmpty)**  
```csharp
collection.Should().BeEmpty();
```

- **Sadrži određeni broj (HaveCount)**  
```csharp
collection.Should().HaveCount(5);
```

#### **5. Ostatak provjera**
- **Izuzeci (Exception)**  
```csharp
Action act = () => { throw new InvalidOperationException(); };
act.Should().Throw<InvalidOperationException>();
```

- **Biti unutar granica (BeInRange)**  
```csharp
value.Should().BeInRange(1, 10);
```

- **Biti unutar tolerance (BeApproximately)**  
```csharp
value.Should().BeApproximately(10.5, 0.1);
```

- **Datum / Vrijeme**  
```csharp
date.Should().BeAfter(otherDate);
date.Should().BeBefore(otherDate);
date.Should().BeSameDateAs(otherDate);
```

#### **6. Negacija i lančane provjere**
- **Negacija**  
```csharp
actual.Should().NotBe(expected);
```

- **Lančana provjera**  
```csharp
actual.Should().BeGreaterThan(0).And.BeLessThan(10);
```

#### **7. Provjera objekata i svojstava**
- **Provjera svojstva objekta**  
```csharp
person.Should().HaveProperty("Name");
```

- **Provjera dubokih objekata**  
```csharp
person.Should().Match(p => p.Name == "John" && p.Age == 30);
```

#### **8. Povratne vrijednosti i metoda**
- **Metoda vraća vrijednost**  
```csharp
myService.GetValue().Should().Be(42);
```

- **Metoda ne vraća vrijednost (void metoda)**  
```csharp
Action act = () => myService.DoSomething();
act.Should().NotThrow();
```

#### **9. Provjera tipova iznimki**
- **Provjera tipa iznimke**  
```csharp
Action act = () => throw new ArgumentNullException();
act.Should().Throw<ArgumentNullException>();
```

#### **10. Kombinirano korištenje s drugim alatima**
- **xUnit, NUnit, MSTest**  
FluentAssertions je potpuno kompatibilan sa svim popularnim frameworkovima za testiranje poput **xUnit**, **NUnit** i **MSTest**. Koristi se u istoj sintaksi za asercije.

