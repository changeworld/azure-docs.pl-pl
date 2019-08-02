---
title: Usługa Azure Cosmos DB Interfejs API programu SQL .NET, zasoby & zestawu SDK
description: Dowiedz się więcej o interfejsie API programu SQL .NET i zestawie SDK, w tym o datach wydania, datach wycofania i zmianach między poszczególnymi wersjami Azure Cosmos DB .NET SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: sngun
ms.openlocfilehash: 4380bf81d05aa5247b57605b2aa53d24a73a0f68
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638577"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET SDK dla interfejsu API SQL: Informacje dotyczące pobierania i zwalniania
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET](sql-api-sdk-dotnet-standard.md)
> * [Kanał informacyjny zmian .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
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
|**Zestaw SDK do pobrania**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**Dokumentacja interfejsu API**|[Dokumentacja interfejsu API platformy .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Przykłady**|[Przykłady kodu platformy .NET](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples)|
|**Wprowadzenie**|[Wprowadzenie do zestawu SDK platformy Azure Cosmos DB .NET](sql-api-get-started.md)|
|**Samouczek dotyczący aplikacji sieci Web**|[Opracowywanie aplikacji sieci Web za pomocą usługi Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Bieżącej struktury obsługiwanej**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

## <a name="release-notes"></a>Informacje o wersji

> [!NOTE]
> Jeśli używasz .NET Framework, zapoznaj się z najnowszą wersją 3. x [zestawu .NET SDK](sql-api-sdk-dotnet-standard.md), która jest przeznaczona dla .NET Standard. 

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

* Ulepszenia śledzenia diagnostyki

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
* Naprawiono usterkę, w której atrybut JsonProperty w klauzuli select dla zapytań LINQ nie został uznany.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* Rozwiązano problem, który został trafiony w pewnych warunkach wyścigu, co powoduje sporadyczne "Microsoft. Azure. Documents. NotFoundException: Sesja odczytu nie jest dostępna w przypadku błędów tokenu sesji wejściowej w przypadku używania poziomu spójności sesji.

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

* Wewnętrzne zmiany dla zestawów zaprzyjaźnionych firmy Microsoft.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* Dodano kilka ulepszenia i poprawki niezawodności.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* Dodano obsługę PartitionKeyRangeId jako FeedOption do określania zakresu wyników zapytania do określonej partycji klucza zakresu wartości. 
* Dodano obsługę StartTime jako ChangeFeedOption do rozpoczęcia wyszukiwania zmiany po upływie tego okresu.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Rozwiązano problem w klasie JsonSerializable, która może spowodować wyjątek przepełnienia stosu.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   Rozwiązano problem, który wymaga ponownego skompilowania aplikacji z powodu wprowadzenia JsonSerializerSettings jako parametru opcjonalnego w konstruktorze DocumentClient.
* Oznaczono Konstruktor DocumentClient przestarzały, który wymaga JsonSerializerSettings jako ostatni parametr, aby zezwolić na domyślne wartości parametrów ConnectionPolicy i ConsistencyLevel podczas przekazywania w parametrze JsonSerializerSettings.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Dodano obsługę określania niestandardowych JsonSerializerSettings podczas tworzenia wystąpienia [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Rozwiązano problem dotyczący maszyn x64, które nie obsługują instrukcji SSE4 i zgłaszają SEHException — podczas uruchamiania Azure Cosmos DB zapytań SQL.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Dodano obsługę nowego poziomu spójności, nazywana ConsistentPrefix.
*   Dodano obsługę zapytań metryki dla pojedynczych partycji.
*   Dodano obsługę ograniczający rozmiar token kontynuacji dla zapytań.
*   Dodano obsługę bardziej szczegółowe śledzenie niepomyślnych żądań.
*   Wprowadzone ulepszenia wydajności w zestawie SDK.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Działa tak samo jak 1.13.3. Wprowadzono pewne zmiany wewnętrzne.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Działa tak samo jak 1.13.2. Wprowadzono pewne zmiany wewnętrzne.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Rozwiązano problem, który jest ignorowana wartość PartitionKey udostępniane w FeedOptions dla zapytania zagregowane.
* Rozwiązano problem z przezroczystym obsługi zarządzania partycji podczas lotu połowie między partycjami Order By wykonywania zapytań.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Rozwiązano problem, co spowodowało zakleszczenia w niektórych asynchronicznych interfejsów API, gdy jest używana w kontekście platformy ASP.NET.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Rozwiązuje się zestaw SDK bardziej odporne na automatyczną pracę awaryjną pod pewnymi warunkami.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Poprawka dotycząca problemu, który sporadycznie powoduje wystąpienie elementu WebException: Nie można rozpoznać nazwy zdalnej.
* Dodano obsługę bezpośrednio odczytu dokumentu wpisane, dodając nowe przeciążenia do interfejsu API ReadDocumentAsync.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* Dodano obsługę LINQ dla zapytań agregacji (COUNT, MIN, MAX, Suma i średnia).
* Poprawka problem przeciek pamięci dla obiektu ConnectionPolicy spowodowane użyciem programu obsługi zdarzeń.
* Poprawkę dla problemu polegającego UpsertAttachmentAsync nie była praca stosowania element ETag.
* Rozwiązywanie problemu polegającego wielu partycji klauzuli order by zapytania kontynuacji została nie działa podczas sortowania na pola ciągu.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, Suma i średnia). Zobacz [Obsługa agregacji](sql-query-aggregates.md).
* Obniżona minimalna przepływność na kolekcji podzielonych na partycje z 10,100 jednostek RU/s 2500 jednostek RU/s.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Rozwiązanie problemu polegającego na tym, że niektóre zapytania między partycjami kończą się niepowodzeniem w procesie hosta 32-bitowego.
* Rozwiązano problem związany z tym, że kontener sesji nie został zaktualizowany przy użyciu tokenu dla żądań zakończonych niepowodzeniem w trybie bramy.
* Rozwiązanie problemu polegającego na tym, że zapytanie z wywołaniami UDF w projekcji zostało zakończone niepowodzeniem w niektórych przypadkach.
* Poprawki wydajności po stronie klienta zwiększające przepływność odczytu i zapisu żądań.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Rozwiązano problem związany z tym, że kontener sesji nie został zaktualizowany przy użyciu tokenu dla żądań zakończonych niepowodzeniem.
* Dodano obsługę zestawu SDK do pracy w procesie hosta 32-bitowego. Należy pamiętać, że jeśli używasz zapytań między partycjami, 64-bitowe przetwarzanie hosta jest zalecane w celu zwiększenia wydajności.
* Zwiększona wydajność dla scenariuszy obejmujących zapytania z dużą liczbą wartości kluczy partycji w wyrażeniu IN.
* Wypełniono różne statystyki przydziału zasobów w ResourceResponse dla żądań odczytu kolekcji dokumentów, gdy opcja żądania PopulateQuotaInfo jest ustawiona.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* Dodatkowa poprawka wydajności dla interfejsu API metody createdocumentcollectionifnotexistsasync wprowadzona w 1.11.0.
* Poprawka wydajności w zestawie SDK dla scenariuszy obejmujących wysoki stopień współbieżnych żądań.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Obsługa nowych klas i metod przetwarzania [źródła zmian](change-feed.md) dokumentów w ramach kolekcji.
* Obsługa kontynuacji zapytań między partycjami i niektórych ulepszeń wydajności dla zapytań między partycjami.
* Dodanie metod CreateDatabaseIfNotExistsAsync i metody createdocumentcollectionifnotexistsasync.
* Obsługa LINQ dla funkcji systemowych: IsDefined, IsNull i isprymityw.
* Poprawka do automatycznej binplacing zestawów Microsoft. Azure. Documents. serviceinterop. dll i DocumentDB. przestrzenny. SQL. dll do folderu bin aplikacji podczas korzystania z pakietu NuGet z projektami, które mają narzędzia Project. JSON.
* Obsługa emitowania śladów ETW po stronie klienta, które mogą być przydatne w scenariuszach debugowania.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Dodano obsługę łączności bezpośredniej dla kolekcji partycjonowanych.
* Ulepszona wydajność dla ograniczonego poziomu spójności o nieodświeżonej.
* Dodano wielokąta i typy danych LineString, podczas określania zasad dla zapytań przestrzennych grodzenia indeksowania kolekcji.
* Dodano obsługę LINQ dla StringEnumConverter, IsoDateTimeConverter i UnixDateTimeConverter podczas tłumaczenia predykatów.
* Różne poprawki błędów zestawu SDK.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Rozwiązano problem, który spowodował następujące NotFoundException: Sesja odczytu nie jest dostępna dla tokenu sesji wejściowej. Ten wyjątek wystąpił w niektórych przypadkach podczas wykonywania zapytania dotyczącego regionu odczytu konta geograficznego.
* Wyświetlona Właściwość ResponseStream w klasie ResourceResponse, która umożliwia bezpośredni dostęp do podstawowego strumienia z odpowiedzi.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Zmodyfikowano klasy ResourceResponse, FeedResponse, StoredProcedureResponse i MediaResponse w celu zaimplementowania odpowiedniego interfejsu publicznego, aby można było je zasymulować dla wdrożenia sterowanego testem (TDD).
* Rozwiązano problem, który spowodował nieprawidłowy nagłówek klucza partycji podczas używania niestandardowego obiektu JsonSerializerSettings do serializowania danych.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Rozwiązano problem, który spowodował długotrwałe wykonywanie zapytań zakończonych niepowodzeniem z powodu błędu: Token autoryzacji jest obecnie nieprawidłowy.
* Rozwiązano problem polegający na tym, że usunięto oryginalny element SqlParameterCollection z zapytań o klauzuli TOP/order-by.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Dodano obsługę zapytań równoległych dla kolekcji partycjonowanych.
* Dodano obsługę PORZĄDKOWANIA między partycjami według i NAJPOPULARNIEJSZych zapytań dla kolekcji partycjonowanych.
* Rozwiązano brakujące odwołania do DocumentDB. przestrzenny. SQL. dll i Microsoft. Azure. Documents. serviceinterop. dll, które są wymagane podczas odwoływania się do projektu Azure Cosmos DB z odwołaniem do pakietu NuGet Azure Cosmos DB.
* Naprawiono możliwość używania parametrów różnych typów podczas korzystania z funkcji zdefiniowanych przez użytkownika w LINQ. 
* Rozwiązano błąd dla kont replikowanych globalnie, gdzie wywołania upsert były kierowane do lokalizacji odczytu, a nie lokalizacji zapisu.
* Dodano metody do nieobecnego interfejsu IDocumentClient: 
  * Metoda UpsertAttachmentAsync, która pobiera mediaStream i opcje jako parametry
  * Metoda CreateAttachmentAsync, która pobiera opcje jako parametr
  * Metoda CreateOfferQuery, która pobiera querySpec jako parametr.
* Niezapieczętowane klasy publiczne, które są widoczne w interfejsie IDocumentClient.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Dodano obsługę multiregionalne konta baz danych.
* Dodano obsługę ponawiania prób w przypadku żądań z ograniczeniami.  Użytkownik może dostosować liczbę ponownych prób i maksymalny czas oczekiwania przez skonfigurowanie właściwości ConnectionPolicy. RetryOptions.
* Dodano nowy interfejs IDocumentClient, który definiuje sygnatury wszystkich właściwości DocumentClient i metod.  W ramach tej zmiany również zmieniono metody rozszerzające, które tworzą interfejs IQueryable i IOrderedQueryable, na metody klasy DocumentClient.
* Dodano opcję konfiguracji w celu ustawienia ServicePoint. ConnectionLimit dla danego identyfikatora URI punktu końcowego Azure Cosmos DB.  Użyj ConnectionPolicy. MaxConnectionLimit, aby zmienić wartość domyślną, która jest ustawiona na 50.
* Przestarzałe IPartitionResolver i jej implementacja.  Obsługa usługi IPartitionResolver jest obecnie przestarzała. Zaleca się używanie partycjonowanych kolekcji w celu zapewnienia większego magazynu i przepływności.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Dodano Przeciążenie metody ExecuteStoredProcedureAsync opartej na identyfikatorze URI, która pobiera RequestOptions jako parametr.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Dodano czas live (TTL) — Pomoc techniczna dla dokumentów.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Rozwiązano usterkę w pakiecie NuGet SDK platformy .NET do pakowania jej w ramach rozwiązania usługi w chmurze platformy Azure.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Zaimplementowane [podzielona na partycje kolekcje](partition-data.md) i [poziomów wydajności zdefiniowanych przez użytkownika](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Stałe]** Wykonywanie zapytania dotyczącego zgłaszania punktów końcowych Azure Cosmos DB: 'System.Net.Http.HttpRequestException: Wystąpił błąd podczas kopiowania zawartości do strumienia ".

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* Rozszerzona obsługa LINQ obejmująca nowe operatory stronicowania, wyrażeń warunkowych i porównywania zakresu.
  * Przejmij operator, aby włączyć wybór najlepszych zachowań w LINQ
  * Operator CompareTo do włączania porównań zakresu ciągów
  * Warunkowe (?) i operatory łączenia (??)
* **[Stałe]** Wyjątku ArgumentOutOfRangeException podczas łączenia projekcji modelu z elementem WHERE w zapytaniu LINQ. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Stałe]** Jeśli opcja Select nie jest ostatnim wyrażeniem, przyjęto, że dostawca LINQ nie przetworzył projekcji i wygenerowało pozycję * nieprawidłowo.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Zaimplementowane Upsert, dodane metody UpsertXXXAsync
* Ulepszenia wydajności dla wszystkich żądań
* Obsługa dostawcy LINQ dla metod warunkowych, łączenia i CompareTo dla ciągów
* **[Stałe]** Dostawca LINQ — > implementacja zawiera metodę na liście w celu wygenerowania tego samego języka SQL jako interfejsu IEnumerable i Array
* **[Stałe]** BackoffRetryUtility ponownie używa tego samego HttpRequestMessageu zamiast tworzenia nowego przy ponowieniu próby
* **[Przestarzałe]** UriFactory. CreateCollection--> powinien teraz używać UriFactory. dbdocumentcollection

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Stałe]** Problemy z lokalizacją w przypadku korzystania z informacji o niezmienionej kulturze, takich jak NL-NL itp. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Dodano routing oparty na IDENTYFIKATORach
  * Nowy pomocnik UriFactory, który pomaga w tworzeniu linków zasobów opartych na IDENTYFIKATORach
  * Nowe przeciążenia na DocumentClient w celu przejęcia identyfikatora URI
* Dodano IsValid () i IsValidDetailed () w LINQ for-geograficzne
* Ulepszona obsługa dostawcy LINQ:
  * **Math** -ABS, acos, asin, atan, sufit, cos, exp, Floor, log, log10 —, pow, Round, Sign, Sin, sqrt, Tan, Truncate
  * **Ciąg** -concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, podciąg, ToUpper
  * **Array** -concat, Contains, Count
  * **In** — operator

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Dodano obsługę modyfikowania zasad indeksowania.
  * Nowa metoda ReplaceDocumentCollectionAsync w DocumentClient
  * Nowa właściwość IndexTransformationProgress w ResourceResponse\<T > na potrzeby śledzenia procentowej postępu zmian zasad indeksu
  * Element documentcollection. IndexingPolicy jest teraz modyfikowalny
* Dodano obsługę indeksowania przestrzennego i zapytania.
  * Nowa przestrzeń nazw Microsoft. Azure. Documents. przestrzenny do serializacji/deserializacji typów przestrzennych, takich jak punkt i Wielokąt
  * Nowa Klasa SpatialIndex do indeksowania danych GEOJSON przechowywanych w Cosmos DB
* **[Stałe]** Wyrażenie LINQ wygenerowało niepoprawną kwerendę SQL [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Dodano zależność w Newtonsoft. JSON v 5.0.7.
* Wprowadzono zmiany w kolejności obsługi według:
  
  * Obsługa dostawcy LINQ dla elementu OrderBy () lub OrderByDescending ()
  * IndexingPolicy do obsługi kolejności według 
    
    **Możliwa przerwana zmiana** 
    
    Jeśli masz istniejący kod, który inicjuje obsługę kolekcji z niestandardowymi zasadami indeksowania, istniejący kod musi zostać zaktualizowany do obsługi nowej klasy IndexingPolicy. Jeśli nie masz niestandardowych zasad indeksowania, ta zmiana nie ma wpływu na użytkownika.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Dodano obsługę partycjonowania danych przy użyciu nowych klas HashPartitionResolver i RangePartitionResolver oraz IPartitionResolver.
* Dodano serializacji obiektu DataContract.
* Dodano obsługę identyfikatora GUID w dostawcy LINQ.
* Dodano obsługę systemu UDF w LINQ.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* ZESTAW SDK W WERSJI OGÓLNIE DOSTĘPNEJ

## <a name="release--retirement-dates"></a>Data wycofania &
Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** przed WYCOFANIEM zestawu SDK w celu zapewnienia sprawnego przejścia do nowszej/obsługiwanej wersji.

Nowe funkcje i funkcje i optymalizacje są dodawane tylko do bieżącego zestawu SDK, dlatego zaleca się, aby zawsze uaktualnić do najnowszej wersji zestawu SDK tak szybko, jak to możliwe. 

Wszystkie żądania do Azure Cosmos DB za pomocą wycofanego zestawu SDK są odrzucane przez usługę.

<br/>

| Version | Data wydania | Data wygaśnięcia |
| --- | --- | --- |
| [2.5.1](#2.5.1) |02 lipca 2019 |--- |
| [2.4.1](#2.4.1) |20 czerwca 2019 |--- |
| [2.4.0](#2.4.0) |05 maja 2019 |--- |
| [2.3.0](#2.3.0) |04, 2019 |--- |
| [2.2.3](#2.2.3) |11 lutego 2019 |--- |
| [2.2.2](#2.2.2) |06, 2019 |--- |
| [2.2.1](#2.2.1) |24 grudnia 2018 |--- |
| [2.2.0](#2.2.0) |07, 2018 |--- |
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
| [1.18.1](#1.18.1) |07, 2017 |--- |
| [1.18.0](#1.18.0) |17 października 2017 r. |--- |
| [1.17.0](#1.17.0) |10 sierpnia 2017 r. |--- |
| [1.16.1](#1.16.1) |07 sierpnia 2017 r. |--- |
| [1.16.0](#1.16.0) |02 sierpnia 2017 r. |--- |
| [1.15.0](#1.15.0) |30 czerwca 2017 |--- |
| [1.14.1](#1.14.1) |23 maja 2017 r. |--- |
| [1.14.0](#1.14.0) |10 maja 2017 |--- |
| [1.13.4](#1.13.4) |09 maja 2017 |--- |
| [1.13.3](#1.13.3) |06, 2017 |--- |
| [1.13.2](#1.13.2) |19 kwietnia 2017 r. |--- |
| [1.13.1](#1.13.1) |29 marca 2017 r. |--- |
| [1.13.0](#1.13.0) |24 marca 2017 |--- |
| [1.12.2](#1.12.2) |20 marca 2017 r. |--- |
| [1.12.1](#1.12.1) |14 marca 2017 r. |--- |
| [1.12.0](#1.12.0) |15 lutego 2017 |--- |
| [1.11.4](#1.11.4) |06, 2017 |--- |
| [1.11.3](#1.11.3) |26 stycznia 2017 |--- |
| [1.11.1](#1.11.1) |21 grudnia 2016 r. |--- |
| [1.11.0](#1.11.0) |08, 2016 |--- |
| [1.10.0](#1.10.0) |27 września, 2016 |--- |
| [1.9.5](#1.9.5) |01, 2016 |--- |
| [1.9.4](#1.9.4) |24 sierpnia 2016 |--- |
| [1.9.3](#1.9.3) |15 sierpnia 2016 |--- |
| [1.9.2](#1.9.2) |23 lipca 2016 |--- |
| [1.8.0](#1.8.0) |14 czerwca 2016 r. |--- |
| [1.7.1](#1.7.1) |06, 2016 |--- |
| [1.7.0](#1.7.0) |26 kwietnia 2016 |--- |
| [1.6.3](#1.6.3) |08 kwietnia, 2016 |--- |
| [1.6.2](#1.6.2) |29 marca 2016 r. |--- |
| [1.5.3](#1.5.3) |19 lutego 2016 |--- |
| [1.5.2](#1.5.2) |14 grudnia 2015 |--- |
| [1.5.1](#1.5.1) |23 listopada, 2015 |--- |
| [1.5.0](#1.5.0) |05 października 2015 r. |--- |
| [1.4.1](#1.4.1) |25 sierpnia 2015 |--- |
| [1.4.0](#1.4.0) |13 sierpnia 2015 |--- |
| [1.3.0](#1.3.0) |05 sierpnia 2015 r. |--- |
| [1.2.0](#1.2.0) |06, 2015 |--- |
| [1.1.0](#1.1.0) |30 kwietnia 2015 |--- |
| [1.0.0](#1.0.0) |08 kwietnia, 2015 |--- |


## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stronę usługi. 

