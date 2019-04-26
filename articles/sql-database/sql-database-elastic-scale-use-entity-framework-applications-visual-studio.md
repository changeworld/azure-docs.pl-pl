---
title: Za pomocą biblioteki klienckiej elastycznej bazy danych za pomocą platformy Entity Framework | Dokumentacja firmy Microsoft
description: Na użytek Biblioteka klienta elastycznej bazy danych i Entity Framework programowania baz danych
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/04/2019
ms.openlocfilehash: 54890aef8dabfa019a5181c155b6668b1c07cf2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60331936"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Biblioteka kliencka elastic Database przy użyciu platformy Entity Framework

Ten dokument przedstawia zmiany w aplikacji Entity Framework, które są wymagane w celu integracji z [narzędzi elastycznej bazy danych](sql-database-elastic-scale-introduction.md). Koncentruje się na tworzenie [procesu zarządzania mapą fragmentów](sql-database-elastic-scale-shard-map-management.md) i [routingu zależnego od danych](sql-database-elastic-scale-data-dependent-routing.md) z platformą Entity Framework **Code First** podejście. [Kod najpierw — Nowa baza danych](https://msdn.microsoft.com/data/jj193542.aspx) samouczek dla platformy EF służy jako przykład uruchomionych w tym dokumencie. Przykładowy kod, towarzyszący ten dokument jest częścią narzędzi elastycznej bazy danych zestaw przykładów w Visual Studio Code Samples.

## <a name="downloading-and-running-the-sample-code"></a>Pobieranie i uruchamianie przykładowego kodu

Aby pobrać kod, w tym artykule:

* Visual Studio 2012 lub nowszy jest wymagany. 
* Pobierz [narzędzia elastyczne bazy danych Azure SQL — przykład Entity Framework integracji](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-bae904ba) z witryny MSDN. Rozpakuj próbki do wybranej lokalizacji.
* Uruchom program Visual Studio. 
* W programie Visual Studio wybierz Plik -> Otwórz projekt/rozwiązanie. 
* W **Otwórz projekt** okno dialogowe, przejdź do przykładu został pobrany, a następnie wybierz pozycję **EntityFrameworkCodeFirst.sln** można otworzyć plik. 

Aby uruchomić przykład, musisz utworzyć trzy pustych baz danych w usłudze Azure SQL Database:

* Bazy danych Menedżera mapowań fragmentów
* Bazy danych fragmentów 1
* Bazy danych fragmentów 2

Po utworzeniu tych baz danych, wypełnij posiadaczy miejsce w **Program.cs** przy użyciu nazwy serwera bazy danych SQL Azure, nazwy baz danych i swoje poświadczenia, aby nawiązać połączenie z bazy danych. Skompiluj rozwiązanie w programie Visual Studio. Program Visual Studio pobierze wymagane pakiety NuGet dla biblioteki klienckiej elastycznej bazy danych, platformy Entity Framework i obsługa jako część procesu kompilacji błędu przejściowego. Upewnij się, że trwa przywracanie pakietów NuGet jest włączony dla Twojego rozwiązania. To ustawienie zostanie włączone, klikając prawym przyciskiem myszy plik rozwiązania w Eksploratorze rozwiązań programu Visual Studio. 

## <a name="entity-framework-workflows"></a>Przepływy pracy programu Entity Framework

Entity Framework deweloperzy opierają się na jednym z następujących czterech przepływy pracy umożliwiające tworzenie aplikacji i zapewnienie trwałości obiektów w aplikacji:

* **Kod pierwszy (Nowa baza danych)**: Dla deweloperów platformy EF tworzy modelu w kodzie aplikacji, a następnie EF generuje bazy danych z niego. 
* **Kod pierwszy (istniejącej bazy danych)**: Projektant umożliwia EF, generowanie kodu aplikacji, dla modelu na podstawie istniejącej bazy danych.
* **Pierwszy model**: Deweloper tworzy model w Projektancie platformy EF, a następnie EF tworzy bazę danych z modelu.
* **Baza danych pierwszy**: Programistka używa EF narzędzia na potrzeby wnioskowania dotyczącego modelu z istniejącej bazy danych. 

Te metody zależy od klasy DbContext w sposób niewidoczny dla użytkownika zarządzać połączeniami bazy danych i schemat bazy danych dla aplikacji. Różne Konstruktory klasy bazowej typu DbContext pozwalają na różne poziomy kontroli nad połączenia tworzenie, uruchamianie bazy danych i tworzenia schematu. Wyzwania powstać przede wszystkim z faktu, że zarządzanie połączenia bazy danych, które są zapewniane przez EF przecina z funkcjami zarządzania połączenia interfejsów routingu zależnego od danych zapewnianych przez Biblioteka kliencka elastic database. 

## <a name="elastic-database-tools-assumptions"></a>Założenia narzędzi elastycznej bazy danych

Aby uzyskać definicje terminów, zobacz [słownik narzędzi elastycznych baz danych](sql-database-elastic-scale-glossary.md).

Za pomocą biblioteki klienckiej elastycznej bazy danych należy zdefiniować partycje dane Twoich aplikacji o nazwie podfragmentów. Podfragmentów są identyfikowane przez klucz fragmentowania tak i są mapowane do określonych baz danych. Aplikacja może mieć dowolną liczbę baz danych zgodnie z potrzebami i dystrybuować podfragmentów w celu zapewnienia wystarczającej liczby pojemność i wydajność, biorąc pod uwagę bieżące wymagania biznesowe. Mapowanie wartości klucza fragmentowania do baz danych są przechowywane przez mapowania fragmentów w postaci dostarczone przez interfejsów API klienta elastycznej bazy danych. Ta funkcja jest wywoływana **procesu zarządzania mapą fragmentów**, lub SMM w skrócie. Mapowania fragmentów służy również jako broker połączeń z bazą danych dla żądań, które zawierają klucz fragmentowania. Ta możliwość jest znana jako **routingu zależnego od danych**. 

Menedżera mapowań fragmentów uniemożliwia użytkownikom niespójne widoków w dane podfragmentu może wystąpić, gdy występują podfragmentu równoczesnych operacji zarządzania (np. przenoszenie danych z jednego fragmentu do drugiego). Aby to zrobić, mapowań fragmentów w postaci zarządza brokera bibliotekę klienta połączenia bazy danych dla aplikacji. Dzięki temu funkcji mapy fragmentów automatycznie kill połączenie z bazą danych, podczas operacji zarządzania fragmentami może mieć wpływ na podfragmentu, że połączenie zostało utworzone dla. Takie podejście wymaga integracji z niektórych funkcji EF firmy, takich jak tworzenie nowych połączeń z istniejącą pod kątem istnienia bazy danych. Ogólnie rzecz biorąc nasze obserwacji zostało działanie standardowe konstruktory DbContext tylko działają niezawodnie dla połączeń zamknięte bazy danych, które można bezpiecznie sklonować na platformie EF. Zasady projektowania elastycznej bazy danych zamiast tego jest tylko brokera otwarte połączenia. Jeden wydaje się, że zamknięcie połączenia obsługiwane przez brokera bibliotekę klienta w przed przekazaniem ich do typu DbContext EF może rozwiązać ten problem. Jednak połączenie jest zamykane, a opieranie się na EF, aby otworzyć go ponownie, jeden foregoes sprawdzania poprawności i sprawdzanie spójności wykonywane przez bibliotekę. Funkcje migracji w programie EF, jednak używa tych połączeń do zarządzania podstawowy schemat bazy danych w sposób niewidoczny dla aplikacji. W idealnym przypadku będzie zachowują i połącz wszystkie te funkcje z biblioteki klienta elastycznej bazy danych i EF w tej samej aplikacji. W poniższej sekcji omówiono te właściwości i wymagania, które bardziej szczegółowo. 

## <a name="requirements"></a>Wymagania

Pracując z biblioteki klienta elastycznej bazy danych i interfejsów API programu Entity Framework, aby zachować następujące właściwości: 

* **Skalowalny w poziomie**: Aby dodać lub usunąć bazy danych z warstwy danych aplikacji podzielonej na fragmenty, stosownie do potrzeb pojemność aplikacji. Oznacza to, kontrolę nad tworzeniem i usuwaniem baz danych i za pomocą elastycznej bazy danych Menedżera mapowań fragmentów interfejsów API do zarządzania bazami danych i mapowania podfragmentów. 
* **Spójność**: Aplikacja stosuje dzielenia na fragmenty i wykorzystuje funkcje routingu zależnego od danych biblioteki klienta. Aby uniknąć uszkodzenia lub wyniki kwerendy problem, połączenia jest przeprowadzana za pośrednictwem Menedżera mapowań fragmentów. Zachowuje również sprawdzania poprawności i spójność.
* **Kod pierwszy**: Aby zachować wygody paradygmat pierwszy kod firmy EF. W Code First klas w aplikacji są mapowane przezroczyste podstawowe struktury bazy danych. Kod aplikacji współdziała z DbSets, który maski większością aspektów przetwarzania podstawowej bazy danych.
* **Schemat**: Entity Framework obsługuje tworzenie schematu początkowej bazy danych i rozwój kolejnych schematu za pomocą migracji. Przy zachowaniu możliwości, dostosowanie aplikacji jest łatwe jak ewoluuje danych. 

Poniższe wskazówki powoduje, że sposób spełniają te wymagania aplikacji Code First, za pomocą narzędzi elastycznych baz danych. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Dane zależne od routing przy użyciu programu EF DbContext

Połączenia z bazą danych przy użyciu platformy Entity Framework zwykle odbywa się za pośrednictwem podklasy **DbContext**. Utwórz następujące podklasy, wynikające z **DbContext**. Jest to, gdzie należy zdefiniować swoje **DbSets** implementują kopii bazy danych kolekcji obiektów CLR dla aplikacji. W kontekście routingu zależnego od danych można określić kilka przydatne właściwości, które nie posiadają niekoniecznie innych pierwszy scenariuszach aplikacji EF kodu: 

* Baza danych już istnieje i został zarejestrowany w ramach mapowania fragmentów elastycznej bazy danych. 
* Schemat aplikacji została już wdrożona w bazie danych (opisana poniżej). 
* Routing zależny od danych połączeń z bazą danych są obsługiwane przez brokera przez mapowanie fragmentów. 

Aby zintegrować **DbContexts** zależne od danych routingu dla skalowalnego w poziomie:

1. Tworzenie połączeń fizycznej bazy danych za pośrednictwem interfejsów klienta elastycznej bazy danych Menedżera mapowań fragmentów 
2. OPAKOWYWANIE połączenie z **DbContext** podklasy
3. Przekazanie połączenia w dół do **DbContext** podstawowej klasy, aby upewnić się, wszystkie przetwarzanie po stronie EF odbywa się w także. 

Poniższy przykład kodu ilustruje takie podejście. (Ten kod jest również w towarzyszącej projektu programu Visual Studio)

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

* Nowy Konstruktor zastępuje konstruktora domyślnego w podklasą typu DbContext 
* Nowy Konstruktor przyjmuje liczbę argumentów, które są wymagane do routingu zależnego od danych za pomocą biblioteki klienckiej elastycznej bazy danych:
  
  * mapowania fragmentów, aby dostęp do interfejsów routingu zależnego od danych
  * klucz fragmentowania tak, aby zidentyfikować podfragmentu,
  * ciąg połączenia przy użyciu poświadczeń dla połączenia routingu zależnego od danych do fragmentu. 
* Wywołanie konstruktora klasy bazowej uwzględnia przekierowania statycznej metody, która wykonuje wszystkie kroki niezbędne do routingu zależnego od danych. 
  
  * Używa wywołań OpenConnectionForKey interfejsów klienta elastycznej bazy danych na mapie fragmentu do nawiązania połączenia.
  * Mapowania fragmentów tworzy otwartego połączenia do przechowujący podfragmentu klucza fragmentowania danego fragmentu.
  * Ta otwarcia połączenia jest przekazywany do konstruktora klasy bazowej typu DbContext, aby wskazać, czy to połączenie jest używane przez EF samodzielny EF automatycznie Utwórz nowe połączenie. W ten sposób połączenia zostały otagowane przez interfejs API klienta elastycznej bazy danych, dzięki czemu można zagwarantować spójności w ramach operacji zarządzania mapy fragmentów.

Użyj nowego konstruktora dla Twojego podklasą typu DbContext zamiast domyślnego konstruktora w kodzie. Oto przykład: 

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

Nowy Konstruktor otwiera połączenie fragmentów, która przechowuje dane dla podfragmentu identyfikowane przez wartość **tenantid1**. Kod w **przy użyciu** bloku pozostaje bez zmian, aby uzyskać dostęp do **DbSet** dla blogów przy użyciu programu EF na fragmentu dla **tenantid1**. Spowoduje to zmianę semantyki dla kodu za pomocą zablokować takie, że wszystkie operacje bazy danych teraz są ograniczone do jednego fragmentu gdzie **tenantid1** są przechowywane. Na przykład zapytanie LINQ na blogach **DbSet** zwróci tylko blogi przechowywanych w bieżącym fragmencie, ale nie te, przechowywane w innych fragmentach.  

#### <a name="transient-faults-handling"></a>Obsługa błędów przejściowych

Opublikowane przez zespół Microsoft Patterns i praktyki [przejściowych błędów obsługi blok aplikacji](https://msdn.microsoft.com/library/dn440719.aspx). Biblioteka jest używana przy użyciu biblioteki klienckiej skalowania elastycznego w połączeniu z programem EF. Jednak upewnij się, że każdy wyjątek przejściowy zwraca do miejsca, w którym można zapewnić, że nowego konstruktora jest używany po błędów przejściowych, aby wszystkie nowe połączenia podejmowana jest używanie konstruktorów, które możesz tweaked. W przeciwnym razie połączenie prawidłowy fragment nie ma żadnej gwarancji, a nie ma żadnych zapewnień, połączenie jest obsługiwane, wraz ze zmianami do mapy fragmentów. 

Poniższy przykład kodu ilustruje, jak można używać dla zasad ponawiania SQL wokół nowy **DbContext** konstruktory podklasy: 

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

**SqlDatabaseUtils.SqlRetryPolicy** w powyższym kodzie jest zdefiniowany jako **SqlDatabaseTransientErrorDetectionStrategy** z liczbą ponowień równą 10 i 5 sekund czas między ponownymi próbami oczekiwania. Ta metoda jest podobna do wskazówek dotyczących EF i transakcje zainicjowanego przez użytkownika (zobacz [ograniczenia dotyczące ponawiania strategii wykonywania (od wersji EF6)](https://msdn.microsoft.com/data/dn307226). Zarówno sytuacje wymagają, że program aplikacji określa zakres, do którego funkcja zwraca wyjątek przejściowy: Aby ponownie otworzyć transakcji albo (jak pokazano) ponownie utwórz kontekst od prawidłowego konstruktora, który używa Biblioteka kliencka elastic database.

Potrzeba kontroli, gdzie przejściowych wyjątków zająć USA w zakresie także wyklucza stosowanie wbudowanych **SqlAzureExecutionStrategy** dostarczany z programem EF. **SqlAzureExecutionStrategy** będzie ponownie otworzyć połączenie, ale używa **OpenConnectionForKey** ominięcie weryfikacji, które jest wykonywane jako część **OpenConnectionForKey**wywołania. Zamiast tego przykład kodu używa wbudowanej **DefaultExecutionStrategy** również dostarczany z programem EF. W przeciwieństwie do **SqlAzureExecutionStrategy**, działa prawidłowo w połączeniu z zasady ponawiania z obsługi błędów przejściowych. Zasady wykonywania jest ustawiony **ElasticScaleDbConfiguration** klasy. Należy zauważyć, że podjęliśmy decyzję o nie należy używać **DefaultSqlExecutionStrategy** ponieważ sugeruje, za pomocą **SqlAzureExecutionStrategy** przypadku przejściowych wyjątków — które mogłyby prowadzić do nieprawidłowego zachowania zgodnie z opisem. Aby uzyskać więcej informacji na temat zasad ponawiania różnych i EF, zobacz [odporności połączenia w programie EF](https://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Konstruktor modyfikacji oprogramowania

Powyższe przykłady kodu ilustrują, domyślny konstruktor ponownie zapisuje wymaganej dla aplikacji, aby można było używać routingu zależnego od danych z programu Entity Framework. Poniższa tabela stanowi uogólnienie takie podejście do innych konstruktorów. 

| Bieżący Konstruktor | Konstruktor nowych danych | Base Constructor | Uwagi |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(ShardMap, TKey) |Kontekst DbContext (DbConnection, wartość logiczna) |Połączenie musi być funkcją mapowania fragmentów i klucz routingu zależnego od danych. Potrzebne do utworzenia połączenia automatycznego pomijania przez EF i zamiast tego użyć mapy fragmentów brokera połączenia. |
| MyContext(string) |ElasticScaleContext(ShardMap, TKey) |Kontekst DbContext (DbConnection, wartość logiczna) |Połączenie jest funkcją mapowania fragmentów i klucz routingu zależnego od danych. Bazy danych nazwa lub parametry połączenia nie działa jako ich pomijania weryfikacji przez mapowanie fragmentów. |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |Kontekst DbContext (DbConnection, DbCompiledModel, wartość logiczna) |Za pomocą modelu podane dla klucza fragmentu danej mapy i dzielenie na fragmenty pobiera utworzyć połączenia. Skompilowany modelu jest przekazywane do podstawowej c'tor. |
| MyContext (DbConnection, wartość logiczna) |ElasticScaleContext (ShardMap, TKey, wartość logiczna) |Kontekst DbContext (DbConnection, wartość logiczna) |Połączenie wymaga był wywnioskowany z mapy fragmentów i klucza. Nie można podać jako dane wejściowe, (chyba że te dane wejściowe już używał mapowania fragmentów i klucz). Wartość logiczna jest przekazywany. |
| MyContext(string, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |Kontekst DbContext (DbConnection, DbCompiledModel, wartość logiczna) |Połączenie wymaga był wywnioskowany z mapy fragmentów i klucza. Nie można podać jako dane wejściowe, (chyba że te dane wejściowe używał mapowania fragmentów i klucz). Skompilowany modelu są przekazywane. |
| MyContext (ObjectContext, wartość logiczna) |ElasticScaleContext (ShardMap TKey, ObjectContext, wartość logiczna) |Kontekst DbContext (ObjectContext, wartość logiczna) |Nowego konstruktora musi upewnij się, że każde połączenie w obiekcie ObjectContext przekazany jako dane wejściowe ponownie przekierowane do połączenia zarządza elastycznego skalowania. Szczegółowe omówienie ObjectContexts wykracza poza zakres tego dokumentu. |
| MyContext (DbConnection, DbCompiledModel, wartość logiczna) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool) |Kontekst DbContext (DbConnection, DbCompiledModel, wartość logiczna); |Połączenie wymaga był wywnioskowany z mapy fragmentów i klucza. Połączenie nie można podać jako dane wejściowe (chyba że te dane wejściowe już używał mapowania fragmentów i klucz). Atrybut typu wartość logiczna i model są przekazywane do konstruktora klasy bazowej. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Wdrożenie schematu fragmentów za pomocą migracji EF

Zarządzanie schematami automatyczne jest wygodne, dostarczane przez program Entity Framework. W kontekście aplikacji za pomocą narzędzi elastycznych baz danych chcesz zachować tej możliwości, aby automatycznie aprowizować schematu do nowo utworzonego fragmentów, gdy bazy danych zostaną dodane do aplikacji podzielonej na fragmenty. Głównym zastosowaniem jest zwiększenie pojemności w warstwie danych dla aplikacji podzielonej na fragmenty, przy użyciu programu EF. Opierając się na możliwości firmy EF Zarządzanie schematami zmniejsza nakład pracy administracyjnej bazy danych za pomocą aplikacji podzielonej na fragmenty, w oparciu o EF. 

Wdrożenie schematu za pomocą migracji EF, sprawdza się najlepiej w **bez otwierania połączenia**. Jest to w przeciwieństwie do scenariusz routingu zależnego od danych, która opiera się na otwarte połączenia, udostępniane przez interfejs API klienta elastycznej bazy danych. Inna różnica polega na wymaganie spójności: Podczas pożądane w celu zapewnienia spójności dla wszystkich połączeń routingu zależnego od danych do ochrony przed manipulowania mapy fragmentów współbieżnych nie jest problemem z wdrożeniem schemat początkowy do nowej bazy danych, która ma jeszcze nie został zarejestrowany w ramach mapowania fragmentów i nie zostały jeszcze przydzielony do przechowywania podfragmentów. W związku z tym polega na połączenia regularne bazy danych dla tego scenariusza, w przeciwieństwie do routingu zależnego od danych.  

Prowadzi to do podejście gdzie wdrażania schematu za pomocą migracji EF jest ściśle powiązany z rejestracją nową bazę danych jako fragmentów w mapowania fragmentów w aplikacji. To zależy od następujących wymagań wstępnych: 

* Baza danych już istnieje. 
* Baza danych jest pusta — przechowuje żaden schemat użytkownika i żadnych danych użytkownika.
* Bazy danych nie są jeszcze dostępne za pośrednictwem interfejsów API klienta elastycznej bazy danych routingu zależnego od danych. 

Te warunki wstępne są spełnione, można utworzyć wyrażenie otwartych niezaznaczone **SqlConnection** Konferencję migracji EF dla wdrażania schematu. To podejście pokazano w następującym przykładzie kodu. 

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

W tym przykładzie pokazano metodę **RegisterNewShard** , rejestruje fragmentu w ramach mapowania fragmentów, wdraża schematu za pomocą migracje EF i przechowuje mapowanie klucz fragmentowania tak do fragmentu. Opiera się na Konstruktor obiektu **DbContext** podklasy (**ElasticScaleContext** w przykładzie), pobiera parametry połączenia SQL jako dane wejściowe. Kod ten konstruktor jest proste, jak w poniższym przykładzie pokazano: 

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

Jedna może być używana wersja konstruktora dziedziczone z klasy podstawowej. Jednak kod wymaga upewnić się, że domyślny inicjator dla platformy EF jest używany podczas nawiązywania połączenia. Dlatego krótkim przekierować do metody statycznej przed wywołaniem do konstruktora klasy bazowej, przy użyciu parametrów połączenia. Należy pamiętać, że rejestracja fragmentów powinny być uruchamiane w domenie innej aplikacji lub proces, aby upewnić się, że ustawienia inicjatora EF nie wchodzą w konflikt. 

## <a name="limitations"></a>Ograniczenia

Metod opisanych w tym dokumencie pociąga za sobą kilka ograniczeń: 

* Aplikacje platformy EF, które używają **LocalDb** najpierw należy przeprowadzić migrację do zwykłej bazy danych programu SQL Server przed rozpoczęciem korzystania z biblioteki klienta elastycznej bazy danych. Skalowanie aplikacji za pomocą fragmentowania przy użyciu elastycznej skali nie jest możliwe dzięki **LocalDb**. Należy pamiętać, że rozwoju można nadal używać **LocalDb**. 
* Wszelkie zmiany w aplikacji, które oznaczają zmiany schematu bazy danych muszą przechodzić przez migracje EF na wszystkich fragmentów. Przykładowy kod dla tego dokumentu nie pokazują, jak to zrobić. Należy rozważyć użycie bazy danych aktualizacji za pomocą parametru ConnectionString Iterowanie wszystkich fragmentów; lub wyodrębnienia skryptu T-SQL, w związku z migracją oczekujące przy użyciu bazy danych aktualizacji za pomocą skryptu opcji, a następnie zastosować skrypt języka T-SQL do Twojej fragmentów.  
* Biorąc pod uwagę na żądanie, zakłada się, że wszystkie jego przetwarzania bazy danych znajduje się w ramach jednego fragmentu identyfikowanego przez klucz fragmentowania dostarczonej przez żądanie. Jednak to założenie nie zawsze ma wartość true. Na przykład, jeśli go nie jest możliwe udostępnić klucz fragmentowania. Aby rozwiązać ten problem, Biblioteka klienta zapewnia **MultiShardQuery** klasę, która implementuje abstrakcji połączenie, podczas wykonywania zapytań w wielu fragmentach. Nauka korzystania **MultiShardQuery** w połączeniu z programem EF wykracza poza zakres tego dokumentu

## <a name="conclusion"></a>Podsumowanie

Kroki opisane w niniejszym dokumencie EF aplikacjom możliwość Biblioteka kliencka elastic database zależne od danych routingu w ramach refaktoryzacji elementu konstruktory **DbContext** podklasy używanych w aplikacji EF. Ogranicza to zmiany wymagane do tych miejscach gdzie **DbContext** klasy już istnieją. Ponadto EF aplikacje mogą w dalszym ciągu korzystać z wdrażania automatycznego schematu, łącząc kroki, które wywołują niezbędne migracje EF z rejestracją nowych fragmentów i mapowania w ramach mapowania fragmentów. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
