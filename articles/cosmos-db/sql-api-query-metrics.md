---
title: Metryki zapytań SQL dla interfejsu API SQL usługi Azure Cosmos DB DB
description: Dowiedz się, jak instrumentować i debugować wydajność zapytań SQL żądań usługi Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: ae1773ec1d470b9cff2efb00c200427b7b4c2fb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69614824"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Tuning query performance with Azure Cosmos DB (Dostosowywanie wydajności zapytań w usłudze Azure Cosmos DB)

Usługa Azure Cosmos DB udostępnia [interfejs API SQL do wykonywania zapytań o dane](how-to-sql-query.md), bez konieczności tworzenia schematu lub indeksów pomocniczych. Ten artykuł zawiera następujące informacje dla deweloperów:

* Szczegółowe informacje na temat działania programu Sql usługi Azure Cosmos DB
* Szczegółowe informacje o nagłówkach żądań i odpowiedzi kwerendy oraz opcjach SDK klienta
* Porady i najlepsze wskazówki dotyczące wydajności kwerend
* Przykłady wykorzystania statystyk wykonania SQL do debugowania wydajności kwerendy

## <a name="about-sql-query-execution"></a>Informacje o wykonywaniu zapytań SQL

W usłudze Azure Cosmos DB przechowujesz dane w kontenerach, które mogą rosnąć do dowolnego [rozmiaru magazynu lub żądania przepływności.](partition-data.md) Usługa Azure Cosmos DB bezproblemowo skaluje dane między partycjami fizycznymi w ramach okładek do obsługi wzrostu danych lub zwiększenia aprowizowanej przepływności. Zapytania SQL można wysyłać do dowolnego kontenera przy użyciu interfejsu API REST lub jednego z [obsługiwanych pakietów SDK SQL](sql-api-sdk-dotnet.md).

Krótki przegląd partycjonowania: można zdefiniować klucz partycji, takich jak "miasto", który określa, jak dane są podzielone między partycje fizyczne. Dane należące do pojedynczego klucza partycji (na przykład "miasto" == "Seattle") są przechowywane w partycji fizycznej, ale zazwyczaj pojedyncza partycja fizyczna ma wiele kluczy partycji. Gdy partycja osiągnie rozmiar magazynu, usługa bezproblemowo dzieli partycję na dwie nowe partycje i dzieli klucz partycji równomiernie na te partycje. Ponieważ partycje są przejściowe, interfejsy API używają abstrakcji "zakresu klucza partycji", co oznacza zakresy skrótów klucza partycji. 

Po wystawieniu kwerendy do usługi Azure Cosmos DB zestaw SDK wykonuje następujące logiczne kroki:

* Przesiemać kwerendę SQL, aby określić plan wykonywania kwerendy. 
* Jeśli kwerenda zawiera filtr względem klucza partycji, na przykład `SELECT * FROM c WHERE c.city = "Seattle"`jest kierowany do jednej partycji. Jeśli kwerenda nie ma filtru na klucz partycji, a następnie jest wykonywany we wszystkich partycjach, a wyniki są scalane po stronie klienta.
* Kwerenda jest wykonywana w ramach każdej partycji w serii lub równolegle, na podstawie konfiguracji klienta. W ramach każdej partycji kwerenda może wykonać co najmniej jedną rundę w zależności od złożoności zapytania, skonfigurowanego rozmiaru strony i aprowizowanej przepływności kolekcji. Każde wykonanie zwraca liczbę [jednostek żądań](request-units.md) zużytych przez wykonanie kwerendy i opcjonalnie statystyki wykonywania kwerendy. 
* SDK wykonuje podsumowanie wyników kwerendy na partycjach. Na przykład jeśli kwerenda obejmuje KOLEJNOŚĆ przez partycje, a następnie wyniki z poszczególnych partycji są scalane sortowane, aby zwrócić wyniki w kolejności globalnie posortowane. Jeśli kwerenda jest agregacja jak `COUNT`, liczby z poszczególnych partycji są sumowane w celu uzyskania ogólnej liczby.

SDKs zapewniają różne opcje wykonywania zapytań. Na przykład w .NET te opcje `FeedOptions` są dostępne w klasie. W poniższej tabeli opisano te opcje i ich wpływ na czas wykonywania kwerendy. 

