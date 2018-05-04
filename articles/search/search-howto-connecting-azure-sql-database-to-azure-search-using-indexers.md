---
title: Połączenie bazy danych Azure SQL do usługi Azure Search przy użyciu indeksatorów | Dokumentacja firmy Microsoft
description: Informacje o sposobie pobierania danych z bazy danych SQL Azure do indeksu usługi Azure Search przy użyciu indeksatorów.
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
ms.openlocfilehash: aa24c3197af28101b2f3a0acda6d0ae81b9e96d5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Połączenie bazy danych SQL Azure do usługi Azure Search przy użyciu indeksatorów

Zanim można zbadać [indeksu usługi Azure Search](search-what-is-an-index.md), musisz wypełnić je z danymi. Jeśli dane znajduje się w bazie danych Azure SQL **indeksator usługi Azure Search bazy danych SQL Azure** (lub **indeksatora Azure SQL** skrócie) można zautomatyzować proces indeksowania, co oznacza mniej kod, aby zapisać i mniej infrastrukturę do interesujących.

W tym artykule omówiono mechanika przy użyciu [indeksatory](search-indexer-overview.md), ale także w tym artykule opisano funkcje dostępne tylko w przypadku baz danych Azure SQL (na przykład zintegrowane śledzenie zmian). 

