---
title: Usługa Azure Cosmos DB Interfejs API SQL platformy .NET Core, zestaw SDK i zasoby
description: Poznaj interfejs API SQL platformy .NET Core i zestawu SDK, w tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami usługi Azure Cosmos DB .NET Core SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: 91f15f9c19b480d950b2c715e6d9290e01184cf7
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272378"
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB platformy .NET Core SDK interfejsu API SQL: Informacje o wersji i zasoby
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Kanał informacyjny zmian .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java (asynchroniczny)](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulkexecutor — platforma .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulkexecutor — platforma Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Zestaw SDK do pobrania**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)|
|**Dokumentacja interfejsu API**|[Dokumentacja interfejsu API platformy .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Przykłady**|[Przykłady kodu platformy .NET](sql-api-dotnet-samples.md)|
|**Wprowadzenie**|[Rozpoczynanie pracy z usługą Azure Cosmos DB platformy .NET Core SDK](sql-api-dotnet-core-get-started-preview.md)|
|**Samouczek dotyczący aplikacji sieci Web**|[Opracowywanie aplikacji sieci Web za pomocą usługi Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Bieżącej struktury obsługiwanej**|[.NET standard w wersji 1.6 i .NET Standard w wersji 1.5](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Informacje o wersji

Azure Cosmos DB platformy .NET Core SDK ma równoważności funkcji z najnowszej wersji [zestawu .NET SDK usługi Azure Cosmos DB](sql-api-sdk-dotnet.md).

### <a name="a-name3001-preview3001-preview"></a><a name="3.0.0.1-preview"/>3.0.0.1-Preview
* Wersja zapoznawcza 1 [wersji 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) zestawu SDK platformy .NET w wersji zapoznawczej.
* Docelowy .NET Standard, która obsługuje .NET framework 4.6.1+ i .NET Core 2.0 +
* Nowy model obiektów za pomocą metod CosmosClient najwyższego poziomu i Podziel na odpowiednich klas CosmosDatabases, CosmosContainers i CosmosItems.
* Obsługa strumieni.
* Zaktualizowano CosmosResponseMessage z serwera, aby zwrócić kod stanu, a tylko zgłosić wyjątek, gdy brak odpowiedzi jest zwracany.

### <a name="a-name250250"></a><a name="2.5.0"/>2.5.0

* Zezwalaj na żądania zapisu powrót do innego regionu, jeśli oryginalny jeden zakończy się niepowodzeniem
* Dodaj zasady ponawiania sesji dla żądania zapisu
* Poprawki śledzenia zapytań, które spowodowały puste strony sytuacja wyścigu

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0

* Wersja System.Net.Http zestawu SDK pasuje, co to jest zdefiniowany w pakiecie NuGet
* Zwiększony rozmiar precyzja dziesiętna zapytań LINQ.
* Dodano nowe klasy CompositePath, CompositePathSortOrder, SpatialSpec, SpatialType i PartitionKeyDefinitionVersion
* Dodano TimeToLivePropertyPath do elementu DocumentCollection
* Dodano CompositeIndexes i SpatialIndexes do IndexPolicy
* Dodano wersji PartitionKeyDefinition
* Brak, aby PartitionKey dodane

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0

 * Dodano IdleTcpConnectionTimeout, OpenTcpConnectionTimeout, MaxRequestsPerTcpConnection i MaxTcpConnectionsPerEndpoint do ConnectionPolicy.
 
### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3

* Ulepszenia diagnostyki

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2

* Ustawienie zmiennej środowiska dodano "POCOSerializationOnly".

* Usunięte DocumentDB.Spatial.Sql.dll i teraz zawarta w Microsoft.Azure.Documents.ServiceInterop.dll

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* Poprawa Logika ponawiania podczas pracy awaryjnej dla StoredProcedure wykonać wywołań.

* Dokonano jeszcze DocumentClientEventSource pojedynczego wystąpienia. 

* Napraw GatewayAddressCache nie zapewniane ConnectionPolicy RequestTimeout przekroczenia limitu czasu.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* Diagnostykę transportu bezpośrednio/TCP należy dodać TransportException, typ wyjątku wewnętrznego zestawu SDK. Jeśli jest obecny w komunikaty o wyjątkach, ten typ Wyświetla dodatkowe informacje dotyczące rozwiązywania problemów z łącznością klienta.

* Dodano nowe przeciążenia konstruktora przyjmująca klasa HttpMessageHandler, stos programu obsługi HTTP, używany do wysyłania żądań HttpClient (np. HttpClientHandler).

* Naprawiono błąd, w której nagłówek o wartości null zostały nie jest obsługiwany poprawnie.

* Ulepszone sprawdzanie poprawności pamięci podręcznej kolekcji.

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
* Naprawiono usterkę, w którym atrybut JsonProperty w klauzuli select zapytań LINQ, został nie już brane pod uwagę.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Naprawiono usterkę, która tych limitów zostanie osiągnięty w niektórych sytuacjach wyścigu, dającą wynik sporadyczne "Microsoft.Azure.Documents.NotFoundException: Błędy odczytu sesji nie jest dostępna dla tokenu sesji wejściowego"korzystając z poziomu spójności sesji.

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

* Poprawkę dla problemu, która czasami powoduje to klasa WebException: Nie można rozpoznać nazwy zdalnej.
* Dodano obsługę bezpośrednio odczytu dokumentu wpisane, dodając nowe przeciążenia do interfejsu API ReadDocumentAsync.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Dodano obsługę LINQ dla zapytań agregacji (COUNT, MIN, MAX, Suma i średnia).
* Poprawka problem przeciek pamięci dla obiektu ConnectionPolicy spowodowane użyciem programu obsługi zdarzeń.
* Poprawkę dla problemu polegającego UpsertAttachmentAsync nie była praca stosowania element ETag.
* Rozwiązywanie problemu polegającego wielu partycji klauzuli order by zapytania kontynuacji została nie działa podczas sortowania na pola ciągu.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, Suma i średnia). Zobacz [Obsługa agregacji](how-to-sql-query.md#Aggregates).
* Obniżona minimalna przepływność na kolekcji podzielonych na partycje z 10,100 jednostek RU/s 2500 jednostek RU/s.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

Azure Cosmos DB platformy .NET Core SDK pozwala na tworzenie szybkich i dla różnych platform [platformy ASP.NET Core](https://www.asp.net/core) i [platformy .NET Core](https://www.microsoft.com/net/core#windows) aplikacji działających na Windows, Mac i Linux. Najnowsza wersja zestawu SDK usługi Azure Cosmos DB platformy .NET Core jest w pełni [Xamarin](https://www.xamarin.com) zgodne i służyć do kompilowania aplikacji przeznaczonych dla systemu iOS, Android oraz Mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-Preview

Azure Cosmos DB platformy .NET Core w wersji zapoznawczej SDK pozwala na tworzenie szybkich i dla różnych platform [platformy ASP.NET Core](https://www.asp.net/core) i [platformy .NET Core](https://www.microsoft.com/net/core#windows) aplikacji działających na Windows, Mac i Linux.

Azure Cosmos DB platformy .NET Core w wersji zapoznawczej SDK ma równoważności funkcji z najnowszej wersji [zestawu .NET SDK usługi Azure Cosmos DB](sql-api-sdk-dotnet.md) i obsługuje następujące czynności:
* Wszystkie [tryby połączeń](performance-tips.md#networking): Tryb bramy, bezpośredniego połączenia TCP i bezpośredniego protokołu HTTPs.
* Wszystkie [poziomów spójności](consistency-levels.md): Silna "," sesja, powiązana nieaktualność oraz "eventual".
* [Kolekcje partycjonowane](partition-data.md).
* [Multiregionalne konta baz danych i replikacja geograficzna](distribute-data-globally.md).

Jeśli masz pytania dotyczące tego zestawu SDK, Opublikuj [StackOverflow](https://stackoverflow.com/questions/tagged/azure-documentdb), lub Prześlij zgłoszenie do [repozytorium GitHub](https://github.com/Azure/azure-documentdb-dotnet/issues).

## <a name="release--retirement-dates"></a>Wersja & dat wycofywania

| Wersja | Data wydania | Data wygaśnięcia |
| --- | --- | --- |
| [2.5.0](#2.5.0) |18 czerwca 2019 r. |--- |
| [2.4.0](#2.4.0) |05 maja 2019 r. |--- |
| [2.3.0](#2.3.0) |04 kwietnia 2019 r. |--- |
| [2.2.3](#2.2.3) |11 marca 2019 r. |--- |
| [2.2.2](#2.2.2) |06 lutego 2019 r. |--- |
| [2.2.1](#2.2.1) |24 grudnia 2018 r. |--- |
| [2.2.0](#2.2.0) |07 grudnia 2018 r. |--- |
| [2.1.3](#2.1.3) |15 października 2018 r. |--- |
| [2.1.2](#2.1.2) |04 października 2018 r. |--- |
| [2.1.1](#2.1.1) |27 września 2018 r. |--- |
| [2.1.0](#2.1.0) |21 września 2018 r. |--- |
| [2.0.0](#2.0.0) |07 września 2018 r. |--- |
| [1.9.1](#1.9.1) |09 marca 2018 r. |--- |
| [1.8.2](#1.8.2) |21 lutego 2018 r. |--- |
| [1.8.1](#1.8.1) |05 lutego 2018 r. |--- |
| [1.7.1](#1.7.1) |16 listopada 2017 r. |--- |
| [1.7.0](#1.7.0) |10 listopada 2017 r. |--- |
| [1.6.0](#1.6.0) |17 października 2017 r. |--- |
| [1.5.1](#1.5.1) |02 października 2017 r. |--- |
| [1.5.0](#1.5.0) |10 sierpnia 2017 r. |--- | 
| [1.4.1](#1.4.1) |07 sierpnia 2017 r. |--- |
| [1.4.0](#1.4.0) |02 sierpnia 2017 r. |--- |
| [1.3.2](#1.3.2) |12 czerwca 2017 r. |--- |
| [1.3.1](#1.3.1) |23 maja 2017 r. |--- |
| [1.3.0](#1.3.0) |10 maja 2017 |--- |
| [1.2.2](#1.2.2) |19 kwietnia 2017 r. |--- |
| [1.2.1](#1.2.1) |29 marca 2017 r. |--- |
| [1.2.0](#1.2.0) |25 marca 2017 r. |--- |
| [1.1.2](#1.1.2) |20 marca 2017 r. |--- |
| [1.1.1](#1.1.1) |14 marca 2017 r. |--- |
| [1.1.0](#1.1.0) |16 lutego 2017 r. |--- |
| [1.0.0](#1.0.0) |21 grudnia 2016 r. |--- |
| [0.1.0-Preview](#0.1.0-preview) |15 listopada 2016 r. |Do 31 grudnia 2016 r. |

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stronę usługi.

