---
title: Metryki kwerendy SQL dla interfejsu API Azure rozwiązania Cosmos bazy danych SQL | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu Instrumentacja i debugowania wydajność kwerend SQL żądań bazy danych Azure rozwiązania Cosmos.
keywords: Składnia SQL, zapytanie sql, zapytania sql, język zapytań json, koncepcje bazy danych i zapytania sql, funkcje agregujące
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: sngun
ms.openlocfilehash: 4ed0008f4b574691387d6e0ee0300b5f05f1ec1b
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798699"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Dostrajanie wydajności kwerend z bazy danych Azure rozwiązania Cosmos

Udostępnia bazę danych systemu Azure rozwiązania Cosmos [interfejsu API SQL na potrzeby zapytań o dane](sql-api-sql-query.md), bez konieczności schematu lub indeksów pomocniczych. Ten artykuł zawiera następujące informacje dla deweloperów:

* Ogólne szczegóły dotyczące sposobu działania wykonanie kwerendy SQL Azure rozwiązania Cosmos DB
* Szczegółowe informacje o nagłówkach żądań i odpowiedzi zapytania i opcje zestawu SDK klienta
* Wskazówki i najlepsze rozwiązania dotyczące wydajności zapytań
* Przykładami mogą korzystać z SQL statystyk wykonywania debugowania wydajność zapytań

## <a name="about-sql-query-execution"></a>Wykonanie kwerendy SQL — informacje

W usłudze Azure DB rozwiązania Cosmos, przechowywanie danych w kontenerach, które może zwiększyć się do dowolnego [rozmiaru lub żądania przepływności](partition-data.md). Azure DB rozwiązania Cosmos bezproblemowo skaluje danych przez fizyczne partycji w obszarze obejmuje do obsługi przyrostu danych lub zwiększyć przepływność. Możesz wykonywać zapytania SQL do dowolnego kontenera za pomocą interfejsu API REST lub jednym z obsługiwanych [zestawów SDK SQL](sql-api-sdk-dotnet.md).

Krótki przegląd partycjonowania: Definiowanie klucza partycji, takich jak "Miasto", który określa sposób danych jest podzielić na partycje fizycznych. Danych należących do klucza jedną partycję (na przykład "Miasto" == "Seattle") są przechowywane w partycji fizycznej, ale zwykle jednej partycji fizycznej ma wiele kluczy partycji. Gdy partycji osiągnie rozmiar magazynu, usługa bezproblemowo dzieli partycji na dwóch nowych partycji i dzieli klucza partycji równomiernie między tych partycji. Ponieważ partycje są przejściowe, interfejsy API Użyj abstrakcję "zakresem kluczy partycji", oznaczająca zakresy skrótów klucza partycji. 

W przypadku wysyłania zapytania do bazy danych Azure rozwiązania Cosmos, zestaw SDK wykonuje te kroki logiczne:

* Przeanalizować zapytania SQL w celu ustalenia planu wykonania zapytania. 
* Jeśli zapytanie zawiera filtr przed klucza partycji, takich jak `SELECT * FROM c WHERE c.city = "Seattle"`, jest kierowany do jednej partycji. Jeśli zapytanie nie ma filtr klucza partycji, a następnie jest wykonywany w wszystkie partycje, a wyniki są scalane po stronie klienta.
* Zapytanie jest wykonywane w ramach każdej partycji w serii lub równolegle, na podstawie konfiguracji klienta. W ramach każdej partycji zapytanie może być jedną lub więcej rund w zależności od złożoności zapytania skonfigurowany rozmiar strony, a udostępnione przepływność kolekcji. Każde wykonanie zwraca liczbę [jednostek żądania](request-units.md) używane przez wykonanie zapytania i opcjonalnie statystyk wykonywania zapytań. 
* Zestaw SDK wykonuje podsumowania wyników zapytania w partycji. Na przykład jeśli zapytanie obejmuje ORDER BY w partycji, następnie wyniki z poszczególnych partycji są sortowane scalania do zwracania wyników globalnie posortowane. Jeśli zapytanie jest agregacją, takich jak `COUNT`, liczby z poszczególnych partycji są sumowane do produkcji ogólnej liczby.

