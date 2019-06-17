---
title: Usługa Azure Cosmos DB Interfejs API SQL platformy .NET, zestaw SDK i zasoby
description: Poznaj interfejs API SQL platformy .NET i zestawu SDK, w tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami zestawu SDK .NET usługi Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: sngun
ms.openlocfilehash: 3c420882b734883039ec95d609c155617359fa25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510718"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB zestawu .NET SDK interfejsu API SQL: Pobierz i informacje o wersji
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
|**Zestaw SDK do pobrania**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**Dokumentacja interfejsu API**|[Dokumentacja interfejsu API platformy .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Przykłady**|[Przykłady kodu platformy .NET](sql-api-dotnet-samples.md)|
|**Wprowadzenie**|[Wprowadzenie do zestawu SDK .NET usługi Azure Cosmos DB](sql-api-get-started.md)|
|**Samouczek dotyczący aplikacji sieci Web**|[Opracowywanie aplikacji sieci Web za pomocą usługi Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Bieżącej struktury obsługiwanej**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="a-name3001-preview3001-preview"></a><a name="3.0.0.1-preview"/>3.0.0.1-Preview
* Wersja zapoznawcza 1 [wersji 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) zestawu SDK platformy .NET w wersji zapoznawczej.
* Docelowy .NET Standard, która obsługuje .NET framework 4.6.1+ i .NET Core 2.0 +
* Nowy model obiektów za pomocą metod CosmosClient najwyższego poziomu i Podziel na odpowiednich klas CosmosDatabases, CosmosContainers i CosmosItems. 
* Obsługa strumieni. 
* Zaktualizowano CosmosResponseMessage z serwera, aby zwrócić kod stanu, a tylko zgłosić wyjątek, gdy brak odpowiedzi jest zwracany. 

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

* Ulepszenia śledzenia diagnostycznego

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

### <a name="a-name12201220"></a><a name="1.22.0"/>1.22.0

* Dodano właściwość ConsistencyLevel FeedOptions.
* Dodano JsonSerializerSettings RequestOptions i FeedOptions.
* Dodano EnableReadRequestsFallback do ConnectionPolicy.

### <a name="a-name12111211"></a><a name="1.21.1"/>1.21.1

* Naprawiono wyjątek KeyNotFoundException dla wielu partycji zamówienia przez zapytania w przypadkach brzegowych.
* Naprawiono usterkę, w którym atrybut JsonProperty w klauzuli select zapytań LINQ, został nie już brane pod uwagę.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* Naprawiono usterkę, która tych limitów zostanie osiągnięty w niektórych sytuacjach wyścigu, dającą wynik sporadyczne "Microsoft.Azure.Documents.NotFoundException: Błędy odczytu sesji nie jest dostępna dla tokenu sesji wejściowego"korzystając z poziomu spójności sesji.

### <a name="a-name12011201"></a><a name="1.20.1"/>1.20.1

* Naprawiono regresję gdzie FeedOptions.MaxItemCount = -1 zgłosił System.ArithmeticException: rozmiar strony jest ujemna.
* Dodano nową funkcję ToString() do QueryMetrics.
* Widoczne statystyk partycji podczas odczytywania kolekcji.
* Dodano właściwości PartitionKey ChangeFeedOptions.
* Niewielkie poprawki błędów.

### <a name="a-name11911191"></a><a name="1.19.1"/>1.19.1

* Dodaje możliwość określenia indeksy unikatowe dla dokumentów za pomocą właściwości UniqueKeyPolicy na elementu DocumentCollection.
* Naprawiono usterkę, w którym z niestandardowymi ustawieniami JsonSerializer są nie już brane pod uwagę dla niektórych kwerend i wykonywanie procedury składowanej.

### <a name="a-name11901190"></a><a name="1.19.0"/>1.19.0

* Znakowanie zmiany z usługi Azure DocumentDB usługi Azure Cosmos DB w dokumentacji interfejsu API, dokumentacja, informacje o metadanych w zestawach i pakietu NuGet. 
* Udostępnianie informacji diagnostycznych i opóźnienia z odpowiedzi żądań wysyłanych z trybem łączności bezpośredniej. Nazwy właściwości są RequestDiagnosticsString i RequestLatency ResourceResponse klasy.
* Ta wersja zestawu SDK wymaga najnowszej wersji emulatora usługi Azure Cosmos dostępne do pobrania z https://aka.ms/cosmosdb-emulator. 

### <a name="a-name11811181"></a><a name="1.18.1"/>1.18.1 

* Wewnętrzny zmiany dla Microsoft znajomych zestawów.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* Dodano kilka ulepszenia i poprawki niezawodności.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* Dodano obsługę PartitionKeyRangeId jako FeedOption do określania zakresu wyników zapytania do określonej partycji klucza zakresu wartości. 
* Dodano obsługę StartTime jako ChangeFeedOption do rozpoczęcia wyszukiwania zmiany po upływie tego okresu.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Rozwiązano problem w klasie JsonSerializable, która może spowodować wyjątek przepełnienia stosu.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   Rozwiązano problem polegający na wymagające ponownego kompilowania aplikacji z powodu wprowadzenia JsonSerializerSettings jako opcjonalny parametr w Konstruktorze DocumentClient.
* Oznaczony jako konstruktora DocumentClient przestarzały tego wymagane JsonSerializerSettings jako ostatni parametr, aby umożliwić wartościami domyślnymi ConnectionPolicy i parametry ConsistencyLevel podczas przekazywania w parametrze JsonSerializerSettings.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Dodano obsługę określania niestandardowych JsonSerializerSettings podczas tworzenia wystąpienia [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Rozwiązano problem, który dotyczy x64 maszyn, które nie obsługują SSE4 instrukcji throw sehexception — podczas uruchamiania zapytań SQL usługi Azure Cosmos DB.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Dodano obsługę nowego poziomu spójności, nazywana ConsistentPrefix.
*   Dodano obsługę zapytań metryki dla pojedynczych partycji.
*   Dodano obsługę ograniczający rozmiar token kontynuacji dla zapytań.
*   Dodano obsługę bardziej szczegółowe śledzenie niepomyślnych żądań.
*   Wprowadzone ulepszenia wydajności w zestawie SDK.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Funkcjonalnie identyczny z 1.13.3. Wprowadzone zmiany wewnętrznego.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Funkcjonalnie identyczny z 1.13.2. Wprowadzone zmiany wewnętrznego.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Rozwiązano problem, który jest ignorowana wartość PartitionKey udostępniane w FeedOptions dla zapytania zagregowane.
* Rozwiązano problem z przezroczystym obsługi zarządzania partycji podczas lotu połowie między partycjami Order By wykonywania zapytań.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Rozwiązano problem, co spowodowało zakleszczenia w niektórych asynchronicznych interfejsów API, gdy jest używana w kontekście platformy ASP.NET.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Rozwiązuje się zestaw SDK bardziej odporne na automatyczną pracę awaryjną pod pewnymi warunkami.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Poprawkę dla problemu, która czasami powoduje to klasa WebException: Nie można rozpoznać nazwy zdalnej.
* Dodano obsługę bezpośrednio odczytu dokumentu wpisane, dodając nowe przeciążenia do interfejsu API ReadDocumentAsync.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* Dodano obsługę LINQ dla zapytań agregacji (COUNT, MIN, MAX, Suma i średnia).
* Poprawka problem przeciek pamięci dla obiektu ConnectionPolicy spowodowane użyciem programu obsługi zdarzeń.
* Poprawkę dla problemu polegającego UpsertAttachmentAsync nie była praca stosowania element ETag.
* Rozwiązywanie problemu polegającego wielu partycji klauzuli order by zapytania kontynuacji została nie działa podczas sortowania na pola ciągu.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, Suma i średnia). Zobacz [Obsługa agregacji](how-to-sql-query.md#Aggregates).
* Obniżona minimalna przepływność na kolekcji podzielonych na partycje z 10,100 jednostek RU/s 2500 jednostek RU/s.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Poprawkę dla problemu polegającego niektórych zapytań między partycjami zakończone niepowodzeniem w procesie 32-bitowego hosta.
* Poprawkę dla problemu polegającego kontenera sesji zostało nie są aktualizowane przy użyciu tokenu dla żądań zakończonych niepowodzeniem w trybie bramy.
* W niektórych przypadkach nie mógł poprawkę dla problemu polegającego wywołuje zapytanie o funkcji zdefiniowanej przez użytkownika w projekcji.
* Rozwiązuje wydajności po stronie klienta pozwalające zwiększyć przepływność odczytu i zapisu żądania.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Poprawkę dla problemu polegającego kontenera sesji zostało nie są aktualizowane przy użyciu tokenu dla żądań zakończonych niepowodzeniem.
* Dodano obsługę zestawu SDK do pracy w procesie 32-bitowego hosta. Należy pamiętać, że jeśli używasz wielu partycji zapytań, 64-bitowego hosta przetwarzania jest zalecane w celu zwiększenia wydajności.
* Zwiększono wydajność w scenariuszach obejmujących zapytania z dużą liczbą wartości klucza partycji w wyrażeniu w.
* Wypełnione różne statystyki limitu przydziału zasobu w ResourceResponse dla kolekcji dokumentów żądań odczytu, gdy ustawiono opcję żądania PopulateQuotaInfo.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* Poprawka mniejszy przyrost wydajności dla interfejsu API CreateDocumentCollectionIfNotExistsAsync wprowadzona w 1.11.0.
* Napraw wydajności w zestawie SDK dla scenariuszach, które wymagają wysokiego stopnia współbieżnych żądań.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Obsługa nowe klasy i metody służące do przetwarzania [zestawienia zmian](change-feed.md) dokumentów w kolekcji.
* Obsługa zapytań między partycjami kontynuacji i kilka ulepszeń wydajności zapytań między partycjami.
* Dodawanie metody CreateDatabaseIfNotExistsAsync i CreateDocumentCollectionIfNotExistsAsync.
* LINQ Obsługa funkcji systemu: IsDefined, IsNull i IsPrimitive.
* Poprawka automatyczne binplacing Microsoft.Azure.Documents.ServiceInterop.dll i DocumentDB.Spatial.Sql.dll zestawów, aby otworzyć folder bin aplikacji w przypadku korzystania z pakietu Nuget z projektów, które mają narzędzi pliku project.json.
* Obsługa emitowania śladów funkcji ETW po stronie klienta, które mogą być przydatne podczas debugowania scenariuszy.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Dodano połączenie bezpośrednie wsparcie dla kolekcji podzielonych na partycje.
* Zwiększona wydajność poziom spójności powiązana nieaktualność.
* Dodano wielokąta i typy danych LineString, podczas określania zasad dla zapytań przestrzennych grodzenia indeksowania kolekcji.
* Dodano obsługę LINQ StringEnumConverter, IsoDateTimeConverter i UnixDateTimeConverter podczas tłumaczenia predykatów.
* Różne poprawki błędów zestawu SDK.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Rozwiązano problem powodujący NotFoundException następujące: Przeczytaj sesji nie jest dostępna dla tokenu sesji danych wejściowych. W niektórych przypadkach wystąpił ten wyjątek, podczas wykonywania zapytania w regionie odczytu, geograficznie rozproszona konta.
* Widoczne właściwości ResponseStream w klasie ResourceResponse umożliwia bezpośredni dostęp do źródłowego strumienia z odpowiedzi.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Zmodyfikowane ResourceResponse, FeedResponse, StoredProcedureResponse i MediaResponse klasy do zaimplementowania odpowiedniego interfejsu publicznego, tak aby ich postaci makiet dla testu opartego na wdrożenia (TDD).
* Rozwiązano problem powodujący nagłówek klucza partycji źle sformułowane, korzystając z niestandardowych obiektów JsonSerializerSettings dla serializacji danych.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Rozwiązano problem powodujący długotrwałe zapytania nie powiedzie się z powodu błędu: Token autoryzacji jest nieprawidłowy w bieżącym momencie.
* Rozwiązano problem, który usunąć oryginalny SqlParameterCollection z wielu partycji top/według zapytania.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Dodano obsługę równoległe zapytania dla kolekcji podzielonych na partycje.
* Dodano obsługę dla wielu zapytań ORDER BY i klauzuli TOP partycji dla kolekcji podzielonych na partycje.
* Naprawiono Brak odwołania do DocumentDB.Spatial.Sql.dll i Microsoft.Azure.Documents.ServiceInterop.dll, które są wymagane, gdy odwołanie do projektu usługi Azure Cosmos DB za pomocą odwołania do pakietu Nuget usługi Azure Cosmos DB.
* Naprawiono możliwość korzystania z parametrami różnego typu, korzystając z funkcji zdefiniowanych przez użytkownika w składniku LINQ. 
* Usunięto usterkę dla globalnie replikowanego konta, w której zostały kierowanie wywołań Upsert odczytać lokalizacje zamiast szukania w lokalizacjach zapisu.
* Dodano metody interfejsu IDocumentClient, których brakowało: 
  * Metoda UpsertAttachmentAsync, która przyjmuje mediaStream i opcje jako parametrów
  * Metoda CreateAttachmentAsync, który przyjmuje opcje jako parametr
  * Metoda CreateOfferQuery, która przyjmuje querySpec jako parametr.
* Niezapieczętowane klasy publiczne, które są dostępne w interfejsie IDocumentClient.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Dodano obsługę multiregionalne konta baz danych.
* Dodano obsługę ponowienie dotyczące żądania ograniczone.  Użytkownik może dostosować liczbę ponownych prób oraz maksymalny czas, przez skonfigurowanie właściwości ConnectionPolicy.RetryOptions.
* Dodano nowy interfejs IDocumentClient, który definiuje podpisy wszystkich DocumentClient właściwości i metod.  W ramach tej zmiany należy również zmienić metody rozszerzenia, które utworzyć IQueryable i IOrderedQueryable do metod na samej klasy DocumentClient.
* Opcja konfiguracji dodano można ustawić ServicePoint.ConnectionLimit dla danego punktu końcowego usługi Azure Cosmos DB identyfikatora Uri.  Aby zmienić wartość domyślna jest równa 50, należy użyć ConnectionPolicy.MaxConnectionLimit.
* Przestarzałe IPartitionResolver i jego wykonania.  Obsługa IPartitionResolver jest obecnie przestarzała. Zaleca się używania podzielona na partycje kolekcji dla nowszej magazynu i przepływności.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Dodano przeciążenia do identyfikatora Uri na podstawie metody ExecuteStoredProcedureAsync, która przyjmuje RequestOptions jako parametr.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Dodano czas live (TTL) — Pomoc techniczna dla dokumentów.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Usunięto usterkę w pakietu Nuget zestawu .NET SDK dla pakowanie w ramach rozwiązania usługi w chmurze Azure.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Zaimplementowane [podzielona na partycje kolekcje](partition-data.md) i [poziomów wydajności zdefiniowanych przez użytkownika](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Naprawiono]**  Zgłasza punktu końcowego zapytań usługi Azure Cosmos DB: 'System.Net.Http.HttpRequestException: Wystąpił błąd podczas kopiowania zawartości do strumienia ".

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* LINQ rozszerzonej obsługi, w tym nowe operatory dla wyrażeń stronicowania, warunkowe i zakresu porównania.
  * Wykonaj operatora, aby włączyć zachowanie wybierz TOP w technologii LINQ
  * Operator CompareTo, aby umożliwić porównań ciągów, w zakresie
  * Warunkowy (?) i coalesce operatorów (?)
* **[Naprawiono]**  Trwa wyjątku ArgumentOutOfRangeException podczas łączenia modelu projekcji o miejsce w w zapytaniu programu LINQ. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Naprawiono]**  Jeśli wybierz opcję nie jest ostatniego wyrażenia dostawcy LINQ zakłada, że nie projekcji, a następnie wybierz utworzony * niepoprawnie.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Zaimplementowano Upsert, dodano UpsertXXXAsync metody
* Ulepszenia wydajności dla wszystkich żądań
* Obsługa dostawców LINQ dla warunkowego, coalesce oraz metody CompareTo dla ciągów
* **[Naprawiono]**  Dostawcy LINQ--> zawiera implementacji metody na liście, aby wygenerować ten sam program SQL na IEnumerable i tablicy
* **[Naprawiono]**  BackoffRetryUtility używa tego samego HttpRequestMessage ponownie zamiast tworzenia nowej po ponowieniu
* **[Nieaktualne]**  UriFactory.CreateCollection--> teraz należy używać UriFactory.CreateDocumentCollection

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Naprawiono]**  Lokalizacji problemy, korzystając z informacji kultury inne niż en, takich jak nl-NL, itp. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Dodano, routing oparty na identyfikatorze
  * Nowy Pomocnik UriFactory na potrzeby tworzenia łączy na podstawie Identyfikatora zasobu
  * Nowe przeciążenia na DocumentClient, aby móc w identyfikatorze URI
* Dodano IsValid() i IsValidDetailed() w składniku LINQ dla danych geoprzestrzennych
* Obsługa dostawcy LINQ, rozszerzone:
  * **Matematyczne** -Abs, funkcje Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log10, Sin, Pow, logowania, Log, Round, Sqrt, Tan, Truncate
  * **Ciąg** -Concat, zawiera, EndsWith, IndexOf, Count, ToLower, trimstart —, Zastąp, odwrócić, trimend —, StartsWith, SubString, ToUpper
  * **Tablica** -Concat, zawiera, liczba
  * **W** — operator

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Dodano obsługę modyfikowanie zasad indeksowania.
  * Nowa metoda ReplaceDocumentCollectionAsync w DocumentClient
  * Nowe właściwości IndexTransformationProgress w ResourceResponse<T> dla śledzenia postępu procent zmian zasad indeksu
  * DocumentCollection.IndexingPolicy teraz jest modyfikowalna
* Dodano obsługę indeksowania przestrzennego i zapytania.
  * Nowej Microsoft.Azure.Documents.Spatial przestrzeni nazw w celu serializacji/deserializacji typów przestrzennych, takich jak punktów i wielokątów
  * Nowa klasa SpatialIndex indeksowania GeoJSON — dane przechowywane w usłudze Cosmos DB
* **[Naprawiono]**  Zapytania SQL nieprawidłowy, generowany na podstawie wyrażenia LINQ [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Dodaje zależność na Newtonsoft.Json v5.0.7.
* Wprowadzone zmiany do obsługi Order By:
  
  * Obsługa dostawcy LINQ OrderBy() lub OrderByDescending()
  * IndexingPolicy do obsługi klauzuli Order By 
    
    **Możliwych przełomowych zmian** 
    
    Jeśli masz istniejący kod w tej kolekcji przepisów za pomocą niestandardowych zasad indeksowania, istniejący kod musi zostać zaktualizowany do obsługi nowej klasy IndexingPolicy. Jeśli nie masz żadnych niestandardowych zasad indeksowania, następnie ta zmiana nie dotyczy.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Dodano obsługę partycjonowania danych przy użyciu nowych klas HashPartitionResolver i RangePartitionResolver i IPartitionResolver.
* Dodano DataContract serializacji.
* Dodano GUID Obsługa dostawcy LINQ.
* Dodano funkcji zdefiniowanej przez użytkownika obsługi w technologii LINQ.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* ZESTAW SDK W WERSJI OGÓLNIE DOSTĘPNEJ

## <a name="release--retirement-dates"></a>Daty wydania i wycofania
Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** ewentualnej wycofanie zestawu SDK w celu złagodzenia przejścia do nowszych/obsługiwanych wersji.

Nowe funkcje i funkcjonalność i optymalizacje są dodawane tylko do bieżącego zestawu SDK, w związku z tym zalecane jest, zawsze uaktualnienie do najnowszej wersji zestawu SDK tak szybko, jak to możliwe. 

Wszystkie żądania do usługi Azure Cosmos DB przy użyciu wycofane zestawu SDK są odrzucane przez usługę.

<br/>

| Version | Data wydania | Data wygaśnięcia |
| --- | --- | --- |
| [2.4.0](#2.4.0) |05 maja 2019 r. |--- |
| [2.3.0](#2.3.0) |04 kwietnia 2019 r. |--- |
| [2.2.3](#2.2.3) |11 lutego 2019 r. |--- |
| [2.2.2](#2.2.2) |06 lutego 2019 r. |--- |
| [2.2.1](#2.2.1) |24 grudnia 2018 r. |--- |
| [2.2.0](#2.2.0) |07 grudnia 2018 r. |--- |
| [2.1.3](#2.1.3) |15 października 2018 r. |--- |
| [2.1.2](#2.1.2) |04 października 2018 r. |--- |
| [2.1.1](#2.1.1) |27 września 2018 r. |--- |
| [2.1.0](#2.1.0) |21 września 2018 r. |--- |
| [2.0.0](#2.0.0) |07 września 2018 r. |--- |
| [1.22.0](#1.22.0) |19 kwietnia 2018 r. |--- |
| [1.21.1](#1.20.1) |09 marca 2018 r. |--- |
| [1.20.2](#1.20.1) |21 lutego 2018 r. |--- |
| [1.20.1](#1.20.1) |05 lutego 2018 r. |--- |
| [1.19.1](#1.19.1) |16 listopada 2017 r. |--- |
| [1.19.0](#1.19.0) |10 listopada 2017 r. |--- |
| [1.18.1](#1.18.1) |07 listopada 2017 r. |--- |
| [1.18.0](#1.18.0) |17 października 2017 r. |--- |
| [1.17.0](#1.17.0) |10 sierpnia 2017 r. |--- |
| [1.16.1](#1.16.1) |07 sierpnia 2017 r. |--- |
| [1.16.0](#1.16.0) |02 sierpnia 2017 r. |--- |
| [1.15.0](#1.15.0) |30 czerwca 2017 r. |--- |
| [1.14.1](#1.14.1) |23 maja 2017 r. |--- |
| [1.14.0](#1.14.0) |10 maja 2017 |--- |
| [1.13.4](#1.13.4) |09 maja 2017 r. |--- |
| [1.13.3](#1.13.3) |06 maja 2017 r. |--- |
| [1.13.2](#1.13.2) |19 kwietnia 2017 r. |--- |
| [1.13.1](#1.13.1) |29 marca 2017 r. |--- |
| [1.13.0](#1.13.0) |24 marca 2017 r. |--- |
| [1.12.2](#1.12.2) |20 marca 2017 r. |--- |
| [1.12.1](#1.12.1) |14 marca 2017 r. |--- |
| [1.12.0](#1.12.0) |15 lutego 2017 r. |--- |
| [1.11.4](#1.11.4) |06 lutego 2017 r. |--- |
| [1.11.3](#1.11.3) |26 stycznia 2017 r. |--- |
| [1.11.1](#1.11.1) |21 grudnia 2016 r. |--- |
| [1.11.0](#1.11.0) |08 grudnia 2016 r. |--- |
| [1.10.0](#1.10.0) |27 września 2016 r. |--- |
| [1.9.5](#1.9.5) |01 września 2016 r. |--- |
| [1.9.4](#1.9.4) |24 sierpnia 2016 r. |--- |
| [1.9.3](#1.9.3) |15 sierpnia 2016 r. |--- |
| [1.9.2](#1.9.2) |23 lipca 2016 r. |--- |
| [1.8.0](#1.8.0) |14 czerwca 2016 r. |--- |
| [1.7.1](#1.7.1) |06 maja 2016 r. |--- |
| [1.7.0](#1.7.0) |26 kwietnia 2016 r. |--- |
| [1.6.3](#1.6.3) |08 kwietnia 2016 r. |--- |
| [1.6.2](#1.6.2) |29 marca 2016 r. |--- |
| [1.5.3](#1.5.3) |19 lutego 2016 r. |--- |
| [1.5.2](#1.5.2) |14 grudnia 2015 r. |--- |
| [1.5.1](#1.5.1) |23 listopada 2015 r. |--- |
| [1.5.0](#1.5.0) |05 października 2015 r. |--- |
| [1.4.1](#1.4.1) |25 sierpnia 2015 r. |--- |
| [1.4.0](#1.4.0) |13 sierpnia 2015 r. |--- |
| [1.3.0](#1.3.0) |05 sierpnia 2015 r. |--- |
| [1.2.0](#1.2.0) |06 lipca 2015 r. |--- |
| [1.1.0](#1.1.0) |30 kwietnia 2015 r. |--- |
| [1.0.0](#1.0.0) |08 kwietnia 2015 r. |--- |


## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stronę usługi. 

