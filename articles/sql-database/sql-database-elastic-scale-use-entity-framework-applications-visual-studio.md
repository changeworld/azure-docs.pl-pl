---
title: Korzystanie z elastycznej biblioteki klienta bazy danych z platformą Entity Framework
description: Używanie biblioteki klienta elastycznej bazy danych i struktury encji do kodowania baz danych
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/04/2019
ms.openlocfilehash: 1653a904875964d86864c59c718603a6dacdcbda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087189"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Biblioteka klienta elastycznej bazy danych z platformą entity framework

W tym dokumencie przedstawiono zmiany w aplikacji Entity Framework, które są potrzebne do integracji z [narzędziami elastycznej bazy danych](sql-database-elastic-scale-introduction.md). Nacisk kładzie się na tworzenie [zarządzania mapami niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md) i [routingu zależnego od danych](sql-database-elastic-scale-data-dependent-routing.md) za pomocą podejścia Najpierw kodu programu Entity Framework **Code.** [Code First — Nowa baza danych](https://msdn.microsoft.com/data/jj193542.aspx) samouczek dla EF służy jako przykład uruchomiony w tym dokumencie. Przykładowy kod towarzyszący temu dokumentowi jest częścią zestawu przykładów narzędzi elastycznej bazy danych w przykładach kodu programu Visual Studio.

## <a name="downloading-and-running-the-sample-code"></a>Pobieranie i uruchamianie przykładowego kodu

Aby pobrać kod do tego artykułu:

* Visual Studio 2012 lub nowsze jest wymagane. 
* Pobierz [przykład elastycznych narzędzi bazy danych dla usługi Azure SQL — entity framework integration sample](https://github.com/Azure/elastic-db-tools/). Rozpakuj próbkę do wybranej lokalizacji.
* Uruchom program Visual Studio. 
* W programie Visual Studio wybierz pozycję Plik -> otwórz projekt/rozwiązanie. 
* W oknie dialogowym **Otwórz projekt** przejdź do pobranej próbki i wybierz pozycję **EntityFrameworkCodeFirst.sln,** aby otworzyć przykład. 

Aby uruchomić przykład, należy utworzyć trzy puste bazy danych w bazie danych SQL Azure:

* Baza danych Menedżera map niezależnego fragmentu
* Baza danych odłamka 1
* Baza danych odłamka 2

Po utworzeniu tych baz danych należy wypełnić posiadaczy miejsc w **Program.cs** nazwą serwera bazy danych usługi Azure SQL DB, nazwami baz danych i poświadczeniami w celu połączenia się z bazami danych. Tworzenie rozwiązania w programie Visual Studio. Visual Studio pobiera wymagane pakiety NuGet dla biblioteki klienta elastycznej bazy danych, Entity Framework i obsługa błędów przejściowych w ramach procesu kompilacji. Upewnij się, że przywracanie pakietów NuGet jest włączone dla rozwiązania. To ustawienie można włączyć, klikając prawym przyciskiem myszy plik rozwiązania w Eksploratorze rozwiązań programu Visual Studio. 

## <a name="entity-framework-workflows"></a>Przepływy pracy programu Entity Framework

Deweloperzy entity framework polegać na jednym z następujących czterech przepływów pracy do tworzenia aplikacji i zapewnienia trwałości dla obiektów aplikacji:

* **Code First (Nowa baza danych):** Deweloper EF tworzy model w kodzie aplikacji, a następnie EF generuje bazę danych z niego. 
* **Code First (Istniejąca baza danych)**: Deweloper umożliwia EF generowania kodu aplikacji dla modelu z istniejącej bazy danych.
* **Model Pierwszy:** Deweloper tworzy model w projektancie EF, a następnie EF tworzy bazę danych z modelu.
* **Najpierw baza danych:** Deweloper używa narzędzi EF do wnioskowania modelu z istniejącej bazy danych. 

Wszystkie te podejścia opierają się na DbContext klasy do przejrzystego zarządzania połączeniami bazy danych i schematu bazy danych dla aplikacji. Różne konstruktory w klasie podstawowej DbContext pozwalają na różne poziomy kontroli nad tworzeniem połączenia, uruchamianiem bazy danych i tworzeniem schematu. Wyzwania wynikają przede wszystkim z faktu, że zarządzanie połączeniami bazy danych dostarczone przez EF przecina się z możliwościami zarządzania połączeniami interfejsów routingu zależnych od danych dostarczonych przez bibliotekę klienta elastycznej bazy danych. 

## <a name="elastic-database-tools-assumptions"></a>Założenia narzędzi elastycznej bazy danych

Definicje terminów można znaleźć w [glosariuszu narzędzi elastycznej bazy danych](sql-database-elastic-scale-glossary.md).

Za pomocą biblioteki klienta elastycznej bazy danych można zdefiniować partycje danych aplikacji o nazwie shardlets. Shardlets są identyfikowane przez klucz dzielenia na fragmenty i są mapowane do określonych baz danych. Aplikacja może mieć tyle baz danych, ile potrzeba i dystrybuować shardlets, aby zapewnić wystarczającą pojemność lub wydajność, biorąc pod uwagę bieżące wymagania biznesowe. Mapowanie wartości klucza dzielenia na fragmenty do baz danych jest przechowywane przez mapę niezależnego fragmentu dostarczone przez interfejsy API klienta elastycznej bazy danych. Ta funkcja jest nazywana **zarządzaniem mapami niezależnego fragmentu**lub w skrócie SMM. Mapa niezależnego fragmentu służy również jako broker połączeń bazy danych dla żądań, które zawierają klucz dzielenia na fragmenty. Ta funkcja jest nazywana **routingu zależnego od danych**. 

Menedżer mapy niezależnego fragmentu chroni użytkowników przed niespójnymi widokami do danych shardlet, które mogą wystąpić, gdy występują równoczesne operacje zarządzania shardlet (takie jak przenoszenie danych z jednego niezależnego fragmentu do drugiego). W tym celu mapy niezależnego fragmentu zarządzane przez brokera biblioteki klienta połączenia bazy danych dla aplikacji. Dzięki temu funkcje mapy niezależnego fragmentu automatycznie zabijają połączenie z bazą danych, gdy operacje zarządzania fragmentami mogą mieć wpływ na shardlet, dla których utworzono połączenie. Takie podejście musi zintegrować się z niektórych funkcji EF, takich jak tworzenie nowych połączeń z istniejącego, aby sprawdzić istnienie bazy danych. Ogólnie rzecz biorąc, nasza obserwacja jest, że standardowe konstruktory DbContext działają niezawodnie tylko dla zamkniętych połączeń bazy danych, które można bezpiecznie sklonować dla pracy EF. Zasada projektowania elastycznej bazy danych zamiast tego jest tylko broker otwarte połączenia. Można by pomyśleć, że zamknięcie połączenia wynegocjowane przez bibliotekę klienta przed przekazaniem go do EF DbContext może rozwiązać ten problem. Jednak przez zamknięcie połączenia i opierając się na EF, aby ponownie otworzyć go, jeden rezygnuje sprawdzania poprawności i spójności kontroli wykonywane przez bibliotekę. Funkcje migracji w EF, jednak używa tych połączeń do zarządzania schematu podstawowej bazy danych w sposób, który jest przezroczysty dla aplikacji. W idealnym przypadku można zachować i połączyć wszystkie te możliwości z biblioteki klienta elastycznej bazy danych i EF w tej samej aplikacji. W poniższej sekcji omówiono te właściwości i wymagania bardziej szczegółowo. 

## <a name="requirements"></a>Wymagania

Podczas pracy z biblioteką klienta elastycznej bazy danych i interfejsami API programu Entity Framework należy zachować następujące właściwości: 

* **Skalowanie w poziomie:** Aby dodać lub usunąć bazy danych z warstwy danych podzielonej aplikacji, zgodnie z potrzebami pojemności aplikacji. Oznacza to kontrolę nad tworzeniem i usuwaniem baz danych oraz przy użyciu interfejsów API menedżera map fragmentów elastycznej bazy danych do zarządzania bazami danych i mapowaniami shardlets. 
* **Spójność:** Aplikacja wykorzystuje dzielenia na fragmenty i używa funkcji routingu zależnego od danych biblioteki klienta. Aby uniknąć uszkodzenia lub nieprawidłowe wyniki kwerendy, połączenia są pośredniczone za pośrednictwem menedżera mapy niezależnego fragmentu. Zachowuje to również sprawdzanie poprawności i spójność.
* **Code First:** Aby zachować wygodę pierwszego paradygmatu kodu EF. W code first klasy w aplikacji są mapowane w sposób przezroczysty do podstawowych struktur bazy danych. Kod aplikacji współdziała z DbSets, które maskują większość aspektów związanych z przetwarzania podstawowej bazy danych.
* **Schemat**: Entity Framework obsługuje tworzenie początkowego schematu bazy danych i późniejsze ewolucji schematu poprzez migracje. Zachowując te możliwości, dostosowanie aplikacji jest łatwe w miarę rozwoju danych. 

Poniższe wskazówki instruuje, jak spełnić te wymagania dla aplikacji Code First przy użyciu narzędzi elastycznej bazy danych. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Routing zależny od danych przy użyciu ef DbContext

Połączenia bazy danych z entity framework są zazwyczaj zarządzane za pośrednictwem podklas **dbContext**. Utwórz te podklasy, wywodząc się z **DbContext**. W tym miejscu można zdefiniować **DbSets,** które implementują kolekcje kopii baz danych obiektów CLR dla aplikacji. W kontekście routingu zależnego od danych można zidentyfikować kilka przydatnych właściwości, które niekoniecznie są wstrzymywać dla innych scenariuszy pierwszej aplikacji kodu EF: 

* Baza danych już istnieje i została zarejestrowana na mapie niezależnego fragmentu elastycznej bazy danych. 
* Schemat aplikacji został już wdrożony w bazie danych (wyjaśniono poniżej). 
* Zależne od danych połączenia routingu z bazą danych są pośredniczone przez mapę niezależnego fragmentu. 

Aby zintegrować **DbContexts** z routingiem zależnym od danych dla skalowania w poziomie:

1. Tworzenie fizycznych połączeń bazy danych za pośrednictwem interfejsów klienta elastycznej bazy danych menedżera mapy niezależnego fragmentu, 
2. Zawijanie połączenia z podklasą **DbContext**
3. Przekaż połączenie w dół do **DbContext** klas podstawowych, aby upewnić się, że wszystkie przetwarzania po stronie EF dzieje się również. 

Poniższy przykład kodu ilustruje to podejście. (Ten kod jest również w towarzyszącym projekcie programu Visual Studio)

```csharp
public class ElasticScaleContext<T> : DbContext
{
public DbSet<Blog> Blogs { get; set; }
...

    // C'tor for data-dependent routing. This call opens a validated connection 
    // routed to the proper shard by the shard map manager. 
    // Note that the base class c'tor call fails for an open connection
    // if migrations need to be done and SQL credentials are used. This is the reason for the 
    // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
    public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
        : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
        true /* contextOwnsConnection */)
    {
    }

    // Only static methods are allowed in calls into base class c'tors.
    private static DbConnection CreateDDRConnection(
    ShardMap shardMap, 
    T shardingKey, 
    string connectionStr)
    {
        // No initialization
        Database.SetInitializer<ElasticScaleContext<T>>(null);

        // Ask shard map to broker a validated connection for the given key
        SqlConnection conn = shardMap.OpenConnectionForKey<T>
                            (shardingKey, connectionStr, ConnectionOptions.Validate);
        return conn;
    }
```

## <a name="main-points"></a>Główne punkty

* Nowy konstruktor zastępuje domyślny konstruktor w podklasie DbContext 
* Nowy konstruktor przyjmuje argumenty, które są wymagane dla routingu zależnego od danych za pośrednictwem biblioteki klienta elastycznej bazy danych:
  
  * mapy niezależnego fragmentu, aby uzyskać dostęp do interfejsów routingu zależnych od danych,
  * klawisza dzielenia na fragmenty w celu zidentyfikowania shardletu,
  * parametry połączenia z poświadczeniami dla zależnego od danych połączenia routingu z fragmentem. 
* Wywołanie konstruktora klasy podstawowej przyjmuje objazd do metody statycznej, która wykonuje wszystkie kroki niezbędne do routingu zależnego od danych. 
  
  * Używa Wywołania OpenConnectionForKey interfejsów klienta elastycznej bazy danych na mapie niezależnego fragmentu w celu ustanowienia otwartego połączenia.
  * Mapa niezależnego fragmentu tworzy otwarte połączenie z niezależnego fragmentu, który przechowuje shardlet dla danego klucza dzielenia na fragmenty.
  * To otwarte połączenie jest przekazywane z powrotem do konstruktora klasy podstawowej DbContext, aby wskazać, że to połączenie ma być używane przez EF zamiast pozwolić EF utworzyć nowe połączenie automatycznie. W ten sposób połączenie zostało oznaczone przez interfejs API klienta elastycznej bazy danych, dzięki czemu może zagwarantować spójność w ramach operacji zarządzania mapami niezależnego fragmentu.

Użyj nowego konstruktora dla podklasy DbContext zamiast domyślnego konstruktora w kodzie. Oto przykład: 

```csharp
// Create and save a new blog.

Console.Write("Enter a name for a new blog: "); 
var name = Console.ReadLine(); 

using (var db = new ElasticScaleContext<int>( 
                        sharding.ShardMap,  
                        tenantId1,  
                        connStrBldr.ConnectionString)) 
{ 
    var blog = new Blog { Name = name }; 
    db.Blogs.Add(blog); 
    db.SaveChanges(); 

    // Display all Blogs for tenant 1 
    var query = from b in db.Blogs 
                orderby b.Name 
                select b; 
    … 
}
```

Nowy konstruktor otwiera połączenie z fragmentem, który przechowuje dane dla shardlet identyfikowane przez wartość **tenantid1**. Kod w **using** bloku pozostaje niezmieniony, aby uzyskać dostęp do **DbSet** dla blogów przy użyciu EF na niezależnego fragmentu dla **tenantid1**. Spowoduje to zmianę semantyki dla kodu w using bloku tak, że wszystkie operacje bazy danych są teraz ograniczone do jednego niezależnego fragmentu, gdzie **tenantid1** jest przechowywany. Na przykład kwerendy LINQ za pomocą blogów **DbSet** będzie zwracać tylko blogi przechowywane na bieżącym niezależnego fragmentu, ale nie te przechowywane na innych fragmentów.  

#### <a name="transient-faults-handling"></a>Obsługa błędów przejściowych

Zespół Microsoft Patterns & Practices opublikował [blok aplikacji do obsługi błędów przejściowych](https://msdn.microsoft.com/library/dn440719.aspx). Biblioteka jest używana z biblioteką klienta skali elastycznej w połączeniu z EF. Należy jednak upewnić się, że każdy wyjątek przejściowy powraca do miejsca, w którym można zapewnić, że nowy konstruktor jest używany po usterce przejściowej, tak aby każda nowa próba połączenia została podjęta przy użyciu konstruktorów, które zostały zmodyfikowane. W przeciwnym razie połączenie z poprawnym fragmentem nie jest gwarantowane i nie ma żadnych gwarancji, że połączenie jest obsługiwane w miarę zmian na mapie niezależnego fragmentu. 

Poniższy przykład kodu ilustruje, jak zasady ponawiania sql mogą być używane wokół nowych konstruktorów podklasy **DbContext:** 

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{ 
    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
        { 
                var blog = new Blog { Name = name }; 
                db.Blogs.Add(blog); 
                db.SaveChanges(); 
        … 
        } 
    }); 
```

**SqlDatabaseUtils.SqlRetryPolicy** w powyższym kodzie jest zdefiniowany jako **SqlDatabaseTransientErrorDetectionStrategy** z liczbą ponownych prób 10 i 5 sekund czasu oczekiwania między ponownych prób. Takie podejście jest podobne do wskazówek dotyczących ef i transakcji inicjowanych przez użytkownika (zobacz [Ograniczenia z ponawianiem strategii wykonywania (EF6)](https://msdn.microsoft.com/data/dn307226). Obie sytuacje wymagają, aby program aplikacji steruje zakresem, do którego zwraca się wyjątek przejściowy: aby ponownie otworzyć transakcję lub (jak pokazano) odtworzyć kontekst z właściwego konstruktora, który używa biblioteki klienta elastycznej bazy danych.

Konieczność kontrolowania, gdzie przejściowe wyjątki zabrać nas z powrotem w zakresie wyklucza również użycie wbudowanego **SqlAzureExecutionStrategy,** który pochodzi z EF. **SqlAzureExecutionStrategy** będzie ponownie otworzyć połączenie, ale nie używać **OpenConnectionForKey** i dlatego pominąć wszystkie sprawdzania poprawności, która jest wykonywana w ramach **wywołania OpenConnectionForKey.** Zamiast tego przykładowy kod używa wbudowanego **DefaultExecutionStrategy,** który również pochodzi z EF. W przeciwieństwie do **SqlAzureExecutionStrategy**, działa poprawnie w połączeniu z zasadami ponawiania z obsługi błędów przejściowych. Zasady wykonywania jest ustawiona w **ElasticScaleDbConfiguration** klasy. Należy zauważyć, że zdecydowaliśmy się nie używać **DefaultSqlExecutionStrategy,** ponieważ sugeruje użycie **SqlAzureExecutionStrategy** jeśli występują przejściowe wyjątki - co prowadziłoby do niewłaściwego zachowania, jak omówiono. Aby uzyskać więcej informacji na temat różnych zasad ponawiania prób i EF, zobacz [Odporność połączeń w EF](https://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Konstruktor przepisuje

Powyższe przykłady kodu ilustrują domyślne konstruktora ponownie zapisuje wymagane dla aplikacji w celu użycia routingu zależnego od danych z Entity Framework. W poniższej tabeli uogólnia to podejście do innych konstruktorów. 

| Konstruktor prądu | Przepisał konstruktor danych | Konstruktor bazowy | Uwagi |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(Mapa niezależnego od fragmentu, klawisz TKey) |DbContext(DbConnection, bool) |Połączenie musi być funkcją mapy niezależnego fragmentu i klucza routingu zależnego od danych. Musisz by-pass automatyczne tworzenie połączenia przez EF i zamiast tego użyć mapy niezależnego fragmentu do brokera połączenia. |
| MyContext(ciąg) |ElasticScaleContext(Mapa niezależnego od fragmentu, klawisz TKey) |DbContext(DbConnection, bool) |Połączenie jest funkcją mapy niezależnego fragmentu i klucza routingu zależnego od danych. Stała nazwa bazy danych lub parametry połączenia nie działa, ponieważ one przez przejście sprawdzania poprawności przez mapę niezależnego fragmentu. |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |Połączenie zostanie utworzone dla danej mapy niezależnego fragmentu i klucza dzielenia na fragmenty z dostarczonym modelem. Skompilowany model jest przekazywany do podstawowej c'tor. |
| MyContext(DbConnection, bool) |ElasticScaleContext(ShardMap, TKey, bool) |DbContext(DbConnection, bool) |Połączenie musi być wywnioskowane z mapy niezależnego fragmentu i klucza. Nie można podać jako dane wejściowe (chyba że dane wejściowe był już przy użyciu mapy niezależnego fragmentu i klucza). Wartość logiczna jest przekazywana dalej. |
| MyContext(ciąg, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |Połączenie musi być wywnioskowane z mapy niezależnego fragmentu i klucza. Nie można podać jako dane wejściowe (chyba że dane wejściowe był przy użyciu mapy niezależnego fragmentu i klucza). Skompilowany model jest przekazywany dalej. |
| MyContext(ObjectContext, bool) |ElasticScaleContext(ShardMap, TKey, ObjectContext, bool) |DbContext(ObjectContext, bool) |Nowy konstruktor musi upewnić się, że każde połączenie w ObjectContext przekazywane jako dane wejściowe jest ponownie kierowane do połączenia zarządzanego przez skalowanie elastyczne. Szczegółowe omówienie ObjectContexts wykracza poza zakres tego dokumentu. |
| MyContext(DbConnection, DbCompiledModel, bool) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool) |DbContext(DbConnection, DbCompiledModel, bool); |Połączenie musi być wywnioskowane z mapy niezależnego fragmentu i klucza. Połączenie nie może być dostarczone jako dane wejściowe (chyba że dane wejściowe już przy użyciu mapy niezależnego fragmentu i klucza). Model i wartość logiczna są przekazywane do konstruktora klasy podstawowej. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Wdrażanie schematu niezależnego fragmentu za pomocą migracji EF

Automatyczne zarządzanie schematem jest wygodą zapewnianą przez entity framework. W kontekście aplikacji przy użyciu narzędzi elastycznej bazy danych, chcesz zachować tę możliwość, aby automatycznie aprowizować schemat do nowo utworzonych fragmentów, gdy bazy danych są dodawane do aplikacji podzielonej na fragmenty. Podstawowym przypadkiem użycia jest zwiększenie pojemności w warstwie danych dla aplikacji podzielonych na fragmenty przy użyciu EF. Poleganie na możliwości EF do zarządzania schematem zmniejsza nakład pracy administracyjnej bazy danych z podzielonej aplikacji zbudowany na EF. 

Wdrożenie schematu za pomocą migracji EF działa najlepiej w odniesieniu do **nieotwartych połączeń**. Jest to w przeciwieństwie do scenariusza routingu zależnego od danych, który opiera się na otwartym połączeniu dostarczonym przez interfejs API klienta elastycznej bazy danych. Inną różnicą jest wymóg spójności: Chociaż pożądane jest zapewnienie spójności dla wszystkich połączeń routingu zależnych od danych w celu ochrony przed równoczesną manipulacją mapą niezależnego fragmentu, nie dotyczy to początkowego wdrożenia schematu w nowej bazie danych, która nie został jeszcze zarejestrowany na mapie niezależnego fragmentu i nie został jeszcze przydzielony do przechowywania shardlets. W związku z tym można polegać na regularnych połączeń bazy danych w tym scenariuszu, w przeciwieństwie do routingu zależnego od danych.  

Prowadzi to do podejścia, w którym wdrożenie schematu za pośrednictwem migracji EF jest ściśle w połączeniu z rejestracją nowej bazy danych jako niezależnego fragmentu na mapie niezależnego fragmentu aplikacji. Opiera się to na następujących wymaganiach wstępnych: 

* Baza danych została już utworzona. 
* Baza danych jest pusta — nie zawiera schematu użytkownika ani danych użytkownika.
* Baza danych nie może jeszcze uzyskać dostępu za pośrednictwem interfejsów API klienta elastycznej bazy danych dla routingu zależnego od danych. 

Z tych wymagań wstępnych w miejscu, można utworzyć regularne un-otwarty **SqlConnection** do rozpoczęcia migracji EF dla wdrożenia schematu. Poniższy przykład kodu ilustruje to podejście. 

```csharp
// Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
// and kick off EF initialization of the database to deploy schema 

public void RegisterNewShard(string server, string database, string connStr, int key) 
{ 

    Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

    SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
    connStrBldr.DataSource = server; 
    connStrBldr.InitialCatalog = database; 

    // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
    // This requires an un-opened connection. 
    using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
    { 
        // Run a query to engage EF migrations 
        (from b in db.Blogs 
            select b).Count(); 
    } 

    // Register the mapping of the tenant to the shard in the shard map. 
    // After this step, data-dependent routing on the shard map can be used 

    this.ShardMap.CreatePointMapping(key, shard); 
} 
```

W tym przykładzie przedstawiono metodę **RegisterNewShard,** która rejestruje fragment na mapie niezależnego fragmentu, wdraża schemat za pośrednictwem migracji EF i przechowuje mapowanie klucza dzielenia na fragmenty do fragmentu. Opiera się na konstruktorze podklasy **DbContext** **(ElasticScaleContext** w przykładzie), który przyjmuje ciąg połączenia SQL jako dane wejściowe. Kod tego konstruktora jest prosty do przodu, jak pokazano w poniższym przykładzie: 

```csharp
// C'tor to deploy schema and migrations to a new shard 
protected internal ElasticScaleContext(string connectionString) 
    : base(SetInitializerForConnection(connectionString)) 
{ 
} 

// Only static methods are allowed in calls into base class c'tors 
private static string SetInitializerForConnection(string connectionString) 
{ 
    // You want existence checks so that the schema can get deployed 
    Database.SetInitializer<ElasticScaleContext<T>>( 
new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

    return connectionString; 
} 
```

Jeden mógł mieć używany wersja konstruktora dziedziczone z klasy podstawowej. Ale kod musi upewnić się, że domyślny inicjator dla EF jest używany podczas łączenia. W związku z tym krótkie objazd do metody statycznej przed wywołaniem do konstruktora klasy podstawowej z ciągu połączenia. Należy zauważyć, że rejestracja fragmentów należy uruchomić w innej domenie aplikacji lub procesu, aby upewnić się, że ustawienia inicjatora dla EF nie są w konflikcie. 

## <a name="limitations"></a>Ograniczenia

Podejścia przedstawione w niniejszym dokumencie wiążą się z kilkoma ograniczeniami: 

* Aplikacje EF, które używają **LocalDb** najpierw trzeba przeprowadzić migrację do zwykłej bazy danych programu SQL Server przed użyciem biblioteki klienta elastycznej bazy danych. Skalowanie wysuwanie aplikacji za pomocą dzielenia na fragmenty za pomocą skali elastycznej nie jest możliwe w **localdb**. Należy pamiętać, że program rozwoju można nadal używać **LocalDb**. 
* Wszelkie zmiany w aplikacji, które implikują zmiany schematu bazy danych należy przejść przez migracje EF na wszystkie fragmenty. Przykładowy kod dla tego dokumentu nie pokazuje, jak to zrobić. Należy rozważyć użycie Update-Database z ConnectionString parametru iteracji ponad wszystkie fragmenty; lub wyodrębnić skrypt T-SQL dla oczekującej migracji przy użyciu update-database z opcją -Script i zastosować skrypt T-SQL do fragmentów.  
* Biorąc pod uwagę żądanie, zakłada się, że wszystkie jego przetwarzania bazy danych jest zawarty w jednym niezależnego fragmentu, jak identyfikowane przez klucz dzielenia na fragmenty dostarczone przez żądanie. Jednak to założenie nie zawsze jest prawdziwe. Na przykład, gdy nie jest możliwe udostępnienie klucza dzielenia na fragmenty. Aby rozwiązać ten problem, biblioteka klienta udostępnia **MultiShardQuery** klasy, która implementuje abstrakcję połączenia do wykonywania zapytań przez kilka fragmentów. Nauka korzystania z **MultiShardQuery** w połączeniu z EF wykracza poza zakres tego dokumentu

## <a name="conclusion"></a>Podsumowanie

W krokach opisanych w tym dokumencie aplikacje EF można użyć możliwości biblioteki klienta elastycznej bazy danych do routingu zależnego od danych przez refaktoryzacyjne konstruktorów podklas **dbcontext** używane w aplikacji EF. Ogranicza to zmiany wymagane do tych miejsc, w których **DbContext** klasy już istnieją. Ponadto aplikacje EF można nadal korzystać z automatycznego wdrażania schematu, łącząc kroki, które wywołują niezbędne migracje EF z rejestracją nowych fragmentów i mapowania na mapie niezależnego fragmentu. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
