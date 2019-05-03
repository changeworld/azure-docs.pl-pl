---
title: Łączenie i indeksu usługi Azure SQL Database zawartości przy użyciu indeksatorów — usługa Azure Search
description: Dowiedz się, jak przeszukiwać dane w usłudze Azure SQL Database przy użyciu indeksatorów w celu wyszukiwania pełnotekstowego w usłudze Azure Search. Ten artykuł dotyczy połączeń, konfiguracji indeksatora i wprowadzanie danych.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: c23933e7f379a438d436fd99c5fea7899c5891ef
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025353"
---
# <a name="connect-to-and-index-azure-sql-database-content-using-azure-search-indexers"></a>Nawiązywanie połączenia i indeksu usługi Azure SQL Database zawartości przy użyciu indeksatorów usługi Azure Search

Zanim można tworzyć zapytania [indeksu usługi Azure Search](search-what-is-an-index.md), należy go wypełnić ze swoimi danymi. Jeśli dane są przechowywane w bazie danych Azure SQL **indeksator usługi Azure Search dla usługi Azure SQL Database** (lub **indeksator usługi Azure SQL** w skrócie) można zautomatyzować proces indeksowania, co oznacza mniej kod, aby zapisywać i mniej Infrastruktura zajmować się.

W tym artykule opisano mechanika przy użyciu [indeksatory](search-indexer-overview.md), ale także w tym artykule opisano funkcje dostępne tylko w bazach danych Azure SQL (na przykład zintegrowane śledzenie zmian). 

