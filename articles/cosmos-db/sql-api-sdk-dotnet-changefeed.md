---
title: Interfejs API procesora kanału informacyjnego usługi Azure Cosmos DB .NET, informacje o wydaniu sdk
description: Dowiedz się wszystkiego o interfejsie API i zestawie SDK z procesorem kanału informacyjnego zmian, w tym datami wydania, datami wycofania i zmianami wprowadzonymi między każdą wersją sdk procesora .NET Change Feed Processor.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/30/2019
ms.author: maquaran
ms.openlocfilehash: 5820778d46f5701b82bb289192350a9e13739d37
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619438"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET Change Feed Processor SDK: Pobieranie i wydawanie informacji

> [!div class="op_single_selector"]
>
> * [.NET](sql-api-sdk-dotnet.md)
> * [Plik danych o zmianach w sieci .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java (asynchroniczny)](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Wykonawca zbiorczy - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Wykonawca luzem - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**Pobieranie SDK**|[Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Dokumentacja interfejsu API**|[Dokumentacja referencyjna interfejsu API biblioteki danych danych](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Rozpoczęcie pracy**|[Wprowadzenie do procesora danych strumieniowych zmian .NET SDK](change-feed.md)|
|**Obecne obsługiwane ramy**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Jeśli używasz zdawania funkcji przetwarzania danych, zapoznaj się z najnowszą wersją 3.x [sdk .NET,](change-feed-processor.md)która ma wbudowany kanał zmian w zestawie SDK. 

## <a name="release-notes"></a>Informacje o wersji

### <a name="v2-builds"></a>kompilacje w wersji 2

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Dodano nową `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` metodę i `ICheckpointPartitionProcessorFactory`odpowiedni interfejs publiczny . Dzięki temu implementacja `IPartitionProcessor` interfejsu do korzystania z wbudowanego mechanizmu punktu kontrolnego. Nowa fabryka jest podobna do `IPartitionProcessorFactory` `Create` istniejącej , `ILeaseCheckpointer` z tą różnicą, że jej metoda również przyjmuje parametr.
* Tylko jedna z dwóch metod, albo `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` lub `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory`, `ChangeFeedProcessorBuilder` może być używana dla tego samego wystąpienia.

### <a name="228"></a><a name="2.2.8"/>2.2.8
* Ulepszenia stabilności i diagnozy:
  * Dodano obsługę wykrywania kanału zmian odczytu, który zajmuje dużo czasu. Gdy trwa dłużej niż wartość `ChangeFeedProcessorOptions.ChangeFeedTimeout` określona przez właściwość, podejmowane są następujące kroki:
    * Operacja odczytu źródła danych zmiany na problematycznej partycji została przerwana.
    * Wystąpienie procesora kanału informacyjnego zmiany porzuca własność problematycznej dzierżawy. Porzucona dzierżawa zostanie pobrana podczas następnego kroku nabycia dzierżawy, który zostanie wykonany przez to samo lub inne wystąpienie procesora kanału informacyjnego zmian. W ten sposób odczytywanie pliku danych zmienią rozpocznie się od nowa.
    * Problem jest zgłaszany do monitora kondycji. Domyślny monitor wrzosowiska wysyła wszystkie zgłoszone problemy do dziennika śledzenia.
  * Dodano nową nieruchomość `ChangeFeedProcessorOptions.ChangeFeedTimeout`publiczną: . Wartość domyślna tej właściwości wynosi 10 minut.
  * Dodano nową wartość wyliczenia publicznego: `Monitoring.MonitoredOperation.ReadChangeFeed`. Gdy wartość `HealthMonitoringRecord.Operation` jest ustawiona na `Monitoring.MonitoredOperation.ReadChangeFeed`, wskazuje, że problem kondycji jest związany z odczytem pliku danych zmian.

### <a name="227"></a><a name="2.2.7"/>2.2.7
* Ulepszona strategia równoważenia obciążenia w przypadku, gdy uzyskanie wszystkich dzierżaw trwa dłużej niż interwał wygaśnięcia dzierżawy, na przykład z powodu problemów z siecią:
  * W tym scenariuszu algorytm równoważenia obciążenia używany do fałszywego rozważenia dzierżawy jako wygasłe, powodując kradzież dzierżawy od aktywnych właścicieli. Może to spowodować niepotrzebne ponowne zrównoważenie wielu leasingów.
  * Ten problem został rozwiązany w tej wersji, unikając ponawiania próby konfliktu podczas uzyskiwania wygasłej dzierżawy, która właściciel nie zmieniła się i posponing nabycia wygasłej dzierżawy do następnej iteracji równoważenia obciążenia.

### <a name="226"></a><a name="2.2.6"/>2.2.6
* Ulepszona obsługa wyjątków Observer.
* Bogatsze informacje o błędach obserwatora:
  * Gdy Observer jest zamknięty z powodu wyjątku zgłaszanego przez ProcessChangesAsync obserwatora, CloseAsync otrzyma teraz parametr reason ustawiony na ChangeFeedObserverCloseReason.ObserverError.
  * Dodano ślady, aby zidentyfikować błędy w kodzie użytkownika w Observer.

### <a name="225"></a><a name="2.2.5"/>2.2.5
* Dodano obsługę podziału w kolekcjach korzystających z przepływności udostępnionej bazy danych.
  * W tej wersji rozwiązana jest problem, który może wystąpić podczas podziału w kolekcjach przy użyciu przepływności udostępnionej bazy danych po podziale wyniku na ponowne równoważenie partycji z utworzonym tylko jednym zakresem klucza partycji podrzędnej, a nie dwoma. W takim przypadku procesor kanału informacyjnego zmiany może utknąć, usuwając dzierżawę dla starego zakresu kluczy partycji i nie tworząc nowych dzierżaw. Problem został rozwiązany w tej wersji.

### <a name="224"></a><a name="2.2.4"/>2.2.4
* Dodano nową właściwość ChangeFeedProcessorOptions.StartContinuation do obsługi uruchamiania źródła danych z tokenu kontynuacji żądania. Jest to używane tylko wtedy, gdy kolekcja dzierżawy jest pusta lub dzierżawy nie ma ContinuationToken zestaw. W przypadku dzierżaw w kolekcji dzierżawy, które mają zestaw ContinuationToken, continuationToken jest używany i ChangeFeedProcessorOptions.StartContinuation jest ignorowana.

### <a name="223"></a><a name="2.2.3"/>2.2.3
* Dodano obsługę przy użyciu magazynu niestandardowego do utrwalania tokenów kontynuacji na partycję.
  * Na przykład niestandardowy magazyn dzierżawy może być zbieranie dzierżawy usługi Azure Cosmos DB na partycje w dowolny sposób niestandardowy.
  * Niestandardowe magazyny dzierżaw mogą używać nowego punktu rozszerzalności ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) i interfejsu publicznego ILeaseStoreManager.
  * Refaktoryzuje interfejs ILeaseManager do wielu interfejsów roli.
* Drobne zmiany podziału: usunięte punkt rozszerzalności ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager), użyj ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) zamiast.