| Opcja | Opis |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Musi być ustawiona na true dla każdej kwerendy, która wymaga wykonania na więcej niż jednej partycji. Jest to wyraźna flaga, aby umożliwić świadome kompromisy wydajności w czasie rozwoju. |
| `EnableScanInQuery` | Musi być ustawiona na true, jeśli zrezygnowano z indeksowania, ale mimo to chcesz uruchomić kwerendę za pomocą skanowania. Dotyczy tylko wtedy, gdy indeksowanie żądanej ścieżki filtru jest wyłączone. | 
| `MaxItemCount` | Maksymalna liczba elementów do zwrotu na czas podróży na serwer. Ustawiając na -1, można pozwolić serwerowi zarządzać liczbą elementów. Można też obniżyć tę wartość, aby pobrać tylko niewielką liczbę elementów na podróż w obie strony. 
| `MaxBufferedItemCount` | Jest to opcja po stronie klienta i służy do ograniczenia zużycia pamięci podczas wykonywania kolejności między partycjami. Wyższa wartość pomaga zmniejszyć opóźnienie sortowania partycji krzyżowej. |
| `MaxDegreeOfParallelism` | Pobiera lub ustawia liczbę równoczesnych operacji uruchomić po stronie klienta podczas wykonywania zapytań równoległych w usłudze bazy danych usługi Azure Cosmos. Wartość właściwości dodatniej ogranicza liczbę równoczesnych operacji do ustawionej wartości. Jeśli jest ustawiona na mniej niż 0, system automatycznie decyduje o liczbie równoczesnych operacji do uruchomienia. |
| `PopulateQueryMetrics` | Umożliwia szczegółowe rejestrowanie statystyk czasu spędzonego w różnych fazach wykonywania kwerendy, takich jak czas kompilacji, czas pętli indeksu i czas ładowania dokumentu. Dane wyjściowe ze statystyk zapytań można udostępniać za pomocą pomocy technicznej platformy Azure w celu zdiagnozowania problemów z wydajnością kwerend. |
| `RequestContinuation` | Można wznowić wykonywanie kwerendy, przekazując w tokenie kontynuacji nieprzezroczyste zwrócone przez dowolną kwerendę. Token kontynuacji hermetyzuje wszystkie stany wymagane do wykonywania kwerendy. |
| `ResponseContinuationTokenLimitInKb` | Można ograniczyć maksymalny rozmiar tokenu kontynuacji zwracanego przez serwer. Może być konieczne ustawienie tego, jeśli host aplikacji ma ograniczenia rozmiaru nagłówka odpowiedzi. Ustawienie to może zwiększyć ogólny czas trwania i rus używane dla kwerendy.  |

Na przykład weźmy przykładową kwerendę na klucz partycji `/city` żądany w kolekcji z jako klucz partycji i aprowizować z 100 000 RU/s przepływności. Żądanie tej kwerendy przy użyciu `CreateDocumentQuery<T>` w .NET, jak następujące:

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
        MaxItemCount = -1, 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();
```

Fragment kodu SDK pokazany powyżej odpowiada następującemu żądaniu interfejsu API REST:

```
POST https://arramacquerymetrics-westus.documents.azure.com/dbs/db/colls/sample/docs HTTP/1.1
x-ms-continuation: 
x-ms-documentdb-isquery: True
x-ms-max-item-count: -1
x-ms-documentdb-query-enablecrosspartition: True
x-ms-documentdb-query-parallelizecrosspartitionquery: True
x-ms-documentdb-query-iscontinuationexpected: True
x-ms-documentdb-populatequerymetrics: True
x-ms-date: Tue, 27 Jun 2017 21:52:18 GMT
authorization: type%3dmaster%26ver%3d1.0%26sig%3drp1Hi83Y8aVV5V6LzZ6xhtQVXRAMz0WNMnUuvriUv%2b4%3d
x-ms-session-token: 7:8,6:2008,5:8,4:2008,3:8,2:2008,1:8,0:8,9:8,8:4008
Cache-Control: no-cache
x-ms-consistency-level: Session
User-Agent: documentdb-dotnet-sdk/1.14.1 Host/32-bit MicrosoftWindowsNT/6.2.9200.0
x-ms-version: 2017-02-22
Accept: application/json
Content-Type: application/query+json
Host: arramacquerymetrics-westus.documents.azure.com
Content-Length: 52
Expect: 100-continue

