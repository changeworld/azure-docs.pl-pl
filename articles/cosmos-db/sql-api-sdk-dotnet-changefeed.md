---
title: 'Azure Cosmos DB: Interfejs API programu .NET zmiany źródła danych procesora, zestawu SDK i zasoby'
description: Dowiedz się wszystkiego o interfejsu API procesora kanału informacyjnego zmian i zestawu SDK, w tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami zmiany źródła danych procesora zestawu SDK programu .NET.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/30/2019
ms.author: maquaran
ms.openlocfilehash: a878ab1937b06f06a27b18f793fc1bfa190969ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627050"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>Zmiana .NET kanału informacyjnego procesora zestawu SDK: Pobierz i informacje o wersji
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

|   |   |
|---|---|
|**Zestaw SDK do pobrania**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Dokumentacja interfejsu API**|[Zmień dokumentację referencyjną interfejsu API bibliotece procesora zestawienia](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Wprowadzenie**|[Wprowadzenie zmian źródła danych procesora zestawu .NET SDK](change-feed.md)|
|**Bieżącej struktury obsługiwanej**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Informacje o wersji

### <a name="v2-builds"></a>kompilacje w wersji 2

### <a name="a-name226226"></a><a name="2.2.6"/>2.2.6
* Lepsza obsługa wyjątków obserwatora.
* Więcej informacji na temat błędów obserwatora:
  * Po zamknięciu obserwatora ze względu na wyjątek generowany przez obserwatora ProcessChangesAsync CloseAsync otrzyma parametr Przyczyna ustawiony ChangeFeedObserverCloseReason.ObserverError.
  * Dodano informacje śledzenia do identyfikowania błędów w kodzie użytkownika w obserwatora.

### <a name="a-name225225"></a><a name="2.2.5"/>2.2.5
* Dodano obsługę obsługi podziału w kolekcjach, korzystających z udostępnionej bazy danych przepływności.
  * W tej wersji rozwiązuje problem, które mogą wystąpić podczas podziału w kolekcji przy użyciu przepływności udostępnionej bazy danych, gdy Podziel wynik na zakres kluczy partycji tylko jeden element podrzędny utworzone zamiast dwóch ponownego równoważenia partycji. W takim przypadku procesora zestawienia zmian może zakończyć się zatrzymaniem usunięcie dzierżawy dla starego zakres kluczy partycji i bez tworzenia nowych dzierżaw. Problem został rozwiązany w tej wersji.

### <a name="a-name224224"></a><a name="2.2.4"/>2.2.4
* Dodano nową właściwość ChangeFeedProcessorOptions.StartContinuation do obsługi zmian począwszy od źródła danych z żądania token kontynuacji. To jest używana tylko podczas dzierżawy kolekcja jest pusta lub dzierżawa nie ma ContinuationToken zestawu. Dla dzierżaw w kolekcję dzierżaw, które mają ustawiony ContinuationToken token kontynuacji jest używany, i ChangeFeedProcessorOptions.StartContinuation jest ignorowana.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3
* Dodano obsługę używania magazyn niestandardowy, aby utrwalić tokenów kontynuacji dla każdej partycji.
  * Na przykład do przechowywania niestandardowych dzierżawy może być podzielona na partycje w jakikolwiek sposób niestandardowy kolekcję dzierżaw usługi Azure Cosmos DB.
  * Dzierżawy niestandardowych magazynów za pomocą nowego ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) punktów rozszerzeń i ILeaseStoreManager interfejsu publicznego.
  * W wielu interfejsach roli, zaprojektowane od nowa interfejs ILeaseManager.