### <a name="222"></a><a name="2.2.2"/>2.2.2
* Ta wersja rozwiązuje problem, który występuje podczas przetwarzania podziału w monitorowanej kolekcji i przy użyciu kolekcji dzierżawy podzielonej na partycje. Podczas przetwarzania dzierżawy dla partycji podzielonej dzierżawy odpowiadające tej partycji nie mogą zostać usunięte. Problem został rozwiązany w tej wersji.

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Poprawiono obliczanie estymatora dla kont Multi Master i nowego formatu tokenu sesji.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Dodano obsługę kolekcji dzierżawy podzielonej na partycje. Klucz partycji musi być zdefiniowany jako /id.
* Drobne zmiany podziału: metody IChangeFeedDocumentClient interfejsu i ChangeFeedDocumentClient klasy zostały zmienione w celu uwzględnienia RequestOptions i CancellationToken parametry. IChangeFeedDocumentClient jest zaawansowanym punktem rozszerzalności, który pozwala zapewnić niestandardową implementację klienta dokumentu do użycia z procesorem kanału informacyjnego Zmian, np. W tej aktualizacji kod, który implementuje IChangeFeedDocumentClient trzeba będzie zmienić, aby uwzględnić nowe parametry w implementacji.
* Drobne ulepszenia diagnostyczne.