{"query":"SELECT * FROM c WHERE c.city = 'Seattle'"}
```

Każda strona wykonywania kwerendy odpowiada `POST` interfejsowi API REST z nagłówkiem `Accept: application/query+json` i kwerendą SQL w treści. Każde zapytanie sprawia, że jeden lub `x-ms-continuation` więcej rund do serwera z tokenu echo między klientem i serwerem, aby wznowić wykonywanie. Opcje konfiguracji w opcjach FeedOptions są przekazywane do serwera w postaci nagłówków żądań. Na przykład `MaxItemCount` odpowiada `x-ms-max-item-count`. 

Żądanie zwraca następującą odpowiedź (obcięta dla czytelności):

```
HTTP/1.1 200 Ok
Cache-Control: no-store, no-cache
Pragma: no-cache
Transfer-Encoding: chunked
Content-Type: application/json
Server: Microsoft-HTTPAPI/2.0
Strict-Transport-Security: max-age=31536000
x-ms-last-state-change-utc: Tue, 27 Jun 2017 21:01:57.561 GMT
x-ms-resource-quota: documentSize=10240;documentsSize=10485760;documentsCount=-1;collectionSize=10485760;
x-ms-resource-usage: documentSize=1;documentsSize=884;documentsCount=2000;collectionSize=1408;
x-ms-item-count: 2000
x-ms-schemaversion: 1.3
x-ms-alt-content-path: dbs/db/colls/sample
x-ms-content-path: +9kEANVq0wA=
x-ms-xp-role: 1
x-ms-documentdb-query-metrics: totalExecutionTimeInMs=33.67;queryCompileTimeInMs=0.06;queryLogicalPlanBuildTimeInMs=0.02;queryPhysicalPlanBuildTimeInMs=0.10;queryOptimizationTimeInMs=0.00;VMExecutionTimeInMs=32.56;indexLookupTimeInMs=0.36;documentLoadTimeInMs=9.58;systemFunctionExecuteTimeInMs=0.00;userFunctionExecuteTimeInMs=0.00;retrievedDocumentCount=2000;retrievedDocumentSize=1125600;outputDocumentCount=2000;writeOutputTimeInMs=18.10;indexUtilizationRatio=1.00
x-ms-request-charge: 604.42
x-ms-serviceversion: version=1.14.34.4
x-ms-activity-id: 0df8b5f6-83b9-4493-abda-cce6d0f91486
x-ms-session-token: 2:2008
x-ms-gatewayversion: version=1.14.33.2
Date: Tue, 27 Jun 2017 21:59:49 GMT
```

Nagłówki odpowiedzi klucza zwrócone z kwerendy są następujące:

| Opcja | Opis |
| ------ | ----------- |
| `x-ms-item-count` | Liczba elementów zwróconych w odpowiedzi. Jest to zależne `x-ms-max-item-count`od dostarczonej liczby elementów, które mogą być zmieszczone w maksymalny rozmiar ładunku odpowiedzi, aprowizowana przepływność i czas wykonywania kwerendy. |  
| `x-ms-continuation:` | Token kontynuacji, aby wznowić wykonywanie kwerendy, jeśli dostępne są dodatkowe wyniki. | 
| `x-ms-documentdb-query-metrics` | Statystyki kwerendy dla wykonania. Jest to ciąg rozdzielany zawierający statystyki czasu spędzonego w różnych fazach wykonywania kwerendy. Zwracany, `x-ms-documentdb-populatequerymetrics` jeśli `True`jest ustawiona na . | 
| `x-ms-request-charge` | Liczba [jednostek żądań](request-units.md) zużytych przez kwerendę. | 

Aby uzyskać szczegółowe informacje na temat nagłówków i opcji żądań interfejsu API REST, zobacz [Wykonywanie zapytań o zasoby przy użyciu interfejsu API REST](https://docs.microsoft.com/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Najważniejsze wskazówki dotyczące wydajności kwerendy
Poniżej przedstawiono najczęstsze czynniki wpływające na wydajność zapytań usługi Azure Cosmos DB. Zagłębiamy się w każdy z tych tematów w tym artykule.

| Czynnikiem | Porada | 
| ------ | -----| 
| Aprowizowana przepływność | Zmierz ru na kwerendę i upewnij się, że masz wymaganą aprowizowaną przepływność dla zapytań. | 
| Partycjonowanie i klucze partycji | Korzyść zapytania z wartością klucza partycji w klauzuli filtru dla małych opóźnień. |
| SDK i opcje kwerend | Postępuj zgodnie z najlepszymi rozwiązaniami sdk, takimi jak łączność bezpośrednia, i dostroić opcje wykonywania zapytań po stronie klienta. |
| Opóźnienie sieci | Uwzględniaj obciążenie sieci w pomiarach i używaj wielowykonanych interfejsów API do odczytu z najbliższego regionu. |
| Zasady indeksowania | Upewnij się, że masz wymagane ścieżki indeksowania/zasady dla kwerendy. |
| Metryki wykonywania kwerend | Analizowanie metryk wykonywania kwerendy w celu zidentyfikowania potencjalnych przepisań kształtów kwerendy i danych.  |

### <a name="provisioned-throughput"></a>Aprowizowana przepływność
W usłudze Cosmos DB tworzysz kontenery danych, z których każdy ma zarezerwowaną przepływność wyrażoną w jednostkach żądań (RU) na sekundę. Odczyt dokumentu 1 KB to 1 RU, a każda operacja (w tym zapytania) jest normalizowana do stałej liczby ru na podstawie jego złożoności. Na przykład jeśli masz 1000 RU/s aprowizować dla kontenera i masz kwerendę taką jak `SELECT * FROM c WHERE c.city = 'Seattle'` ta zużywa 5 ru, następnie można wykonać (1000 RU/s) / (5 RU/query) = 200 zapytań/s takich zapytań na sekundę. 

Jeśli prześlesz więcej niż 200 zapytań/s, usługa rozpocznie ograniczanie szybkości żądań przychodzących powyżej 200/s. SDK automatycznie obsługiwać ten przypadek, wykonując backoff/retry, w związku z tym można zauważyć większe opóźnienie dla tych zapytań. Zwiększenie aprowizowanej przepływności do wymaganej wartości zwiększa opóźnienie zapytania i przepływność. 

Aby dowiedzieć się więcej o jednostkach żądań, zobacz [Żądania jednostek](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Partycjonowanie i klucze partycji
W usłudze Azure Cosmos DB zapytania są przeważnie wykonywane w kolejności od najszybszego/nabardziej efektywnego do wolniejszego/mniej efektywnego. 

* GET na jednym kluczu partycji i kluczu elementu
* Zapytanie z klauzulą filtru na jednym kluczu partycji
* Zapytanie bez klauzuli filtru równości lub zakresu dla dowolnej właściwości
* Zapytanie bez filtrów

Zapytania, które należy skonsultować się ze wszystkimi partycjami, wymagają większego opóźnienia i mogą zużywać wyższe procesory RU. Ponieważ każda partycja ma automatyczne indeksowanie względem wszystkich właściwości, kwerenda może być skutecznie obsługiwana z indeksu w tym przypadku. Można wywrzeć zapytania, które obejmują partycje szybciej przy użyciu opcji równoległości.

Aby dowiedzieć się więcej na temat partycjonowania i kluczy partycji, zobacz [Partycjonowanie w usłudze Azure Cosmos DB](partition-data.md).

### <a name="sdk-and-query-options"></a>SDK i opcje kwerend
Zobacz [wskazówki dotyczące wydajności](performance-tips.md) i [testowanie wydajności,](performance-testing.md) aby uzyskać najlepszą wydajność po stronie klienta z usługi Azure Cosmos DB. Obejmuje to korzystanie z najnowszych zestawów SDK, konfigurowanie konfiguracji specyficznych dla platformy, takich jak domyślna liczba połączeń, częstotliwość wyrzucania elementów bezużytecznych i używanie lekkich opcji łączności, takich jak Direct/TCP. 


#### <a name="max-item-count"></a>Maksymalna liczba przedmiotów
W przypadku kwerend wartość `MaxItemCount` może mieć znaczący wpływ na czas kwerendy end-to-end. Każda podróż w obie strony na serwer zwraca `MaxItemCount` nie więcej niż liczbę elementów w (Domyślnie 100 elementów). Ustawienie tej wartości na wyższą wartość (-1 jest maksymalna i zalecane) poprawi ogólny czas trwania zapytania, ograniczając liczbę rund między serwerem a klientem, szczególnie w przypadku zapytań z dużymi zestawami wyników.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Maksymalny stopień równoległości
W przypadku kwerend `MaxDegreeOfParallelism` dostroić, aby zidentyfikować najlepsze konfiguracje dla aplikacji, zwłaszcza w przypadku wykonywania kwerend między partycjami (bez filtru na wartość klucza partycji). `MaxDegreeOfParallelism`steruje maksymalną liczbą równoległych zadań, tj. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();
```

