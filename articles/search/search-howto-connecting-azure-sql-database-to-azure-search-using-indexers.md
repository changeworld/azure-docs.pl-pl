---
title: Wyszukiwanie danych SQL platformy Azure
titleSuffix: Azure Cognitive Search
description: Importowanie danych z usługi Azure SQL Database przy użyciu indeksatorów do wyszukiwania pełnotekstowego w usłudze Azure Cognitive Search. W tym artykule omówiono połączenia, konfigurację indeksatora i pozyskiwania danych.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c09727e8d92a449b41124eae6ad8381d66cb2619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113304"
---
# <a name="connect-to-and-index-azure-sql-database-content-using-an-azure-cognitive-search-indexer"></a>Łączenie się z zawartością bazy danych SQL usługi Azure i indeksowanie ich przy użyciu indeksatora usługi Azure Cognitive Search

Aby można było zbadać [indeks usługi Azure Cognitive Search,](search-what-is-an-index.md)należy wypełnić go danymi. Jeśli dane są dostępne w bazie danych SQL platformy Azure, **indeksator usługi Azure Cognitive Search dla usługi Azure SQL Database** (lub w skrócie **indeksator SQL platformy Azure)** może zautomatyzować proces indeksowania, co oznacza mniej kodu do pisania i mniej infrastruktury, na której należy dbać.

W tym artykule opisano mechanikę korzystania z [indeksatorów,](search-indexer-overview.md)ale także opisano funkcje dostępne tylko w bazach danych SQL platformy Azure (na przykład zintegrowane śledzenie zmian). 

