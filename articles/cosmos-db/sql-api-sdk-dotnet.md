---
title: 'Azure Cosmos DB: interfejs API programu SQL .NET, zasoby & zestawu SDK'
description: Dowiedz się więcej o interfejsie API programu SQL .NET i zestawie SDK, w tym o datach wydania, datach wycofania i zmianach między poszczególnymi wersjami Azure Cosmos DB .NET SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: sngun
ms.openlocfilehash: f8c38c46bd60834c166721f62088d8edb2c722a9
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949582"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET SDK dla interfejsu API SQL: pobieranie i informacje o wersji
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET](sql-api-sdk-dotnet-standard.md)
> * [Źródło zmian platformy .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
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
|**Pobieranie zestawu SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**Dokumentacja interfejsu API**|[Dokumentacja interfejsu API platformy .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Przykłady**|[Przykłady kodu platformy .NET](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples)|
|**Wprowadzenie**|[Wprowadzenie do zestawu SDK platformy Azure Cosmos DB .NET](sql-api-get-started.md)|
|**Samouczek aplikacji sieci Web**|[Tworzenie aplikacji sieci Web za pomocą Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Bieżąca obsługiwana platforma**|[Microsoft .NET Framework 4,5](https://www.microsoft.com/download/details.aspx?id=30653)|

## <a name="release-notes"></a>Uwagi do wersji

> [!NOTE]
> Jeśli używasz .NET Framework, zapoznaj się z najnowszą wersją 3. x [zestawu .NET SDK](sql-api-sdk-dotnet-standard.md), która jest przeznaczona dla .NET Standard. 

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

* Ulepszenia śledzenia diagnostyki

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

### <a name="a-name12201220"></a>@no__t — 01.22.0

* Dodano Właściwość ConsistencyLevel do FeedOptions.
* Dodano JsonSerializerSettings do RequestOptions i FeedOptions.
* Dodano EnableReadRequestsFallback do ConnectionPolicy.

### <a name="a-name12111211"></a>@no__t — 01.21.1

* Stała KeyNotFoundException dla zamówienia między partycjami przez zapytania w przypadku rogów.
* Naprawiono usterkę, w której atrybut JsonProperty w klauzuli select dla zapytań LINQ nie został uznany.

### <a name="a-name12021202"></a>@no__t — 01.20.2

* Rozwiązano problem, który został osiągnięty w pewnych warunkach wyścigu, co powoduje sporadyczne "Microsoft. Azure. Documents. NotFoundException: sesja odczytu nie jest dostępna dla tokenu sesji wejściowej, podczas gdy jest używany poziom spójności sesji.

### <a name="a-name12011201"></a>@no__t — 01.20.1

* Stała regresja, gdzie FeedOptions. MaxItemCount =-1 wywołała element System. arytmetyczny: rozmiar strony jest ujemny.
* Dodano nową funkcję ToString () do QueryMetrics.
* Udostępnione statystyki partycji dotyczące odczytywania kolekcji.
* Dodano Właściwość PartitionKey do ChangeFeedOptions.
* Drobne poprawki błędów.

### <a name="a-name11911191"></a>@no__t — 01.19.1

* Dodaje możliwość określania unikatowych indeksów dla dokumentów przy użyciu właściwości UniqueKeyPolicy w Dokumenciecollection.
* Naprawiono usterkę, w której niestandardowe ustawienia JsonSerializer nie zostały uznane za niektóre zapytania i wykonywanie procedury składowanej.

### <a name="a-name11901190"></a>@no__t — 01.19.0

* Zmiana marki z usługi Azure DocumentDB w celu Azure Cosmos DB w dokumentacji dotyczącej interfejsów API, informacji o metadanych w zestawach i pakiecie NuGet. 
* Ujawnianie informacji diagnostycznych i opóźnienia od odpowiedzi na żądania wysyłane z trybem bezpośredniej łączności. Nazwy właściwości to RequestDiagnosticsString i RequestLatency klasy ResourceResponse.
* Ta wersja zestawu SDK wymaga najnowszej wersji emulatora Azure Cosmos DB dostępnej do pobrania z https://aka.ms/cosmosdb-emulator. 

### <a name="a-name11811181"></a>@no__t — 01.18.1 

* Wewnętrzne zmiany dla zestawów zaprzyjaźnionych firmy Microsoft.

### <a name="a-name11801180"></a>@no__t — 01.18.0 

* Dodano kilka poprawek niezawodności i ulepszeń.

### <a name="a-name11701170"></a>@no__t — 01.17.0 

* Dodano obsługę PartitionKeyRangeId jako FeedOption do określania zakresu wyników zapytania do określonej wartości zakresu kluczy partycji. 
* Dodano obsługę polecenia StartTime jako ChangeFeedOption, aby rozpocząć wyszukiwanie zmian po tym czasie.

### <a name="a-name11611161"></a>@no__t — 01.16.1
* Rozwiązano problem w klasie JsonSerializable, który może spowodować wyjątek przepełnienia stosu.

### <a name="a-name11601160"></a>@no__t — 01.16.0
*   Rozwiązano problem, który wymaga ponownego skompilowania aplikacji z powodu wprowadzenia JsonSerializerSettings jako parametru opcjonalnego w konstruktorze DocumentClient.
* Oznaczono Konstruktor DocumentClient przestarzały, który wymaga JsonSerializerSettings jako ostatni parametr, aby zezwolić na domyślne wartości parametrów ConnectionPolicy i ConsistencyLevel podczas przekazywania w parametrze JsonSerializerSettings.

### <a name="a-name11501150"></a>@no__t — 01.15.0
*   Dodano obsługę określania niestandardowych JsonSerializerSettings podczas tworzenia wystąpienia [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a>@no__t — 01.14.1
*   Rozwiązano problem dotyczący maszyn x64, które nie obsługują instrukcji SSE4 i zgłaszają SEHException — podczas uruchamiania Azure Cosmos DB zapytań SQL.

### <a name="a-name11401140"></a>@no__t — 01.14.0
*   Dodano obsługę nowego poziomu spójności o nazwie ConsistentPrefix.
*   Dodano obsługę metryk kwerend dla poszczególnych partycji.
*   Dodano obsługę ograniczania rozmiaru tokenu kontynuacji dla zapytań.
*   Dodano obsługę bardziej szczegółowych śledzenia żądań zakończonych niepowodzeniem.
*   Wprowadzono pewne ulepszenia wydajności w zestawie SDK.

### <a name="a-name11341134"></a>@no__t — 01.13.4
* Działa tak samo jak 1.13.3. Wprowadzono pewne zmiany wewnętrzne.

### <a name="a-name11331133"></a>@no__t — 01.13.3
* Działa tak samo jak 1.13.2. Wprowadzono pewne zmiany wewnętrzne.

### <a name="a-name11321132"></a>@no__t — 01.13.2
* Rozwiązano problem, który zignorował wartość PartitionKey podaną w FeedOptions dla zapytań agregujących.
* Rozwiązano problem polegający na niewidocznej obsłudze zarządzania partycjami w ramach zamówienia między różnymi partycjami w ramach procesu tworzenia zapytań.

### <a name="a-name11311131"></a>@no__t — 01.13.1
* Rozwiązano problem, który spowodował zakleszczenia w niektórych asynchronicznych interfejsach API, gdy jest używany wewnątrz kontekstu ASP.NET.

### <a name="a-name11301130"></a>@no__t — 01.13.0
* Poprawki zwiększające odporność zestawu SDK na automatyczną pracę awaryjną w pewnych warunkach.

### <a name="a-name11221122"></a>@no__t — 01.12.2
* Poprawka dotycząca problemu, który sporadycznie powoduje wystąpienie elementu WebException: nie można rozpoznać nazwy zdalnej.
* Dodano obsługę bezpośredniego odczytywania wpisanego dokumentu przez dodanie nowych przeciążeń do interfejsu API ReadDocumentAsync.

### <a name="a-name11211121"></a>@no__t — 01.12.1
* Dodano obsługę LINQ dla zapytań agregacji (COUNT, MIN, MAX, SUM i AVG).
* Poprawka dotycząca problemu przecieku pamięci dla obiektu ConnectionPolicy z powodu użycia procedury obsługi zdarzeń.
* Poprawka dotycząca problemu, który UpsertAttachmentAsync nie działa, gdy użyto elementu ETag.
* Poprawka dotycząca problemu w przypadku, gdy sortowanie między różnymi partycjami nie działa podczas sortowania w polu ciągu.

### <a name="a-name11201120"></a>@no__t — 01.12.0
* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, SUM i AVG). Zobacz [Obsługa agregacji](sql-query-aggregates.md).
* Obniżona minimalna przepływność na partycjonowanych kolekcjach z 10 100 RU/s do 2500 RU/s.

### <a name="a-name11141114"></a>@no__t — 01.11.4
* Rozwiązanie problemu polegającego na tym, że niektóre zapytania między partycjami kończą się niepowodzeniem w procesie hosta 32-bitowego.
* Rozwiązano problem związany z tym, że kontener sesji nie został zaktualizowany przy użyciu tokenu dla żądań zakończonych niepowodzeniem w trybie bramy.
* Rozwiązanie problemu polegającego na tym, że zapytanie z wywołaniami UDF w projekcji zostało zakończone niepowodzeniem w niektórych przypadkach.
* Poprawki wydajności po stronie klienta zwiększające przepływność odczytu i zapisu żądań.

### <a name="a-name11131113"></a>@no__t — 01.11.3
* Rozwiązano problem związany z tym, że kontener sesji nie został zaktualizowany przy użyciu tokenu dla żądań zakończonych niepowodzeniem.
* Dodano obsługę zestawu SDK do pracy w procesie hosta 32-bitowego. Należy pamiętać, że jeśli używasz zapytań między partycjami, 64-bitowe przetwarzanie hosta jest zalecane w celu zwiększenia wydajności.
* Zwiększona wydajność dla scenariuszy obejmujących zapytania z dużą liczbą wartości kluczy partycji w wyrażeniu IN.
* Wypełniono różne statystyki przydziału zasobów w ResourceResponse dla żądań odczytu kolekcji dokumentów, gdy opcja żądania PopulateQuotaInfo jest ustawiona.

### <a name="a-name11111111"></a>@no__t — 01.11.1
* Dodatkowa poprawka wydajności dla interfejsu API metody createdocumentcollectionifnotexistsasync wprowadzona w 1.11.0.
* Poprawka wydajności w zestawie SDK dla scenariuszy obejmujących wysoki stopień współbieżnych żądań.

### <a name="a-name11101110"></a>@no__t — 01.11.0
* Obsługa nowych klas i metod przetwarzania [źródła zmian](change-feed.md) dokumentów w ramach kolekcji.
* Obsługa kontynuacji zapytań między partycjami i niektórych ulepszeń wydajności dla zapytań między partycjami.
* Dodanie metod CreateDatabaseIfNotExistsAsync i metody createdocumentcollectionifnotexistsasync.
* Obsługa LINQ dla funkcji systemowych: IsDefined, IsNull i isprymityw.
* Poprawka do automatycznej binplacing zestawów Microsoft. Azure. Documents. serviceinterop. dll i DocumentDB. przestrzenny. SQL. dll do folderu bin aplikacji podczas korzystania z pakietu NuGet z projektami, które mają narzędzia Project. JSON.
* Obsługa emitowania śladów ETW po stronie klienta, które mogą być przydatne w scenariuszach debugowania.

### <a name="a-name11001100"></a>@no__t — 01.10.0
* Dodano obsługę łączności bezpośredniej dla kolekcji partycjonowanych.
* Ulepszona wydajność dla ograniczonego poziomu spójności o nieodświeżonej.
* Dodano wielokąta i LineString typy danych podczas określania zasad indeksowania kolekcji dla zapytań przestrzennych geograficznych.
* Dodano obsługę LINQ dla StringEnumConverter, IsoDateTimeConverter i UnixDateTimeConverter podczas tłumaczenia predykatów.
* Różne poprawki błędów zestawu SDK.

### <a name="a-name195195"></a>@no__t — 01.9.5
* Rozwiązano problem, który spowodował następujące NotFoundException: sesja odczytu nie jest dostępna dla tokenu sesji wejściowej. Ten wyjątek wystąpił w niektórych przypadkach podczas wykonywania zapytania dotyczącego regionu odczytu konta geograficznego.
* Wyświetlona Właściwość ResponseStream w klasie ResourceResponse, która umożliwia bezpośredni dostęp do podstawowego strumienia z odpowiedzi.

### <a name="a-name194194"></a>@no__t — 01.9.4
* Zmodyfikowano klasy ResourceResponse, FeedResponse, StoredProcedureResponse i MediaResponse w celu zaimplementowania odpowiedniego interfejsu publicznego, aby można było je zasymulować dla wdrożenia sterowanego testem (TDD).
* Rozwiązano problem, który spowodował nieprawidłowy nagłówek klucza partycji podczas używania niestandardowego obiektu JsonSerializerSettings do serializowania danych.

### <a name="a-name193193"></a>@no__t — 01.9.3
* Rozwiązano problem, który spowodował długotrwałe wykonywanie zapytań zakończonych niepowodzeniem z powodu błędu: Token autoryzacji nie jest obecnie prawidłowy.
* Rozwiązano problem polegający na tym, że usunięto oryginalny element SqlParameterCollection z zapytań o klauzuli TOP/order-by.

### <a name="a-name192192"></a>@no__t — 01.9.2
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

### <a name="a-name180180"></a>@no__t — 01.8.0
* Dodano obsługę wieloregionowych kont baz danych.
* Dodano obsługę ponawiania prób w przypadku żądań z ograniczeniami.  Użytkownik może dostosować liczbę ponownych prób i maksymalny czas oczekiwania przez skonfigurowanie właściwości ConnectionPolicy. RetryOptions.
* Dodano nowy interfejs IDocumentClient, który definiuje sygnatury wszystkich właściwości DocumentClient i metod.  W ramach tej zmiany również zmieniono metody rozszerzające, które tworzą interfejs IQueryable i IOrderedQueryable, na metody klasy DocumentClient.
* Dodano opcję konfiguracji w celu ustawienia ServicePoint. ConnectionLimit dla danego identyfikatora URI punktu końcowego Azure Cosmos DB.  Użyj ConnectionPolicy. MaxConnectionLimit, aby zmienić wartość domyślną, która jest ustawiona na 50.
* Przestarzałe IPartitionResolver i jej implementacja.  Obsługa usługi IPartitionResolver jest obecnie przestarzała. Zaleca się używanie partycjonowanych kolekcji w celu zapewnienia większego magazynu i przepływności.

### <a name="a-name171171"></a>@no__t — 01.7.1
* Dodano Przeciążenie metody ExecuteStoredProcedureAsync opartej na identyfikatorze URI, która pobiera RequestOptions jako parametr.

### <a name="a-name170170"></a>@no__t — 01.7.0
* Dodano obsługę czasu wygaśnięcia (TTL) dla dokumentów.

### <a name="a-name163163"></a>@no__t — 01.6.3
* Rozwiązano usterkę w pakiecie NuGet SDK platformy .NET do pakowania jej w ramach rozwiązania usługi w chmurze platformy Azure.

### <a name="a-name162162"></a>@no__t — 01.6.2
* Wdrożone [kolekcje partycjonowane](partition-data.md) i [poziomy wydajności zdefiniowane przez użytkownika](performance-levels.md). 

### <a name="a-name153153"></a>@no__t — 01.5.3
* **[Stałe]** Trwa wykonywanie zapytania dotyczącego Azure Cosmos DB punktu końcowego: "System .NET. http. HttpRequestexception: Wystąpił błąd podczas kopiowania zawartości do strumienia".

### <a name="a-name152152"></a>@no__t — 01.5.2
* Rozszerzona obsługa LINQ obejmująca nowe operatory stronicowania, wyrażeń warunkowych i porównywania zakresu.
  * Przejmij operator, aby włączyć wybór najlepszych zachowań w LINQ
  * Operator CompareTo do włączania porównań zakresu ciągów
  * Warunkowe (?) i operatory łączenia (??)
* **[Stałe]** Wyjątku ArgumentOutOfRangeException podczas łączenia projekcji modelu z elementem WHERE w zapytaniu LINQ. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a>@no__t — 01.5.1
* **[Stałe]** Jeśli opcja Select nie jest ostatnim wyrażeniem, przyjęto, że dostawca LINQ nie przetworzył projekcji i wygenerowało pozycję * nieprawidłowo.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a>@no__t — 01.5.0
* Zaimplementowane Upsert, dodane metody UpsertXXXAsync
* Ulepszenia wydajności dla wszystkich żądań
* Obsługa dostawcy LINQ dla metod warunkowych, łączenia i CompareTo dla ciągów
* **[Stałe]** Dostawca LINQ — > implementacja zawiera metodę na liście w celu wygenerowania tego samego języka SQL jako interfejsu IEnumerable i Array
* **[Stałe]** BackoffRetryUtility ponownie używa tego samego HttpRequestMessageu zamiast tworzenia nowego przy ponowieniu próby
* **[Przestarzałe]** UriFactory. CreateCollection--> powinien teraz używać UriFactory. dbdocumentcollection

### <a name="a-name141141"></a>@no__t — 01.4.1
* **[Stałe]** Problemy z lokalizacją w przypadku korzystania z informacji o niezmienionej kulturze, takich jak NL-NL itp. 

### <a name="a-name140140"></a>@no__t — 01.4.0
* Dodano routing oparty na IDENTYFIKATORach
  * Nowy pomocnik UriFactory, który pomaga w tworzeniu linków zasobów opartych na IDENTYFIKATORach
  * Nowe przeciążenia na DocumentClient w celu przejęcia identyfikatora URI
* Dodano IsValid () i IsValidDetailed () w LINQ for-geograficzne
* Ulepszona obsługa dostawcy LINQ:
  * **Math** -ABS, acos, asin, atan, sufit, cos, exp, Floor, log, log10 —, pow, Round, Sign, Sin, sqrt, Tan, Truncate
  * **Ciąg** -concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, podciąg, ToUpper
  * **Array** -concat, Contains, Count
  * **In** — operator

### <a name="a-name130130"></a>@no__t — 01.3.0
* Dodano obsługę modyfikowania zasad indeksowania.
  * Nowa metoda ReplaceDocumentCollectionAsync w DocumentClient
  * Nowa właściwość IndexTransformationProgress w ResourceResponse @ no__t-0T > do śledzenia procentowej postępu zmian zasad indeksu
  * Element documentcollection. IndexingPolicy jest teraz modyfikowalny
* Dodano obsługę indeksowania przestrzennego i zapytania.
  * Nowa przestrzeń nazw Microsoft. Azure. Documents. przestrzenny do serializacji/deserializacji typów przestrzennych, takich jak punkt i Wielokąt
  * Nowa Klasa SpatialIndex do indeksowania danych GEOJSON przechowywanych w Cosmos DB
* **[Stałe]** Wyrażenie LINQ wygenerowało niepoprawną kwerendę SQL [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a>@no__t — 01.2.0
* Dodano zależność w Newtonsoft. JSON v 5.0.7.
* Wprowadzono zmiany w kolejności obsługi według:
  
  * Obsługa dostawcy LINQ dla elementu OrderBy () lub OrderByDescending ()
  * IndexingPolicy do obsługi kolejności według 
    
    **Możliwa przerwana zmiana** 
    
    Jeśli masz istniejący kod, który inicjuje obsługę kolekcji z niestandardowymi zasadami indeksowania, istniejący kod musi zostać zaktualizowany do obsługi nowej klasy IndexingPolicy. Jeśli nie masz niestandardowych zasad indeksowania, ta zmiana nie ma wpływu na użytkownika.

### <a name="a-name110110"></a>@no__t — 01.1.0
* Dodano obsługę partycjonowania danych przy użyciu nowych klas HashPartitionResolver i RangePartitionResolver oraz IPartitionResolver.
* Dodano serializacji obiektu DataContract.
* Dodano obsługę identyfikatora GUID w dostawcy LINQ.
* Dodano obsługę systemu UDF w LINQ.

### <a name="a-name100100"></a>@no__t — 01.0.0
* ZESTAW SDK GA

## <a name="release--retirement-dates"></a>Data wycofania &
Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** przed WYCOFANIEM zestawu SDK w celu zapewnienia sprawnego przejścia do nowszej/obsługiwanej wersji.

Nowe funkcje i funkcje i optymalizacje są dodawane tylko do bieżącego zestawu SDK, dlatego zaleca się, aby zawsze uaktualnić do najnowszej wersji zestawu SDK tak szybko, jak to możliwe. 

Wszystkie żądania do Azure Cosmos DB za pomocą wycofanego zestawu SDK są odrzucane przez usługę.

> [!WARNING]
> Wszystkie wersje **1. x** zestawu .NET SDK dla interfejsu API SQL zostaną wycofane **30 sierpnia 2020**.
> 
>
<br/>

| Wersja | Data wydania | Data wycofania |
| --- | --- | --- |
| [2.7.0](#2.7.0) |23 września 2019 r. |--- |
| [2.6.0](#2.6.0) |30 sierpnia 2019 |--- |
| [2.5.1](#2.5.1) |02 lipca 2019 |--- |
| [2.4.1](#2.4.1) |20 czerwca 2019 |--- |
| [2.4.0](#2.4.0) |05 maja 2019 |--- |
| [2.3.0](#2.3.0) |04, 2019 |--- |
| [Regulamin](#2.2.3) |11 lutego 2019 |--- |
| [ppkt](#2.2.2) |06, 2019 |--- |
| [2.2.1](#2.2.1) |24 grudnia 2018 |--- |
| [2.2.0](#2.2.0) |07, 2018 |--- |
| [2.1.3](#2.1.3) |15 października 2018 |--- |
| [2.1.2](#2.1.2) |04, 2018 |--- |
| [pkt](#2.1.1) |27 września, 2018 |--- |
| [2.1.0](#2.1.0) |21 września 2018 |--- |
| [2.0.0](#2.0.0) |07 września, 2018 |--- |
| [1.22.0](#1.22.0) |19 kwietnia 2018 | 30 sierpnia 2020 |
| [1.21.1](#1.20.1) |09 marca, 2018 |30 sierpnia 2020 |
| [1.20.2](#1.20.1) |21 lutego 2018 |30 sierpnia 2020 |
| [1.20.1](#1.20.1) |05 lutego, 2018 |30 sierpnia 2020 |
| [1.19.1](#1.19.1) |16 listopada 2017 |30 sierpnia 2020 |
| [1.19.0](#1.19.0) |10 listopada 2017 |30 sierpnia 2020 |
| [1.18.1](#1.18.1) |07, 2017 |30 sierpnia 2020 |
| [1.18.0](#1.18.0) |17 października 2017 |30 sierpnia 2020 |
| [1.17.0](#1.17.0) |10 sierpnia 2017 |30 sierpnia 2020 |
| [1.16.1](#1.16.1) |07 sierpnia 2017 |30 sierpnia 2020 |
| [1.16.0](#1.16.0) |02 sierpnia 2017 |30 sierpnia 2020 |
| [1.15.0](#1.15.0) |30 czerwca 2017 |30 sierpnia 2020 |
| [1.14.1](#1.14.1) |23 maja 2017 |30 sierpnia 2020 |
| [1.14.0](#1.14.0) |10 maja 2017 |30 sierpnia 2020 |
| [1.13.4](#1.13.4) |09 maja 2017 |30 sierpnia 2020 |
| [1.13.3](#1.13.3) |06, 2017 |30 sierpnia 2020 |
| [1.13.2](#1.13.2) |19 kwietnia 2017 |30 sierpnia 2020 |
| [1.13.1](#1.13.1) |29 marca 2017 |30 sierpnia 2020 |
| [1.13.0](#1.13.0) |24 marca 2017 |30 sierpnia 2020 |
| [1.12.2](#1.12.2) |20 marca 2017 |30 sierpnia 2020 |
| [1.12.1](#1.12.1) |14 marca 2017 |30 sierpnia 2020 |
| [1.12.0](#1.12.0) |15 lutego 2017 |30 sierpnia 2020 |
| [1.11.4](#1.11.4) |06, 2017 |30 sierpnia 2020 |
| [1.11.3](#1.11.3) |26 stycznia 2017 |30 sierpnia 2020 |
| [1.11.1](#1.11.1) |21 grudnia 2016 |30 sierpnia 2020 |
| [1.11.0](#1.11.0) |08, 2016 |30 sierpnia 2020 |
| [1.10.0](#1.10.0) |27 września, 2016 |30 sierpnia 2020 |
| [1.9.5](#1.9.5) |01, 2016 |30 sierpnia 2020 |
| [1.9.4](#1.9.4) |24 sierpnia 2016 |30 sierpnia 2020 |
| [1.9.3](#1.9.3) |15 sierpnia 2016 |30 sierpnia 2020 |
| [1.9.2](#1.9.2) |23 lipca 2016 |30 sierpnia 2020 |
| [1.8.0](#1.8.0) |14 czerwca 2016 |30 sierpnia 2020 |
| [1.7.1](#1.7.1) |06, 2016 |30 sierpnia 2020 |
| [1.7.0](#1.7.0) |26 kwietnia 2016 |30 sierpnia 2020 |
| [1.6.3](#1.6.3) |08 kwietnia, 2016 |30 sierpnia 2020 |
| [1.6.2](#1.6.2) |29 marca 2016 |30 sierpnia 2020 |
| [1.5.3](#1.5.3) |19 lutego 2016 |30 sierpnia 2020 |
| [1.5.2](#1.5.2) |14 grudnia 2015 |30 sierpnia 2020 |
| [1.5.1](#1.5.1) |23 listopada, 2015 |30 sierpnia 2020 |
| [1.5.0](#1.5.0) |05, 2015 |30 sierpnia 2020 |
| [1.4.1](#1.4.1) |25 sierpnia 2015 |30 sierpnia 2020 |
| [1.4.0](#1.4.0) |13 sierpnia 2015 |30 sierpnia 2020 |
| [1.3.0](#1.3.0) |05 sierpnia, 2015 |30 sierpnia 2020 |
| [1.2.0](#1.2.0) |06, 2015 |30 sierpnia 2020 |
| [1.1.0](#1.1.0) |30 kwietnia 2015 |30 sierpnia 2020 |
| [1.0.0](#1.0.0) |08 kwietnia, 2015 | 30 sierpnia 2020 |


## <a name="faq"></a>Najczęściej zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) . 

