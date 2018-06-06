---
title: Zestaw .NET SDK usługi Azure interfejs API Graph CosmosDB & zasobów | Dokumentacja firmy Microsoft
description: Dowiedz się wszystkiego o Azure CosmosDB interfejsu API programu Graph w tym daty wydania, daty wycofania i zmiany wprowadzone od każdej wersji.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/17/2017
ms.author: lbosq
ms.openlocfilehash: c5975809b0f196fe88a11504549b046e910fc93c
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795928"
---
# <a name="azure-cosmos-db-graph-net-api-download-and-release-notes"></a>Azure rozwiązania Cosmos interfejs API .NET wykresu DB: Pobierz i informacje o wersji

|   |   |
|---|---|
|**Pobierz zestaw SDK**|[NuGet](https://aka.ms/acdbgraphnuget)|
|**Dokumentacja interfejsu API**|[Dokumentacji interfejsu API platformy .NET](https://aka.ms/acdbgraphapiref)|
|**Szybki start**|[Azure rozwiązania Cosmos bazy danych: Tworzenie aplikacji wykresu przy użyciu platformy .NET i interfejsu API programu Graph](create-graph-dotnet.md)|
|**Samouczek**|[Azure CosmosDB: Utworzyć kontener z interfejsu API programu Graph](tutorial-develop-graph-dotnet.md)|
|**Bieżąca platforma obsługiwane**| [Microsoft .NET Framework 4.6.1](https://www.microsoft.com/en-us/download/details.aspx?id=49981)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |


## <a name="release-notes"></a>Informacje o wersji

Zaleca się za pomocą sterownika Gremlin open source dla platformy .NET, Gremlin.Net. Dowiedz się, jak rozpocząć pracę z [bazy danych Azure rozwiązania Cosmos: tworzenie aplikacji wykresu przy użyciu platformy .NET i interfejsu API programu Graph](create-graph-dotnet.md).

### <a name="a-name031-preview031-preview"></a><a name="0.3.1-preview"/>0.3.1-Preview

#### <a name="bug-fixes"></a>Poprawki błędów
* Napraw, aby załadować opcjonalnie `appsettings.json` (`netstandard1.6`)

#### <a name="whats-new"></a>Co nowego
* Przełącz Microsoft.Azure.Graphs Docelowa platforma AnyCPU.
* Usuń zestaw Mono z `net461` manifestu pakietu.

### <a name="a-name030-preview030-preview"></a><a name="0.3.0-preview"/>0.3.0-Preview

#### <a name="whats-new"></a>Co nowego
* Dodano obsługę `.netstandard 1.6`
  * Wymaga `Microsoft.Azure.DocumentDB.Core >= 1.5.1`
* Dodano nową `gremlin-groovy` analizatora składni, aby zastąpić istniejącą analizatora. Ten parser obsługuje podzbiór w Tinkerpop `gremlin-groovy` składni i obejmuje:
  * Zwiększona wydajność podczas analizowania x 2.
  * Rozwiązane szereg problemów związanych z znak anulowanie ciągów, obsługi wartości literałów i innych nieprawidłowości w starym analizatora.
* Dodano optymalizacji dla traversals z predykaty krawędzi.
  *  Traversal przeskoków z filtrami powinna zostać wyświetlona temu, na przykład: `g.V('1').outE().has('name', 'marko').inV()`.
* Dodaje optymalizacji dla traversals z `limit()` kroku.

#### <a name="breaking-changes"></a>Zmiany powodujące niezgodność
* Usunięto obsługę .NET Framework 4.5.1

* Nowy analizator składni wyrównana z `gremlin-groovy` gramatyki. W związku z tym niektóre wyrażenia, które działały wcześniej są niejednoznaczne dla nowego analizatora. Jeden przypadek Uwaga:
  * `in` i `as` są zastrzeżone słowa kluczowe w `gremlin-groovy`, więc te kroki musi być kwalifikowana za `.in()` lub `.as()` w celu uniknięcia błędów składni. Na przykład: `g.V().repeat(in()).times(2)`  ->  _zgłasza błąd składni_  
 `g.V().repeat(__.in()).times(2)` -> _zakończy się powodzeniem_

### <a name="a-name024-preview024-preview"></a><a name="0.2.4-preview"/>0.2.4-Preview

### <a name="a-name022-preview022-preview"></a><a name="0.2.2-preview"/>0.2.2-Preview

### <a name="a-name021-preview021-preview"></a><a name="0.2.1-preview"/>0.2.1-Preview

### <a name="a-name020-preview020-preview"></a><a name="0.2.0-preview"/>0.2.0-Preview

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-Preview
* Wersja zapoznawcza początkowej.

## <a name="release--retirement-dates"></a>Wersja & wycofania dat
Firma Microsoft udostępni powiadomienia co najmniej **12 miesięcy** klienta z wyprzedzeniem wycofanie SDK w celu złagodzenia przejścia do nowszej/nieobsługiwaną wersję.

Nowe funkcje i funkcjonalność i optymalizację, które są dodawane tylko do bieżącego zestawu SDK, w związku jest zalecane, zawsze Uaktualnij zestaw SDK najnowszą tak szybko jak to możliwe. 

Każde żądanie do bazy danych rozwiązania Cosmos Azure przy użyciu wycofane zestawu SDK będą odrzucane przez usługę.

<br/>

| Wersja | Data wydania | Dacie wycofania |
| --- | --- | --- |
| [0.3.1-Preview](#0.3.1-preview) |17 października 2017 r. |--- |
| [0.3.0-Preview](#0.3.0-preview) |2 października 2017 r. |--- |
| [0.2.4-preview](#0.2.4-preview) |4 sierpnia 2017 r. |--- |
| [0.2.2-Preview](#0.2.2-preview) |23 czerwca 2017 r. |--- |
| [0.2.1-Preview](#0.2.2-preview) |8 czerwca 2017 r. |--- |
| [0.2.0-Preview](#0.2.2-preview) |10 maja 2017 |--- |
| [0.1.0-Preview](#0.1.0-preview) |8 maja 2017 r. |--- |

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat interfejsu API Graph usługi Azure rozwiązania Cosmos bazy danych, zobacz [wprowadzenie do bazy danych Azure rozwiązania Cosmos: interfejs API programu Graph](graph-introduction.md). 