### <a name="210"></a><a name="2.1.0"/>2.1.0
* Dodano nowy interfejs&lt;API, Task&lt;IReadOnlyList RemainingPartitionWork&gt; &gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(). Może to służyć do uzyskania szacowanej pracy dla każdej partycji.
* Obsługuje zestaw SDK 2.0 usługi Microsoft.Azure.DocumentDB. Wymaga microsoft.Azure.DocumentDB 2.0 lub nowszego.

### <a name="206"></a><a name="2.0.6"/>2.0.6
* Dodano właściwość publiczną ChangeFeedEventHost.HostName w celu zapewnienia zgodności z v1.

### <a name="205"></a><a name="2.0.5"/>2.0.5
* Naprawiono warunek wyścigu, który występuje podczas podziału partycji. Stan wyścigu może prowadzić do nabycia dzierżawy i natychmiast tracąc go podczas podziału partycji i powodując rywalizację. Problem z kondycją wyścigu został rozwiązany w tej wersji.

### <a name="204"></a><a name="2.0.4"/>2.0.4
* GA SDK

### <a name="203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3-wydanie wstępne
* Rozwiązano następujące problemy:
  * W przypadku podziału partycji może być zduplikowane przetwarzanie dokumentów zmodyfikowanych przed podziałem.
  * Interfejs API GetEstimatedRemainingWork zwrócił 0, gdy w kolekcji dzierżawy nie było żadnych dzierżaw.

* Następujące wyjątki są upublicznione. Rozszerzenia, które implementują IPartitionProcessor można zgłosić te wyjątki.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-wydanie wstępne
* Drobne zmiany interfejsu API:
  * Usunięto ChangeFeedProcessorOptions.IsAutoCheckpointEnabled, który został oznaczony jako przestarzały.

### <a name="201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-wydanie wstępne
* Stabilności:
  * Lepsza obsługa inicjowania sklepu dzierżawy. Gdy magazyn dzierżawy jest pusty, tylko jedno wystąpienie procesora może go zainicjować, inni będą czekać.
  * Bardziej stabilne/efektywne odnawianie/zwalnianie dzierżawy. Odnawianie i zwalnianie dzierżawy jednej partycji jest niezależne od odnawiania innych. W wersji 1, która została wykonana sekwencyjnie dla wszystkich partycji.
* Nowy interfejs API w wersji 2:
  * Wzorzec konstruktora dla elastycznej konstrukcji procesora: ChangeFeedProcessorBuilder klasy.
    * Może przyjmować dowolną kombinację parametrów.
    * Można wziąć DocumentClient wystąpieniu monitorowania i/lub dzierżawy kolekcji (niedostępne w wersji 1).
  * IChangeFeedObserver.ProcessChangesAsync teraz przyjmuje CancellationToken.
  * IRemainingWorkEstimator - pozostały estymator pracy może być używany oddzielnie od procesora.
  * Nowe punkty rozszerzalności:
    * IPartitionLoadBalancingStrategy — dla niestandardowego równoważenia obciążenia partycji między wystąpieniami procesora.
    * ILease, ILeaseManager - do zarządzania dzierżawą niestandardową.
    * IPartitionProcessor — do niestandardowego przetwarzania zmian na partycji.
