---
title: Projektant widoku usługi Azure Monitor do konwersji kafelków skoroszytów
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f07d15521c787dfd588c285bff57616059caa2f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658630"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Konwersje kafelków widoku usługi Azure Monitor
[Projektant widoku](view-designer.md) jest funkcją usługi Azure Monitor, która umożliwia tworzenie widoków niestandardowych ułatwiające wizualizację danych w obszarze roboczym usługi Log Analytics za pomocą wykresów, list i osi czasu. Są one stopniowo wycofywane i zastępowane skoroszytami, które zapewniają dodatkowe funkcje. Ten artykuł zawiera szczegółowe informacje dotyczące konwertowania różnych kafelków na skoroszyty.

## <a name="donut--list-tile"></a>Płytka listy & pączków

![Lista pączków](media/view-designer-conversion-tiles/donut-list.png)

Ponownetworzenie kafelka listy & pączka w skoroszytach obejmuje dwie oddzielne wizualizacje. Dla porcji pączka istnieją dwie opcje.
Dla obu rozpocząć od **wybrania dodaj kwerendy** i wklej oryginalną kwerendę z projektanta widoku do komórki.

**Wariant 1:** Wybierz **wykres kołowy** z menu ![rozwijanego **Wizualizacja:** Wykres kołowy](media/view-designer-conversion-tiles/pie-chart.png)

**Wariant 2:** Wybierz **pozycję Ustaw według kwerendy** z listy rozwijanej **Wizualizacja** i dodaj `| render piechart` do kwerendy:

 ![Menu wizualizacji](media/view-designer-conversion-tiles/set-by-query.png)

**Przykład**

Oryginalna kwerenda
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

Zaktualizowana kwerenda
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

Aby utworzyć listę i włączyć wykresy przebiegu w przebiegu, zobacz artykuł dotyczący [typowych zadań](view-designer-conversion-tasks.md).

Oto przykład, w jaki sposób kafelek listy & pączka może być ponownie interpretowany w skoroszytach:

![Skoroszyty listy pączków](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>Kafelek listy wykresu liniowego &
![Lista wykresów liniowych](media/view-designer-conversion-tiles/line-list.png) 

Aby ponownie utworzyć część wykresu liniowego, zaktualizuj kwerendę w następujący sposób:

Oryginalna kwerenda
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Zaktualizowana kwerenda
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

Istnieją dwie opcje wizualizacji wykresu liniowego

**Wariant 1:** Wybierz **wykres liniowy** z listy rozwijanej **Wizualizacja:**
 
 ![Menu wykresu liniowego](media/view-designer-conversion-tiles/line-visualization.png)

**Wariant 2:** Wybierz **pozycję Ustaw według kwerendy** z listy rozwijanej **Wizualizacja** i dodaj `| render linechart` do kwerendy:

 ![Menu wizualizacji](media/view-designer-conversion-tiles/set-by-query.png)

**Przykład**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

Aby utworzyć listę i włączyć wykresy przebiegu w przebiegu, zobacz artykuł dotyczący [typowych zadań](view-designer-conversion-tasks.md).

Poniżej przedstawiono przykład, w jaki sposób kafelek & listy wykresu liniowego może być ponownie interpretowany w skoroszytach:

![Skoroszyty listy wykresów liniowych](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>Numer & kafelek listy

 ![Lista kafelków](media/view-designer-conversion-tiles/tile-list-example.png)

W przypadku kafelka liczb należy zaktualizować kwerendę w następujący sposób:

Oryginalna kwerenda
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

Zaktualizowana kwerenda
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

Zmień menu rozwijane Wizualizacja na **Kafelki,** a następnie wybierz pozycję **Ustawienia kafelków**.
 ![Wizualizacja kafelka](media/view-designer-conversion-tiles/tile-visualization.png)

Pozostaw sekcję **Tytuł** pustą i wybierz pozycję **Lewy**. Zmień wartość **kolumny Użyj:** na **Zliczanie,** a **renderowanie kolumn** na **dużą liczbę:**

![Ustawienia kafelka](media/view-designer-conversion-tiles/tile-settings.png)

 
Aby utworzyć listę i włączyć wykresy przebiegu w przebiegu, zobacz artykuł dotyczący [typowych zadań](view-designer-conversion-tasks.md).

Oto przykład, w jaki sposób kafelek listy numerów & może być ponownie interpretowany w skoroszytach:

![Skoroszyty listy numerów](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>Oś czasu i lista

 ![Lista osi czasu](media/view-designer-conversion-tiles/time-list.png)

Dla osi czasu zaktualizować kwerendę w następujący sposób:

Oryginalna kwerenda
```KQL
search * 
| sort by TimeGenerated desc
```

Zaktualizowana kwerenda
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

Istnieją dwie opcje wizualizacji kwerendy jako wykresu słupkowego:

**Wariant 1:** Wybierz **wykres słupkowy** z listy ![rozwijanej **Wizualizacja:** Wizualizacja barchart](media/view-designer-conversion-tiles/bar-visualization.png)
 
**Wariant 2:** Wybierz **pozycję Ustaw według kwerendy** z listy rozwijanej **Wizualizacja** i dodaj `| render barchart` do kwerendy:

 ![Menu wizualizacji](media/view-designer-conversion-tiles/set-by-query.png)

 
Aby utworzyć listę i włączyć wykresy przebiegu w przebiegu, zobacz artykuł dotyczący [typowych zadań](view-designer-conversion-tasks.md).

Poniżej przedstawiono przykład, w jaki sposób kafelek listy & osi czasu może być ponownie interpretowany w skoroszytach:

![Skoroszyty listy osi czasu](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>Następne kroki

- [Omówienie przejścia projektanta widoku do skoroszytów](view-designer-conversion-overview.md)