* Pomocnicza, zmiana powodująca niezgodność: Usunięto rozszerzalności punktu ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager), zamiast tego użyj ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager).

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* W tej wersji rozwiązuje problem występujący podczas przetwarzania podziału w monitorowanej kolekcji i przy użyciu kolekcji partycjonowanej dzierżawy. Podczas przetwarzania dzierżawy dla partycji podziału, dzierżawy odpowiadający tej partycji nie mogą zostać usunięte. Problem został rozwiązany w tej wersji.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Naprawiono narzędzie do szacowania obliczenia wzorzec wielu kont i nowy format tokenu sesji.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Dodano obsługę kolekcje partycjonowane dzierżawy. Klucz partycji musi być zdefiniowany jako/identyfikator.
* Pomocnicza, zmiana powodująca niezgodność: metody interfejsu IChangeFeedDocumentClient i klasa ChangeFeedDocumentClient zostały zmienione na obejmują parametry RequestOptions i token anulowania. IChangeFeedDocumentClient jest punktem zaawansowanych rozszerzeń, który pozwala na dostarczenie niestandardową implementację klientem dokumentu do korzystania z procesora zestawienia zmian, np. dekoracji DocumentClient i przechwytywać wszystkie wywołania do niego wykonaj dodatkowe śledzenia obsługi błędów , itp. Dzięki tej aktualizacji kodu, który implementuje IChangeFeedDocumentClient należy zostać zmienione w celu uwzględnienia nowych parametrów w implementacji.
* Diagnostyka drobne ulepszenia.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Dodano nowy interfejs API, zadanie&lt;IReadOnlyList&lt;RemainingPartitionWork&gt; &gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(). Może to służyć do pracy szacowany dla każdej partycji.
* Obsługuje Microsoft.Azure.DocumentDB zestawu SDK w wersji 2.0. Wymaga Microsoft.Azure.DocumentDB 2.0 lub nowszej.

### <a name="a-name206206"></a><a name="2.0.6"/>2.0.6
* Dodano ChangeFeedEventHost.HostName właściwość publiczna dla zachowania zgodności z v1.

### <a name="a-name205205"></a><a name="2.0.5"/>2.0.5
* Naprawiono wyścigu, która występuje podczas dzielenia partycji. Sytuacja wyścigu może prowadzić do Uzyskiwanie dzierżawy i natychmiast utraty go podczas dzielenia partycji i powoduje rywalizacji o zasoby. Wyścig warunek naprawienia w tej wersji.

### <a name="a-name204204"></a><a name="2.0.4"/>2.0.4
* ZESTAW SDK W WERSJI OGÓLNIE DOSTĘPNEJ

### <a name="a-name203-prerelease203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3-Prerelease
* Rozwiązano następujące problemy:
  * Sytuacji podziału partycji może istnieć zduplikowane przetwarzania dokumentów przed podziału.
  * Interfejs API GetEstimatedRemainingWork zwrócił wartość 0, gdy dzierżawy nie znajdowały się w kolekcji dzierżawy.

* Następujące wyjątki są ujawniane. Rozszerzenia, które implementują IPartitionProcessor może zgłosić tych wyjątków.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="a-name202-prerelease202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-Prerelease
* Drobne zmiany interfejsu API:
  * Usunięte ChangeFeedProcessorOptions.IsAutoCheckpointEnabled, który został oznaczony jako przestarzały.

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-Prerelease
* Ulepszenia w zakresie stabilności:
  * Lepszej obsługi inicjowania magazynu dzierżawy. Gdy magazyn dzierżawy jest pusta, tylko jedno wystąpienie procesora można go zainicjować, pozostałe będzie czekać.
  * Odnawianie dzierżawy stabilny/wydajne więcej/wydania. Odnowienia i zwalniania dzierżawy z jednej partycji jest niezależna od innych odnawiania. W wersji 1, która została wykonana po kolei wszystkich partycji.
* Nowe interfejsy API w wersji 2:
  * Wzorzec Konstruktor do tworzenia elastycznych procesora: klasa ChangeFeedProcessorBuilder.
    * Może być dowolną kombinacją parametrów.
    * Może to potrwać wystąpienia DocumentClient dla kolekcji monitorowania i/lub dzierżawy (opcja nie jest dostępna w wersji 1).
  * IChangeFeedObserver.ProcessChangesAsync przyjmuje teraz token anulowania.
  * IRemainingWorkEstimator — narzędzie do szacowania pracy pozostałej może służyć oddzielnie z procesora.
  * Nowe punkty rozszerzeń:
    * IPartitionLoadBalancingStrategy — dla niestandardowych równoważenia obciążenia partycji między wystąpieniami procesora.
    * ILease, ILeaseManager — do zarządzania niestandardowej dzierżawy.
    * IPartitionProcessor — niestandardowe przetwarzanie zmian na partycji.
