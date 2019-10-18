---
title: Przekształcenie podziału warunkowego w Azure Data Factory mapowanie przepływu danych | Microsoft Docs
description: Dzielenie danych na różne strumienie przy użyciu transformacji podziału warunkowego w przepływie danych mapowania Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 2d794714f27340e8886843988b6c075dd8d3366e
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72527419"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Przekształcenie podziału warunkowego w mapowaniu przepływu danych

Transformacja podziału warunkowego przekierowuje wiersze danych do różnych strumieni na podstawie pasujących warunków. Przekształcenie podziału warunkowego jest podobne do struktury decyzji CASE w języku programowania. Transformacja oblicza wyrażenia, a na podstawie wyników kieruje wiersz danych do określonego strumienia.

## <a name="configuration"></a>Konfigurowanie

Ustawienie **Split on** określa, czy wiersz danych jest przepływem do pierwszego zgodnego strumienia lub każdego strumienia, do którego jest zgodny.

Użyj konstruktora wyrażeń przepływu danych, aby wprowadzić wyrażenie dla warunku podziału. Aby dodać nowy warunek, kliknij ikonę znaku plus w istniejącym wierszu. Domyślny strumień można również dodać do wierszy, które nie pasują do żadnego warunku.

![podział warunkowy](media/data-flow/conditionalsplit1.png "Opcje podziału warunkowego")

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>Przykład

Poniższy przykład to transformacja podziału warunkowego o nazwie `SplitByYear`, która przyjmuje `CleanData` strumienia przychodzącego. To przekształcenie ma dwa warunki podziału `year < 1960` i `year > 1980`. `disjoint` ma wartość false, ponieważ dane przechodzą do pierwszego pasującego warunku. Każdy wiersz pasujący do pierwszego warunku przechodzi do strumienia wyjściowego `moviesBefore1960`. Wszystkie pozostałe wiersze zgodne z drugim warunkiem przechodzą do strumienia wyjściowego `moviesAFter1980`. Wszystkie inne wiersze przepływają przez domyślny `AllOtherMovies` strumienia.

W Data Factory środowisku użytkownika Ta transformacja wygląda jak na poniższym obrazie:

![podział warunkowy](media/data-flow/conditionalsplit1.png "Opcje podziału warunkowego")

Skrypt przepływu danych dla tego przekształcenia znajduje się w poniższym fragmencie kodu:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Następne kroki

Typowe przekształcenia przepływu danych używane z podziałem warunkowym to [transformacja sprzężenia](data-flow-join.md), [transformacja wyszukiwania](data-flow-lookup.md)i [wybór transformacji](data-flow-select.md) .
