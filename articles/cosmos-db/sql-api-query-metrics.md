---
title: Metryki zapytania SQL dla interfejsu API SQL usługi Azure Cosmos DB
description: Dowiedz się więcej o tym, jak Instrumentacja i debugować wydajność zapytań SQL usługi Azure Cosmos DB żądań.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: 61bb102e17d9980d991fdf423174d7110cd5433d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237865"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Dostrajanie wydajności zapytań za pomocą usługi Azure Cosmos DB

Usługa Azure Cosmos DB zapewnia [interfejsu API SQL dla wykonywanie zapytań o dane](how-to-sql-query.md), bez konieczności schematu lub indeksów pomocniczych. Ten artykuł zawiera następujące informacje dla deweloperów:

* Szczegółowych informacji o sposobie działania wykonywania zapytania SQL usługi Azure Cosmos DB
* Szczegółowe informacje na temat zapytania nagłówki żądania i odpowiedzi oraz opcje zestawu SDK klienta
* Wskazówki i najlepsze rozwiązania dotyczące wydajności zapytań
* Przykłady wykorzystania statystyk wykonywania SQL, aby debugować wydajność zapytań

## <a name="about-sql-query-execution"></a>Wykonywanie zapytania SQL — informacje

W usłudze Azure Cosmos DB przechowywać dane w kontenerach, które można powiększać do dowolnego [przepływności żądania lub rozmiar](partition-data.md). Usługa Azure Cosmos DB jest skalowana bezproblemowo danych na fizyczne partycje dzieje się w tle do obsługi wzrostu ilości danych lub zwiększenie aprowizowanej przepływności. Możesz wykonywać zapytania SQL do dowolnego kontenera przy użyciu interfejsu API REST lub jednym z obsługiwanych [zestawów SDK SQL](sql-api-sdk-dotnet.md).

Krótkie omówienie partycjonowania: zdefiniowanie klucza partycji, takich jak "city", który określa, jak dane zostanie podzielona na partycje fizyczne. Dane należące do pojedynczego klucza partycji (na przykład "city" == "Seattle") są przechowywane w partycji fizycznych, ale zazwyczaj jedna partycja fizycznej ma wiele kluczy partycji. Gdy partycja osiągnie rozmiar magazynu, usługa bezproblemowo dzieli partycji na dwóch nowych partycji i dzieli klucza partycji równomiernie na te partycje. Ponieważ partycje są przejściowy, interfejsy API należy użyć klasą abstrakcyjną "zakres kluczy partycji", oznacza zakresy skróty kluczy partycji. 

Po wykonaniu zapytania do usługi Azure Cosmos DB zestawu SDK wykonuje te etapy logiczne:

* Analizuje zapytania SQL w celu ustalenia planu wykonania zapytania. 
* Jeśli zapytanie zawiera filtr względem klucza partycji, takich jak `SELECT * FROM c WHERE c.city = "Seattle"`, jest kierowany do pojedynczej partycji. Jeśli zapytanie nie ma filtr klucza partycji, a następnie jest wykonywany w wszystkie partycje, a wyniki są scalane po stronie klienta.
* Zapytanie jest wykonywane w ramach każdej partycji w serii lub równolegle, na podstawie konfiguracji klienta. Każdej partycji zapytania może spowodować, że jeden lub więcej wystąpień komunikacji dwustronnej w zależności od złożoności zapytania skonfigurowany rozmiar strony, a aprowizowana przepływność kolekcji. Każdego wykonania zwraca liczbę [jednostek żądania](request-units.md) używane przez wykonanie zapytania i opcjonalnie statystyk wykonywania zapytań. 
* Zestaw SDK wykonuje podsumowania wyników zapytań między partycjami. Na przykład jeśli zapytanie obejmuje klauzuli ORDER BY w partycjach, następnie wyniki z poszczególnymi partycjami są sortowane scalania do zwracają wyniki w kolejności posortowanej globalnie. Jeśli zapytanie jest agregacji, takiej jak `COUNT`, liczby elementów z poszczególnymi partycjami są sumowane do produkcji liczby całkowitej.

Zestawy SDK udostępniają różne opcje w celu wykonywania zapytań. Na przykład na platformie .NET te opcje są dostępne w `FeedOptions` klasy. W poniższej tabeli opisano te opcje i ich wpływ na czas wykonywania zapytania. 