* Rejestrowanie - używa [LibLog](https://github.com/damianh/LibLog) biblioteki.
* 100% zgodne z poprzednimi wersjami z interfejsem API w wersji 1.
* Nowy kod podstawowy.
* Zgodne z [zestawu .NET SDK SQL](sql-api-sdk-dotnet.md) wersji 1.21.1 lub nowszej.

### <a name="v1-builds"></a>kompilacje w wersji 1

### <a name="a-name133133"></a><a name="1.3.3"/>1.3.3
* Dodano rejestrowanie większej ilości.
* Naprawiono przeciek DocumentClient podczas wywoływania szacowania pracy oczekujące wiele razy.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* Poprawki w szacowania pracy oczekujące.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* Ulepszenia w zakresie stabilności.
  * Poprawki dotyczące obsługi problem anulowanych zadań, które mogą prowadzić do zatrzymania obserwatorów na partycji.
* Pomoc techniczna dla ręcznego tworzenia punktów kontrolnych.
* Zgodne z [zestawu .NET SDK SQL](sql-api-sdk-dotnet.md) wersji 1.21 i nowsze wersje.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Dodano obsługę programu .NET Standard 2.0. Obsługuje teraz pakiet `netstandard2.0` i `net451` monikerów framework.
* Zgodne z [zestawu .NET SDK SQL](sql-api-sdk-dotnet.md) wersji 1.17.0 lub nowszej.
* Zgodne z [SQL platformy .NET Core SDK](sql-api-sdk-dotnet-core.md) wersji 1.5.1 lub nowszej.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Rozwiązuje problem z wyliczeniem oszacowanie pracy pozostałej, gdy kanału informacyjnego zmian jest pusta lub oczekiwała żadnej pracy.
* Zgodne z [zestawu .NET SDK SQL](sql-api-sdk-dotnet.md) wersji 1.13.2 lub nowszej.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Dodaje metodę, aby uzyskać szacunkową ilość pozostałej pracy, które mają być przetwarzane w kanału informacyjnego zmian.
* Zgodne z [zestawu .NET SDK SQL](sql-api-sdk-dotnet.md) wersji 1.13.2 lub nowszej.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* ZESTAW SDK W WERSJI OGÓLNIE DOSTĘPNEJ
* Zgodne z [zestawu .NET SDK SQL](sql-api-sdk-dotnet.md) wersji 1.14.1 i poniżej.


## <a name="release--retirement-dates"></a>Daty wydania i wycofania
Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** ewentualnej wycofanie zestawu SDK w celu złagodzenia przejścia do nowszych/obsługiwanych wersji.

Nowe funkcje i funkcjonalność i optymalizacje są dodawane tylko do bieżącego zestawu SDK, w związku z tym zalecane jest, zawsze uaktualnienie do najnowszej wersji zestawu SDK tak szybko, jak to możliwe. 

Wszelkie żądania do usługi Cosmos DB przy użyciu wycofane zestawu SDK zostanie odrzucone przez usługę.

<br/>

| Wersja | Data wydania | Data wygaśnięcia |
| --- | --- | --- |
| [2.2.6](#2.2.6) |29 stycznia 2019 r. |--- |
| [2.2.5](#2.2.5) |13 grudnia 2018 r. |--- |
| [2.2.4](#2.2.4) |29 listopada 2018 r. |--- |
| [2.2.3](#2.2.3) |19 listopada 2018 r. |--- |
| [2.2.2](#2.2.2) |31 października 2018 r. |--- |
| [2.2.1](#2.2.1) |24 października 2018 r. |--- |
| [1.3.3](#1.3.3) |08 maja 2018 r. |--- |
| [1.3.2](#1.3.2) |18 kwietnia 2018 r. |--- |
| [1.3.1](#1.3.1) |13 marca 2018 r. |--- |
| [1.2.0](#1.2.0) |31 października 2017 r. |--- |
| [1.1.1](#1.1.1) |29 sierpnia 2017 r. |--- |
| [1.1.0](#1.1.0) |13 sierpnia 2017 r. |--- |
| [1.0.0](#1.0.0) |07 lipca 2017 r. |--- |


## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stronę usługi. 