Oprócz baz danych SQL platformy Azure usługa Azure Cognitive Search udostępnia indeksatory dla [usługi Azure Cosmos DB,](search-howto-index-cosmosdb.md) [magazynu obiektów Blob platformy Azure](search-howto-indexing-azure-blob-storage.md)i magazynu tabel platformy [Azure.](search-howto-indexing-azure-tables.md) Aby poprosić o pomoc techniczną dla innych źródeł danych, przekaż swoją opinię na [forum opinii usługi Azure Cognitive Search](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indeksatory i źródła danych

**Źródło danych** określa, które dane mają być indeksowane, poświadczenia dostępu do danych i zasady, które skutecznie identyfikują zmiany w danych (nowe, zmodyfikowane lub usunięte wiersze). Jest zdefiniowany jako niezależny zasób, dzięki czemu może być używany przez wiele indeksatorów.

**Indeksator** jest zasobem, który łączy pojedyncze źródło danych z indeksem wyszukiwania docelowego. Indeksator jest używany w następujący sposób:

* Wykonaj jednorazową kopię danych, aby wypełnić indeks.
* Aktualizowanie indeksu ze zmianami w źródle danych zgodnie z harmonogramem.
* Uruchom na żądanie, aby zaktualizować indeks w razie potrzeby.

Pojedynczy indeksator może zużywać tylko jedną tabelę lub widok, ale można utworzyć wiele indeksatorów, jeśli chcesz wypełnić wiele indeksów wyszukiwania. Aby uzyskać więcej informacji na temat pojęć, zobacz [Operacje indeksatora: Typowy przepływ pracy](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow).

Indeksator SQL platformy Azure można skonfigurować i skonfigurować za pomocą:

* Kreator importu danych w [witrynie Azure portal](https://portal.azure.com)
* Zestaw [SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet) usługi Azure Cognitive Search .NET
* [Interfejs API REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) usługi Azure Cognitive Search

W tym artykule użyjemy interfejsu API REST do tworzenia **indeksatorów** i **źródeł danych.**

## <a name="when-to-use-azure-sql-indexer"></a>Kiedy używać programu Azure SQL Indexer
W zależności od kilku czynników związanych z danymi użycie indeksatora SQL platformy Azure może lub nie może być odpowiednie. Jeśli dane spełniają następujące wymagania, można użyć indeksatora SQL platformy Azure.

| Kryteria | Szczegóły |
|----------|---------|
| Dane pochodzą z pojedynczej tabeli lub widoku | Jeśli dane są rozproszone w wielu tabelach, można utworzyć pojedynczy widok danych. Jednak jeśli używasz widoku, nie będzie można użyć SQL Server zintegrowane wykrywanie zmian, aby odświeżyć indeks ze zmianami przyrostowymi. Aby uzyskać więcej informacji, zobacz [Przechwytywanie zmienionych i usuniętych wierszy](#CaptureChangedRows) poniżej. |
| Typy danych są zgodne | Większość, ale nie wszystkie typy SQL są obsługiwane w indeksie usługi Azure Cognitive Search. Aby uzyskać listę, zobacz [Mapowanie typów danych](#TypeMapping). |
| Synchronizacja danych w czasie rzeczywistym nie jest wymagana | Indeksator może ponownie indeksować tabelę co najwyżej co pięć minut. Jeśli dane często się zmieniają, a zmiany muszą być odzwierciedlone w indeksie w ciągu kilku sekund lub pojedynczych minut, zalecamy użycie [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) lub [.NET SDK](search-import-data-dotnet.md) do bezpośredniego wypychania zaktualizowanych wierszy. |
| Możliwe jest indeksowanie przyrostowe | Jeśli masz duży zestaw danych i planujesz uruchomić indeksator zgodnie z harmonogramem, usługa Azure Cognitive Search musi mieć możliwość efektywnego identyfikowania nowych, zmienionych lub usuniętych wierszy. Indeksowanie niekwęzłowe jest dozwolone tylko wtedy, gdy indeksujesz na żądanie (nie zgodnie z harmonogramem) lub indeksujesz mniej niż 100 000 wierszy. Aby uzyskać więcej informacji, zobacz [Przechwytywanie zmienionych i usuniętych wierszy](#CaptureChangedRows) poniżej. |

> [!NOTE] 
> Usługa Azure Cognitive Search obsługuje tylko uwierzytelnianie programu SQL Server. Jeśli potrzebujesz pomocy technicznej dotyczącej uwierzytelniania hasła usługi Azure Active Directory, zagłosuj na tę [sugestię UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Tworzenie indeksatora SQL platformy Azure

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

   Ciąg połączenia można uzyskać z [witryny Azure portal;](https://portal.azure.com) użyj `ADO.NET connection string` tej opcji.

2. Utwórz docelowy indeks usługi Azure Cognitive Search, jeśli jeszcze go nie masz. Indeks można utworzyć za pomocą [portalu](https://portal.azure.com) lub [interfejsu API tworzenia indeksu](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Upewnij się, że schemat indeksu docelowego jest zgodny ze schematem tabeli źródłowej — zobacz [mapowanie między typami danych wyszukiwania SQL i Azure Cognitive.](#TypeMapping)

3. Utwórz indeksator, nadając mu nazwę i odwołując się do źródła danych i indeksu docelowego:

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

Indeksator utworzony w ten sposób nie ma harmonogramu. Jest automatycznie uruchamiany raz po utworzeniu. Można go uruchomić ponownie w dowolnym momencie przy użyciu żądania **indeksatora uruchamiania:**

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2019-05-06
    api-key: admin-key

Można dostosować kilka aspektów zachowania indeksatora, takich jak rozmiar partii i ile dokumentów można pominąć, zanim wykonanie indeksatora zakończy się niepowodzeniem. Aby uzyskać więcej informacji, zobacz [Tworzenie interfejsu API indeksatora](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

Może być konieczne zezwolenie usługom platformy Azure na łączenie się z bazą danych. Zobacz [Łączenie z platformy Azure,](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) aby uzyskać instrukcje, jak to zrobić.

Aby monitorować stan indeksatora i historię wykonywania (liczba elementów indeksowanych, błędy itp.), należy użyć żądania **stanu indeksatora:**

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2019-05-06
    api-key: admin-key

Odpowiedź powinna wyglądać podobnie do następującego:

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

Historia wykonywania zawiera do 50 ostatnio zakończonych wykonań, które są sortowane w odwrotnej kolejności chronologicznej (tak, że najnowsze wykonanie jest pierwsze w odpowiedzi).
Dodatkowe informacje na temat odpowiedzi można znaleźć w [get indexer status](https://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Uruchamianie indeksatorów zgodnie z harmonogramem
Można również zorganizować indeksatora do uruchamiania okresowo zgodnie z harmonogramem. Aby to zrobić, należy dodać właściwość **harmonogram** podczas tworzenia lub aktualizowania indeksatora. Poniższy przykład przedstawia żądanie PUT, aby zaktualizować indeksatora:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Wymagany jest parametr **interwału.** Interwał odnosi się do czasu między rozpoczęciem dwóch kolejnych wykonań indeksatora. Najmniejszy dozwolony interwał wynosi 5 minut; najdłuższy jest jeden dzień. Musi być sformatowany jako XSD "dayTimeDuration" wartość (ograniczony podzbiór [iso 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) wartość czasu trwania). Wzór do tego `P(nD)(T(nH)(nM))`jest: . Przykłady: `PT15M` co 15 `PT2H` minut, co 2 godziny.

Aby uzyskać więcej informacji na temat definiowania harmonogramów indeksatora, zobacz [Jak zaplanować indeksatory dla usługi Azure Cognitive Search](search-howto-schedule-indexers.md).

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Przechwytywanie nowych, zmienionych i usuniętych wierszy

Usługa Azure Cognitive Search używa **indeksowania przyrostowego,** aby uniknąć konieczności ponownego indeksowania całej tabeli lub widoku za każdym razem, gdy działa indeksator. Usługa Azure Cognitive Search udostępnia dwie zasady wykrywania zmian do obsługi indeksowania przyrostowego. 

### <a name="sql-integrated-change-tracking-policy"></a>Zintegrowane zasady śledzenia zmian SQL
Jeśli baza danych SQL obsługuje [śledzenie zmian,](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)zalecamy stosowanie **zasad śledzenia zintegrowanych zmian SQL**. Jest to najbardziej efektywna polityka. Ponadto umożliwia usłudze Azure Cognitive Search identyfikowanie usuniętych wierszy bez konieczności dodawania jawnej kolumny "usuwania nietrwałego" do tabeli.

#### <a name="requirements"></a>Wymagania 

+ Wymagania dotyczące wersji bazy danych:
  * SQL Server 2012 SP3 i nowsze, jeśli używasz programu SQL Server na maszynach wirtualnych platformy Azure.
  * Usługa Azure SQL Database V12, jeśli używasz usługi Azure SQL Database.
+ Tylko tabele (bez widoków). 
+ W bazie danych [włącz śledzenie zmian](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) dla tabeli. 
+ Brak złożonego klucza podstawowego (klucza podstawowego zawierającego więcej niż jedną kolumnę) w tabeli.  

#### <a name="usage"></a>Sposób użycia

Aby użyć tych zasad, utwórz lub zaktualizuj źródło danych w ten sposób:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

Korzystając ze zintegrowanych zasad śledzenia zmian SQL, nie należy określać osobnych zasad wykrywania usuwania danych — ta zasada ma wbudowaną obsługę identyfikacji usuniętych wierszy. Jednak aby usunięcia zostały wykryte "automagicznie", klucz dokumentu w indeksie wyszukiwania musi być taki sam jak klucz podstawowy w tabeli SQL. 

> [!NOTE]  
> Podczas korzystania [z TABELI TRUNCATE,](https://docs.microsoft.com/sql/t-sql/statements/truncate-table-transact-sql) aby usunąć dużą liczbę wierszy z tabeli SQL, indeksator musi zostać [zresetowany,](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) aby zresetować stan śledzenia zmian, aby odebrać usunięcia wierszy.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Zasady wykrywania zmian w znakach wody

Ta zasada wykrywania zmian opiera się na kolumnie "znak wysokiej wody" przechwytywania wersji lub czasu, kiedy wiersz został ostatnio zaktualizowany. Jeśli używasz widoku, musisz użyć zasad wysokiego znaku wody. Kolumna wysokiego znaku wody musi spełniać następujące wymagania.

#### <a name="requirements"></a>Wymagania 

* Wszystkie wstawienia określają wartość kolumny.
* Wszystkie aktualizacje elementu również zmienić wartość kolumny.
* Wartość tej kolumny zwiększa się z każdym wstawiania lub aktualizacji.
* Zapytania z następującymi klauzulami WHERE i ORDER BY mogą być wykonywane wydajnie:`WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Zdecydowanie zalecamy użycie typu danych [rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) dla kolumny znaku wysokiej wody. Jeśli używany jest inny typ danych, śledzenie zmian nie jest gwarantowane do przechwytywania wszystkich zmian w obecności transakcji wykonywanych jednocześnie z kwerendą indeksatora. Podczas korzystania z **rowversion** w konfiguracji z repliki tylko do odczytu, należy wskazać indeksatora w repliki podstawowej. Tylko replika podstawowa może służyć do scenariuszy synchronizacji danych.

#### <a name="usage"></a>Sposób użycia

Aby użyć zasad wysokiego znaku wody, utwórz lub zaktualizuj źródło danych w ten sposób:

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
> Jeśli tabela źródło nie ma indeksu w kolumnie wysokiego znaku wody, kwerendy używane przez indeksator SQL może limit czasu. W szczególności `ORDER BY [High Water Mark Column]` klauzula wymaga indeksu, aby działać wydajnie, gdy tabela zawiera wiele wierszy.
>
>

Jeśli wystąpią błędy limitu czasu, `queryTimeout` można użyć ustawienia konfiguracji indeksatora, aby ustawić limit czasu kwerendy na wartość wyższą niż domyślny limit czasu 5 minut. Na przykład, aby ustawić limit czasu na 10 minut, utwórz lub zaktualizuj indeksator za pomocą następującej konfiguracji:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Można również wyłączyć `ORDER BY [High Water Mark Column]` klauzulę. Jednak nie jest to zalecane, ponieważ jeśli wykonanie indeksatora zostanie przerwane przez błąd, indeksator musi ponownie przetworzyć wszystkie wiersze, jeśli zostanie uruchamiany później — nawet jeśli indeksator przetworzył już prawie wszystkie wiersze do czasu jego przerwania. Aby wyłączyć `ORDER BY` klauzulę, `disableOrderByHighWaterMarkColumn` należy użyć ustawienia w definicji indeksatora:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Zasady wykrywania usuwania nietrwałego usunięcia kolumny
Gdy wiersze są usuwane z tabeli źródłowej, prawdopodobnie chcesz usunąć te wiersze z indeksu wyszukiwania, jak również. Jeśli używasz zintegrowanych zasad śledzenia zmian SQL, to jest załatwiony dla Ciebie. Jednak zasady śledzenia zmian wysokich znaków wody nie pomagają w usuniętych wierszach. Postępowanie

Jeśli wiersze są fizycznie usunięte z tabeli, usługa Azure Cognitive Search nie ma możliwości wywnioskowania obecności rekordów, które już nie istnieją.  Można jednak użyć techniki "soft-delete", aby logicznie usunąć wiersze bez usuwania ich z tabeli. Dodaj kolumnę do tabeli lub widoku i oznacz wiersze jako usunięte za pomocą tej kolumny.

Podczas korzystania z techniki usuwania nietrwałego można określić zasady usuwania nietrwałego w następujący sposób podczas tworzenia lub aktualizowania źródła danych:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

**SoftDeleteMarkerValue** musi być ciągiem — użyj reprezentacji ciągu rzeczywistej wartości. Jeśli na przykład kolumna całkowita, w której usunięte wiersze są oznaczone `"1"`wartością 1, użyj programu . Jeśli masz kolumnę BIT, w której usunięte wiersze są oznaczone wartością rzeczywistą wartością logiczną, użyj literału `True` ciągu lub `true`, sprawa nie ma znaczenia.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>Mapowanie między typami danych SQL i Azure Cognitive Search
| Typ danych SQL | Dozwolone typy pól indeksu docelowego | Uwagi |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| prawdziwe, float |Edm.Double, Edm.String | |
| smallmoney, pieniądze dziesiętne numeryczne |Edm.String |Usługa Azure Cognitive Search nie obsługuje konwertowania typów dziesiętnych na Edm.Double, ponieważ spowoduje to utratę precyzji |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Ciąg SQL może służyć do wypełniania pola Collection(Edm.String), jeśli ciąg reprezentuje tablicę ciągów JSON:`["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| unikalnyidentifer |Edm.String | |
| Geografia |Edm.GeographyPoint |Obsługiwane są tylko wystąpienia geograficzne typu PUNKT z identyfikatorem SRID 4326 (który jest wartością domyślną) |
| Rowversion |Nie dotyczy |Kolumny wersji wiersza nie mogą być przechowywane w indeksie wyszukiwania, ale mogą być używane do śledzenia zmian |
| czas, timespan, binarny, varbinary, obraz, xml, geometria, typy CLR |Nie dotyczy |Nieobsługiwane |

## <a name="configuration-settings"></a>Ustawienia konfiguracji
Indeksator SQL udostępnia kilka ustawień konfiguracji:

| Ustawienie | Typ danych | Przeznaczenie | Wartość domyślna |
| --- | --- | --- | --- |
| Querytimeout |ciąg |Ustawia limit czasu wykonania kwerendy SQL |5 minut ("00:05:00") |
| disableOrderByHighWaterMarkColumn |bool |Powoduje, że kwerenda SQL używana przez zasady znaku wysokiej wody, aby pominąć order by klauzuli. Zobacz [zasady dotyczące znaku wody](#HighWaterMarkPolicy) |false |

Te ustawienia są `parameters.configuration` używane w obiekcie w definicji indeksatora. Na przykład, aby ustawić limit czasu kwerendy na 10 minut, utwórz lub zaktualizuj indeksator za pomocą następującej konfiguracji:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>Najczęściej zadawane pytania

**Pyt.: Czy można używać indeksatora SQL platformy Azure z bazami danych SQL uruchomionymi na maszynach wirtualnych IaaS na platformie Azure?**

Tak. Należy jednak zezwolić usłudze wyszukiwania na łączenie się z bazą danych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia z indeksatora usługi Azure Cognitive Search do programu SQL Server na maszynie Wirtualnej platformy Azure.](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

**Pyt.: Czy można używać indeksatora SQL platformy Azure z bazami danych SQL uruchomionymi lokalnie?**

Nie bezpośrednio. Nie zaleca się ani nie obsługuje bezpośredniego połączenia, ponieważ wymagałoby to otwarcia baz danych w celu kierowania ich do Internetu. Klienci odnieśli sukces w tym scenariuszu przy użyciu technologii mostkowych, takich jak usługa Azure Data Factory. Aby uzyskać więcej informacji, zobacz [Wypychanie danych do indeksu usługi Azure Cognitive Search przy użyciu usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector).

**Pyt.: Czy mogę używać indeksatora SQL platformy Azure z bazami danych innych niż SQL Server uruchomionymi w systemie IaaS na platformie Azure?**

Nie. Nie obsługujemy tego scenariusza, ponieważ nie przetestowaliśmy indeksatora z żadnymi bazami danych innymi niż SQL Server.  

**Pyt.: Czy można utworzyć wiele indeksatorów uruchomionych zgodnie z harmonogramem?**

Tak. Jednak tylko jeden indeksator może być uruchomiony na jednym węźle w tym czasie. Jeśli potrzebujesz wielu indeksatorów uruchomionych jednocześnie, należy rozważyć skalowanie usługi wyszukiwania do więcej niż jednej jednostki wyszukiwania.

**Pyt.: Czy uruchomienie indeksatora wpływa na obciążenie zapytania?**

Tak. Indeksator działa na jednym z węzłów w usłudze wyszukiwania i zasobów tego węzła są współużytkowane między indeksowania i obsługi ruchu zapytań i innych żądań interfejsu API. Jeśli uruchamiasz intensywne indeksowanie i obciążenia zapytań i napotkasz wysoką liczbę błędów 503 lub wydłużasz czas odpowiedzi, rozważ [skalowanie usługi wyszukiwania.](search-capacity-planning.md)

**Pyt.: Czy można użyć repliki pomocniczej w [klastrze trybu failover](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) jako źródła danych?**

To zależy. Do pełnego indeksowania tabeli lub widoku można użyć repliki pomocniczej. 

W przypadku indeksowania przyrostowego usługa Azure Cognitive Search obsługuje dwie zasady wykrywania zmian: zintegrowane śledzenie zmian SQL i znak wysokiej wody.

W replikach tylko do odczytu baza danych SQL nie obsługuje zintegrowanego śledzenia zmian. W związku z tym należy użyć zasad High Water Mark. 

Naszym standardowym zaleceniem jest użycie typu danych rowversion dla kolumny wysokiego znaku wody. Jednak przy użyciu rowversion opiera `MIN_ACTIVE_ROWVERSION` się na funkcji bazy danych SQL, która nie jest obsługiwana w replikach tylko do odczytu. W związku z tym należy wskazać indeksatora do repliki podstawowej, jeśli używasz rowversion.

Jeśli spróbujesz użyć rowversion w replice tylko do odczytu, zostanie wyświetlony następujący błąd: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**P: Czy mogę użyć alternatywnej kolumny non-rowversion do śledzenia zmian znaku wody?**

Nie jest to zalecane. Tylko **rowversion** pozwala na niezawodną synchronizację danych. Jednak w zależności od logiki aplikacji może być bezpieczne, jeśli:

+ Można upewnić się, że po uruchomieniu indeksatora nie ma żadnych zaległych transakcji w tabeli, która jest indeksowana (na przykład wszystkie aktualizacje tabeli odbywają się jako partia zgodnie z harmonogramem, a harmonogram indeksatora usługi Azure Cognitive Search jest ustawiony, aby uniknąć nakładania się z tabelą harmonogram aktualizacji).  

+ Okresowo zrobić pełny redex odebrać wszelkie nieodebrane wiersze. 