Zestawy SDK zapewniają różne opcje w celu wykonywania zapytań. Na przykład w środowisku .NET te opcje są dostępne w `FeedOptions` klasy. W poniższej tabeli opisano te opcje i ich wpływ na czas wykonania zapytania. 

| Opcja | Opis |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Musi mieć ustawioną wartość true dla dowolnego zapytania, który wymaga wykonywania na więcej niż jednej partycji. Jest to jawne flagi umożliwiają kompromisy świadome wydajności w czasie tworzenia. |
| `EnableScanInQuery` | Musi mieć ustawioną wartość true, jeśli użytkownik zgodził się poza indeksowania, ale chcesz uruchomić zapytanie za pomocą skanowania, mimo to. Tylko dotyczy indeksowania ścieżki żądany filtr jest wyłączona. | 
| `MaxItemCount` | Maksymalna liczba elementów, aby powrócić na obiegu do serwera. Przez ustawienie wartości -1, możesz pozwolić, aby serwer zarządzania liczbę elementów. Alternatywnie można zmniejszyć tę wartość, aby pobrać tylko niewielka liczba elementów na obiegu. 
| `MaxBufferedItemCount` | Jest to opcja po stronie klienta i pozwala ograniczyć zużycie pamięci podczas wykonywania różnych partycji ORDER BY. Wyższa wartość pomaga zmniejszyć opóźnienia między partycji sortowania. |
| `MaxDegreeOfParallelism` | Pobiera lub ustawia liczbę jednoczesnych operacji podczas równoległego wykonywania zapytań w usłudze Azure DB rozwiązania Cosmos bazy danych należy uruchomić po stronie klienta. Wartość właściwości dodatnią ogranicza liczbę jednoczesnych operacji z wartością zestawu. Jeśli jest ustawiona na wartość mniejszą niż 0, system automatycznie decyduje, liczba jednoczesnych operacji do uruchomienia. |
| `PopulateQueryMetrics` | Czas ładowania umożliwia szczegółowe rejestrowanie statystyk czasu trwania fazy wykonywania zapytania, takie jak czas kompilacji, czas pętli indeksu i dokumentu. Dane wyjściowe z statystyki zapytania można udostępniać z pomocą techniczną platformy Azure do diagnozowania problemów z wydajnością zapytania. |
| `RequestContinuation` | Wykonanie kwerendy można wznowić przez przekazywanie token kontynuacji nieprzezroczyste zwracany przez każde zapytanie. Token kontynuacji hermetyzuje stan wszystkich wymaganych do wykonywania zapytań. |
| `ResponseContinuationTokenLimitInKb` | Można ograniczyć maksymalny rozmiar token kontynuacji zwrócony przez serwer. Może być konieczne ustawienie, jeśli dostęp do aplikacji hosta ma limity rozmiaru nagłówka odpowiedzi. To ustawienie może zwiększyć ogólną czas trwania i RUs używane dla zapytania.  |

Na przykład wytłumaczone przedstawiono przykładowe zapytanie na klucz partycji, Żądana kolekcja o `/city` jako partycja klucza i elastycznie o 100 000 RU/s przepustowości. To zapytanie przy użyciu żądania `CreateDocumentQuery<T>` w środowisku .NET, takie jak następujące:

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

Fragment kodu zestawu SDK przedstawionych powyżej, odpowiada następujące żądania interfejsu API REST:

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

Każdej stronie wykonywania zapytania odnosi się do interfejsu API REST `POST` z `Accept: application/query+json` nagłówka i zapytanie SQL w treści. Każde zapytanie sprawia, że jeden lub więcej przekazywanych do serwera o `x-ms-continuation` token powtarzana między klientem i serwerem można wznowić wykonywania. Opcje konfiguracji w FeedOptions są przekazywane do serwera w formie nagłówki żądania. Na przykład `MaxItemCount` odpowiada `x-ms-max-item-count`. 

Żądanie zwraca odpowiedź następujące (obcięty dla czytelności):

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

Następujące nagłówki odpowiedzi klucza zwróconych przez kwerendę:

| Opcja | Opis |
| ------ | ----------- |
| `x-ms-item-count` | Liczba elementów zwracanych w odpowiedzi. Jest to zależne od podane `x-ms-max-item-count`, liczba elementów, które można zmieścić w odpowiedzi maksymalny rozmiar ładunku, udostępnionej przepływności i czasu wykonywania zapytania. |  
| `x-ms-continuation:` | Token kontynuacji można wznowić wykonywania zapytania, jeśli dostępne są dodatkowe wyniki. | 
| `x-ms-documentdb-query-metrics` | Statystyka wykonywania zapytania. Jest to ciąg rozdzielany zawierający statystyk czasu poświęconego na różnych etapach wykonywania zapytania. Jeśli zwrócony `x-ms-documentdb-populatequerymetrics` ma ustawioną wartość `True`. | 
| `x-ms-request-charge` | Liczba [jednostek żądania](request-units.md) używane przez zapytanie. | 

Aby uzyskać więcej informacji o opcjach i nagłówki żądania interfejsu API REST, zobacz [zapytań zasobów przy użyciu interfejsu API REST](https://docs.microsoft.com/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Najlepsze rozwiązania dotyczące wydajności zapytań
Poniżej przedstawiono najbardziej typowych czynników, które mają wpływ na wydajność kwerend bazy danych Azure rozwiązania Cosmos. Firma Microsoft głębiej do szczegółów każdego z tych tematów w tym artykule.

| Współczynnik | Porada | 
| ------ | -----| 
| Aprowizowana przepływność | Zmierz RU na zapytania i upewnij się, czy masz wymagane udostępnionej przepływności dla zapytań. | 
| Partycjonowanie i kluczy partycji | Preferuj zapytania w klauzuli filtru dla małych opóźnieniach wartość klucza partycji. |
| Opcje zestawu SDK i zapytań | Postępuj zgodnie z zestawu SDK najlepszych rozwiązań, takich jak bezpośrednie połączenie między i Dostosuj opcje wykonywania zapytania po stronie klienta. |
| Opóźnienie sieci | Konta dla sieci obciążenie w pomiaru i odczytywanie najbliższy region przy użyciu wielu interfejsów API. |
| Zasady indeksowania | Upewnij się, że masz wymagane indeksowania ścieżki/zasady dla zapytania. |
| Metryki wykonywania kwerendy | Przeanalizuj metryki wykonywanie zapytania do identyfikowania potencjalnych ponownego kształtów zapytań i danych.  |

### <a name="provisioned-throughput"></a>Aprowizowana przepływność
Do rozwiązania Cosmos bazy danych możesz utworzyć kontenery danych, każda z zarezerwowaną przepływnością wyrażoną w żądaniu jednostki (RU) na sekundę. Odczyt dokumentu 1 KB jest 1 RU i każdej operacji (w tym zapytań) jest znormalizowane do stałej liczby RUs w oparciu o jego złożoności. Na przykład, jeśli masz 1000 elastycznie RU/s dla Twojego kontenera i masz zapytania, takie jak `SELECT * FROM c WHERE c.city = 'Seattle'` która zużywa 5 RUs, a następnie przeprowadzić (1000 RU/s) / (5 RU na zapytanie) = 200 zapytania/s takie zapytania na sekundę. 

Po przesłaniu zapytania więcej niż 200 na sekundę żądań przychodzących limitów szybkości ponad 200/s uruchomienia usługi. Zestawy SDK automatycznie obsługiwać ten przypadek, wykonując wycofywania/ponów próbę, w związku z tym można zauważyć większego opóźnienia dla tych zapytań. Zwiększa przepływność wymaganą wartość zwiększa z kwerendami i przepływności. 

Aby dowiedzieć się więcej na temat jednostek żądania, zobacz [jednostek żądania](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Partycjonowanie i kluczy partycji
W usłudze Azure Cosmos DB zapytania są przeważnie wykonywane w kolejności od najszybszego/nabardziej efektywnego do wolniejszego/mniej efektywnego. 

* Pobierz klucz jednej partycji i klucz
* Zapytanie z klauzuli filtru dla klucza jednej partycji
* Zapytanie bez klauzuli filtru równości lub zakres na dowolną właściwość
* Zapytanie bez filtrów

Zapytania, które należy zapoznać się wszystkie partycje muszą większego opóźnienia, a może wykorzystać RUs wyższy. Ponieważ każda partycja zawiera automatycznego indeksowania względem wszystkich właściwości, zapytania mogą być przekazywane wydajnie z indeksu w tym przypadku. Można wykonać zapytania, które szybciej span partycje przy użyciu opcji równoległości.

Aby dowiedzieć się więcej na temat partycjonowania i kluczy partycji, zobacz [partycjonowania w usłudze Azure DB rozwiązania Cosmos](partition-data.md).

### <a name="sdk-and-query-options"></a>Opcje zestawu SDK i zapytań
Zobacz [porady dotyczące wydajności](performance-tips.md) i [testowania wydajności](performance-testing.md) instrukcje uzyskać najlepszą wydajność klienta z bazy danych Azure rozwiązania Cosmos. W tym przy użyciu najnowszych zestawów SDK, konfigurowanie konfiguracje specyficzne dla platformy, takich jak domyślną liczbę połączeń, częstotliwość operacji wyrzucania elementów bezużytecznych i opcji łączności lekkie, takich jak bezpośrednio/TCP. 


#### <a name="max-item-count"></a>Maksymalna liczba elementów
Dla zapytań, wartość `MaxItemCount` może mieć znaczący wpływ na czas kwerendy end-to-end. Zwraca większa niż liczba elementów w każdej obiegu do serwera `MaxItemCount` (domyślnie: 100 elementów). To ustawienie wyższej wartości (wartość -1 jest maksymalną i zalecane) poprawia ogólną czasu trwania kwerendy poprzez ograniczenie liczby rund między serwerem a klientem, szczególnie w przypadku zapytań dotyczących dużych zestawów wyników.

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
Dla zapytań, dostroić `MaxDegreeOfParallelism` Aby zidentyfikować najlepsze konfiguracje dla aplikacji, zwłaszcza, jeśli wykonywania zapytań między partycji (bez filtr na wartość klucza partycji). `MaxDegreeOfParallelism`  Określa maksymalną liczbę równoległych zadań, tj., maksymalną liczbę partycji odwiedzin równolegle. 

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
* D = Domyślna maksymalna liczba zadań równoległych (łączna liczba procesorów na maszynie klienta =)
* P = określone przez użytkownika maksymalną liczbę równoległych zadań
* N = liczba partycji, które wymaga odwiedzin odpowiedzi na kwerendę

Poniżej przedstawiono efekty zapytania równoległe spowoduje zachowanie dla różnych wartości P.
* (P == 0) = > Tryb szeregowe
* (P == 1) = > maksymalna jednego zadania
* (P > 1) = > zadań równoległych Min (P, N) 
* (P < 1) = > zadań równoległych Min (N, D)

Informacje o wersji zestawu SDK i Zobacz szczegóły dotyczące zaimplementowanych klasy i metody [zestawów SDK SQL](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Opóźnienie sieci
Zobacz [dystrybucji globalnej bazy danych Azure rozwiązania Cosmos](tutorial-global-distribution-sql-api.md) jak skonfigurować globalne dystrybucji i połącz się najbliżej regionu. Opóźnienie sieci ma znaczący wpływ na wydajność zapytań, gdy trzeba utworzyć wiele rund lub pobrać dużych zestawu wyników z kwerendy. 

Sekcję metryki wykonywania zapytania wyjaśniono, jak pobrać czas wykonywania zapytania na serwerach ( `totalExecutionTimeInMs`), dzięki czemu można odróżnić czas przeznaczony na wykonanie kwerendy i czas przesyłania w sieci.

### <a name="indexing-policy"></a>Zasady indeksowania
Zobacz [Konfigurowanie zasady indeksowania](indexing-policies.md) indeksowania ścieżki, rodzajów i tryby i ich wpływ na wykonanie kwerendy. Domyślnie zasady indeksowania używa skrótu indeksowania dla ciągów, które obowiązuje dla zapytań o równość, ale nie dla zakresu zapytania/order przez zapytania. Jeśli potrzebujesz kwerendy zakresu dla ciągów, zaleca się określenie zakresu typu indeksu dla wszystkich ciągów. 

## <a name="query-execution-metrics"></a>Metryki wykonywania kwerendy
Szczegółowe metryki na wykonanie kwerendy można uzyskać przez przekazywanie opcjonalny `x-ms-documentdb-populatequerymetrics` nagłówka (`FeedOptions.PopulateQueryMetrics` zestawu .NET SDK). Wartość zwrócona w `x-ms-documentdb-query-metrics` ma następujące pary klucz-wartość przeznaczone do zaawansowanego rozwiązywania problemów dotyczących wykonywania zapytania. 

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
| `queryLogicalPlanBuildTimeInMs` | milisekundy | Czas kompilacji planu zapytania logiczne | 
| `queryPhysicalPlanBuildTimeInMs` | milisekundy | Czas kompilacji planu zapytania fizycznych | 
| `queryOptimizationTimeInMs` | milisekundy | Czas spędzony w zapytaniu optymalizacji | 
| `VMExecutionTimeInMs` | milisekundy | Czas spędzony w czasie wykonywania kwerendy | 
| `indexLookupTimeInMs` | milisekundy | Czas spędzony w warstwie fizycznej indeksu | 
| `documentLoadTimeInMs` | milisekundy | Czas ładowania dokumentów  | 
| `systemFunctionExecuteTimeInMs` | milisekundy | Łączny czas podczas wykonywania funkcji (wbudowane) systemu w milisekundach  | 
| `userFunctionExecuteTimeInMs` | milisekundy | Łączny czas podczas wykonywania funkcji zdefiniowanej przez użytkownika (w milisekundach) | 
| `retrievedDocumentCount` | liczba | Całkowita liczba pobranych dokumentów  | 
| `retrievedDocumentSize` | B | Całkowity rozmiar pobrane dokumentów w bajtach  | 
| `outputDocumentCount` | liczba | Liczba dokumentów, dane wyjściowe | 
| `writeOutputTimeInMs` | milisekundy | Czas wykonywania kwerendy w milisekundach | 
| `indexUtilizationRatio` | stosunek (< = 1) | Załadowano stosunek liczby dokumentów, które są dopasowane wg filtr, aby liczba dokumentów  | 

Zestawy SDK klientów wewnętrznie mogą mieć wiele operacji zapytania do obsługi zapytań w ramach każdej partycji. Klient wysyła więcej niż jedno wywołanie każdej partycji jeżeli całkowita liczba wyników przekracza `x-ms-max-item-count`, jeśli zapytanie przekracza udostępnionej przepływności dla partycji, lub jeśli ładunek zapytania osiągnie maksymalny rozmiar na stronie lub zapytanie przez nią miejsce osiągnie limit czasu przydzielonego systemu. Zwraca każde wykonanie zapytania z częściowa `x-ms-documentdb-query-metrics` dla tej strony. 

Poniżej przedstawiono niektóre przykładowe zapytania i jak interpretować niektóre metryki zwrócone przez wykonanie zapytania: 

| Zapytanie | Metryka próbki | Opis | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Liczba dokumentów pobierana jest 100 + 1, aby dopasować klauzuli TOP. Przede wszystkim jest zużywany czas kwerendy w `WriteOutputTime` i `DocumentLoadTime` ponieważ skanowania. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount jest teraz nowszej (500 + 1 odpowiadające klauzuli TOP). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Około 0,9 ms odbywa się w IndexLookupTime dla klucza wyszukiwania, ponieważ jest on wyszukiwania indeksu `/N/?`. | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Nieco więcej (1.7 ms) czas, w IndexLookupTime za pośrednictwem skanowania zakresu, ponieważ jest on wyszukiwania indeksu `/N/?`. | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Sam czas spędzony na `DocumentLoadTime` jako poprzednich zapytań, ale niższe `WriteOutputTime` ponieważ możemy Cię projekcji tylko jedną właściwość. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Działania o 213 ms `UserDefinedFunctionExecutionTime` wykonywania UDF w każdej wartości `c.N`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Działania około 0,6 ms `IndexLookupTime` na `/Name/?`. Większość czasu wykonywania zapytania (~ 7 ms) w `SystemFunctionExecutionTime`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Ponieważ jest on używany jako skanowania jest przeprowadzana kwerenda `LOWER`, a 500 poza 2491 pobrane dokumenty zostaną zwrócone. |


## <a name="next-steps"></a>Kolejne kroki
* Informacje na temat obsługiwanych operatorów zapytań SQL i słów kluczowych, zobacz [zapytania SQL](sql-api-sql-query.md). 
* Aby dowiedzieć się więcej na temat jednostek żądania, zobacz [jednostek żądania](request-units.md).
* Aby uzyskać informacje dotyczące zasady indeksowania, zobacz [indeksowania zasad](indexing-policies.md) 


