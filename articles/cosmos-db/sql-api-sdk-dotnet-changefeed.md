---
title: 'Azure rozwiązania Cosmos bazy danych: Interfejs API programu .NET zmiany źródła procesora, zestawu SDK & zasobów | Dokumentacja firmy Microsoft'
description: Dowiedz się wszystkiego o interfejsie API procesora źródła danych zmian i zestawu SDK, w tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami zmiany źródła procesora zestawu SDK .NET.
services: cosmos-db
author: ealsur
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/21/2018
ms.author: maquaran
ms.openlocfilehash: a2770b9349dac8caa8e0611d77522ab56ca1bf07
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798899"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>Procesor kanału informacyjnego zmiany .NET SDK: Pobierz i informacje o wersji
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
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**Pobierz zestaw SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Dokumentacja interfejsu API**|[Zmień dokumentacji interfejsu API biblioteki procesora źródła danych](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Wprowadzenie**|[Wprowadzenie zmian źródła danych procesora zestawu .NET SDK](change-feed.md)|
|**Bieżąca platforma obsługiwane**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Informacje o wersji

### <a name="stable-builds"></a>Stabilna kompilacji

### <a name="a-name133133"></a><a name="1.3.3"/>1.3.3
* Dodano więcej rejestrowania.
* Stała przeciek DocumentClient podczas wywoływania metody oszacowania pracy oczekujących wiele razy.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* Poprawki oszacowania pracy oczekujących.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* Ulepszenia.
  * Rozwiąż problem anulowane zadania, które mogą prowadzić do zatrzymania obserwatorów na niektóre partycje obsługi.
* Obsługa ręczne tworzenie punktów kontrolnych.
* Zgodny z [zestawu .NET SDK SQL](sql-api-sdk-dotnet.md) wersji 1.21 i powyżej.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Dodaje obsługę platformy .NET Standard 2.0. Pakiet obsługuje teraz `netstandard2.0` i `net451` monikerów framework.
* Zgodny z [zestawu .NET SDK SQL](sql-api-sdk-dotnet.md) wersji 1.17.0 lub nowszym.
* Zgodny z [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) wersji 1.5.1 lub nowszym.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Rozwiązuje problem z wyliczeniem oszacowania pracy pozostałej podczas zmiany źródła danych jest pusta lub oczekiwała żadne czynności.
* Zgodny z [zestawu .NET SDK SQL](sql-api-sdk-dotnet.md) wersji 1.13.2 lub nowszym.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Dodaje metody uzyskać szacunkową Praca pozostała na przetworzenie w źródła danych zmian.
* Zgodny z [zestawu .NET SDK SQL](sql-api-sdk-dotnet.md) wersji 1.13.2 lub nowszym.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* Zgodny z [zestawu .NET SDK SQL](sql-api-sdk-dotnet.md) wersji 1.14.1 i poniżej.

### <a name="pre-release-builds"></a>Kompilacje wydania wstępnego

### <a name="a-name202-prerelease202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-Prerelease
* Drobne zmiany interfejsu API:
  * Usunięte ChangeFeedProcessorOptions.IsAutoCheckpointEnabled, który został oznaczony jako przestarzały.

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-Prerelease
* Ulepszenia:
  * Lepszą obsługę zainicjowanie magazynu dzierżawy. Gdy dzierżawa magazynu jest pusta, tylko jedno wystąpienie procesora można go zainicjować, innych będzie czekać.
  * Więcej odnawiania dzierżawy stabilny/wydajne i wersji. Odnawianie i wydanie jedną partycję dzierżawy jest niezależna od innych odnawiania. W wersji 1, która została wykonana po kolei dla wszystkich partycji.
* Nowe v2 interfejsu API:
  * Wzorzec konstruktora do konstrukcji elastyczne procesora: klasa ChangeFeedProcessorBuilder.
    * Może być dowolną kombinacją parametrów.
    * Możliwe jest wystąpienie DocumentClient dla kolekcji monitorowania i/lub dzierżawy (niedostępne w wersji 1).
  * IChangeFeedObserver.ProcessChangesAsync ma teraz CancellationToken.
  * IRemainingWorkEstimator — pozostałe narzędzia do szacowania pracy można osobno z procesora.
  * Nowe punkty rozszerzeń:
    * IParitionLoadBalancingStrategy — dla niestandardowych równoważenia obciążenia partycji między wystąpieniami procesora.
    * ILease, ILeaseManager - zarządzania niestandardowe dzierżawy.
    * IPartitionProcessor - niestandardowych przetwarzania zmian na partycji.
* Rejestrowanie - używa [LibLog](https://github.com/damianh/LibLog) biblioteki.
* 100% zgodności z wcześniejszymi wersjami API w wersji 1.
* Nowy kod podstawowy.
* Zgodny z [zestawu .NET SDK SQL](sql-api-sdk-dotnet.md) wersji 1.21.1 lub nowszym.

## <a name="release--retirement-dates"></a>Wersja & wycofania dat
Firma Microsoft udostępni powiadomienia co najmniej **12 miesięcy** klienta z wyprzedzeniem wycofanie SDK w celu złagodzenia przejścia do nowszej/nieobsługiwaną wersję.

Nowe funkcje i funkcjonalność i optymalizację, które są dodawane tylko do bieżącego zestawu SDK, w związku jest zalecane, zawsze Uaktualnij zestaw SDK najnowszą tak szybko jak to możliwe. 

Każde żądanie do rozwiązania Cosmos bazy danych przy użyciu wycofane zestawu SDK będą odrzucane przez usługę.

<br/>

| Wersja | Data wydania | Dacie wycofania |
| --- | --- | --- |
| [1.3.3](#1.3.3) |08 mogą 2018 |--- |
| [1.3.2](#1.3.2) |18 kwietnia 2018 |--- |
| [1.3.1](#1.3.1) |13 marca 2018 r. |--- |
| [1.2.0](#1.2.0) |31 października 2017 r. |--- |
| [1.1.1](#1.1.1) |29 sierpnia 2017 r. |--- |
| [1.1.0](#1.1.0) |13 sierpnia 2017 r. |--- |
| [1.0.0](#1.0.0) |07 lipca 2017 r. |--- |


## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat rozwiązania Cosmos bazy danych, zobacz [bazy danych programu Microsoft Azure rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/) stronę usługi. 