* Rejestrowanie — używa biblioteki [LibLog.](https://github.com/damianh/LibLog)
* Zgodność w 100% z interfejsem API w wersji 1.
* Nowa baza kodu.
* Zgodność z [pakietem SDK SQL .NET](sql-api-sdk-dotnet.md) w wersji 1.21.1 i nowszej.

### <a name="v1-builds"></a>kompilacje w wersji 1

### <a name="133"></a><a name="1.3.3"/>1.3.3
* Dodano więcej rejestrowania.
* Naprawiono przeciek Dokumentu podczas wielokrotnego wywoływania oczekiwanego szacowania pracy.

### <a name="132"></a><a name="1.3.2"/>1.3.2
* Poprawki w oczekiwanej szacowaniu pracy.

### <a name="131"></a><a name="1.3.1"/>1.3.1
* Stabilności.
  * Poprawka do obsługi anulowanych zadań problem, który może prowadzić do zatrzymanych obserwatorów na niektórych partycjach.
* Obsługa ręcznego punktowania kontrolnego.
* Zgodność z [pakietem SDK SQL .NET](sql-api-sdk-dotnet.md) w wersji 1.21 i nowszej.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Dodaje obsługę platformy .NET Standard 2.0. Pakiet obsługuje `netstandard2.0` teraz `net451` i monikers ram.
* Zgodność z [pakietem SDK SQL .NET](sql-api-sdk-dotnet.md) w wersji 1.17.0 i nowszej.
* Zgodność z [pakietem SDK programu SQL .NET Core](sql-api-sdk-dotnet-core.md) w wersji 1.5.1 i nowszej.

### <a name="111"></a><a name="1.1.1"/>1.1.1
* Rozwiązuje problem z obliczaniem szacowania pozostałej pracy, gdy plik danych o zmianach był pusty lub nie było żadnych prac oczekujących.
* Zgodność z [pakietem SDK SQL .NET](sql-api-sdk-dotnet.md) w wersji 1.13.2 i nowszej.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Dodano metodę uzyskania szacunkowej ilości pozostałej pracy do przetworzenia w pliku danych ze zmianami.
* Zgodność z [pakietem SDK SQL .NET](sql-api-sdk-dotnet.md) w wersji 1.13.2 i nowszej.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* Zgodność z [pakietem SDK SQL .NET](sql-api-sdk-dotnet.md) w wersji 1.14.1 i nowszej.

## <a name="release--retirement-dates"></a>Zwolnij & daty przejścia na emeryturę

Firma Microsoft powiadomi o wycofaniu sdk sdk z co najmniej **12-miesięcznym** wyprzedzeniem w celu usprawnienia przejścia do nowszej/obsługiwanej wersji.

Nowe funkcje i funkcje i optymalizacje są dodawane tylko do bieżącego sdk, jako takie zaleca się, aby zawsze uaktualnić do najnowszej wersji SDK tak wcześnie, jak to możliwe. 

Każde żądanie do usługi Cosmos DB przy użyciu wycofanego SDK zostanie odrzucone przez usługę.

<br/>

| Wersja | Data wydania | Data przejścia na emeryturę |
| --- | --- | --- |
| [2.3.0](#2.3.0) |2 kwietnia 2020 r. |--- |
| [2.2.8](#2.2.8) |28 października 2019 r. |--- |
| [2.2.7](#2.2.7) |14 maja 2019 r. |--- |
| [2.2.6](#2.2.6) |29 stycznia 2019 r. |--- |
| [2.2.5](#2.2.5) |13 grudnia 2018 r. |--- |
| [2.2.4](#2.2.4) |29 listopada 2018 r. |--- |
| [2.2.3](#2.2.3) |19 listopada 2018 r. |--- |
| [2.2.2](#2.2.2) |31 października 2018 r. |--- |
| [2.2.1](#2.2.1) |24 października 2018 r. |--- |
| [1.3.3](#1.3.3) |8 maja 2018 r. |--- |
| [1.3.2](#1.3.2) |18 kwietnia 2018 r. |--- |
| [1.3.1](#1.3.1) |13 marca 2018 r. |--- |
| [1.2.0](#1.2.0) |31 października 2017 r. |--- |
| [1.1.1](#1.1.1) |29 sierpnia 2017 r. |--- |
| [1.1.0](#1.1.0) |13 sierpnia 2017 r. |--- |
| [1.0.0](#1.0.0) |7 lipca 2017 r. |--- |

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz też

Aby dowiedzieć się więcej o usłudze Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)