| Opcja | Opis |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Musi być ustawiona na wartość true dla dowolnego zapytania, który wymaga wykonywania na więcej niż jednej partycji. Jest to jawne flagę, aby umożliwić kompromisy świadome wydajności w czasie projektowania. |
| `EnableScanInQuery` | Musi być równa true, jeśli rezygnujesz z indeksowania, ale chcesz uruchomić zapytanie za pomocą skanowania, mimo to. Tylko zastosowanie, gdy indeksowanie dla ścieżki żądany filtr jest wyłączona. | 
| `MaxItemCount` | Maksymalna liczba elementów do zwrócenia na komunikacji dwustronnej z serwerem. Przez ustawienie wartości -1, można pozwolić, serwer zarządzania liczby elementów. Alternatywnie można obniżyć tę wartość, aby pobrać tylko niewielka liczba elementów na komunikację dwustronną. 
| `MaxBufferedItemCount` | To jest opcja po stronie klienta i używana do ograniczania zmniejszenie zużycia pamięci podczas wykonywania między partycjami klauzuli ORDER BY. Wyższa wartość pomaga zmniejszyć opóźnienie między partycjami sortowania. |
| `MaxDegreeOfParallelism` | Pobiera lub ustawia liczbę jednoczesnych operacji po stronie klienta są uruchamiane podczas równoległego wykonywania zapytań w usłudze bazy danych Azure Cosmos DB. Wartość właściwości dodatnią ogranicza liczbę jednoczesnych operacji wartość zestawu. Jeśli jest ustawiona na wartość mniejszą niż 0, system automatycznie decyduje, liczby równoczesnych operacji do uruchomienia. |
| `PopulateQueryMetrics` | Czas ładowania umożliwia rejestrowanie szczegółowe statystyki czasu spędzonego w różnych faz wykonywania zapytania, takie jak czas kompilacji, czas pętli indeksów i dokumentów. Pomoc techniczna systemu Azure, aby zdiagnozować problemy z wydajnością zapytań mogą udostępniać dane wyjściowe z statystyki zapytań. |
| `RequestContinuation` | Można wznowić wykonywania zapytań, przekazując token kontynuacji nieprzezroczyste zwracany przez każde zapytanie. Token kontynuacji hermetyzuje stan wszystkich wymaganych do wykonywania zapytań. |
| `ResponseContinuationTokenLimitInKb` | Można ograniczyć maksymalny rozmiar token kontynuacji zwrócony przez serwer. Należy ustawić, jeśli aplikacja hosta ma limity rozmiaru nagłówka odpowiedzi. To ustawienie może zwiększyć ogólną czas trwania i RU dla zapytania.  |

Na przykład Przyjrzyjmy przedstawiono przykładowe zapytanie na klucz partycji, Żądana kolekcja o `/city` jako partycję kluczy i zainicjowana przy użyciu 100 000 jednostek RU/s przepływności. W przypadku żądania tego zapytania za pomocą `CreateDocumentQuery<T>` na platformie .NET, podobnie do poniższego:

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

Fragment kodu zestawu SDK, pokazana powyżej, odnosi się do następujących żądania interfejsu API REST:

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

Każdej strony wykonanie zapytania odnosi się do interfejsu API REST `POST` z `Accept: application/query+json` nagłówek i zapytanie SQL w treści. Każda kwerenda sprawia, że jeden lub więcej rund do serwera przy użyciu `x-ms-continuation` token powtarzane między klientem i serwerem, aby wznowić wykonywanie. Opcje konfiguracji w FeedOptions są przekazywane do serwera w formie nagłówki żądania. Na przykład `MaxItemCount` odpowiada `x-ms-max-item-count`. 

Żądanie zwraca odpowiedź następujących (tutaj obcięto je dla czytelności):

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
| `x-ms-item-count` | Liczba elementów zwróconych w odpowiedzi. Jest to zależne od podane `x-ms-max-item-count`, liczba elementów, które można dopasować w ramach odpowiedzi maksymalny rozmiar ładunku, aprowizowanej przepływności i czasu wykonywania zapytania. |  
| `x-ms-continuation:` | Token kontynuacji, aby wznowić wykonywanie zapytania, jeśli są dostępne dodatkowe wyniki. | 
| `x-ms-documentdb-query-metrics` | Statystyki zapytań do wykonywania. Jest to rozdzielany ciąg zawierający statystyk czasu poświęconego na różnych etapach wykonywania zapytania. Zwracane, gdy `x-ms-documentdb-populatequerymetrics` ustawiono `True`. | 
| `x-ms-request-charge` | Liczba [jednostek żądania](request-units.md) używane przez zapytanie. | 

