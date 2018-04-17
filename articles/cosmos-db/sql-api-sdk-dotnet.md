---
title: 'Azure rozwiązania Cosmos bazy danych: Interfejs API .NET SQL, zestaw SDK & zasobów | Dokumentacja firmy Microsoft'
description: Dowiedz się wszystkiego o interfejs API .NET SQL i zestawu SDK, w tym daty wydania, daty wycofania i zmiany wprowadzone od każdej wersji zestawu SDK .NET usługi Azure rozwiązania Cosmos bazy danych.
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: kfile
editor: cgronlun
ms.assetid: 8e239217-9085-49f5-b0a7-58d6e6b61949
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/09/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e4d33fc513b3a2aaa70a3402a42b31efe63c62dd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Azure SDK .NET dla interfejsu API SQL rozwiązania Cosmos DB: Pobierz i informacje o wersji
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Źródła danych zmian .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java (asynchroniczny)](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**Pobierz zestaw SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>

<tr><td>**Dokumentacja interfejsu API**</td><td>[Dokumentacji interfejsu API platformy .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Przykłady**</td><td>[Przykłady kodu platformy .NET](sql-api-dotnet-samples.md)</td></tr>

<tr><td>**Wprowadzenie**</td><td>[Wprowadzenie do zestawu .NET SDK usługi Azure rozwiązania Cosmos bazy danych](sql-api-get-started.md)</td></tr>

<tr><td>**Samouczek aplikacji sieci Web**</td><td>[Tworzenie aplikacji sieci Web z bazy danych Azure rozwiązania Cosmos](sql-api-dotnet-application.md)</td></tr>

<tr><td>**Bieżąca platforma obsługiwane**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Informacje o wersji
### <a name="a-name12111211"></a><a name="1.21.1"/>1.21.1

* Stała KeyNotFoundException dla innej kolejności partycji w zapytaniach w sytuacjach wyjątkowych.
* Stały usterki, którym nie został trwa honorowane atrybutu JsonProperty w klauzuli select do kwerend LINQ.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* Stałe usterkę, która zostaje trafiony w niektórych warunkach wyścigu, powodujące sporadyczne "Microsoft.Azure.Documents.NotFoundException: odczytu sesji nie jest dostępna dla tokenu sesji wejściowy" błędy podczas korzystania z poziomu spójności sesji.

### <a name="a-name12011201"></a><a name="1.20.1"/>1.20.1

* Stałe regresji gdzie FeedOptions.MaxItemCount = -1 zwrócił System.ArithmeticException: rozmiar strony jest ujemna.
* Dodano nową funkcję ToString() do QueryMetrics.
* Statystyki partycji narażonych na odczytywanie kolekcji.
* Dodano właściwość PartitionKey ChangeFeedOptions.
* Niewielkie poprawki błędów.

### <a name="a-name11911191"></a><a name="1.19.1"/>1.19.1

* Dodaje możliwość określenia unikatowe indeksy dokumentów za pomocą właściwości UniqueKeyPolicy na elementu DocumentCollection.
* Stałe błędów, w którym niestandardowe ustawienia JsonSerializer nie zostały trwa honorowane dla niektórych kwerend i wykonywanie procedury składowanej.

### <a name="a-name11901190"></a><a name="1.19.0"/>1.19.0

* Znakowanie zmiany z usługi Azure DocumentDB do bazy danych rozwiązania Cosmos Azure w dokumentacji interfejsu API dokumentacji, informacje o metadanych w zestawach i pakietu NuGet. 
* Udostępnianie informacji diagnostycznych i opóźnienia z odpowiedzi żądania wysyłane z bezpośrednie połączenie między trybem. Nazwy właściwości są RequestDiagnosticsString i RequestLatency ResourceResponse klasy.
* Ta wersja zestawu SDK wymaga najnowszej wersji emulatora DB rozwiązania Cosmos Azure dostępne do pobrania z https://aka.ms/cosmosdb-emulator. 

### <a name="a-name11811181"></a><a name="1.18.1"/>1.18.1 

* Wewnętrzny zmiany dla zestawów znajomych firmy Microsoft.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* Dodano kilka poprawek niezawodność i ulepszenia.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* Dodano obsługę PartitionKeyRangeId jako FeedOption do określania zakresu wyniki zapytania, aby wartość klucza zakresu określonej partycji. 
* Dodano obsługę StartTime jako ChangeFeedOption rozpocząć wyszukiwanie zmiany po upływie tego czasu.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Rozwiązano problem w klasie JsonSerializable, która może spowodować wyjątek przepełnienia stosu.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   Rozwiązano problem, co wymagało ponownego kompilowania aplikacji z powodu wprowadzenia klasy JsonSerializerSettings jako opcjonalny parametr w Konstruktorze DocumentClient.
* Oznaczony jako konstruktora DocumentClient przestarzały tego wymagane klasy JsonSerializerSettings jako ostatni parametr, aby umożliwić wartościami domyślnymi ConnectionPolicy i parametry ConsistencyLevel podczas przekazywania w parametrze klasy JsonSerializerSettings.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Dodano obsługę służący do określania niestandardowej klasy JsonSerializerSettings podczas tworzenia wystąpienia [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Rozwiązano problem dotyczący x64 maszyny, które nie obsługują instrukcji SSE4 i throw sehexception — podczas uruchamiania zapytań Azure rozwiązania Cosmos bazy danych SQL.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Dodano obsługę nowego poziomu spójności o nazwie ConsistentPrefix.
*   Dodano obsługę metryki zapytania dla poszczególnych partycji.
*   Dodano obsługę ograniczenie rozmiaru token kontynuacji dla zapytań.
*   Dodano obsługę dokładniejsze śledzenie niepomyślnych żądań.
*   Niektóre ulepszenia wydajności w zestawie SDK.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Taką jak 1.13.3. Wprowadzone zmiany wewnętrznego.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Taką jak 1.13.2. Wprowadzone zmiany wewnętrznego.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Rozwiązano problem, który ignorowany podana w FeedOptions zapytania agregujące wartość PartitionKey.
* Rozwiązano problem w przezroczysty obsługi zarządzania partycji podczas pośredniej przesyłane między partycji Order By wykonywania zapytania.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Rozwiązano problem, który spowodował zakleszczenie w niektórych async interfejsów API, gdy jest używany w kontekście programu ASP.NET.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Poprawki wprowadzić bardziej odporne SDK automatycznej pracy awaryjnej w niektórych warunkach.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Rozwiązać problemu, który czasami powoduje to klasa WebException: nie można rozpoznać nazwy zdalnej.
* Dodano obsługę bezpośrednio odczytywania typu dokumentu, dodając nowe przeciążenia ReadDocumentAsync interfejsu API.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* Dodano obsługę LINQ zapytań agregacji (COUNT, MIN, MAX, SUM i Śr.).
* Napraw problemu przeciek pamięci dla obiektu ConnectionPolicy spowodowane użyciem obsługi zdarzeń.
* Poprawkę dla problemu polegającego UpsertAttachmentAsync został nie działa, kiedy użyto ETag.
* Napraw problemu polegającego krzyżowego partycji zapytania według kontynuacji nie opracowanego podczas sortowania pola ciągu.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, SUM i Śr.). Zobacz [Obsługa agregacji](sql-api-sql-query.md#Aggregates).
* Obniżony minimalnej przepustowości w kolekcji partycjonowanych z 10,100 RU/s do 2500 RU/s.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Poprawkę dotyczącą problemu, w którym niektórych zapytań między partycji niepowodzeniem w procesie 32-bitowego hosta.
* Poprawkę dotyczącą problemu, w którym kontenera sesji zostało nie są aktualizowane przy użyciu tokenu żądań zakończonych niepowodzeniem w trybie bramy.
* Napraw problemu polegającego sprawdzano zapytanie z wywołania funkcji UDF w projekcji w niektórych przypadkach.
* Poprawki wydajności po stronie klienta dla zwiększenia przepływności odczytu i zapisu żądań.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Poprawkę dla problemu polegającego kontenera sesji zostało nie są aktualizowane przy użyciu tokenu żądań zakończonych niepowodzeniem.
* Dodano obsługę dla zestawu SDK do pracy w procesie 32-bitowego hosta. Należy pamiętać, że użycie krzyżowego partycji zapytania przetwarzania przez hosta 64-bitowych jest zalecane zwiększonej wydajności.
* Zwiększona wydajność dla scenariuszy obejmujących zapytań z dużą liczbą wartości klucza partycji w wyrażeniu IN.
* Wypełnione różnych Statystyka przydziału zasobów w ResourceResponse dla żądań odczytu po ustawieniu opcji żądania PopulateQuotaInfo kolekcji dokumentów.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* Wydajności drobne poprawki dotyczące interfejsu API CreateDocumentCollectionIfNotExistsAsync wprowadzone w 1.11.0.
* Napraw wydajności zestawu SDK dla scenariuszy obejmujących wysoki stopień równoczesnych żądań.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Obsługa nowe klasy i metody przetwarzania [zmienić źródła strumieniowego](change-feed.md) dokumentów w kolekcji.
* Obsługa kontynuacji zapytanie między partycji i usprawnień wydajności dla różnych partycji zapytań.
* Dodanie metody CreateDatabaseIfNotExistsAsync i CreateDocumentCollectionIfNotExistsAsync.
* LINQ obsługę funkcji systemowych: IsDefined, IsNull i IsPrimitive.
* Napraw dla automatycznego binplacing Microsoft.Azure.Documents.ServiceInterop.dll i DocumentDB.Spatial.Sql.dll zestawów, aby otworzyć folder bin aplikacji podczas korzystania z pakietu Nuget z projektów, które mają narzędzi project.json.
* Obsługa emitowanie ślady ETW po stronie klienta, które mogą być przydatne w scenariuszach debugowania.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Dodano bezpośrednie połączenie między obsługi dla kolekcji partycjonowanych.
* Zwiększona wydajność poziomu spójności nieaktualności ograniczone.
* Dodano wielokąta i typy danych LineString podczas określania kolekcji indeksowania zasad dla zapytań przestrzennych grodzenia.
* Dodano obsługę LINQ StringEnumConverter, IsoDateTimeConverter i UnixDateTimeConverter podczas tłumaczenia predykatów.
* Różne poprawki błędów zestawu SDK.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Rozwiązano problem powodujący następujące NotFoundException: odczytu sesji nie jest dostępna dla tokenu sesji wejściowego. W niektórych przypadkach wystąpił ten wyjątek, podczas wykonywania zapytania dotyczącego odczytu region konta geograficznie rozproszonych.
* Widoczne właściwości ResponseStream w klasie ResourceResponse umożliwia bezpośredni dostęp do źródłowego strumienia z odpowiedzi.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Zmodyfikowane klasy ResourceResponse, FeedResponse, StoredProcedureResponse i MediaResponse do zaimplementowania odpowiedni interfejs publiczny, dzięki czemu mogą być mocked dla testu pracy wdrażania (TDD).
* Rozwiązano problem powodujący nagłówka klucza partycji utworzonym w przypadku używania niestandardowego obiektu klasy JsonSerializerSettings dla serializacji danych.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Rozwiązano problem powodujący długotrwała zapytania, aby zakończyć się niepowodzeniem z powodu błędu: token autoryzacji jest nieprawidłowy w danym momencie.
* Rozwiązano problem usunięte oryginalnego SqlParameterCollection z cross partycji top/według zapytania.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Dodano obsługę równoległe zapytania dla kolekcji partycjonowanych.
* Dodano obsługę dla wielu zapytań ORDER BY i od góry dla kolekcji partycjonowanych partycji.
* Stałe brakujących odwołań do DocumentDB.Spatial.Sql.dll i Microsoft.Azure.Documents.ServiceInterop.dll, które są wymagane podczas odwoływania się do projektu bazy danych Azure rozwiązania Cosmos w odniesieniu do pakietów Nuget DB rozwiązania Cosmos Azure.
* Stałe możliwość używania parametrów o różnych typach, korzystając z funkcji zdefiniowanych przez użytkownika w składniku LINQ. 
* Stałej usterki dla kont replikowany globalnie, gdzie wywołania Upsert zostały być kierowany do odczytu lokalizacji zamiast lokalizacji zapisu.
* Dodano metody interfejsu IDocumentClient, których brakowało: 
  * Metoda UpsertAttachmentAsync korzysta z mediaStream i opcje jako parametry
  * Metoda CreateAttachmentAsync, która przyjmuje opcje jako parametr
  * Metoda CreateOfferQuery, która przyjmuje querySpec jako parametr.
* Niezapieczętowanych klas publicznych, które są widoczne w interfejsie IDocumentClient.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Dodano obsługę konta w przypadku bazy danych.
* Dodano obsługę ponownych prób w odpowiedzi na żądania z ograniczeniem przepustowości.  Użytkownik może dostosować liczbę ponownych prób oraz maksymalny czas, przez skonfigurowanie właściwości ConnectionPolicy.RetryOptions.
* Dodaje nowy interfejs IDocumentClient definiujący podpisów wszystkich DocumenClient właściwości i metod.  W ramach tej zmiany należy również zmienić metody rozszerzenia, które utworzyć IQueryable i IOrderedQueryable do metody w samej klasy DocumentClient.
* Opcja konfiguracji dodano można ustawić ServicePoint.ConnectionLimit dla danego punktu końcowego bazy danych Azure rozwiązania Cosmos identyfikatora Uri.  Użyj ConnectionPolicy.MaxConnectionLimit, aby zmienić domyślną wartość, która jest ustawiona na 50.
* Przestarzałe IPartitionResolver i jej wdrożenia.  Obsługa IPartitionResolver jest już nieaktualny. Zalecane jest używanie podzielona na partycje kolekcji dla nowszej magazynu i przepustowości.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Dodać przeciążenia do identyfikatora Uri na podstawie metody ExecuteStoredProcedureAsync, która przyjmuje RequestOptions jako parametr.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Czas do live (TTL) — Pomoc techniczna dla dokumentów.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Stała usterki w pakiecie Nuget zestawu SDK .NET dla pakowania w ramach rozwiązania usługi w chmurze Azure.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Zaimplementowane [kolekcje partycjonowane](partition-data.md) i [poziomy wydajności zdefiniowanych przez użytkownika](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Stałej]**  Zgłasza punktu końcowego badania rozwiązania Cosmos bazy danych platformy Azure: "System.Net.Http.HttpRequestException: Wystąpił błąd podczas kopiowania zawartości do strumienia".

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* Rozwinięte LINQ obsługuje operatorów nowych stronicowania, warunkowego wyrażeń i zakres porównania.
  * Take — operator umożliwia zachowanie wybierz TOP w składniku LINQ
  * Operator CompareTo umożliwia porównywanie ciągów zakresu
  * Warunkowy (?) i łączyć operatory (?)
* **[Stałej]**  ArgumentOutOfRangeException sprzęganych projekcji modelu o Where w w zapytaniu składnika LINQ. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Stałej]**  Jeśli wybór nie jest ostatni wyrażenie dostawcy LINQ zakłada, że nie projekcji i wybierz utworzony * niepoprawnie.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Upsert zaimplementowany, dodać UpsertXXXAsync metody
* Ulepszenia wydajności dla wszystkich żądań
* Obsługa dostawcy LINQ warunkowego, połączenie oraz metody CompareTo dla ciągów
* **[Stałej]**  Wdrożenie zawiera metody na liście do wygenerowania tego samego SQL na interfejs IEnumerable i Tablica--> Dostawca LINQ
* **[Stałej]**  BackoffRetryUtility używa tego samego HttpRequestMessage ponownie zamiast tworzenia nowej przy ponownej próbie
* **[Przestarzały]**  UriFactory.CreateCollection--> należy użyć UriFactory.CreateDocumentCollection

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Stałej]**  Lokalizacja problemy podczas korzystania z systemem innym niż en kultury informacji, takich jak nl-NL, itd. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Dodane na podstawie Identyfikatora routingu
  * Nowy Pomocnik UriFactory pomocne w tworzeniu linki do zasobów na podstawie Identyfikatora
  * Nowe przeciążenia na DocumentClient zaczęły w identyfikatorze URI
* Dodano IsValid() i IsValidDetailed() w składniku LINQ dla lokalizacji geograficznych
* Rozszerzona obsługa dostawcy LINQ:
  * **Matematyczne** -Abs, Acos, Asin, Atan Ceiling Cos Exp, Floor, dziennika, Log10, Pow, Round, logowania, Sin, Sqrt, Tan, obcięcia
  * **Ciąg** -Concat, zawiera, EndsWith, IndexOf, Count, ToLower, TrimStart, Zastąp, wstecznego TrimEnd, StartsWith, SubString, ToUpper
  * **Tablica** -Concat, zawiera, liczba
  * **W** — operator

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Dodano obsługę modyfikowanie zasad indeksowania.
  * Nowa metoda ReplaceDocumentCollectionAsync w DocumentClient
  * Nowe właściwości IndexTransformationProgress w ResourceResponse<T> śledzenia procent postępu zmian zasad indeksu
  * DocumentCollection.IndexingPolicy teraz jest modyfikowalna
* Dodano obsługę przestrzennych indeksowanie i zapytania.
  * Nowy Microsoft.Azure.Documents.Spatial obszar nazw dla serializacji/deserializacji typów przestrzennych, takie jak punkt i wielokąta
  * Nowa klasa SpatialIndex indeksowania GeoJSON dane przechowywane w bazie danych rozwiązania Cosmos
* **[Stałej]**  Zapytania SQL niepoprawne generowane na podstawie wyrażenia LINQ [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Dodaje zależność na Newtonsoft.Json v5.0.7.
* Wprowadzone zmiany do obsługi Order By:
  
  * Obsługa dostawcy LINQ OrderBy() lub OrderByDescending()
  * IndexingPolicy do obsługi Order By 
    
    **Możliwe istotne zmiany** 
    
    Jeśli masz istniejący kod tej kolekcji przepisy z niestandardowe zasady indeksowania, istniejącego kodu musi zostać zaktualizowany do obsługi nowej klasy IndexingPolicy. Jeśli nie niestandardowe zasady indeksowania, następnie ta zmiana nie dotyczy.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Dodano obsługę partycjonowanie danych przy użyciu nowej klasy HashPartitionResolver i RangePartitionResolver oraz IPartitionResolver.
* Serializacja obiektu DataContract dodany.
* Dodano identyfikatora GUID Obsługa dostawcy LINQ.
* Obsługuje dodano UDF w składniku LINQ.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release--retirement-dates"></a>Wersja & wycofania dat
Firma Microsoft udostępnia powiadomienia co najmniej **12 miesięcy** klienta z wyprzedzeniem wycofanie SDK w celu złagodzenia przejścia do nowszej/nieobsługiwaną wersję.

Nowe funkcje i funkcjonalność i optymalizację, które są dodawane tylko do bieżącego zestawu SDK, w związku jest zalecane, zawsze Uaktualnij zestaw SDK najnowszą tak szybko jak to możliwe. 

Wszystkie żądania dotyczące bazy danych rozwiązania Cosmos Azure przy użyciu wycofane zestawu SDK są odrzucane przez usługę.

<br/>

| Wersja | Data wydania | Dacie wycofania |
| --- | --- | --- |
| [1.21.1](#1.20.1) |09 marca 2018 |--- |
| [1.20.2](#1.20.1) |21 lutego 2018 r. |--- |
| [1.20.1](#1.20.1) |05 lutego 2018 |--- |
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
| [1.9.5](#1.9.5) |01 wrześniu 2016 r. |--- |
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
| [1.5.0](#1.5.0) |05 października 2015 |--- |
| [1.4.1](#1.4.1) |25 sierpnia 2015 |--- |
| [1.4.0](#1.4.0) |13 sierpnia 2015 |--- |
| [1.3.0](#1.3.0) |05 sierpnia 2015 |--- |
| [1.2.0](#1.2.0) |06 lipca 2015 r. |--- |
| [1.1.0](#1.1.0) |30 kwietnia 2015 r. |--- |
| [1.0.0](#1.0.0) |08 kwietnia 2015 r. |--- |


## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat rozwiązania Cosmos bazy danych, zobacz [bazy danych programu Microsoft Azure rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/) stronę usługi. 