Oprócz bazy danych Azure SQL, usługa Azure Search udostępnia indeksatory dla [usługi Azure Cosmos DB](search-howto-index-cosmosdb.md), [usługi Azure Blob storage](search-howto-indexing-azure-blob-storage.md), i [usługi Azure table storage](search-howto-indexing-azure-tables.md). Aby zgłosić żądanie pomocy technicznej dla innych źródeł danych, przekazania swojej opinii na [forum opinii w usłudze Azure Search](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indeksatory i źródła danych

A **źródła danych** określa danych do indeksu, poświadczenia na potrzeby dostępu do danych i zasady, które efektywnie zidentyfikować zmiany danych (nowe, zmodyfikowane lub usunięte wiersze). Jest on zdefiniowany jako niezależnym zasobem, dzięki czemu mogą być używane przez wiele indeksatorów.

**Indeksatora** jest zasobem, który łączy się z jednego źródła danych z indeksu wyszukiwania docelowych. Indeksator jest używany w następujący sposób:

* Wykonaj jednorazowej kopii danych do wypełniania indeksu.
* Aktualizowanie indeksu ze zmianami w źródle danych, zgodnie z harmonogramem.
* Uruchamianie na żądanie do aktualizacji indeksu, zgodnie z potrzebami.

Pojedynczy indeksator może używać tylko jedną tabelę lub widok, ale można utworzyć wiele indeksatorów, jeśli chcesz wypełnić wiele indeksów wyszukiwania. Aby uzyskać więcej informacji na temat pojęć, zobacz [Operacje indeksatora: Typowy przepływ pracy](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow).

Można skonfigurować i skonfigurować indeksator usługi Azure SQL przy użyciu:

* Kreator importu danych w [witryny Azure portal](https://portal.azure.com)
* Usługa Azure Search [zestawu SDK platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* Usługa Azure Search [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)

W tym artykule użyjemy interfejsu API REST, aby utworzyć **indeksatory** i **źródeł danych**.

## <a name="when-to-use-azure-sql-indexer"></a>Kiedy należy używać indeksator SQL Azure
W zależności od kilku czynników związanych z danymi użytkowania indeksator usługi Azure SQL może być lub może nie być odpowiednie. Jeśli dane spełnia następujące wymagania, możesz użyć indeksator usługi Azure SQL.

| Kryteria | Szczegóły |
|----------|---------|
| Dane pochodzą z jednej tabeli lub widoku | Jeśli dane są rozproszone w wielu tabel, można utworzyć pojedynczy widok danych. Jednak jeśli używasz widoku, nie będzie można na potrzeby wykrywania zmian programu SQL Server, zintegrowane Odśwież indeks o zmiany przyrostowe. Aby uzyskać więcej informacji, zobacz [przechwytywania zmienione, a także usunięte wiersze](#CaptureChangedRows) poniżej. |
| Typy danych są zgodne | Większość, ale nie wszystkie typy SQL są obsługiwane w ramach indeksu usługi Azure Search. Aby uzyskać listę, zobacz [mapowania typów danych](#TypeMapping). |
| Synchronizacja danych w czasie rzeczywistym nie jest wymagane | Indeksator może ponownego poindeksowania danych tabeli co najwyżej co pięć minut. Jeśli dane ulegają częstym zmianom, a zmiany zostaną odzwierciedlone w indeksie w ciągu sekund lub minut pojedynczego, zaleca się używanie [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) lub [zestawu .NET SDK](search-import-data-dotnet.md) bezpośrednio wypychania zaktualizowanych wierszy. |
| Możliwe jest Indeksowanie przyrostowe | W przypadku dużych zestawów danych i zamierzasz uruchomić indeksator zgodnie z harmonogramem, usługa Azure Search musi mieć możliwość efektywnie Zidentyfikuj nowe, zmodyfikowane lub usunięte wiersze. Indeksowanie non-incremental jest dozwolone tylko, jeśli jesteś indeksowania na żądanie (a nie według harmonogramu) lub indeksowania mniej niż 100 000 wierszy. Aby uzyskać więcej informacji, zobacz [przechwytywania zmienione, a także usunięte wiersze](#CaptureChangedRows) poniżej. |

> [!NOTE] 
> Usługa Azure Search obsługuje tylko uwierzytelnianie programu SQL Server. Jeśli potrzebujesz pomocy technicznej dla uwierzytelniania hasła usługi Azure Active Directory, Zagłosuj na to [sugestię w witrynie UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Utwórz indeksator usługi Azure SQL

1. Utwórz źródło danych:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Możesz uzyskać parametry połączenia z [witryny Azure portal](https://portal.azure.com); użyj `ADO.NET connection string` opcji.

2. Utwórz indeks usługi Azure Search docelowy, jeśli nie masz jeszcze takiego. Można utworzyć indeksu przy użyciu [portal](https://portal.azure.com) lub [interfejsu API tworzenia indeksu](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Upewnij się, że schemat indeksu docelowego jest zgodny ze schematem tabeli źródłowej — zobacz [mapowanie między SQL i usługa Azure search typy danych](#TypeMapping).

3. Tworzenie indeksatora, nadając mu nazwę i odwołanie do indeksu danych w źródłowym i docelowym:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

Indeksator utworzone w ten sposób nie ma zgodnie z harmonogramem. Automatycznie uruchomiony po po jego utworzeniu. Uruchom go ponownie w każdej chwili **uruchomić indeksator** żądania:

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2019-05-06
    api-key: admin-key

Możesz dostosować kilka aspektów zachowania indeksatora, takich jak rozmiar partii i liczby dokumentów można było pominąć, zanim środowisko wykonawcze indeksator zakończy się niepowodzeniem. Aby uzyskać więcej informacji, zobacz [Tworzenie interfejsu API indeksatora](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

Konieczne może być Zezwalaj usługom platformy Azure, nawiązać połączenia z bazą danych. Zobacz [łączenie z platformy Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) Aby uzyskać instrukcje, jak to zrobić.

Aby monitorować historię stanu i wykonywanie indeksatora (liczba elementów indeksowane, błędy itp.), użyj **stan indeksatora** żądania:

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2019-05-06
    api-key: admin-key

Odpowiedź powinna wyglądać podobnie do poniższej:

    {
        "\@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }

Historia wykonywania zawiera maksymalnie 50 ostatnio wykonanych wykonań, które są sortowane w kolejności chronologicznej odwrotnej (tak, aby najnowsze wykonywania wykorzystasz w odpowiedzi).
Dodatkowe informacje na temat odpowiedzi można znaleźć w [pobierania stanu indeksatora](https://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Uruchamianie indeksatory zgodnie z harmonogramem
Można także porządkować indeksatora okresowe uruchamianie zgodnie z harmonogramem. Aby to zrobić, Dodaj **harmonogram** właściwości podczas tworzenia lub aktualizowania indeksatora. W poniższym przykładzie przedstawiono żądanie PUT, aby zaktualizować indeksatora:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**Interwał** parametr jest wymagany. Interwał odnosi się do czasu między rozpoczęciem dwóch następujących po sobie indeksatora wykonań. Najmniejszy dozwolony interwał wynosi 5 minut; najdłuższej to jeden dzień. Musi być sformatowany jako wartość XSD "dayTimeDuration" (ograniczony podzestaw [czas trwania ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) wartości). Jest to wzorzec: `P(nD)(T(nH)(nM))`. Przykłady: `PT15M` co 15 minut `PT2H` co 2 godziny.

Opcjonalny **startTime** wskazuje, kiedy należy rozpocząć zaplanowanego wykonania. Jeśli zostanie pominięty, używany jest bieżący czas UTC. Teraz można w przeszłości — w którym przypadku pierwszego wykonania jest zaplanowane tak, jakby działał nieprzerwanie od startTime indeksatora.  

Tylko jedno wykonywanie indeksatora można uruchamiać jednocześnie. Jeśli indeksatora jest uruchomiona, gdy zaplanowano jego wykonanie, wykonanie jest odroczone do czasu następnego zaplanowanego czasu.

Rozważmy przykład się to bardziej konkretne. Załóżmy, że firma Microsoft skonfigurowany harmonogram co godzinę następujące:

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Oto, co się dzieje:

1. Pierwsze wykonanie indeksatora rozpoczyna się o lub około 1 marca 2015 r. od 12:00 czasu UTC.
2. Załóżmy, że to wykonywanie trwa 20 minut (lub w dowolnej chwili mniejsza niż 1 godzina).
3. Wykonanie drugiej rozpoczyna się o lub około 1 marca 2015 r. 1:00:00
4. Teraz załóżmy, że ma ponad godzinę — na przykład 70 minut — w tym wykonywania, więc zostanie zakończona około 2:10:00
5. Jest teraz 2:00:00, czas wykonywania trzeci start. Jednak ponieważ drugi wykonywania od 01: 00 nadal działa, trzeci wykonanie zostało pominięte. Trzeci wykonywania rozpoczyna się od 3: 00.

Dodać, zmienić lub usunąć harmonogram dla istniejącego indeksatora za pomocą **indeksatora PUT** żądania.

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Przechwytywanie nowego, zmiany i usunięcia wierszy

Usługa Azure Search używa **przyrostowe indeksowania** pozwala uniknąć ponownego poindeksowania danych całej tabeli lub widoku, za każdym razem, gdy jest uruchamiany indeksator. Usługa Azure Search udostępnia dwie zmiany zasad wykrywania do obsługi przyrostowego indeksowania. 

### <a name="sql-integrated-change-tracking-policy"></a>Zasady śledzenie zmian zintegrowane ze środowiskiem SQL
Jeśli baza danych SQL obsługuje [śledzenie zmian](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server), firma Microsoft zaleca używanie **SQL zintegrowane zmienić zasady śledzenia**. Jest to najbardziej efektywny sposób zasad. Ponadto umożliwia usłudze Azure Search zidentyfikować usunięte wiersze bez konieczności dodawania kolumny jawne "usuwania nietrwałego" do tabeli.

#### <a name="requirements"></a>Wymagania 

+ Wymagania dotyczące wersji bazy danych:
  * SQL Server 2012 SP3 lub nowszy, jeśli używasz programu SQL Server na maszynach wirtualnych platformy Azure.
  * Usługa Azure SQL Database V12, jeśli używasz usługi Azure SQL Database.
+ Tabele tylko (Brak widoków). 
+ W bazie danych [śledzenia zmian Włącz](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) dla tabeli. 
+ Nie złożony klucz podstawowy (klucza podstawowego zawierających więcej niż jedną kolumnę) w tabeli.  

#### <a name="usage"></a>Sposób użycia

Aby użyć tych zasad, należy utworzyć lub zaktualizować źródła danych w następujący sposób:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

Gdy za pomocą zasad, śledzenia zmian programu SQL, zintegrowane nie określaj zasady wykrywania usuwania osobne dane — tej zasady ma wbudowaną obsługę identyfikowanie usunięte wiersze. Jednak dla usunięć być wykryte "automagically", klucz dokumentu w indeksie wyszukiwania musi być taka sama jak klucz podstawowy w tabeli SQL. 

> [!NOTE]  
> Korzystając z [TRUNCATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/truncate-table-transact-sql) Aby usunąć dużą liczbę wierszy z tabeli SQL, indeksator musi być [resetowania](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) zresetować stanu, aby wczytać usunięcia wiersza śledzenia zmian.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Zasady wykrywania zmian znacznik limitu górnego

Te zasady wykrywania zmian opiera się na kolumnę "znacznik limitu górnego" Przechwytywanie wersji lub godzinę ostatniej aktualizacji wiersza. Jeśli używasz widoku, należy użyć zasad znacznik limitu górnego. Kolumna znacznik limitu górnego musi spełniać następujące wymagania.

#### <a name="requirements"></a>Wymagania 

* Wszystkie operacje wstawiania określ wartości dla kolumny.
* Wszystkie aktualizacje z elementem również zmienić wartość kolumny.
* Wartość tej kolumny zwiększa się wraz z każdym insert nebo update.
* Zapytania z następującymi gdzie i w klauzuli ORDER BY, które mogą być wykonywane efektywnie: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Zdecydowanie zalecamy używanie [rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) typ danych dla kolumny znacznik limitu górnego. Jeśli jest używany dowolny inny typ danych, śledzenie zmian nie jest gwarantowane do przechwytywania wszystkich zmian obecności transakcji wykonywania wątkom kwerendę indeksatora. Korzystając z **rowversion** w konfiguracji tylko do odczytu repliki, należy wskazać indeksatora w replice podstawowej. Tylko replikę podstawową może służyć do scenariuszy synchronizacji danych.

#### <a name="usage"></a>Sposób użycia

Można użyć zasad znacznik limitu górnego, utworzyć lub zaktualizować źródła danych w następujący sposób:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }

> [!WARNING]
> Jeśli tabela źródłowa nie ma indeksu w kolumnie znacznik limitu górnego, zapytaniami używanymi przez indeksator SQL może limit czasu. W szczególności `ORDER BY [High Water Mark Column]` klauzula wymaga indeksu do sprawnego działania w tabeli zawierającej wiele wierszy.
>
>

Jeśli wystąpią błędy przekroczenia limitu czasu, możesz użyć `queryTimeout` indeksatora ustawienie konfiguracji umożliwiające ustawienie limitu czasu zapytania na wartość większą niż 5-minutowych domyślna wartość limitu czasu. Na przykład aby ustawić limit czasu 10 minut, należy utworzyć lub zaktualizować indeksator o następującej konfiguracji:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Można również wyłączyć `ORDER BY [High Water Mark Column]` klauzuli. Jednak nie jest to zalecane, ponieważ w przypadku wykonywania indeksatora zostanie przerwany w wyniku błędu, indeksator musi ponownie przetworzyć wszystkie wiersze Jeśli później — działa, nawet wtedy, gdy indeksatora został już przetworzony prawie wszystkie wiersze przez razem, gdy została ona przerwana. Aby wyłączyć `ORDER BY` klauzuli, użyj `disableOrderByHighWaterMarkColumn` ustawienie w definicja indeksatora:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Elastyczne zasady usuwania wykrywania usunięcia kolumny
Usunięcie wierszy z tabeli źródłowej prawdopodobnie chcesz usunąć te wiersze z z indeksu wyszukiwania. Jeśli używasz zasad śledzenia zmian programu SQL, zintegrowane, to jest wykonywane dla Ciebie. Jednak zasad śledzenia zmian znacznik limitu górnego nie będą pomocne z usuniętymi wierszami. Co można zrobić w takiej sytuacji?

Jeśli wiersze są fizycznie usunięty z tabeli, usługa Azure Search nie ma możliwości wywnioskować obecności rekordy, które już istnieją.  Jednak można użyć techniki "opcji soft-delete" logicznie usunięcie wierszy bez ich usuwania z tabeli. Dodaj kolumnę do wierszy z tabeli lub widoku i Oznacz jako usunięty, użycie tej kolumny.

Gdy korzystające z techniki opcji soft-delete, można określić zasady usuwania nietrwałego w następujący sposób podczas tworzenia lub aktualizowania źródła danych:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

**SoftDeleteMarkerValue** musi być ciągiem — ciąg reprezentujący wartość rzeczywistego użycia. Na przykład jeśli kolumna liczb całkowitych, gdzie oznaczone jako usunięte wiersze z wartością 1, użyć `"1"`. Jeśli masz kolumnę BITOWA, gdzie usunięte wiersze są oznaczone wartość logiczną PRAWDA, należy użyć literału ciągu `True` lub `true`, nie ma znaczenia w przypadku.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-search-data-types"></a>Mapowanie między typami danych SQL i Azure Search
| Typ danych SQL | Dozwolone typy pól indeks docelowy | Uwagi |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| prawdziwe, float |Edm.Double, Edm.String | |
| Smallmoney, numeryczne dziesiętna pieniędzy |Edm.String |Usługa Azure Search nie obsługuje konwersji typów dziesiętna do Edm.Double, ponieważ spowoduje to utratę dokładności |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Ciąg SQL może służyć do wypełnienia pola Collection(Edm.String), jeśli ciąg reprezentuje tablicę ciągów w formacie JSON: `["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| Lokalizacja geograficzna |Edm.GeographyPoint |Obsługiwane są tylko lokalizacja geograficzna wystąpień typu punktu z 4326 SRID, (jest to ustawienie domyślne) |
| rowversion |ND |Wersja wiersza kolumny nie mogą być przechowywane w indeksie wyszukiwania, ale może służyć do śledzenia zmian |
| czas, przedział czasu, binary, varbinary, obraz, xml, geometrii, typy CLR |ND |Nieobsługiwane |

## <a name="configuration-settings"></a>Ustawienia konfiguracji
Indeksator SQL udostępnia kilka ustawień konfiguracji:

| Ustawienie | Typ danych | Przeznaczenie | Wartość domyślna |
| --- | --- | --- | --- |
| queryTimeout |string |Ustawia limit czasu w celu wykonywania zapytań SQL |5 minut ("00: 05:00") |
| disableOrderByHighWaterMarkColumn |bool |Powoduje, że zapytania SQL używany przez zasady znacznik limitu górnego, aby pominąć klauzuli ORDER BY. Zobacz [zasad znacznik limitu górnego](#HighWaterMarkPolicy) |false |

Te ustawienia są używane w `parameters.configuration` obiektu w definicja indeksatora. Na przykład aby ustawić limit czasu zapytania do 10 minut, należy utworzyć lub zaktualizować indeksator o następującej konfiguracji:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>Często zadawane pytania

**Pyt.: Czy można używać indeksator usługi Azure SQL z bazy danych SQL, uruchomione na maszynach wirtualnych IaaS na platformie Azure?**

Tak. Jednakże należy zezwolić na usługi wyszukiwania w celu połączenia z bazą danych. Aby uzyskać więcej informacji, zobacz [skonfigurować połączenie z indeksator usługi Azure Search do programu SQL Server na Maszynie wirtualnej platformy Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**Pyt.: Czy można używać indeksator usługi Azure SQL, z bazami danych SQL, uruchamiane lokalnie?**

Nie bezpośrednio. Firma Microsoft nie zaleca się i nie obsługuje bezpośredniego połączenia, ponieważ spowoduje to więc wymagałoby otwarcie bazy danych w celu ruch internetowy. Klientów zakończyły się powodzeniem, w tym scenariuszu przy użyciu technologii most, takich jak usługi Azure Data Factory. Aby uzyskać więcej informacji, zobacz [wypychanie danych do indeksu usługi Azure Search przy użyciu usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector).

**Pyt.: Czy można używać indeksator usługi Azure SQL z bazy danych innych niż SQL Server działających w modelu IaaS na platformie Azure?**

Nie. Nie obsługujemy tego scenariusza, ponieważ nigdy nie próbowaliśmy indeksator o żadnych baz danych innych niż SQL Server.  

**Pyt.: Można tworzyć wiele indeksatorów uruchomione zgodnie z harmonogramem?**

Tak. Jednak tylko jeden indeksator może działać na jednym węźle w tym samym czasie. Wiele indeksatorów uruchomionych jednocześnie, należy rozważyć skalowanie w górę usługi wyszukiwania do więcej niż jednej jednostki wyszukiwania.

**Pyt.: Uruchamianie indeksatora wpływa na obciążenia zapytania?**

Tak. Indeksator jest uruchamiane na jednym z węzłów w usłudze wyszukiwania, a ten węzeł zasoby są współdzielone między indeksowania i obsługująca ruch zapytania i inne żądania interfejsu API. Jeśli uruchamianie obciążeń z intensywnym wykorzystaniem indeksowania i zapytania i wystąpi wysoki stopień 503 błędy lub rosnącej czasy odpowiedzi, należy wziąć pod uwagę [skalowanie w górę usługi wyszukiwania](search-capacity-planning.md).

**Pyt.: Czy mogę używać repliki pomocniczej w [klastra trybu failover](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) jako źródło danych?**

To zależy. Pełne indeksowaniu tabelę lub widok, można użyć jako repliki pomocniczej. 

Przyrostowe indeksowania usługi Azure Search obsługuje dwie zasady wykrywania zmian: Zintegrowane ze środowiskiem SQL rozwiązania change tracking and znacznik limitu górnego.

W trybie tylko do odczytu replikach bazy danych SQL database nie obsługuje śledzenie zmian zintegrowane. W związku z tym należy użyć zasad znacznik limitu górnego. 

Nasze standardowe zaleca się użycie rowversion typ danych dla kolumny znacznik limitu górnego. Jednak przy użyciu rowversion opiera się na SQL Database `MIN_ACTIVE_ROWVERSION` funkcji, która nie jest obsługiwana w trybie tylko do odczytu replik. W związku z tym Jeśli używasz rowversion musi wskazywać indeksator do repliki podstawowej.

Jeśli spróbujesz użyć rowversion repliki tylko do odczytu, zostanie wyświetlony następujący błąd: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**Pyt.: Śledzenie zmian znacznik limitu górnego można używać alternatywnego, kolumny bez rowversion?**

Nie jest zalecane. Tylko **rowversion** umożliwia synchronizację danych niezawodne. Jednak w zależności od logika aplikacji może być bezpieczne jeśli:

+ Można upewnić się, że po uruchomieniu indeksatora, nie ma żadnych oczekujących transakcji w tabeli, które są indeksowane (na przykład wszystkie aktualizacje tabeli przeprowadzane tak partii zgodnie z harmonogramem, i planowanie uruchamiania indeksatora usługi Azure Search jest równa należy unikać nakładania się wraz z aktualizacją tabeli Harmonogram).  

+ Możesz to zrobić okresowe pełne reindex do pobrania wszystkich pominiętych wierszy. 