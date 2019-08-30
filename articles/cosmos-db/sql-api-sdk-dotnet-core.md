---
title: Usługa Azure Cosmos DB SQL .NET Core API, zasoby & SDK
description: Poznaj interfejs API SQL platformy .NET Core i zestawu SDK, w tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami usługi Azure Cosmos DB .NET Core SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: 1b1e9338f92aaefab8e7754139066ac03e8b8213
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142632"
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>Zestaw .NET Core SDK Azure Cosmos DB dla interfejsu API SQL: Informacje o wersji i zasoby
> [!div class="op_single_selector"]
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [Kanał informacyjny zmian .NET](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java (asynchroniczny)](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Wykonawca zbiorczy — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonawczy zbiorczej — Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Zestaw SDK do pobrania**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)|
|**Dokumentacja interfejsu API**|[Dokumentacja interfejsu API platformy .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Przykłady**|[Przykłady kodu platformy .NET](sql-api-dotnet-samples.md)|
|**Wprowadzenie**|[Wprowadzenie do Azure Cosmos DB .NET](sql-api-sdk-dotnet.md)|
|**Samouczek dotyczący aplikacji sieci Web**|[Opracowywanie aplikacji sieci Web za pomocą usługi Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Bieżącej struktury obsługiwanej**|[.NET standard w wersji 1.6 i .NET Standard w wersji 1.5](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Informacje o wersji

> [!NOTE]
> W przypadku korzystania z platformy .NET Core zapoznaj się z najnowszą wersją 3. x [zestawu SDK platformy .NET](sql-api-sdk-dotnet-standard.md), która jest przeznaczona dla .NET Standard. 

### <a name="a-name251251"></a><a name="2.5.1"/>2.5.1

* Wersja systemu .NET. http zestawu SDK jest teraz zgodna z definicją w pakiecie NuGet.
* Dopuszczaj żądania zapisu, aby powracać do innego regionu, jeśli oryginalne zakończyło się niepowodzeniem.
* Dodaj zasady ponawiania sesji dla żądania zapisu.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1

* Rozwiązuje warunek wyścigu śledzenia dla zapytań, które spowodowały puste strony

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0

* Zwiększony rozmiar dokładności dla zapytań LINQ.
* Dodano nowe klasy CompositePath, CompositePathSortOrder, SpatialSpec, Przestrzennytype i PartitionKeyDefinitionVersion
* Dodano TimeToLivePropertyPath do Documentcollection
* Dodano CompositeIndexes i SpatialIndexes do IndexPolicy
* Dodano wersję do PartitionKeyDefinition
* Dodano brak do PartitionKey

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0

 * Dodano IdleTcpConnectionTimeout, OpenTcpConnectionTimeout, MaxRequestsPerTcpConnection i MaxTcpConnectionsPerEndpoint do ConnectionPolicy.
 
### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3

* Ulepszenia diagnostyki

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2

* Dodano ustawienie zmiennej środowiskowej "POCOSerializationOnly".

* Usunięto DocumentDB. przestrzenny. SQL. dll i teraz zawarte w Microsoft. Azure. Documents. serviceinterop. dll

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* Poprawa logiki ponawiania podczas pracy w trybie failover dla wywołań StoredProcedure Execute.

* DocumentClientEventSource pojedynczo. 

* Poprawka GatewayAddressCache przekroczenie limitu czasu ConnectionPolicy RequestTimeout.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* W przypadku komunikacji bezpośredniej/TCP, dodano element transportexception, wewnętrzny typ wyjątku zestawu SDK. Ten typ wyświetla dodatkowe informacje na temat rozwiązywania problemów z łącznością klienta, gdy jest obecny w komunikatach o wyjątku.

* Dodano nowe przeciążenie konstruktora, które pobiera HttpMessageHandler, stos obsługi HTTP do wysyłania żądań HttpClient (np. HttpClientHandler).

* Naprawianie usterki, w której nie został poprawnie obsłużony nagłówek z wartościami null.

* Ulepszono walidację pamięci podręcznej kolekcji.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3

* Zaktualizowano System.Net.Security do 4.3.2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* Ulepszenia śledzenia diagnostycznego.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1

* Dodać więcej odporności na awarie przejściowe żądania w wielu regionach.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Dodano wiele regionów zapisu pomocy technicznej.
* Wiele partycji wydajność zapytań, u góry i MaxBufferedItemCount wzrasta.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Dodano żądanie anulowania obsługi.
* Dodano SetCurrentLocation do ConnectionPolicy, który automatycznie wypełni preferowane lokalizacje, w oparciu o regionie.
* Usunięto usterkę występującą w wielu zapytaniach partycji Min/Max i filtr, który pasuje do żadnych dokumentów w poszczególnych partycji.
* Metody DocumentClient mają teraz zgodność z IDocumentClient.
* Zaktualizowano TCP transportu stos funkcji bezpośrednie do zmniejszenia liczby połączeń.
* Dodano obsługę bezpośredniego połączenia TCP w trybie dla klientów innych niż Windows.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Dodano żądanie anulowania obsługi.
* Dodano SetCurrentLocation do ConnectionPolicy, który automatycznie wypełni preferowane lokalizacje, w oparciu o regionie.
* Usunięto usterkę występującą w wielu zapytaniach partycji Min/Max i filtr, który pasuje do żadnych dokumentów w poszczególnych partycji.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview

* Metody DocumentClient mają teraz zgodność z IDocumentClient.
* Zaktualizowano TCP transportu stos funkcji bezpośrednie do zmniejszenia liczby połączeń.
* Dodano obsługę bezpośredniego połączenia TCP w trybie dla klientów innych niż Windows.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0

* Dodano właściwość ConsistencyLevel FeedOptions.
* Dodano JsonSerializerSettings RequestOptions i FeedOptions.
* Dodano EnableReadRequestsFallback do ConnectionPolicy.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1

* Naprawiono wyjątek KeyNotFoundException dla wielu partycji zamówienia przez zapytania w przypadkach brzegowych.
* Naprawiono usterkę, w której atrybut JsonProperty w klauzuli select dla zapytań LINQ nie został uznany.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Rozwiązano problem, który został trafiony w pewnych warunkach wyścigu, co powoduje sporadyczne "Microsoft. Azure. Documents. NotFoundException: Sesja odczytu nie jest dostępna w przypadku błędów tokenu sesji wejściowej w przypadku używania poziomu spójności sesji.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1

* Naprawiono regresję gdzie FeedOptions.MaxItemCount = -1 zgłosił System.ArithmeticException: rozmiar strony jest ujemna.
* Dodano nową funkcję ToString() do QueryMetrics.
* Widoczne statystyk partycji podczas odczytywania kolekcji.
* Dodano właściwości PartitionKey ChangeFeedOptions.
* Niewielkie poprawki błędów.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
 
 * Dodaje możliwość określenia indeksy unikatowe dla dokumentów za pomocą właściwości UniqueKeyPolicy na elementu DocumentCollection.
 * Naprawiono usterkę, w którym z niestandardowymi ustawieniami JsonSerializer są nie już brane pod uwagę dla niektórych kwerend i wykonywanie procedury składowanej.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
 
 * Znakowanie zmiany z usługi Azure DocumentDB usługi Azure Cosmos DB w dokumentacji interfejsu API, dokumentacja, informacje o metadanych w zestawach i pakietu NuGet.
 * Udostępnianie informacji diagnostycznych i opóźnienia z odpowiedzi żądań wysyłanych z trybem łączności bezpośredniej. Nazwy właściwości są RequestDiagnosticsString i RequestLatency ResourceResponse klasy.
 * Ta wersja zestawu SDK wymaga najnowszej wersji emulatora usługi Azure Cosmos dostępne do pobrania z https://aka.ms/cosmosdb-emulator.
 
### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Dodano kilka ulepszenia i poprawki niezawodności.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* Wewnętrzny zmiany związane z obsługi [Microsoft.Azure.Graphs](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* Dodano obsługę PartitionKeyRangeId jako FeedOption do określania zakresu wyników zapytania do określonej partycji klucza zakresu wartości.
* Dodano obsługę StartTime jako ChangeFeedOption do rozpoczęcia wyszukiwania zmiany po upływie tego okresu.

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   Rozwiązano problem w klasie JsonSerializable, która może spowodować wyjątek przepełnienia stosu.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   Dodano obsługę określania niestandardowych JsonSerializerSettings podczas tworzenia wystąpienia [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet) wystąpienia.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   Obsługa .NET Standard w wersji 1.5 jako jedną z platform docelowych.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   Rozwiązano problem, który dotyczy x64 maszyn, które nie obsługują SSE4 instrukcji throw sehexception — podczas uruchamiania zapytań usługi Azure Cosmos DB.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   Dodano obsługę nowego poziomu spójności, nazywana ConsistentPrefix.
*   Dodano obsługę zapytań metryki dla pojedynczych partycji.
*   Dodano obsługę ograniczający rozmiar token kontynuacji dla zapytań.
*   Dodano obsługę bardziej szczegółowe śledzenie niepomyślnych żądań.
*   Wprowadzone ulepszenia wydajności w zestawie SDK.

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* Rozwiązano problem, który jest ignorowana wartość PartitionKey udostępniane w FeedOptions dla zapytania zagregowane.
* Rozwiązano problem z przezroczystym obsługi zarządzania partycji podczas lotu połowie między partycjami Order By wykonywania zapytań.

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* Rozwiązano problem, co spowodowało zakleszczenia w niektórych asynchronicznych interfejsów API, gdy jest używana w kontekście platformy ASP.NET.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Rozwiązuje się zestaw SDK bardziej odporne na automatyczną pracę awaryjną pod pewnymi warunkami.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Poprawka dotycząca problemu, który sporadycznie powoduje wystąpienie elementu WebException: Nie można rozpoznać nazwy zdalnej.
* Dodano obsługę bezpośrednio odczytu dokumentu wpisane, dodając nowe przeciążenia do interfejsu API ReadDocumentAsync.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Dodano obsługę LINQ dla zapytań agregacji (COUNT, MIN, MAX, Suma i średnia).
* Poprawka problem przeciek pamięci dla obiektu ConnectionPolicy spowodowane użyciem programu obsługi zdarzeń.
* Poprawkę dla problemu polegającego UpsertAttachmentAsync nie była praca stosowania element ETag.
* Rozwiązywanie problemu polegającego wielu partycji klauzuli order by zapytania kontynuacji została nie działa podczas sortowania na pola ciągu.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, Suma i średnia). Zobacz [Obsługa agregacji](sql-query-aggregates.md).
* Obniżona minimalna przepływność na kolekcji podzielonych na partycje z 10,100 jednostek RU/s 2500 jednostek RU/s.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

Azure Cosmos DB platformy .NET Core SDK pozwala na tworzenie szybkich i dla różnych platform [platformy ASP.NET Core](https://www.asp.net/core) i [platformy .NET Core](https://www.microsoft.com/net/core#windows) aplikacji działających na Windows, Mac i Linux. Najnowsza wersja zestawu SDK usługi Azure Cosmos DB platformy .NET Core jest w pełni [Xamarin](https://www.xamarin.com) zgodne i służyć do kompilowania aplikacji przeznaczonych dla systemu iOS, Android oraz Mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-Preview

Azure Cosmos DB platformy .NET Core w wersji zapoznawczej SDK pozwala na tworzenie szybkich i dla różnych platform [platformy ASP.NET Core](https://www.asp.net/core) i [platformy .NET Core](https://www.microsoft.com/net/core#windows) aplikacji działających na Windows, Mac i Linux.

Azure Cosmos DB platformy .NET Core w wersji zapoznawczej SDK ma równoważności funkcji z najnowszej wersji [zestawu .NET SDK usługi Azure Cosmos DB](sql-api-sdk-dotnet.md) i obsługuje następujące czynności:
* Wszystkie [tryby połączeń](performance-tips.md#networking): Tryb bramy, bezpośrednie TCP i bezpośrednie HTTPs.
* Wszystkie [poziomy spójności](consistency-levels.md): Silne, powiązana nieaktualność i ostateczne.
* [Kolekcje partycjonowane](partition-data.md).
* [Multiregionalne konta baz danych i replikacja geograficzna](distribute-data-globally.md).

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


| Version | Data wydania | Data wygaśnięcia |
| --- | --- | --- |
| [2.5.1](#2.5.1) |02 lipca 2019 |--- |
| [2.4.1](#2.4.1) |20 czerwca 2019 |--- |
| [2.4.0](#2.4.0) |05 maja 2019 |--- |
| [2.3.0](#2.3.0) |04, 2019 |--- |
| [2.2.3](#2.2.3) |11 marca 2019 |--- |
| [2.2.2](#2.2.2) |06, 2019 |--- |
| [2.2.1](#2.2.1) |24 grudnia 2018 |--- |
| [2.2.0](#2.2.0) |07, 2018 |--- |
| [2.1.3](#2.1.3) |15 października 2018 r. |--- |
| [2.1.2](#2.1.2) |04 października 2018 r. |--- |
| [2.1.1](#2.1.1) |27 września 2018 r. |--- |
| [2.1.0](#2.1.0) |21 września 2018 r. |--- |
| [2.0.0](#2.0.0) |07 września 2018 r. |--- |
| [1.9.1](#1.9.1) |09 marca 2018 r. |30 sierpnia 2020 |
| [1.8.2](#1.8.2) |21 lutego 2018 r. |30 sierpnia 2020 |
| [1.8.1](#1.8.1) |05 lutego 2018 r. |30 sierpnia 2020 |
| [1.7.1](#1.7.1) |16 listopada 2017 r. |30 sierpnia 2020 |
| [1.7.0](#1.7.0) |10 listopada 2017 r. |30 sierpnia 2020 |
| [1.6.0](#1.6.0) |17 października 2017 r. |30 sierpnia 2020 |
| [1.5.1](#1.5.1) |02 października 2017 r. |30 sierpnia 2020 |
| [1.5.0](#1.5.0) |10 sierpnia 2017 r. |30 sierpnia 2020 | 
| [1.4.1](#1.4.1) |07 sierpnia 2017 r. |30 sierpnia 2020 |
| [1.4.0](#1.4.0) |02 sierpnia 2017 r. |30 sierpnia 2020 |
| [1.3.2](#1.3.2) |12 czerwca 2017 r. |30 sierpnia 2020 |
| [1.3.1](#1.3.1) |23 maja 2017 r. |30 sierpnia 2020 |
| [1.3.0](#1.3.0) |10 maja 2017 |30 sierpnia 2020 |
| [1.2.2](#1.2.2) |19 kwietnia 2017 r. |30 sierpnia 2020 |
| [1.2.1](#1.2.1) |29 marca 2017 r. |30 sierpnia 2020 |
| [1.2.0](#1.2.0) |25 marca 2017 r. |30 sierpnia 2020 |
| [1.1.2](#1.1.2) |20 marca 2017 r. |30 sierpnia 2020 |
| [1.1.1](#1.1.1) |14 marca 2017 r. |30 sierpnia 2020 |
| [1.1.0](#1.1.0) |16 lutego 2017 r. |30 sierpnia 2020 |
| [1.0.0](#1.0.0) |21 grudnia 2016 r. |30 sierpnia 2020 |
| [0.1.0-Preview](#0.1.0-preview) |15 listopada 2016 r. |Do 31 grudnia 2016 r. |

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stronę usługi.

