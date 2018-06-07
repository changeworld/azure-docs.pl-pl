---
title: Korzystanie z biblioteki klienta elastycznej bazy danych z programu Entity Framework | Dokumentacja firmy Microsoft
description: Użyj kodowania bazy danych biblioteki klienta elastycznej bazy danych i strukturą Entity Framework
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 2eafd4b23da8f21f1a4b3ffcf29e50b65882d6c0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646766"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Biblioteka klienta usługi elastycznej bazy danych z programu Entity Framework
Ten dokument przedstawia zmiany w aplikacji programu Entity Framework, które są potrzebne do integracji z [narzędzi elastycznej bazy danych](sql-database-elastic-scale-introduction.md). Koncentruje się na tworzenie [zarządzania mapy niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md) i [routingu zależne od danych](sql-database-elastic-scale-data-dependent-routing.md) z programu Entity Framework **Code First** podejście. [Najpierw - Code nową bazę danych](http://msdn.microsoft.com/data/jj193542.aspx) samouczek dotyczący EF służy jako przykład uruchomionych w tym dokumencie. Przykładowy kod towarzyszące ten dokument jest częścią narzędzi elastycznej bazy danych, ustaw próbek w przykładach kodu programu Visual Studio.

## <a name="downloading-and-running-the-sample-code"></a>Pobieranie i uruchamianie przykładowy kod
Aby pobrać kod w tym artykule:

* Visual Studio 2012 lub nowszy jest wymagany. 
* Pobierz [elastyczne narzędzia bazy danych dla bazy danych SQL Azure — przykładowy Entity Framework integracji](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-bae904ba) w witrynie MSDN. Rozpakuj próbki lokalizacji wybrane.
* Uruchom program Visual Studio. 
* W programie Visual Studio wybierz Plik -> Otwórz projekt/rozwiązanie. 
* W **Otwórz projekt** okno dialogowe, przejdź do przykładowej został pobrany i wybierz **EntityFrameworkCodeFirst.sln** otworzyć próbki. 

Aby uruchomić próbki, należy utworzyć trzy pusty bazy danych w bazie danych SQL Azure:

* Bazy danych Menedżera Map niezależnego fragmentu
* 1 niezależnego fragmentu bazy danych
* 2 niezależnego fragmentu bazy danych

Po utworzeniu tych baz danych, wypełnij posiadaczy miejsce w **Program.cs** o nazwę serwera bazy danych SQL Azure, nazwy bazy danych i poświadczeń do nawiązania połączenia bazy danych. Skompiluj rozwiązanie w programie Visual Studio. Visual Studio pobierze wymagane pakiety NuGet biblioteki klienta elastycznej bazy danych, Entity Framework i wystąpienia błędu przejściowego obsługi jako część procesu kompilacji. Upewnij się, że trwa przywracanie pakietów NuGet jest włączona dla rozwiązania. To ustawienie zostanie włączone, klikając prawym przyciskiem myszy plik rozwiązania w Eksploratorze rozwiązań programu Visual Studio. 

## <a name="entity-framework-workflows"></a>Przepływy pracy programu Entity Framework
Entity Framework deweloperzy polegać na jednym z następujących czterech przepływy pracy do tworzenia aplikacji i zapewnienia trwałości dla obiektów aplikacji: 

* **Code First (Nowa baza danych)**: developer EF tworzy model w kodzie aplikacji, a następnie EF generuje bazy danych z niego. 
* **Code First (istniejącej bazy danych)**: dewelopera umożliwia EF generowanie kodu aplikacji dla modelu na podstawie istniejącej bazy danych.
* **Model pierwszy**: dewelopera tworzy model w programie EF designer, a następnie EF utworzy bazę danych z modelu.
* **Baza danych pierwszej**: projektanta używa EF narzędzi w celu uwzględnienia modelu z istniejącej bazy danych. 

Tych sposobów zależne od klasy DbContext niewidocznie Zarządzanie połączenia bazy danych i schemat bazy danych dla aplikacji. Różne konstruktory w klasie podstawowej DbContext umożliwiają różne poziomy kontroli nad Tworzenie połączenia, uruchamianie bazy danych i tworzenie schematu. Wyzwania wynikają głównie z faktu, że zarządzanie połączenia bazy danych, które są zapewniane przez EF przecina z funkcjami zarządzania połączenia interfejsów routingu zależne od danych dostarczanych przez biblioteki klienta elastycznej bazy danych. 

## <a name="elastic-database-tools-assumptions"></a>Założenia narzędzi elastycznej bazy danych
Aby uzyskać definicje terminów, zobacz [słownik narzędzi elastycznej bazy danych](sql-database-elastic-scale-glossary.md).

Z biblioteki klienta elastycznej bazy danych należy zdefiniować partycji o nazwie shardlets danych aplikacji. Shardlets są identyfikowane za pomocą klucza dzielenia na fragmenty i są mapowane na konkretnych baz danych. Aplikacja może mieć dowolną liczbę baz danych, zgodnie z potrzebami i dystrybuować shardlets jest zapewnienie wystarczającej ilości pojemności lub wydajności danego bieżące wymagania biznesowe. Mapowanie dzielenia na fragmenty wartości klucza do bazy danych jest przechowywany przez mapy niezależnego fragmentu pochodzącymi z interfejsów API klienta elastycznej bazy danych. Ta funkcja jest wywoływana **zarządzania mapy niezależnego fragmentu**, lub SMM skrócie. Mapa niezależnego fragmentu służy również jako broker połączeń z bazą danych dla żądań zawierających klucz dzielenia na fragmenty. Ta funkcja jest nazywany **routingu zależne od danych**. 

Menedżer mapy niezależnego fragmentu chroni użytkowników z widoków niespójne w shardlet dane, które mogą wystąpić, gdy są wykonywane operacje zarządzania równoczesnych shardlet (np. przenoszenie danych z jednego niezależnego fragmentu do innego). Aby to zrobić, mapy niezależnego fragmentu zarządzane przez brokera biblioteki klienta połączenia bazy danych dla aplikacji. Dzięki temu funkcja mapy niezależnego fragmentu automatycznie kill połączenia z bazą danych podczas operacji zarządzania niezależnego fragmentu może mieć wpływ na shardlet, który został utworzony dla połączenia. Tej metody należy zintegrować z niektórych funkcji EF firmy, takich jak tworzenie nowych połączeń z jednego z istniejących, aby sprawdzić, czy baza danych. Ogólnie rzecz biorąc naszych obserwacji została działanie standardowe tylko pracy niezawodnie dla połączenia zamkniętego bazy danych, które można bezpiecznie sklonować dla EF konstruktorów typu DbContext. Zamiast tego projektu zasady elastycznej bazy danych jest tylko broker otwarte połączenia. Jeden wydaje się, że zamknięcie połączenia obsługiwane przez brokera w bibliotece klienta przed przekazaniem ich do EF DbContext może rozwiązać ten problem. Jednak połączenie jest zamykane i zależne EF otworzyć go ponownie, co foregoes sprawdzania poprawności i sprawdzanie spójności wykonywana przez bibliotekę. Funkcje migracji w EF, jednak używa tych połączeń do zarządzania podstawowy schemat bazy danych w sposób niewidoczny dla aplikacji. W idealnym przypadku będzie zachowują i połączyć wszystkie te funkcje z biblioteki klienta elastycznej bazy danych i EF w tej samej aplikacji. W poniższej sekcji omówiono te właściwości i wymagania bardziej szczegółowo. 

## <a name="requirements"></a>Wymagania
Podczas pracy z biblioteki klienta elastycznej bazy danych i interfejsów API programu Entity Framework, ma zostać zachowany następujące właściwości: 

* **Skalowalny w poziomie**: Aby dodać lub usunąć baz danych z aplikacji podzielonej odpowiednio do potrzeb pojemność aplikacji warstwy danych. Oznacza to, kontrolę nad tworzeniem i usuwaniem baz danych i zarządzanie baz danych i mapowania shardlets za pomocą Menedżera map niezależnego fragmentu elastycznej bazy danych interfejsów API. 
* **Spójność**: aplikacja wykorzystuje dzielenia na fragmenty i korzysta z funkcji routingu zależne od danych biblioteki klienta. Aby uniknąć uszkodzenia lub wyników zapytania niewłaściwy, połączeń są przeprowadzana za pośrednictwem Menedżera map niezależnego fragmentu. Zachowuje również sprawdzania poprawności i spójność.
* **"Code First"**: zachowania wygodę EF przez kod pierwszego modelu. W pierwszym kod klasy w aplikacji są mapowane niewidocznie podstawowej struktury bazy danych. Kod aplikacji współdziała z DbSets, który zamaskować większością aspektów przetwarzania podstawowej bazy danych.
* **Schemat**: Entity Framework obsługuje tworzenie schematu bazy danych początkowej i kolejnych schematu zmiany za pomocą migracji. Zachowując te możliwości dostosowywania aplikacji jest łatwe w miarę rozwoju środowisko danych. 

Poniższe wskazówki nakazuje jak spełnia wymagania Code First aplikacji za pomocą narzędzi elastycznej bazy danych. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Dane zależne od routingu przy użyciu EF DbContext
Połączenia bazy danych z programu Entity Framework zwykle są zarządzane przez podklasy **DbContext**. Utworzyć te podklasy pochodny **DbContext**. Jest to, gdzie został zdefiniowany z **DbSets** implementują kolekcje kopii bazy danych obiektu CLR dla aplikacji. W kontekście routingu zależne od danych można określić kilka właściwości pomocne, które nie posiadają niekoniecznie dla innych pierwszy scenariuszy aplikacji EF kodu: 

* Baza danych już istnieje i został zarejestrowany w mapie niezależnego fragmentu elastycznej bazy danych. 
* Schemat aplikacji zostało już wdrożone w bazie danych (co omówiono poniżej). 
* Zależne od danych routingu połączeń z bazą danych jest przeprowadzana przez niezależnego fragmentu mapy. 

Aby zintegrować **DbContexts** zależne od danych routingu dla skalowalnego w poziomie:

1. Utwórz połączenia fizycznej bazy danych za pośrednictwem interfejsów klienta elastycznej bazy danych Menedżera mapy niezależnego fragmentu, 
2. Zawijaj połączenie z **DbContext** podklasy
3. Przekaż połączenie w dół do **DbContext** podstawowa klasy, aby upewnić się, wszystkie przetwarzania po stronie EF się stanie, a także. 

Poniższy przykład kodu pokazuje tej metody. (Ten kod jest również towarzyszący projektu programu Visual Studio)

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    …

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

## <a name="main-points"></a>Główne punkty
* Nowy Konstruktor zastępuje konstruktora domyślnego w podklasy DbContext 
* Nowy Konstruktor przyjmuje argumenty, które są wymagane dla routingu zależne od danych za pomocą biblioteki klienta elastycznej bazy danych:
  
  * Mapa niezależnych można uzyskać dostępu do interfejsów routingu zależne od danych
  * klucz dzielenia na fragmenty, aby zidentyfikować shardlet,
  * Parametry połączenia przy użyciu poświadczeń dla połączenia routingu zależne od danych niezależnego fragmentu. 
* Wywołanie konstruktora klasy podstawowej uwzględnia przekierowania statyczną metodę, która wykonuje wszystkie kroki niezbędne do routingu zależne od danych. 
  
  * Używa wywołania OpenConnectionForKey interfejsów klienta elastycznej bazy danych na mapie niezależnego fragmentu ustanowienie otwartego połączenia.
  * Mapa niezależnego fragmentu tworzy Otwieranie połączenia z niezależnego fragmentu, przechowujący shardlet klucza danego dzielenia na fragmenty.
  * To połączenie otwarte jest przekazywane z powrotem do konstruktora klasy podstawowej typu DbContext, aby wskazać, czy to połączenie jest używane przez EF zamiast czekać na EF automatycznie Utwórz nowe połączenie. W ten sposób połączenia zostały oznakowane przez interfejs API klienta elastycznej bazy danych, dzięki czemu może zagwarantować spójności w operacjach zarządzania mapy niezależnego fragmentu.

Użyj nowego konstruktora dla Twojego podklasy DbContext zamiast domyślnego konstruktora w kodzie. Oto przykład: 

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

Nowy Konstruktor otwiera połączenie niezależnego fragmentu, przechowujący dane dla shardlet zidentyfikowane przez wartość **tenantid1**. Kod w **przy użyciu** bloku pozostanie bez zmian, aby uzyskać dostęp do **DbSet** dla blogów za pomocą EF na niezależnych dla **tenantid1**. Spowoduje to zmianę semantyki dla kodu za pomocą zablokować taki, że wszystkie operacje bazy danych obecnie ograniczone do jednego niezależnego fragmentu gdzie **tenantid1** jest przechowywany. Na przykład zapytania LINQ za pośrednictwem blogów **DbSet** zwróci tylko blogi przechowywane na bieżący identyfikator niezależnego fragmentu, ale nie te przechowywane na inne niezależne.  

#### <a name="transient-faults-handling"></a>Obsługa błędów przejściowych
Zespół Microsoft Patterns & wskazówki opublikowane [przejściowy błąd obsługi aplikacji bloku](https://msdn.microsoft.com/library/dn440719.aspx). Biblioteka jest używany z biblioteki klienta elastycznej skali w połączeniu z EF. Jednak zapewnić zwracanych do miejsca, w którym można zapewnić, że nowy konstruktor jest używany po błędu przejściowego tak, aby wszystkie nowe połączenia podejmowana jest używanie konstruktorów, które można tweaked przejściowej wyjątku. W przeciwnym razie połączenie z poprawną niezależnego fragmentu nie jest gwarantowana i nie ma żadnych gwarancji, połączenie jest obsługiwane w chwili wystąpienia zmian do mapy niezależnego fragmentu. 

Poniższy przykładowy kod przedstawia sposób używania zasady ponawiania SQL wokół nowe **DbContext** podklas: 

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

**SqlDatabaseUtils.SqlRetryPolicy** w powyższym kodzie jest zdefiniowany jako **SqlDatabaseTransientErrorDetectionStrategy** z liczbą ponowień równą 10 i 5 sekund oczekiwania czasu między kolejnymi próbami. Ta metoda jest podobna do wskazówek dotyczących EF i transakcji inicjowanych przez użytkownika (zobacz [ograniczenia strategiami ponowną próbą wykonania (EF6 i jego nowszych wersjach)](http://msdn.microsoft.com/data/dn307226). Obu sytuacjach wymagają, że program aplikacji steruje zakresu, do którego zwraca wyjątek przejściowej: Aby ponownie otworzyć transakcji albo (jak pokazano) odtworzyć kontekstu z prawidłowego konstruktora, który używa biblioteki klienta elastycznej bazy danych.

Potrzeba kontroli w przypadku gdy wyjątki przejściowej podjąć nam w zakresie także wyklucza stosowanie wbudowanych **klasy SqlAzureExecutionStrategy** dołączony EF. **Klasy SqlAzureExecutionStrategy** będzie ponownie otworzyć połączenie, ale nie używać **OpenConnectionForKey** ominięcie walidacji utworzonej w ramach **OpenConnectionForKey**wywołania. Zamiast tego przykładowy kod używa wbudowanej **DefaultExecutionStrategy** również dołączony EF. W przeciwieństwie do **klasy SqlAzureExecutionStrategy**, działa on prawidłowo w połączeniu z zasady ponawiania z obsługi błędów przejściowych. Zasady wykonywania jest ustawiony **ElasticScaleDbConfiguration** klasy. Należy pamiętać, że zdecydowaliśmy nie należy używać **DefaultSqlExecutionStrategy** ponieważ sugeruje, za pomocą **klasy SqlAzureExecutionStrategy** Jeśli wystąpią wyjątki przejściowy — która może spowodować nieprawidłowe zachowanie zgodnie z opisem. Aby uzyskać więcej informacji na zasady ponawiania różnych i EF, zobacz [połączenia odporność EF](http://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Konstruktor modyfikacji oprogramowania
Powyższe przykłady kodu przedstawiono domyślny konstruktor ponownie zapisuje wymagane dla aplikacji, aby można było korzystać z routingu zależne od danych z programu Entity Framework. Poniższa tabela stanowi uogólnienie takie podejście do innych konstruktorów. 

| Bieżący Konstruktor | Konstruktor nowych danych | Konstruktora podstawowego | Uwagi |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(ShardMap, TKey) |DbContext (DbConnection, wartość logiczna) |Połączenie musi być funkcją mapy niezależnego fragmentu i klucza routingu zależne od danych. Trzeba obejścia połączenia automatycznego tworzenia przez EF i zamiast tego użyć mapy niezależnego fragmentu do broker połączenia. |
| MyContext(string) |ElasticScaleContext(ShardMap, TKey) |DbContext (DbConnection, wartość logiczna) |Połączenie jest funkcją mapy niezależnego fragmentu i klucz routingu zależne od danych. Stałej bazy danych nazwa lub parametry połączenia nie działa jako ich obejścia weryfikacji przez niezależnego fragmentu mapy. |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, model DbCompiledModel, wartość logiczna) |Połączenie pobiera utworzone dla danego niezależnych klucza mapy i dzielenia na fragmenty za pomocą modelu podane. Skompilowany modelu są przekazywane do podstawowej c'tor. |
| MyContext (DbConnection, wartość logiczna) |ElasticScaleContext (ShardMap, TKey, wartość logiczna) |DbContext (DbConnection, wartość logiczna) |Połączenie musi można wywnioskować na podstawie mapowania niezależnego fragmentu i klucz. Nie można podać jako dane wejściowe, (chyba że te dane wejściowe korzystał już z mapy niezależnych oraz klucz). Wartość logiczna są przekazywane. |
| MyContext(string, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, model DbCompiledModel, wartość logiczna) |Połączenie musi można wywnioskować na podstawie mapowania niezależnego fragmentu i klucz. Nie można podać jako dane wejściowe, (chyba że te dane wejściowe używał mapy niezależnych oraz klucz). Skompilowany modelu są przekazywane. |
| MyContext (ObjectContext, wartość logiczna) |ElasticScaleContext (ShardMap TKey, ObjectContext, wartość logiczna) |DbContext (ObjectContext, wartość logiczna) |Nowy Konstruktor musi upewnić, że wszystkie połączenia w obiekcie ObjectContext przekazany jako dane wejściowe jest przekierowane do połączenia zarządza elastycznego skalowania. Szczegółowe omówienie ObjectContexts wykracza poza zakres tego dokumentu. |
| MyContext (DbConnection, model DbCompiledModel, wartość logiczna) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool) |DbContext (DbConnection, model DbCompiledModel, wartość logiczna); |Połączenie musi można wywnioskować na podstawie mapowania niezależnego fragmentu i klucz. Połączenie nie można podać jako danych wejściowych (chyba że te dane wejściowe korzystał już z mapy niezależnych oraz klucz). Model i wartość logiczną są przekazywane do konstruktora klasy podstawowej. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Identyfikator niezależnego fragmentu wdrożenia schematu za pomocą migracji EF
Zarządzanie automatyczne schematu jest udogodnienie pochodzącymi z programu Entity Framework. W kontekście aplikacji przy użyciu narzędzi elastycznej bazy danych ma zostać zachowany tej możliwości, aby automatycznie udostępniać schemat odłamków nowo utworzone po dodaniu bazy danych podzielonej aplikacji. Pierwotnym zastosowaniem jest zwiększenie pojemności w warstwie danych podzielonej aplikacji za pomocą EF. Zależne EF jego możliwości zarządzania schematu zmniejsza nakład pracy administracyjnej bazy danych z aplikacją podzielonej oparty na EF. 

Wdrożenia schematu za pomocą migracji EF najlepiej **bez otwierania połączenia**. Dzięki temu nie trzeba scenariusz routingu zależne od danych, który polega na otwarte połączenie udostępniane przez interfejs API klienta elastycznej bazy danych. Inna różnica polega na wymaganie spójności: podczas pożądane w celu zapewnienia spójności dla wszystkich połączeń routingu zależne od danych do ochrony przed manipulowania mapy równoczesnych niezależnego fragmentu nie jest problemem za pomocą początkowego wdrożenia schemat na nowe bazy danych ma jeszcze nie został zarejestrowany w mapie niezależnego fragmentu i jeszcze nie została przydzielona do przechowywania shardlets. W związku z tym polega na połączenia zwykłej bazy danych dla tego scenariusza, w przeciwieństwie zależne od danych routingu.  

Prowadzi to do podejście gdzie wdrożenia schematu za pomocą migracji EF jest ściśle powiązane z rejestracją nową bazę danych jako niezależnego fragmentu w mapie niezależnych aplikacji. To opiera się na następujące wymagania wstępne: 

* Bazy danych już istnieje. 
* Baza danych jest pusta — posiada nie użytkowników schematu i danych użytkownika.
* Bazy danych nie są jeszcze dostępne za pośrednictwem interfejsów API klienta elastycznej bazy danych dla routingu zależne od danych. 

O spełnieniu tych wymaganiach wstępnych, można utworzyć zwykły bez otwartego **SqlConnection** można rozpocząć poza EF migracji wdrożenia schematu. Poniższy przykładowy kod przedstawia tej metody. 

        // Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
        // and kick off EF intialization of the database to deploy schema 

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


W tym przykładzie pokazano metodę **RegisterNewShard** który rejestruje niezależnego fragmentu w mapie niezależnego fragmentu, wdraża schematu za pomocą migracji EF i przechowuje mapowanie klucz dzielenia na fragmenty, aby niezależnego fragmentu. Zależy od konstruktora **DbContext** podklasy (**ElasticScaleContext** w próbce) pobierającej parametrów połączenia SQL jako dane wejściowe. Kod tego konstruktora jest proste, jak przedstawiono na poniższym przykładzie: 

        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // You want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 

Co najmniej jedna może być używana wersja konstruktora dziedziczona z klasy podstawowej. Jednak kod musi zapewnić, że inicjator domyślne EF jest używana podczas łączenia. Dlatego krótkim przekierować do metody statycznej przed wywołaniem do konstruktora klasy podstawowej z parametrami połączenia. Należy pamiętać, że rejestracja odłamków powinno być ono uruchomione w innej domeny aplikacji lub proces, aby upewnić się, czy ustawienia inicjatora EF nie są w konflikcie. 

## <a name="limitations"></a>Ograniczenia
Metod opisanych w tym dokumencie pociąga za sobą kilka ograniczeń: 

* EF aplikacji, które używają **LocalDb** najpierw należy przeprowadzić migrację do zwykłej bazy danych programu SQL Server przed rozpoczęciem korzystania z biblioteki klienta elastycznej bazy danych. Skalowania aplikacji za pośrednictwem dzielenia na fragmenty o elastycznego skalowania nie jest możliwe za pomocą **LocalDb**. Należy pamiętać, że programowanie można nadal używać **LocalDb**. 
* Wszelkie zmiany do aplikacji, które oznacza zmiany schematu bazy danych muszą przejść przez migracje EF na wszystkich fragmentów. Przykładowy kod dla tego dokumentu nie pokazują, jak to zrobić. Przy użyciu polecenia Update-Database z parametrem ConnectionString iteracja wszystkich odłamków; lub Wyodrębnij skryptu T-SQL oczekujące migracji, za pomocą Update-Database skrypt opcji i dotyczą skryptu T-SQL z fragmentów.  
* Biorąc pod uwagę na żądanie, zakłada się, że wszystkie jego przetwarzanie bazy danych znajduje się w obrębie jednego niezależnego fragmentu określonej za pomocą klucza dzielenia na fragmenty udostępnionego przez żądanie. Jednak to założenie nie zawsze ma wartość true. Na przykład, gdy go nie jest możliwe do udostępnienia klucza dzielenia na fragmenty. Aby rozwiązać ten problem, udostępnia biblioteki klienckiej **MultiShardQuery** klasa implementująca abstrakcji połączenia, na potrzeby zapytań przez kilka fragmentów. Nauka korzystania **MultiShardQuery** w połączeniu z EF wykracza poza zakres tego dokumentu

## <a name="conclusion"></a>Podsumowanie
Kroki opisane w niniejszym dokumencie EF aplikacje mogą używać biblioteki klienta elastycznej bazy danych możliwości zależne od danych routingu w ramach refaktoryzacji elementu konstruktorów **DbContext** używane w aplikacji EF podklasy. To ogranicza zmiany wymagane do tych miejscach gdzie **DbContext** klasy już istnieje. Ponadto aplikacje EF można nadal korzystać z wdrażania automatycznego schematu łącząc kroki, które wywołują migracji konieczne EF z rejestracją nowych fragmentów i mapowania na mapie niezależnego fragmentu. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
