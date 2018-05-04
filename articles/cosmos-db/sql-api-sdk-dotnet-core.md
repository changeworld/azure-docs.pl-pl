---
title: 'Azure DB rozwiązania Cosmos: SQL .NET Core API zestawu SDK & zasobów | Dokumentacja firmy Microsoft'
description: Dowiedz się wszystkiego o interfejsu API SQL .NET Core i zestawu SDK, w tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami Azure rozwiązania Cosmos DB .NET Core SDK.
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: kfile
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/22/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f4d69145eb9c1dd941beeafc9cc8793f96a541d8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>Azure rozwiązania Cosmos DB .NET Core SDK dla interfejsu API SQL: informacje o wersji i zasoby
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

<tr><td>**Pobierz zestaw SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**Dokumentacja interfejsu API**</td><td>[Dokumentacji interfejsu API platformy .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Przykłady**</td><td>[Przykłady kodu platformy .NET](sql-api-dotnet-samples.md)</td></tr>

<tr><td>**Wprowadzenie**</td><td>[Wprowadzenie do usługi Azure rozwiązania Cosmos DB .NET Core SDK](sql-api-dotnetcore-get-started.md)</td></tr>

<tr><td>**Samouczek aplikacji sieci Web**</td><td>[Tworzenie aplikacji sieci Web z bazy danych Azure rozwiązania Cosmos](sql-api-dotnet-application.md)</td></tr>

<tr><td>**Bieżąca platforma obsługiwane**</td><td>[.NET standard 1.6 i .NET Standard w wersji 1.5](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>Informacje o wersji

Azure rozwiązania Cosmos DB .NET Core SDK ma parzystość funkcji z najnowszej wersji [zestawu .NET SDK usługi Azure rozwiązania Cosmos DB](sql-api-sdk-dotnet.md).

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0

* Dodano właściwość ConsistencyLevel FeedOptions.
* Dodano klasy JsonSerializerSettings RequestOptions i FeedOptions.
* Dodano EnableReadRequestsFallback do ConnectionPolicy.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1

* Stała KeyNotFoundException dla innej kolejności partycji w zapytaniach w sytuacjach wyjątkowych.
* Stały usterki, którym nie został trwa honorowane atrybutu JsonPropery w klauzuli select do kwerend LINQ.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Stałe usterkę, która zostaje trafiony w niektórych warunkach wyścigu, powodujące sporadyczne "Microsoft.Azure.Documents.NotFoundException: odczytu sesji nie jest dostępna dla tokenu sesji wejściowy" błędy podczas korzystania z poziomu spójności sesji.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1

* Stałe regresji gdzie FeedOptions.MaxItemCount = -1 zwrócił System.ArithmeticException: rozmiar strony jest ujemna.
* Dodano nową funkcję ToString() do QueryMetrics.
* Statystyki partycji narażonych na odczytywanie kolekcji.
* Dodano właściwość PartitionKey ChangeFeedOptions.
* Niewielkie poprawki błędów.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
 
 * Dodaje możliwość określenia unikatowe indeksy dokumentów za pomocą właściwości UniqueKeyPolicy na elementu DocumentCollection.
 * Stałe błędów, w którym niestandardowe ustawienia JsonSerializer nie zostały trwa honorowane dla niektórych kwerend i wykonywanie procedury składowanej.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
 
 * Znakowanie zmiany z usługi Azure DocumentDB do bazy danych rozwiązania Cosmos Azure w dokumentacji interfejsu API dokumentacji, informacje o metadanych w zestawach i pakietu NuGet. 
 * Udostępnianie informacji diagnostycznych i opóźnienia z odpowiedzi żądania wysyłane z bezpośrednie połączenie między trybem. Nazwy właściwości są RequestDiagnosticsString i RequestLatency ResourceResponse klasy.
 * Ta wersja zestawu SDK wymaga najnowszej wersji emulatora DB rozwiązania Cosmos Azure dostępne do pobrania z https://aka.ms/cosmosdb-emulator.
 
### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Dodano kilka poprawek niezawodność i ulepszenia.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* Wewnętrzny zmiany związane z pomocniczych [Microsoft.Azure.Graphs](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* Dodano obsługę PartitionKeyRangeId jako FeedOption do określania zakresu wyniki zapytania, aby wartość klucza zakresu określonej partycji. 
* Dodano obsługę StartTime jako ChangeFeedOption rozpocząć wyszukiwanie zmiany po upływie tego czasu. 

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   Rozwiązano problem w klasie JsonSerializable, która może spowodować wyjątek przepełnienia stosu.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   Dodano obsługę służący do określania niestandardowej klasy JsonSerializerSettings podczas tworzenia wystąpienia [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet) wystąpienia.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   Obsługa platformy .NET Standard w wersji 1.5 wśród docelowych platform.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   Rozwiązano problem dotyczący x64 maszyny, które nie obsługują instrukcji SSE4 i throw sehexception — podczas uruchamiania zapytań bazy danych Azure rozwiązania Cosmos.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   Dodano obsługę nowego poziomu spójności o nazwie ConsistentPrefix.
*   Dodano obsługę metryki zapytania dla poszczególnych partycji.
*   Dodano obsługę ograniczenie rozmiaru token kontynuacji dla zapytań.
*   Dodano obsługę dokładniejsze śledzenie niepomyślnych żądań.
*   Niektóre ulepszenia wydajności w zestawie SDK.

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* Rozwiązano problem, który ignorowany podana w FeedOptions zapytania agregujące wartość PartitionKey.
* Rozwiązano problem w przezroczysty obsługi zarządzania partycji podczas pośredniej przesyłane między partycji Order By wykonywania zapytania.

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* Rozwiązano problem, który spowodował zakleszczenie w niektórych async interfejsów API, gdy jest używany w kontekście programu ASP.NET.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Poprawki wprowadzić bardziej odporne SDK automatycznej pracy awaryjnej w niektórych warunkach.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Rozwiązać problemu, który czasami powoduje to klasa WebException: nie można rozpoznać nazwy zdalnej.
* Dodano obsługę bezpośrednio odczytywania typu dokumentu, dodając nowe przeciążenia ReadDocumentAsync interfejsu API.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Dodano obsługę LINQ zapytań agregacji (COUNT, MIN, MAX, SUM i Śr.).
* Napraw problemu przeciek pamięci dla obiektu ConnectionPolicy spowodowane użyciem obsługi zdarzeń.
* Poprawkę dla problemu polegającego UpsertAttachmentAsync został nie działa, kiedy użyto ETag.
* Napraw problemu polegającego krzyżowego partycji zapytania według kontynuacji nie opracowanego podczas sortowania pola ciągu.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, SUM i Śr.). Zobacz [Obsługa agregacji](sql-api-sql-query.md#Aggregates).
* Obniżony minimalnej przepustowości w kolekcji partycjonowanych z 10,100 RU/s do 2500 RU/s.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

Azure rozwiązania Cosmos DB .NET Core SDK umożliwia tworzenie szybkie, międzyplatformowa [platformy ASP.NET Core](https://www.asp.net/core) i [.NET Core](https://www.microsoft.com/net/core#windows) uruchamiania aplikacji na systemie Windows, Mac i Linux. Najnowszej wersji Azure rozwiązania Cosmos DB .NET Core SDK jest w pełni [Xamarin](https://www.xamarin.com) zgodny i służyć do tworzenia aplikacji przeznaczonych dla systemu iOS, Android i Mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-Preview

Rozwiązania Cosmos DB .NET Core Podgląd zestaw SDK usługi Azure umożliwia szybkie, tworzenie i platform [platformy ASP.NET Core](https://www.asp.net/core) i [.NET Core](https://www.microsoft.com/net/core#windows) uruchamiania aplikacji na systemie Windows, Mac i Linux.

Azure rozwiązania Cosmos DB .NET Core Podgląd SDK ma parzystość funkcji z najnowszej wersji [zestawu .NET SDK usługi Azure rozwiązania Cosmos DB](sql-api-sdk-dotnet.md) i obsługuje następujące:
* Wszystkie [tryby połączeń](performance-tips.md#networking): tryb bramy, bezpośrednie TCP i bezpośredniego HTTPs. 
* Wszystkie [poziomy spójności](consistency-levels.md): silne, sesji nieaktualności ograniczone i Eventual.
* [Kolekcje partycjonowane](partition-data.md). 
* [W przypadku bazy danych konta i replikacja geograficzna](distribute-data-globally.md).

Jeśli masz pytania dotyczące tego zestawu SDK, opublikuj wpis na [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb), lub plik problemu w [repozytorium github](https://github.com/Azure/azure-documentdb-dotnet/issues). 

## <a name="release--retirement-dates"></a>Wersja & daty wycofania

| Wersja | Data wydania | Dacie wycofania |
| --- | --- | --- |
| [1.9.1](#1.9.1) |09 marca 2018 |--- |
| [1.8.2](#1.8.2) |21 lutego 2018 r. |--- |
| [1.8.1](#1.8.1) |05 lutego 2018 |--- |
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
| [0.1.0-Preview](#0.1.0-preview) |15 listopada 2016 r. |31 grudnia 2016 r. |

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej na temat rozwiązania Cosmos bazy danych, zobacz [bazy danych programu Microsoft Azure rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/) stronę usługi. 