Aby uzyskać więcej informacji na temat nagłówków żądań interfejsu API REST i opcji, zobacz [wykonywanie zapytań o zasoby przy użyciu interfejsu API REST](https://docs.microsoft.com/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Najlepsze rozwiązania dotyczące wydajności zapytań
Poniżej przedstawiono najbardziej typowe czynniki, które mają wpływ na wydajność zapytań usługi Azure Cosmos DB. Firma Microsoft zagłęb się w każdej z tych tematów, w tym artykule.

| współczynnik | Porada | 
| ------ | -----| 
| Aprowizowana przepływność | Zmierz jednostek RU na zapytanie i upewnij się, że wymagane aprowizowanej przepływności zapytań. | 
| Partycjonowanie i klucze partycji | Preferuj zapytania z wartością klucza partycji w klauzuli filtru w celu uzyskania niskich opóźnień. |
| Opcje zestawu SDK i zapytań | Zestaw SDK najlepszych rozwiązań, takich jak połączenie bezpośrednie i Dostosuj opcje wykonywania zapytania po stronie klienta. |
| Opóźnienie sieci | Konto w ramach sieci obciążenie w pomiaru i odczytu z najbliższego regionu za pomocą międzyregionalnych interfejsów API. |
| Zasady indeksowania | Upewnij się, że masz wymagany ścieżki/zasady indeksowania dla zapytania. |
| Metryk wykonywania zapytania | Analizuj metryki wykonywania zapytania, aby zidentyfikować potencjalne ponownego kształtów zapytań i danych.  |

### <a name="provisioned-throughput"></a>Aprowizowana przepływność
Cosmos DB tworzenia kontenerów, danych, z których każdy z zarezerwowaną przepływnością wyrażoną w żądaniu jednostki (RU) na sekundę. Odczytu dokumentu o rozmiarze 1 KB jest 1 RU i każdej operacji (takich jak zapytania) jest znormalizować do stałej liczby jednostek żądania oparte na jego złożoność. Na przykład, jeśli masz 1000 jednostek RU/s aprowizowaną dla kontenera i masz zapytania, takiego jak `SELECT * FROM c WHERE c.city = 'Seattle'` który zużywa 5 jednostek RU, a następnie przeprowadzić (1000 jednostek RU/s) / (5 jednostek RU/query) = 200 zapytań na sekundę takich zapytań na sekundę. 

Jeśli prześlesz ponad 200 zapytań na sekundę, szybkości żądań przychodzących ponad 200/s uruchomienia usługi. Zestawy SDK automatycznie obsługiwać ten przypadek, wykonując wycofywania/ponawiania, w związku z tym można zauważyć większych opóźnieniach, aby te zapytania. Zwiększenie aprowizowanej przepływności wymaganą wartość zwiększa Twoje zapytanie opóźnienia i przepływności. 

Aby dowiedzieć się więcej na temat jednostek żądania, zobacz [jednostek żądania](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Partycjonowanie i klucze partycji
W usłudze Azure Cosmos DB zapytania są przeważnie wykonywane w kolejności od najszybszego/nabardziej efektywnego do wolniejszego/mniej efektywnego. 

* Pobierz na pojedynczym kluczu partycji i klucz elementu
* Zapytanie z klauzulą filtru pojedynczego klucza partycji
* Zapytania bez klauzuli filtru równości lub zakres dowolne właściwości
* Zapytania bez filtrów

Zapytania, które należy zapoznać się z wszystkie partycje muszą większych opóźnień, a następnie wykorzystywać wyższe (RUS). Ponieważ każda partycja zawiera, automatycznego indeksowania wszystkich właściwości, zapytanie może ich obsługa jest wydajna z indeksu w tym przypadku. Można tworzyć zapytania, obejmujących wiele partycji szybciej przy użyciu opcji równoległości.

Aby dowiedzieć się więcej na temat partycjonowania i klucze partycji, zobacz [partycjonowanie w usłudze Azure Cosmos DB](partition-data.md).

### <a name="sdk-and-query-options"></a>Opcje zestawu SDK i zapytań
Zobacz [porady dotyczące wydajności](performance-tips.md) i [testowania Wydajnościowego](performance-testing.md) dotyczące sposobu uzyskania optymalnej wydajności po stronie klienta z usługi Azure Cosmos DB. W tym za pomocą najnowszych zestawów SDK, konfigurowanie konfiguracje specyficzne dla platformy, takich jak domyślna liczba połączeń, częstotliwość operacji wyrzucania elementów bezużytecznych i przy użyciu opcji łączności uproszczone, takich jak bezpośrednio/TCP. 


#### <a name="max-item-count"></a>Maksymalna liczba elementów
Dla zapytań, wartość `MaxItemCount` może mieć znaczący wpływ na czas wykonywania zapytania end-to-end. Każdy komunikacji dwustronnej z serwerem zwróci nie więcej niż liczba elementów w `MaxItemCount` (domyślnie 100 elementów). Ustawienie tej opcji na wartość większą (-1 jest maksymalną i zalecane) zwiększa ogólną czasu trwania zapytania, ograniczenie liczby rund między serwerem a klientem, szczególnie w przypadku zapytań przy użyciu dużych zestawów wyników.

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
Zapytania można dostrajanie `MaxDegreeOfParallelism` Aby zidentyfikować najlepsze konfiguracje dla aplikacji, szczególnie w przypadku wykonywania zapytań między partycjami (bez filtrowania na wartość klucza partycji). `MaxDegreeOfParallelism`  Określa maksymalną liczbę zadań równoległych, czyli maksymalną liczbę partycji odwiedzin równolegle. 

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
* D = Domyślna maksymalna liczba zadań równoległych (= łączna liczba procesorów w komputerze klienckim)
* P = określonych przez użytkownika maksymalną liczbę równoległych zadań podrzędnych
* N = liczba partycji, które potrzeb odwiedzin odpowiedzi na kwerendę

Poniżej przedstawiono wpływ jak zapytania równolegle będzie się zachowywał tak dla różnych wartości P.
* (P == 0) = > Tryb szeregowej
* (P == 1) = > maksymalna liczba jedno zadanie
* (P > 1) = > zadań równoległych Min (P, N) 
* (P < 1) = > zadań równoległych Min (N, D)

Informacje o wersji zestawu SDK i wyświetlić szczegółowe informacje na temat metod i klas zaimplementowano [zestawów SDK SQL](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Opóźnienie sieci
Zobacz [dystrybucję globalną usługi Azure Cosmos DB](tutorial-global-distribution-sql-api.md) jak skonfigurować dystrybucję globalną i nawiązać połączenie z najbliższego regionu. Opóźnienie sieci ma znaczący wpływ na wydajność zapytań, gdy trzeba utworzyć wiele rund lub pobieranie dużych zestawu wyników z zapytania. 

Sekcję metryk wykonywania zapytania wyjaśnia, jak pobrać czas wykonywania zapytania na serwerach ( `totalExecutionTimeInMs`), dzięki czemu można odróżnić czas potrzebny do wykonywania zapytań i czas potrzebny do sieci tranzytowej.

### <a name="indexing-policy"></a>Zasady indeksowania
Zobacz [konfigurowania zasad indeksowania](index-policy.md) indeksowania ścieżek, typów i trybów i ich wpływ na wykonanie zapytania. Domyślnie zasady indeksowania używa skrótu indeksowania dla ciągów, która obowiązuje dla zapytań o równość, ale nie dla zakresu zapytania/order przez zapytania. Jeśli potrzebujesz zapytań o zakres dla ciągów, zaleca się określenie zakresu typu indeksu dla wszystkich ciągów. 

Domyślnie usługa Azure Cosmos DB zostaną zastosowane, automatyczne indeksowanie wszystkich danych. Dla wysoko wydajnych Wstaw scenariuszy, należy rozważyć wykluczenie ścieżek, ponieważ spowoduje to zmniejszenie kosztów jednostek RU dla każdej operacji wstawiania. 

## <a name="query-execution-metrics"></a>Metryk wykonywania zapytania
Możesz uzyskać szczegółowych metryk dotyczących wykonywania zapytań, przekazując opcjonalny `x-ms-documentdb-populatequerymetrics` nagłówka (`FeedOptions.PopulateQueryMetrics` w zestawie SDK platformy .NET). Wartość zwrócona w `x-ms-documentdb-query-metrics` ma następujące pary klucz-wartość przeznaczone do zaawansowanego rozwiązywania problemów dotyczących wykonywania zapytania. 

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
| `totalExecutionTimeInMs` | milisekundy | Czas wykonywania zapytania | 
| `queryCompileTimeInMs` | milisekundy | Czas kompilacji kwerendy  | 
| `queryLogicalPlanBuildTimeInMs` | milisekundy | Czas na utworzenie planu zapytania logiczne | 
| `queryPhysicalPlanBuildTimeInMs` | milisekundy | Czas na utworzenie planu zapytania fizycznych | 
| `queryOptimizationTimeInMs` | milisekundy | Czas potrzebny do optymalizacji zapytań | 
| `VMExecutionTimeInMs` | milisekundy | Czas potrzebny do wykonywania zapytań | 
| `indexLookupTimeInMs` | milisekundy | Czas spędzony w warstwie fizycznej indeksu | 
| `documentLoadTimeInMs` | milisekundy | Czas poświęcony na ładowanie dokumentów  | 
| `systemFunctionExecuteTimeInMs` | milisekundy | Całkowity czas wykonywania funkcji (wbudowane) systemu (w milisekundach)  | 
| `userFunctionExecuteTimeInMs` | milisekundy | Całkowity czas wykonywania funkcje zdefiniowane przez użytkownika (w milisekundach) | 
| `retrievedDocumentCount` | count | Całkowita liczba pobranych dokumentów  | 
| `retrievedDocumentSize` | B | Całkowity rozmiar pobrane dokumenty w bajtach  | 
| `outputDocumentCount` | count | Liczba dokumentów, danych wyjściowych | 
| `writeOutputTimeInMs` | milisekundy | Czas wykonywania kwerendy w milisekundach | 
| `indexUtilizationRatio` | stosunek (< = 1) | Załadowano stosunek liczby dokumentów dopasowane przez filtr, aby liczba dokumentów  | 

Zestawy SDK klientów wewnętrznie mogą mieć wiele operacji zapytania do obsługi zapytań w ramach każdej partycji. Klient wysyła więcej niż jedno wywołanie dla partycji, jeśli łączna liczba wyników przekracza `x-ms-max-item-count`, jeśli zapytanie przekroczy aprowizowaną przepływność na partycję, lub jeśli ładunek zapytania osiągnie maksymalny rozmiar na stronie lub jeśli kwerenda osiągnie system przydzielonych limit czasu. Zwraca każdego wykonania zapytania częściowego `x-ms-documentdb-query-metrics` dla tej strony. 

Poniżej przedstawiono kilka przykładowych zapytań i jak interpretować niektóre metryki zwrócone w wyniku wykonywania zapytań: 

| Zapytanie | Przykładowe metryki | Opis | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Liczba dokumentów, pobierane jest 100 + 1, aby dopasować klauzuli TOP. Zapytanie jest przede wszystkim zużywany czas w `WriteOutputTime` i `DocumentLoadTime` ponieważ skanowania. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount jest teraz wyższa (500 + 1, aby dopasować klauzuli TOP). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Około 0.9 ms odbywa się w IndexLookupTime dla klucza wyszukiwania, ponieważ jest on indeksu wyszukiwania `/N/?`. | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Nieco więcej czasu (w wersji 1.7 ms) działania IndexLookupTime przez skanowanie zakresu, ponieważ jest on indeksu wyszukiwania `/N/?`. | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Ten sam czas spędzony na `DocumentLoadTime` jako poprzednich zapytań, ale niższe `WriteOutputTime` ponieważ firma Microsoft jest projekcji tylko jedną właściwość. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Działania o 213 ms `UserDefinedFunctionExecutionTime` wykonywania funkcji zdefiniowanej przez użytkownika w każdej wartości `c.N`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Około 0,6 ms odbywa się w `IndexLookupTime` na `/Name/?`. Większość czasu wykonywania zapytania (OK. 7 ms) w `SystemFunctionExecutionTime`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Zapytanie jest wykonywane jako skanowania, ponieważ używa ona `LOWER`, a 500 poza 2491 pobrane dokumenty zostaną zwrócone. |


## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej o obsługiwanych operatorów zapytań SQL i słów kluczowych, zobacz [zapytania SQL](how-to-sql-query.md). 
* Aby dowiedzieć się więcej na temat jednostek żądania, zobacz [jednostek żądania](request-units.md).
* Aby dowiedzieć się więcej na temat zasad indeksowania, zobacz [zasad indeksowania](index-policy.md) 