Oprócz bazy danych Azure SQL, Azure Search udostępnia indeksatory dla [bazy danych Azure rozwiązania Cosmos](search-howto-index-cosmosdb.md), [magazynu obiektów Blob Azure](search-howto-indexing-azure-blob-storage.md), i [magazynu tabel Azure](search-howto-indexing-azure-tables.md). Aby poprosić o obsługę innych źródeł danych, reagowanie na [forum opinii usługi Azure Search](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indeksatorami i źródłami danych

A **źródła danych** Określa, które dane do indeksu, poświadczenia na potrzeby dostępu do danych i zasad, które skutecznie zidentyfikować zmiany danych (nowych, zmodyfikowanych lub usuniętych wierszy). Jest on zdefiniowany jako niezależnym zasobem, dzięki czemu mogą być używane przez wiele indeksatorów.

**Indeksatora** jest z zasobem, który nawiązuje połączenie z jednym źródłem danych z indeksem wyszukiwania docelowych. Indeksator jest używana w następujący sposób:

* Wykonywać jednorazowe kopię danych do wypełniania indeksu.
* Aktualizowanie indeksu ze zmianami w źródle danych, zgodnie z harmonogramem.
* Uruchom na żądanie można zaktualizować indeksu, zgodnie z potrzebami.

Jeden indeksator zajmowane tylko jedną tabelę lub widok, ale można utworzyć wiele indeksatorów, aby wypełnić wiele indeksy wyszukiwania. Aby uzyskać więcej informacji dotyczących pojęć, zobacz [Operacje indeksatora: typowy przepływ pracy](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow).

Można skonfigurować i skonfigurować indeksator Azure SQL za pomocą:

* Kreator importu danych w [portalu Azure](https://portal.azure.com)
* Usługa Azure Search [zestawu .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* Usługa Azure Search [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)

W tym artykule, użyjemy interfejsu API REST, aby utworzyć **indeksatory** i **źródeł danych**.

## <a name="when-to-use-azure-sql-indexer"></a>Kiedy należy używać indeksatora SQL Azure
W zależności od wielu czynników odnoszących się do danych użycie indeksatora Azure SQL może lub nie może być odpowiednie. Jeśli dane spełnia następujące wymagania, możesz użyć indeksatora Azure SQL.

| Kryteria | Szczegóły |
|----------|---------|
| Dane pochodzą z pojedynczej tabeli lub widoku | Jeśli dane są rozproszone w wielu tabel, można utworzyć jeden widok danych. Jednak jeśli używasz widoku, nie będzie można używać wykrywania zmian programu SQL Server zintegrowanego odświeżyć indeks o zmiany przyrostowe. Aby uzyskać więcej informacji, zobacz [Przechwytywanie zmienione i usunąć wiersze](#CaptureChangedRows) poniżej. |
| Typy danych są zgodne | Większość, ale nie wszystkie typy SQL są obsługiwane w indeksie usługi wyszukiwanie Azure. Aby uzyskać listę, zobacz [mapowania typów danych](#TypeMapping). |
| Synchronizacja danych w czasie rzeczywistym nie jest wymagane | Indeksator można ponownie indeksu tabeli, co najwyżej co pięć minut. Jeśli dane ulegają częstym zmianom i zmiany muszą być odzwierciedlone w indeksie w ciągu sekund lub minut pojedynczego, firma Microsoft zaleca używanie [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) lub [zestawu .NET SDK](search-import-data-dotnet.md) wypychanej bezpośrednio zaktualizowanych wierszy. |
| Możliwe jest przyrostowe indeksowania | Jeśli masz dużych zestawów danych i zamierzasz uruchomić indeksatora zgodnie z harmonogramem, musi być mógł zidentyfikować nowych, zmodyfikowane lub usunięte wiersze, wydajne usługi Azure Search. Indeksowanie przyrostowe nie jest dozwolona tylko indeksowania na żądanie (nie zgodnie z harmonogramem), lub indeksowania mniej niż 100 000 wierszy. Aby uzyskać więcej informacji, zobacz [Przechwytywanie zmienione i usunąć wiersze](#CaptureChangedRows) poniżej. |

> [!NOTE] 
> Usługa Azure Search obsługuje tylko uwierzytelnianie programu SQL Server. Jeśli wymagana jest Obsługa uwierzytelniania hasła usługi Azure Active Directory, proszę Zagłosuj na to [sugestię UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Utwórz indeksator Azure SQL

1. Utwórz źródło danych:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Można pobrać parametrów połączenia z [portalu Azure](https://portal.azure.com); użyj `ADO.NET connection string` opcji.

2. Tworzenie indeksu usługi Azure Search docelowego, jeśli nie masz już. Można utworzyć indeksu przy użyciu [portal](https://portal.azure.com) lub [utworzyć indeks API](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Upewnij się, że schemat indeksu docelowego jest zgodny ze schematem tabeli źródłowej — zobacz [mapowanie między SQL i usługa Azure search typy danych](#TypeMapping).

3. Utwórz indeksator przez nadanie mu nazwy i odwołuje się do danych indeksu źródłowej i docelowej:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

Utworzone w ten sposób indeksatora nie ma zgodnie z harmonogramem. Automatycznie uruchomiony po po jego utworzeniu. Uruchom go ponownie w dowolnej chwili za pomocą **uruchomić indeksatora** żądania:

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2017-11-11
    api-key: admin-key

Można dostosować kilka aspektów zachowanie indeksatora, takich jak rozmiar partii i liczby dokumentów można pominięty zanim wykonywanie indeksatora nie powiedzie się. Aby uzyskać więcej informacji, zobacz [Tworzenie interfejsu API indeksatora](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

Konieczne może być Zezwalaj usługom platformy Azure do połączenia z bazą danych. Zobacz [łączenie z Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) instrukcje, jak to zrobić.

Aby monitorować historii stanu i wykonywanie indeksatora (liczba elementów indeksowane, błędów, itp.), należy użyć **stan indeksatora** żądania:

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2017-11-11
    api-key: admin-key

Odpowiedź powinna wyglądać podobnie do poniższej:

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
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

Historii wykonywania zawiera maksymalnie 50 ostatnio wykonanych wykonaniami, które są sortowane w kolejności odwrotnej (dzięki czemu wykonanie najnowsze zostanie osiągnięty jako pierwszy w odpowiedzi).
Dodatkowe informacje o odpowiedzi znajduje się w [pobierania stanu indeksatora](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Uruchom indeksatory zgodnie z harmonogramem
Można także porządkować indeksatora, aby okresowo uruchamiane zgodnie z harmonogramem. Aby to zrobić, należy dodać **harmonogram** właściwości podczas tworzenia lub aktualizowania indeksatora. W poniższym przykładzie pokazano żądanie PUT, aby zaktualizować indeksatora:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**Interwał** parametr jest wymagany. Interwał odwołuje się do czasu między rozpoczęciem dwóch wykonania kolejnych indeksatora. Najmniejszy dopuszczalny interwał wynosi 5 minut; najdłuższej to jeden dzień. Musi być sformatowany jako wartość XSD "dayTimeDuration" (ograniczony podzestaw [czasu trwania ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) wartości). Wzór: `P(nD)(T(nH)(nM))`. Przykłady: `PT15M` co 15 minut, `PT2H` co 2 godziny.

Opcjonalny **startTime** wskazuje, kiedy należy rozpocząć zaplanowane wykonania. Jeśli zostanie pominięty, bieżący czas UTC jest używany. Teraz można w przeszłości — w którym przypadku pierwszy wykonywania jest zaplanowane tak, jakby była uruchomiona indeksatora stale od momentu rozpoczęcia.  

Tylko jeden wykonywanie indeksatora można uruchamiać jednocześnie. Jeśli indeksatora działa podczas jego wykonywania, wykonanie zostanie wstrzymane do czasu następnego zaplanowanego czasu.

Zastanówmy się przykładem, aby ustawić to bardziej konkretną. Załóżmy, że firma Microsoft skonfigurowany harmonogram co godzinę następujące:

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Oto, co się stanie:

1. Rozpoczyna się pierwszego wykonywanie indeksatora, w tym miejscu lub wokół 1 marca 2015 od 12:00 UTC.
2. Załóżmy, że wykonanie tego ma 20 minut (lub w dowolnym momencie mniej niż 1 godzina).
3. Wykonanie drugiej rozpoczyna się w tym miejscu lub wokół 1 marca 2015 od godziny 1:00
4. Teraz załóżmy, że wykonanie tego ma ponad godzinę — na przykład 70 minut —, więc ukończy około 2:10:00
5. Jest teraz 2:00:00, czas wykonywania trzeci można uruchomić. Jednak ponieważ drugiego wykonywania od 01: 00 jest nadal uruchomiony, trzeci wykonywania jest pominięta. Trzeci wykonywania rozpoczyna się od 3: 00.

Można dodać, zmienić lub usunąć harmonogram dla istniejącego indeksatora za pomocą **indeksatora PUT** żądania.

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Przechwytywanie nowego, zmiany i usunięcia wierszy

Usługa Azure Search korzysta **przyrostowe indeksowania** Aby uniknąć konieczności ponownego indeksowania całej tabeli lub widoku każdym uruchomieniu indeksatora. Wyszukiwanie Azure oferuje dwie zmiany zasad wykrywania do obsługi przyrostowe indeksowania. 

### <a name="sql-integrated-change-tracking-policy"></a>Zasad śledzenia zmian zintegrowane ze środowiskiem SQL
Jeśli baza danych SQL obsługuje [śledzenie zmian](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server), zaleca się używanie **SQL zintegrowane zmienić zasady śledzenia**. Jest to najbardziej efektywny zasad. Ponadto pozwala on Azure Search zidentyfikować usuniętych wierszy bez konieczności dodać kolumnę explicit "usuwania nietrwałego" do tabeli.

#### <a name="requirements"></a>Wymagania 

+ Wymagania dotyczące wersji bazy danych:
  * SQL Server 2012 z dodatkiem SP3 lub nowszym, jeśli używasz programu SQL Server na maszynach wirtualnych platformy Azure.
  * Azure SQL Database V12, jeśli używasz bazy danych SQL Azure.
+ Tabele tylko (Brak widoków). 
+ W bazie danych [śledzenia zmian Włącz](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) dla tabeli. 
+ Nie złożonego klucza podstawowego (klucza podstawowego zawierające więcej niż jedną kolumnę) w tabeli.  

#### <a name="usage"></a>Sposób użycia

Aby użyć tej zasady, tworzenie lub aktualizowanie źródła danych w następujący sposób:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

Gdy za pomocą zasad, śledzenia zmian programu SQL zintegrowane nie określaj odrębne zasady usuwania i wykrywania — ta zasada ma wbudowaną obsługę identyfikowanie usuniętych wierszy. Jednak dla usunięć być wykryte "automagically", klucz dokumentu w indeksie wyszukiwania musi być taka sama jak klucz podstawowy w tabeli SQL. 

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Zasady wykrywania zmian znacznik limitu górnego

Te zasady wykrywania zmian polega na kolumnę "znacznik limitu górnego" Przechwytywanie wersji lub godzinę ostatniej aktualizacji wiersza. Jeśli używasz widoku, musisz użyć zasad znacznik limitu górnego. Kolumna znacznik limitu górnego musi spełniać następujące wymagania.

#### <a name="requirements"></a>Wymagania 

* Wstawia wszystkie Określ wartość dla kolumny.
* Wszystkie aktualizacje do elementu również zmienić wartość kolumny.
* Wartość w tej kolumnie zwiększa się wraz z każdym insert lub update.
* Zapytania z następującymi gdzie i klauzuli ORDER BY, które mogą być wykonywane wydajnie: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Zdecydowanie zaleca się używanie [rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) typu danych dla kolumny znacznik limitu górnego. Jeśli jest używany inny typ danych, śledzenie zmian nie jest gwarantowana Aby przechwytywać zmiany wszystkich obecności transakcji wykonywanych równocześnie z zapytania indeksatora. Korzystając z **rowversion** w konfiguracji z replikami tylko do odczytu musi wskazywać indeksatora w replice podstawowej. Tylko replika podstawowa może służyć do scenariuszy synchronizacji danych.

#### <a name="usage"></a>Sposób użycia

Aby użyć zasad, znacznik limitu górnego, utworzyć lub zaktualizować źródła danych w następujący sposób:

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
> Jeśli tabela źródłowa nie ma indeksu w kolumnie znacznik limitu górnego, zapytania używane przez indeksator SQL może upłynął limit czasu. W szczególności `ORDER BY [High Water Mark Column]` klauzula wymaga indeksu sprawne działanie, gdy tabela zawiera wiele wierszy.
>
>

Jeśli wystąpią błędy przekroczenia limitu czasu, można użyć `queryTimeout` ustawienie konfiguracji indeksatora ustawioną na wartość większą niż domyślny limit czasu 5-minutowy limit czasu zapytania. Na przykład ustawić limitu czasu na 10 minut, należy utworzyć lub zaktualizować indeksatora przy użyciu następującej konfiguracji:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Można również wyłączyć `ORDER BY [High Water Mark Column]` klauzuli. Jednak nie jest to zalecane, ponieważ jeśli wykonanie indeksatora zostało przerwane z powodu błędu, indeksatora musi ponownie przetworzyć wszystkie wiersze, jeśli była uruchamiana później -, nawet jeśli indeksatora przetworzył już prawie wszystkie wiersze w czasie, który został przerwany. Aby wyłączyć `ORDER BY` klauzuli, użyj `disableOrderByHighWaterMarkColumn` ustawienie w definicji indeksatora:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Elastyczne zasady usunąć wykrywania usuwanie kolumn
Usunięcie wierszy z tabeli źródłowej prawdopodobnie chcesz usunąć te wiersze z z indeksu wyszukiwania. Jeśli używasz zasad śledzenia zmian programu SQL zintegrowane, to jest poświęcony na obsługę dla Ciebie. Jednak zasad śledzenia zmian znacznik limitu górnego nie będą pomocne z usuniętymi wierszami. Co można zrobić w takiej sytuacji?

Jeśli wiersze są fizycznie usunięty z tabeli, usługi Azure Search nie ma możliwości wnioskować obecności rekordów, które już istnieją.  Jednak można użyć techniki "soft-delete" logicznie usunięcie wierszy bez ich usuwania z tabeli. Dodaj kolumnę do wierszy z tabeli lub widoku i Oznacz jako usunięte przy użyciu tej kolumny.

Korzystając z techniki usuwania nietrwałego, można określić zasady usuwania nietrwałego następujący podczas tworzenia lub aktualizowania źródła danych:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

**SoftDeleteMarkerValue** musi być ciągiem — Użyj reprezentacja ciągu wartości rzeczywistych. Na przykład, jeśli kolumna liczb całkowitych, w którym usuniętych wierszy są oznaczone ikoną z wartością 1, użyj `"1"`. Jeśli masz kolumny BIT, gdzie usuniętych wierszy są oznaczone ikoną z wartość logiczną PRAWDA, użyj `"True"`.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-search-data-types"></a>Mapowanie między typy danych SQL i usługi Azure Search
| Typ danych SQL | Dozwolone typy pól indeksu docelowego | Uwagi |
| --- | --- | --- |
| bitowe |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| rzeczywiste, float |Edm.Double, Edm.String | |
| Smallmoney, pieniędzy dziesiętną liczbowe |Edm.String |Usługa Azure Search nie obsługuje konwersji typu decimal do Edm.Double, ponieważ spowoduje to utratę dokładności |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Ciąg SQL może służyć do wypełnienia pola Collection(Edm.String), czy ciąg reprezentuje tablicę JSON ciągów: `["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| Lokalizacja geograficzna |Edm.GeographyPoint |Obsługiwane są tylko geograficzne wystąpienia typu punktu z 4326 SRID, (która jest wartością domyślną) |
| ROWVERSION |ND |Kolumny wersji wiersza nie mogą być przechowywane w indeksie wyszukiwania, ale może służyć do śledzenia zmian |
| czas, timespan, binary, varbinary, obraz, xml, geometry, typy CLR |ND |Nieobsługiwane |

## <a name="configuration-settings"></a>Ustawienia konfiguracji
Indeksator SQL udostępnia kilka ustawień konfiguracji:

| Ustawienie | Typ danych | Przeznaczenie | Wartość domyślna |
| --- | --- | --- | --- |
| queryTimeout |ciąg |Ustawia limit czasu wykonywania zapytań SQL |5 minut ("00: 05:00") |
| disableOrderByHighWaterMarkColumn |wartość logiczna |Powoduje, że zapytanie SQL używanego przez zasady znacznik limitu górnego, aby pominąć klauzuli ORDER BY. Zobacz [zasad znacznik limitu górnego](#HighWaterMarkPolicy) |false |

Te ustawienia są używane w `parameters.configuration` obiektu w definicji indeksatora. Na przykład aby ustawić limit czasu zapytania do 10 minut, utworzyć lub zaktualizować indeksatora przy użyciu następującej konfiguracji:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>Często zadawane pytania

**Pytanie: czy można użyć indeksatora Azure SQL z bazy danych SQL działające na maszynach wirtualnych IaaS na platformie Azure?**

Tak. Jednak należy zezwolić na połączenia z bazą danych usługi wyszukiwania. Aby uzyskać więcej informacji, zobacz [skonfigurować połączenie z indeksator usługi Azure Search do programu SQL Server na maszynie Wirtualnej platformy Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**Pytanie: czy można użyć indeksatora Azure SQL z bazy danych SQL, uruchamiane lokalnie?**

Nie bezpośrednio. Firma Microsoft nie zaleca się ani nie obsługuje bezpośredniego połączenia, jak to będzie wymagają Otwieranie baz danych na ruch internetowy. Klienci pomyślnie w tym scenariuszu przy użyciu technologii mostek, takich jak fabryki danych Azure. Aby uzyskać więcej informacji, zobacz [wypychanie danych do indeksu usługi Azure Search przy użyciu fabryki danych Azure](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector).

**Pytanie: czy można użyć indeksatora Azure SQL z bazami danych innych niż SQL Server działającego w IaaS na platformie Azure?**

Nie. Ten scenariusz nie jest obsługiwane, ponieważ nie zostało to jeszcze przetestowaliśmy indeksator o żadnych baz danych innych niż SQL Server.  

**Pytanie: czy można utworzyć wiele indeksatorów uruchomionych na podstawie harmonogramu?**

Tak. Jednak tylko jeden indeksator mogą działać na jednym węźle jednocześnie. Wiele indeksatorów równoczesne działanie, należy wziąć pod uwagę skalowaniu usługi wyszukiwania do więcej niż jednej jednostki wyszukiwania.

**Pytanie: czy pracą indeksator mają wpływ na Moje zapytania?**

Tak. Indeksator działa w jednym z węzłów w swojej usłudze wyszukiwania i zasobów w tym węźle są udostępniane między indeksowania oraz obsługująca ruch zapytania i inne żądania interfejsu API. Uruchom intensywnych obciążeń indeksowanie i zapytania i wystąpi wysoki współczynnik błędów 503 lub zwiększa czasy odpowiedzi, warto rozważyć [skalowaniu usługi wyszukiwania](search-capacity-planning.md).

**Pytanie: czy można używać repliki pomocniczej w [klastra pracy awaryjnej](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) jako źródło danych?**

To zależy. Pełna indeksowaniu tabelę lub widok, można użyć replikę pomocniczą. 

Przyrostowe indeksowania obsługuje usługi Azure Search, dwie zmiany zasad wykrywania: SQL zintegrowane zmienić śledzeniem i wysoki znacznik limitu górnego.

W trybie tylko do odczytu replikach baza danych SQL nie obsługuje śledzenie zmian zintegrowane. W związku z tym musisz użyć zasad znacznik limitu górnego. 

Nasze standardowe zaleca się użycie rowversion typ danych dla kolumny znacznik limitu górnego. Jednak przy użyciu rowversion zależy od usługi SQL Database `MIN_ACTIVE_ROWVERSION` funkcja, która nie jest obsługiwana w trybie tylko do odczytu replikach. Dlatego jeśli używasz rowversion musi wskazywać indeksatora do repliki podstawowej.

Jeśli spróbujesz użyć rowversion repliki tylko do odczytu, zostanie wyświetlony następujący błąd: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**Pytanie: czy śledzenie zmian znacznik limitu górnego można użyć alternatywnych, kolumny z systemem innym niż rowversion?**

Nie jest zalecane. Tylko **rowversion** umożliwia synchronizacji danych. Jednak w zależności od logika aplikacji może być bezpieczne czy:

+ Można upewnij się, że po uruchomieniu indeksatora, nie ma żadnych oczekujących transakcji w tabeli, która jest indeksowany (na przykład tabeli wszystkie aktualizacje wystąpić, ponieważ partia na harmonogram i harmonogram indeksator usługi Azure Search jest ustawiony na uniknąć nakładania się przy użyciu aktualizacji tabeli zaplanować).  

+ Okresowo są pełne indeksowanie do pobrania wszystkich pominiętych wierszy. 