Załóżmy, że
* D = Domyślna maksymalna liczba równoległych zadań (= całkowita liczba procesora na komputerze klienckim)
* P = Określona przez użytkownika maksymalna liczba równoległych zadań
* N = Liczba partycji, które należy odwiedzić, aby odpowiedzieć na zapytanie

Poniżej przedstawiono implikacje zachowania zapytań równoległych dla różnych wartości P.
* (P == 0) => trybie szeregowym
* (P == 1) => Maksymalnie jedno zadanie
* (P > 1) => Min (P, N) zadania równoległe 
* (P < 1) => Min (N, D) zadania równoległe

Informacje o wersji sdk i szczegóły dotyczące zaimplementowanych klas i metod można znaleźć w [pakietach SDK SQL](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Opóźnienie sieci
Zobacz [dystrybucja globalna usługi Azure Cosmos DB,](tutorial-global-distribution-sql-api.md) aby dowiedzieć się, jak skonfigurować dystrybucję globalną i połączyć się z najbliższym regionem. Opóźnienie sieci ma znaczący wpływ na wydajność kwerendy, gdy trzeba zrobić wiele rund lub pobrać duży zestaw wyników z kwerendy. 

Sekcja dotycząca metryk wykonywania kwerend wyjaśnia sposób pobierania czasu wykonywania `totalExecutionTimeInMs`serwerów kwerend ( ), dzięki czemu można odróżnić czas spędzony w wykonywaniu kwerend a czas spędzony w tranzycie sieciowym.

### <a name="indexing-policy"></a>Zasady indeksowania
Zobacz [Konfigurowanie zasad indeksowania](index-policy.md) dla indeksowania ścieżek, rodzajów i trybów oraz wpływu na wykonywanie kwerend. Domyślnie zasady indeksowania używa indeksowania hash dla ciągów, który jest skuteczny dla zapytań równości, ale nie dla zakres kwerend/kolejność przez kwerendy. Jeśli potrzebujesz zapytań zakres dla ciągów, zaleca się określenie typu indeksu zakresu dla wszystkich ciągów. 

Domyślnie usługa Azure Cosmos DB zastosuje automatyczne indeksowanie do wszystkich danych. W przypadku scenariuszy wstawiania o wysokiej wydajności należy rozważyć wyłączenie ścieżek, ponieważ zmniejszy to koszt RU dla każdej operacji wstawiania. 

## <a name="query-execution-metrics"></a>Metryki wykonywania kwerend
Można uzyskać szczegółowe metryki dotyczące wykonywania kwerendy, przekazując w opcjonalnym `x-ms-documentdb-populatequerymetrics` nagłówku (`FeedOptions.PopulateQueryMetrics` w pliku .NET SDK). Wartość zwrócona w `x-ms-documentdb-query-metrics` ma następujące pary klucz-wartość przeznaczone do zaawansowanego rozwiązywania problemów dotyczących wykonywania zapytania. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;

```

| Metryka | Jednostka | Opis | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | milisekundy | Czas wykonywania kwerendy | 
| `queryCompileTimeInMs` | milisekundy | Czas kompilacji kwerendy  | 
| `queryLogicalPlanBuildTimeInMs` | milisekundy | Czas na zbudowanie logicznego planu kwerend | 
| `queryPhysicalPlanBuildTimeInMs` | milisekundy | Czas na tworzenie fizycznego planu zapytań | 
| `queryOptimizationTimeInMs` | milisekundy | Czas poświęcony na optymalizację kwerendy | 
| `VMExecutionTimeInMs` | milisekundy | Czas spędzony w czasie wykonywania kwerendy | 
| `indexLookupTimeInMs` | milisekundy | Czas spędzony w warstwie indeksu fizycznego | 
| `documentLoadTimeInMs` | milisekundy | Czas spędzony na załadunku dokumentów  | 
| `systemFunctionExecuteTimeInMs` | milisekundy | Całkowity czas spędzony na wykonywaniu funkcji systemu (wbudowanego) w milisekundach  | 
| `userFunctionExecuteTimeInMs` | milisekundy | Całkowity czas spędzony na wykonywaniu funkcji zdefiniowanych przez użytkownika w milisekundach | 
| `retrievedDocumentCount` | count | Całkowita liczba pobranych dokumentów  | 
| `retrievedDocumentSize` | Bajtów | Całkowity rozmiar pobranych dokumentów w bajtach  | 
| `outputDocumentCount` | count | Liczba dokumentów wyjściowych | 
| `writeOutputTimeInMs` | milisekundy | Czas wykonywania kwerendy w milisekundach | 
| `indexUtilizationRatio` | (<=1) | Stosunek liczby dokumentów dopasowanych przez filtr do liczby załadowanych dokumentów  | 

Zestaw SDK klienta może wewnętrznie wykonać wiele operacji kwerendy do obsługi kwerendy w ramach każdej partycji. Klient wykonuje więcej niż jedno wywołanie na `x-ms-max-item-count`partycję, jeśli całkowite wyniki przekraczają , jeśli kwerenda przekracza aprowizowaną przepływność dla partycji lub jeśli ładunek zapytania osiągnie maksymalny rozmiar na stronę lub jeśli kwerenda osiągnie limit czasu przydzielony do systemu. Każde częściowe wykonanie `x-ms-documentdb-query-metrics` kwerendy zwraca dla tej strony. 

Oto kilka przykładowych zapytań i jak interpretować niektóre metryki zwrócone z wykonania kwerendy: 

| Zapytanie | Przykładowa metryka | Opis | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Liczba pobranych dokumentów wynosi 100+1, aby dopasować je do klauzuli TOP. Czas zapytania jest najczęściej `WriteOutputTime` `DocumentLoadTime` spędzany w i ponieważ jest to skanowanie. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount jest teraz wyższa (500+1, aby dopasować klauzulę TOP). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Około 0,9 ms jest wydawana w IndexLookupTime dla wyszukiwania klucza, `/N/?`ponieważ jest to wyszukiwanie indeksu na . | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Nieco więcej czasu (1,7 ms) spędzonych w IndexLookupTime w zakresie `/N/?`skanowania, ponieważ jest to wyszukiwanie indeksu na . | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Ten sam `DocumentLoadTime` czas spędzony `WriteOutputTime` na poprzednich kwerendach, ale niższy, ponieważ wyświetlamy tylko jedną właściwość. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Około 213 ms `UserDefinedFunctionExecutionTime` jest wydawane na wykonywanie UDF na każdej wartości `c.N`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Około 0,6 ms `IndexLookupTime` jest `/Name/?`wydawane na . Większość czasu wykonywania kwerendy (~7 `SystemFunctionExecutionTime`ms) w programie . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Kwerenda jest wykonywana jako skanowanie, ponieważ używa `LOWER`, i 500 z 2491 pobranych dokumentów są zwracane. |


## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o obsługiwanych operatorach zapytań SQL i słowach kluczowych, zobacz [kwerenda SQL](sql-query-getting-started.md). 
* Aby dowiedzieć się więcej o jednostkach żądań, zobacz [jednostki żądań](request-units.md).
* Aby dowiedzieć się więcej o zasadach indeksowania, zobacz [zasady indeksowania](index-policy.md) 


