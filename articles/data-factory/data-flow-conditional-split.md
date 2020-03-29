---
title: Transformacja podziału warunkowego w przepływie danych mapowania
description: Dzielenie danych na różne strumienie przy użyciu transformacji podziału warunkowego w przepływie danych mapowania usługi Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: d7e2af6c98951e685192656b37226716e4340bfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930451"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Transformacja podziału warunkowego w przepływie danych mapowania

Transformacja podziału warunkowego kieruje wiersze danych do różnych strumieni na podstawie warunków dopasowania. Transformacja podziału warunkowego jest podobna do struktury decyzji CASE w języku programowania. Transformacja ocenia wyrażenia i na podstawie wyników kieruje wiersz danych do określonego strumienia.

## <a name="configuration"></a>Konfigurowanie

**Podziel na** ustawienie określa, czy wiersz danych przepływa do pierwszego pasującego strumienia lub każdy strumień, który pasuje do.

Użyj konstruktora wyrażeń przepływu danych, aby wprowadzić wyrażenie dla warunku podziału. Aby dodać nowy warunek, kliknij ikonę plus w istniejącym wierszu. Domyślny strumień można również dodać dla wierszy, które nie pasują do żadnego warunku.

![podział warunkowy](media/data-flow/conditionalsplit1.png "warunkowe opcje podziału")

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

Poniższy przykład jest warunkową `SplitByYear` transformacją podziału `CleanData`o nazwie, która ma w strumieniu przychodzącym . Transformacja ta ma `year < 1960` `year > 1980`dwa podzielone warunki i . `disjoint`false, ponieważ dane przechodzi do pierwszego dopasowania warunek. Każdy wiersz pasujący do pierwszego `moviesBefore1960`warunku przechodzi do strumienia wyjściowego . Wszystkie pozostałe wiersze pasujące do `moviesAFter1980`drugiego warunku przechodzą do strumienia wyjściowego . Wszystkie pozostałe wiersze przepływają `AllOtherMovies`przez domyślny strumień .

W ux fabryki danych ta transformacja wygląda jak poniższy obraz:

![podział warunkowy](media/data-flow/conditionalsplit1.png "warunkowe opcje podziału")

Skrypt przepływu danych dla tej transformacji znajduje się we urywce poniżej:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Następne kroki

Typowe przekształcenia przepływu danych używane z podziałem warunkowym to [transformacja sprzężenia,](data-flow-join.md) [transformacja odnośnika](data-flow-lookup.md)i [transformacja wyboru](data-flow-select.md)
