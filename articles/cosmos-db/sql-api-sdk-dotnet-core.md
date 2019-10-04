---
title: 'Azure Cosmos DB: SQL .NET Core API, zasoby & SDK'
description: Poznaj wszystkie informacje o interfejsie API programu SQL .NET Core i zestawie SDK, w tym daty wydania, daty wycofania i zmiany wprowadzone między poszczególnymi wersjami zestaw .NET Core SDK Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: 325e84ec4489eb2f1f530585af7d3c4bc4b201fc
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949571"
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB zestaw .NET Core SDK dla interfejsu API SQL: informacje o wersji i zasoby
> [!div class="op_single_selector"]
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [Źródło zmian platformy .NET](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Asynchroniczne środowisko Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Wykonawca zbiorczy — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonawczy zbiorczej — Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Pobieranie zestawu SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)|
|**Dokumentacja interfejsu API**|[Dokumentacja interfejsu API platformy .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Przykłady**|[Przykłady kodu platformy .NET](sql-api-dotnet-samples.md)|
|**Wprowadzenie**|[Wprowadzenie do Azure Cosmos DB .NET](sql-api-sdk-dotnet.md)|
|**Samouczek aplikacji sieci Web**|[Tworzenie aplikacji sieci Web za pomocą Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Bieżąca obsługiwana platforma**|[.NET Standard 1,6 i .NET Standard 1,5](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Uwagi do wersji

> [!NOTE]
> W przypadku korzystania z platformy .NET Core zapoznaj się z najnowszą wersją 3. x [zestawu SDK platformy .NET](sql-api-sdk-dotnet-standard.md), która jest przeznaczona dla .NET Standard. 

### <a name="a-name270270"></a>@no__t — 02.7.0

* Dodano obsługę tablic i obiektów w kolejności według zapytań
* Obsługuj efektywne kolizje kluczy partycji
* Dodano obsługę LINQ dla wielu operatorów OrderBy z operatorem ThenBy
* Niestandardowe ustawienia serializacji są teraz stosowane do wszystkich operacji upsert i Replace
* Rozwiązano problem zakleszczenia AysncCache, dzięki czemu będzie on działał przy użyciu pojedynczego wątku harmonogramu zadań

### <a name="a-name260260"></a>@no__t — 02.6.0

* Dodano PortReusePolicy do ConnectionPolicy
* Naprawiono ntdll! Problem z RtlGetVersion TypeLoadException, gdy zestaw SDK jest używany w aplikacji platformy UWP

### <a name="a-name251251"></a>@no__t — 02.5.1

* Wersja systemu .NET. http zestawu SDK jest teraz zgodna z definicją w pakiecie NuGet.
* Dopuszczaj żądania zapisu, aby powracać do innego regionu, jeśli oryginalne zakończyło się niepowodzeniem.
* Dodaj zasady ponawiania sesji dla żądania zapisu.

### <a name="a-name241241"></a>@no__t — 02.4.1

* Rozwiązuje warunek wyścigu śledzenia dla zapytań, które spowodowały puste strony

### <a name="a-name240240"></a>@no__t — 02.4.0

* Zwiększony rozmiar dokładności dla zapytań LINQ.
* Dodano nowe klasy CompositePath, CompositePathSortOrder, SpatialSpec, Przestrzennytype i PartitionKeyDefinitionVersion
* Dodano TimeToLivePropertyPath do Documentcollection
* Dodano CompositeIndexes i SpatialIndexes do IndexPolicy
* Dodano wersję do PartitionKeyDefinition
* Dodano brak do PartitionKey

### <a name="a-name230230"></a>@no__t — 02.3.0

 * Dodano IdleTcpConnectionTimeout, OpenTcpConnectionTimeout, MaxRequestsPerTcpConnection i MaxTcpConnectionsPerEndpoint do ConnectionPolicy.
 
### <a name="a-name223223"></a>@no__t — 02.2.3

* Ulepszenia diagnostyki

### <a name="a-name222222"></a>@no__t — 02.2.2

* Dodano ustawienie zmiennej środowiskowej "POCOSerializationOnly".

* Usunięto DocumentDB. przestrzenny. SQL. dll i teraz zawarte w Microsoft. Azure. Documents. serviceinterop. dll

### <a name="a-name221221"></a>@no__t — 02.2.1

* Poprawa logiki ponawiania podczas pracy w trybie failover dla wywołań StoredProcedure Execute.

* DocumentClientEventSource pojedynczo. 

* Poprawka GatewayAddressCache przekroczenie limitu czasu ConnectionPolicy RequestTimeout.

### <a name="a-name220220"></a>@no__t — 02.2.0

* W przypadku komunikacji bezpośredniej/TCP, dodano element transportexception, wewnętrzny typ wyjątku zestawu SDK. Ten typ wyświetla dodatkowe informacje na temat rozwiązywania problemów z łącznością klienta, gdy jest obecny w komunikatach o wyjątku.

* Dodano nowe przeciążenie konstruktora, które pobiera HttpMessageHandler, stos obsługi HTTP do wysyłania żądań HttpClient (np. HttpClientHandler).

* Naprawianie usterki, w której nie został poprawnie obsłużony nagłówek z wartościami null.

* Ulepszono walidację pamięci podręcznej kolekcji.

### <a name="a-name213213"></a>@no__t — 02.1.3

* Zaktualizowano system .NET. Security do 4.3.2.

### <a name="a-name212212"></a>@no__t — 02.1.2

* Ulepszenia śledzenia diagnostyki.

### <a name="a-name211211"></a>@no__t — 02.1.1

* Dodano więcej odporności na błędy przejściowe żądań wieloregionowych.

### <a name="a-name210210"></a>@no__t — 02.1.0

* Dodano obsługę zapisu dla wieloregionu.
* Udoskonalenia wydajności zapytań między partycjami przy użyciu funkcji TOP i MaxBufferedItemCount.

### <a name="a-name200200"></a>@no__t — 02.0.0

* Dodano obsługę anulowania żądania.
* Dodano SetCurrentLocation do ConnectionPolicy, który automatycznie wypełnia preferowane lokalizacje na podstawie regionu.
* Naprawiono usterkę w zapytaniach obejmujących wiele partycji z minimalnym/maksymalnym i filtrem, który nie pasuje do żadnego dokumentu w pojedynczej partycji
* Metody DocumentClient mają teraz parzystość z IDocumentClient.
* Zaktualizowano bezpośredni stos transportu TCP w celu zmniejszenia liczby ustanowionych połączeń.
* Dodano obsługę protokołu TCP w trybie bezpośrednim dla klientów spoza systemu Windows.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Dodano obsługę anulowania żądania.
* Dodano SetCurrentLocation do ConnectionPolicy, który automatycznie wypełnia preferowane lokalizacje na podstawie regionu.
* Naprawiono usterkę w zapytaniach obejmujących wiele partycji z minimalnym/maksymalnym i filtrem, który nie pasuje do żadnego dokumentu w pojedynczej partycji

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0 — wersja zapoznawcza

* Metody DocumentClient mają teraz parzystość z IDocumentClient.
* Zaktualizowano bezpośredni stos transportu TCP w celu zmniejszenia liczby ustanowionych połączeń.
* Dodano obsługę protokołu TCP w trybie bezpośrednim dla klientów spoza systemu Windows.

### <a name="a-name11001100"></a>@no__t — 01.10.0

* Dodano Właściwość ConsistencyLevel do FeedOptions.
* Dodano JsonSerializerSettings do RequestOptions i FeedOptions.
* Dodano EnableReadRequestsFallback do ConnectionPolicy.

### <a name="a-name191191"></a>@no__t — 01.9.1

* Stała KeyNotFoundException dla zamówienia między partycjami przez zapytania w przypadku rogów.
* Naprawiono usterkę, w której atrybut JsonProperty w klauzuli select dla zapytań LINQ nie został uznany.

### <a name="a-name182182"></a>@no__t — 01.8.2

* Rozwiązano problem, który został osiągnięty w pewnych warunkach wyścigu, co powoduje sporadyczne "Microsoft. Azure. Documents. NotFoundException: sesja odczytu nie jest dostępna dla tokenu sesji wejściowej, podczas gdy jest używany poziom spójności sesji.

### <a name="a-name181181"></a>@no__t — 01.8.1

* Stała regresja, gdzie FeedOptions. MaxItemCount =-1 wywołała element System. arytmetyczny: rozmiar strony jest ujemny.
* Dodano nową funkcję ToString () do QueryMetrics.
* Udostępnione statystyki partycji dotyczące odczytywania kolekcji.
* Dodano Właściwość PartitionKey do ChangeFeedOptions.
* Drobne poprawki błędów.

### <a name="a-name171171"></a>@no__t — 01.7.1
 
 * Dodaje możliwość określania unikatowych indeksów dla dokumentów przy użyciu właściwości UniqueKeyPolicy w Dokumenciecollection.
 * Naprawiono usterkę, w której niestandardowe ustawienia JsonSerializer nie zostały uznane za niektóre zapytania i wykonywanie procedury składowanej.

### <a name="a-name170170"></a>@no__t — 01.7.0
 
 * Zmiana marki z usługi Azure DocumentDB w celu Azure Cosmos DB w dokumentacji dotyczącej interfejsów API, informacji o metadanych w zestawach i pakiecie NuGet.
 * Ujawnianie informacji diagnostycznych i opóźnienia od odpowiedzi na żądania wysyłane z trybem bezpośredniej łączności. Nazwy właściwości to RequestDiagnosticsString i RequestLatency klasy ResourceResponse.
 * Ta wersja zestawu SDK wymaga najnowszej wersji emulatora Azure Cosmos DB dostępnej do pobrania z https://aka.ms/cosmosdb-emulator.
 
### <a name="a-name160160"></a>@no__t — 01.6.0

* Dodano kilka poprawek niezawodności i ulepszeń.

### <a name="a-name151151"></a>@no__t — 01.5.1 

* Wewnętrzne zmiany związane z obsługą [Microsoft. Azure.](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet) graphs

### <a name="a-name150150"></a>@no__t — 01.5.0 

* Dodano obsługę PartitionKeyRangeId jako FeedOption do określania zakresu wyników zapytania do określonej wartości zakresu kluczy partycji.
* Dodano obsługę polecenia StartTime jako ChangeFeedOption, aby rozpocząć wyszukiwanie zmian po tym czasie.

### <a name="a-name141141"></a>@no__t — 01.4.1

*   Rozwiązano problem w klasie JsonSerializable, który może spowodować wyjątek przepełnienia stosu.

### <a name="a-name140140"></a>@no__t — 01.4.0

*   Dodano obsługę określania niestandardowego JsonSerializerSettings podczas tworzenia wystąpienia instancji [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet) .

### <a name="a-name132132"></a>@no__t — 01.3.2

*   Obsługa .NET Standard 1,5 jako jednej z platform docelowych.

### <a name="a-name131131"></a>@no__t — 01.3.1

*   Rozwiązano problem, który dotyczy maszyn x64, które nie obsługują instrukcji SSE4 i throw SEHException — podczas uruchamiania zapytań Azure Cosmos DB.

### <a name="a-name130130"></a>@no__t — 01.3.0

*   Dodano obsługę nowego poziomu spójności o nazwie ConsistentPrefix.
*   Dodano obsługę metryk kwerend dla poszczególnych partycji.
*   Dodano obsługę ograniczania rozmiaru tokenu kontynuacji dla zapytań.
*   Dodano obsługę bardziej szczegółowych śledzenia żądań zakończonych niepowodzeniem.
*   Wprowadzono pewne ulepszenia wydajności w zestawie SDK.

### <a name="a-name122122"></a>@no__t — 01.2.2

* Rozwiązano problem, który zignorował wartość PartitionKey podaną w FeedOptions dla zapytań agregujących.
* Rozwiązano problem polegający na niewidocznej obsłudze zarządzania partycjami w ramach zamówienia między różnymi partycjami w ramach procesu tworzenia zapytań.

### <a name="a-name121121"></a>@no__t — 01.2.1

* Rozwiązano problem, który spowodował zakleszczenia w niektórych asynchronicznych interfejsach API, gdy jest używany wewnątrz kontekstu ASP.NET.

### <a name="a-name120120"></a>@no__t — 01.2.0

* Poprawki zwiększające odporność zestawu SDK na automatyczną pracę awaryjną w pewnych warunkach.

### <a name="a-name112112"></a>@no__t — 01.1.2

* Poprawka dotycząca problemu, który sporadycznie powoduje wystąpienie elementu WebException: nie można rozpoznać nazwy zdalnej.
* Dodano obsługę bezpośredniego odczytywania wpisanego dokumentu przez dodanie nowych przeciążeń do interfejsu API ReadDocumentAsync.

### <a name="a-name111111"></a>@no__t — 01.1.1

* Dodano obsługę LINQ dla zapytań agregacji (COUNT, MIN, MAX, SUM i AVG).
* Poprawka dotycząca problemu przecieku pamięci dla obiektu ConnectionPolicy z powodu użycia procedury obsługi zdarzeń.
* Poprawka dotycząca problemu, który UpsertAttachmentAsync nie działa, gdy użyto elementu ETag.
* Poprawka dotycząca problemu w przypadku, gdy sortowanie między różnymi partycjami nie działa podczas sortowania w polu ciągu.

### <a name="a-name110110"></a>@no__t — 01.1.0

* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, SUM i AVG). Zobacz [Obsługa agregacji](sql-query-aggregates.md).
* Obniżona minimalna przepływność na partycjonowanych kolekcjach z 10 100 RU/s do 2500 RU/s.

### <a name="a-name100100"></a>@no__t — 01.0.0

Azure Cosmos DB zestaw .NET Core SDK umożliwia tworzenie szybkich, międzyplatformowych aplikacji [ASP.NET Core](https://www.asp.net/core) i [.NET Core](https://www.microsoft.com/net/core#windows) do uruchamiania w systemach Windows, Mac i Linux. Najnowsza wersja zestaw .NET Core SDK Azure Cosmos DB jest w pełni zgodna z platformą [Xamarin](https://www.xamarin.com) i służy do kompilowania aplikacji przeznaczonych dla systemów iOS, Android i mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0 — wersja zapoznawcza

Zestaw SDK programu Azure Cosmos DB .NET Core umożliwia tworzenie szybkich, międzyplatformowych aplikacji [ASP.NET Core](https://www.asp.net/core) i [.NET Core](https://www.microsoft.com/net/core#windows) do uruchamiania w systemach Windows, Mac i Linux.

Zestaw SDK programu Azure Cosmos DB .NET Core ma dostęp do najnowszej wersji [zestawu sdk Azure Cosmos DB .NET](sql-api-sdk-dotnet.md) i obsługuje następujące funkcje:
* Wszystkie [tryby połączeń](performance-tips.md#networking): tryb bramy, bezpośrednie TCP i bezpośrednie https.
* Wszystkie [poziomy spójności](consistency-levels.md): silna, sesja, powiązana nieaktualność i ostateczna.
* [Partycjonowane kolekcje](partition-data.md).
* [Wieloregionowe konta bazy danych i replikacja geograficzna](distribute-data-globally.md).

Jeśli masz pytania związane z tym zestawem SDK, Opublikuj je w usłudze [StackOverflow](https://stackoverflow.com/questions/tagged/azure-documentdb)lub zageneruj problem w [repozytorium GitHub](https://github.com/Azure/azure-documentdb-dotnet/issues).

## <a name="release--retirement-dates"></a>Data wycofania &
Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** przed WYCOFANIEM zestawu SDK w celu zapewnienia sprawnego przejścia do nowszej/obsługiwanej wersji.

Nowe funkcje i funkcje i optymalizacje są dodawane tylko do bieżącego zestawu SDK, dlatego zaleca się, aby zawsze uaktualnić do najnowszej wersji zestawu SDK tak szybko, jak to możliwe. 

Wszystkie żądania do Azure Cosmos DB za pomocą wycofanego zestawu SDK są odrzucane przez usługę.

> [!WARNING]
> Wszystkie wersje **1. x** zestaw .NET Core SDK dla interfejsu API SQL zostaną wycofane **30 sierpnia 2020**.
> 
>
<br/>


| Wersja | Data wydania | Data wycofania |
| --- | --- | --- |
| [2.7.0](#2.7.0) |23 września, 2019 |--- |
| [2.6.0](#2.6.0) |30 sierpnia 2019 |--- |
| [2.5.1](#2.5.1) |02 lipca 2019 |--- |
| [2.4.1](#2.4.1) |20 czerwca 2019 |--- |
| [2.4.0](#2.4.0) |05 maja 2019 |--- |
| [2.3.0](#2.3.0) |04, 2019 |--- |
| [Regulamin](#2.2.3) |11 marca 2019 |--- |
| [ppkt](#2.2.2) |06, 2019 |--- |
| [2.2.1](#2.2.1) |24 grudnia 2018 |--- |
| [2.2.0](#2.2.0) |07, 2018 |--- |
| [2.1.3](#2.1.3) |15 października 2018 |--- |
| [2.1.2](#2.1.2) |04, 2018 |--- |
| [pkt](#2.1.1) |27 września, 2018 |--- |
| [2.1.0](#2.1.0) |21 września 2018 |--- |
| [2.0.0](#2.0.0) |07 września, 2018 |--- |
| [1.9.1](#1.9.1) |09 marca, 2018 |30 sierpnia 2020 |
| [1.8.2](#1.8.2) |21 lutego 2018 |30 sierpnia 2020 |
| [1.8.1](#1.8.1) |05 lutego, 2018 |30 sierpnia 2020 |
| [1.7.1](#1.7.1) |16 listopada 2017 |30 sierpnia 2020 |
| [1.7.0](#1.7.0) |10 listopada 2017 |30 sierpnia 2020 |
| [1.6.0](#1.6.0) |17 października 2017 |30 sierpnia 2020 |
| [1.5.1](#1.5.1) |2 października 2017 |30 sierpnia 2020 |
| [1.5.0](#1.5.0) |10 sierpnia 2017 |30 sierpnia 2020 | 
| [1.4.1](#1.4.1) |07 sierpnia 2017 |30 sierpnia 2020 |
| [1.4.0](#1.4.0) |02 sierpnia 2017 |30 sierpnia 2020 |
| [1.3.2](#1.3.2) |12 czerwca 2017 |30 sierpnia 2020 |
| [1.3.1](#1.3.1) |23 maja 2017 |30 sierpnia 2020 |
| [1.3.0](#1.3.0) |10 maja 2017 |30 sierpnia 2020 |
| [ppkt](#1.2.2) |19 kwietnia 2017 |30 sierpnia 2020 |
| [ppkt](#1.2.1) |29 marca 2017 |30 sierpnia 2020 |
| [1.2.0](#1.2.0) |25 marca 2017 |30 sierpnia 2020 |
| [1.1.2](#1.1.2) |20 marca 2017 |30 sierpnia 2020 |
| [ppkt](#1.1.1) |14 marca 2017 |30 sierpnia 2020 |
| [1.1.0](#1.1.0) |16 lutego 2017 |30 sierpnia 2020 |
| [1.0.0](#1.0.0) |21 grudnia 2016 |30 sierpnia 2020 |
| [0.1.0 — wersja zapoznawcza](#0.1.0-preview) |15 listopada 2016 |31 grudnia 2016 |

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej na temat Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) .

