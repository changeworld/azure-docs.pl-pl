---
title: Projektant widoku usługi Azure Monitor do typowych zadań konwersji skoroszytów
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 5559dac916262998d621b40757398088ec613609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658748"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>Wyświetlanie typowych zadań konwersji projektanta w skoroszytach
[Projektant widoku](view-designer.md) jest funkcją usługi Azure Monitor, która umożliwia tworzenie widoków niestandardowych ułatwiające wizualizację danych w obszarze roboczym usługi Log Analytics za pomocą wykresów, list i osi czasu. Są one stopniowo wycofywane i zastępowane skoroszytami, które zapewniają dodatkowe funkcje. W tym artykule opisano zadania typowe podczas konwertowania widoków na skoroszyty.


## <a name="quickstart-with-preset-view-designer-templates"></a>Szybki start z wstępnie ustawionymi szablonami projektanta widoku

Skoroszyty w obszarach roboczych usługi Log Analytics mają już szablony wykonane w taki sposób, aby pasowały do niektórych widoków w projektancie widoku. W kategorii **Wyświetl prowadnice projektanta** wybierz pozycję **Wyświetl przewodnik po przejściu projektanta,** aby dowiedzieć się więcej o opcjach, lub wybierz jeden z gotowych szablonów.

![Przykładowe szablony](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>Włączanie filtru zakresu czasu
Projektant widoku ma wbudowany domyślny filtr zakresu czasu, jednak w skoroszytach to ustawienie nie jest domyślnie włączone. Skoroszyty umożliwiają użytkownikom tworzenie własnych filtrów zakresu czasu, które mogą mieć większe zastosowanie do dzienników danych. Kroki generowania filtru są wymienione poniżej:

Wybierz opcję **Dodaj parametry.** Domyślny **styl** jest ustawiony na *Pigułki*.

![Dodaj param](media/view-designer-conversion-tasks/add-param.png)

 Wybierz przycisk **Dodaj parametr.**

![Dodaj parametr](media/view-designer-conversion-tasks/add-parameter.png)

Z menu paska bocznego w pola tekstowym **Nazwa parametru** wpisz *TimeRange*. Ustaw **typ parametru** jako *selektor zakresu czasu*. Zaznacz pole wyboru **Wymagane?**

![Menu parametrów](media/view-designer-conversion-tasks/parameter-menu.png)

Zapisz parametr w lewym górnym rogu menu paska bocznego. Można pozostawić listy rozwijanej jako *nierozstawionej* domyślnie lub wybrać domyślną wartość **TimeRange,** taką jak *24 godziny*. Wybierz **pozycję Gotowe edytowanie**.

Parametry mogą być używane w kwerendach, {} dodając nawiasy klamrowe wokół nazwy parametru. Więcej informacji na temat parametrów można znaleźć w [dokumentacji skoroszytów na temat parametrów](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md).

## <a name="updating-queries-with-the-timerange-parameter"></a>Aktualizowanie zapytań za pomocą parametru TimeRange

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>Opcja 1: Wybierz timerange z listy rozwijanej Zakres czasu

![Parametr czasu](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>Opcja 2: Aktualizowanie zapytań dziennika

W zapytaniu dodaj `| where TimeGenerated {TimeRange}` wiersz: jak w poniższym przykładzie:

Oryginalna kwerenda
```KQL
search * 
| summarize count() by Type
```

Zaktualizowana kwerenda
```KQL
search * 
| where TimeGenerated {TimeRange} 
| summarize count() by Type
```

## <a name="including-a-list"></a>W tym lista
Większość widoków projektanta widoku zawiera listę i można odtworzyć tę standardową listę w skoroszycie.

![Lista kafelków](media/view-designer-conversion-tasks/tile-list.png)

Dodaj wizualizację, klikając pozycję **Dodaj zapytanie** z opcji komórki.

![Dodaj param](media/view-designer-conversion-tasks/add-param.png)

Projektant widoku używa kwerendy domyślnej, która pasuje do składni z oryginalnego przykładu. Można to zaktualizować, zmieniając kwerendę na zaktualizowany formularz, tak jak w poniższym przykładzie:

Oryginalna kwerenda
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Zaktualizowana kwerenda
```KQL
search * 
| summarize Count = count() by Type
```

Spowoduje to wygenerowanie listy, która wygląda podobnie do następujących:

![Przykład listy](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>Włączanie wykresów przebiegu w przebiegu w przebiegu
Częstą cechą dla siatek jest dodanie wykresów przebiegu w czasie w celu podsumowania różnych wzorców danych w czasie. Projektant widoku oferuje funkcję **Włącz wykresy przebiegu w przebiegu dla** wszystkich list, podobnie jak skoroszyty. Aby uwzględnić wykresy przebiegu w przebiegu w przebiegu w przebiegu w danych, które pasują do projektanta widoku, dołącz do danych z oryginalną kwerendą, tak jak w poniższym przykładzie:

Oryginalna kwerenda
```KQL
search *
| summarize AggregatedValue = count() by Type) on Type
```

Zaktualizowana kwerenda
```KQL
search * 
| summarize AggregatedValue = count() by Type
| join kind = inner (search * 
    | make-series Trend = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type) on Type
| project Type, AggregatedValue, Trend
```

Wybierz **pozycję Ustawienia kolumn**.
![Ustawienia kolumny](media/view-designer-conversion-tasks/column-settings.png)

Zaktualizuj listy rozwijane **modułu renderowania kolumn** jako *obszar iskierki*.
![Sparklines](media/view-designer-conversion-tasks/sparkline.png)

Zapisz ustawienia i uruchom kwerendę ponownie, aby zaktualizować tabelę w celu uwzględnienia wykresu przebiegu w przebiegu w przebiegu.

Wynikowa siatka będzie wyglądać ![podobnie do następującego: przykład wykresu przebiegu w przebiegu w przebiegu w przebiegu](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>Zaawansowane ustawienia komórek
Aby odblodawać odbicie lustrzane projektanta widoku, można wykonywać zadania, takie jak zmiana rozmiaru komórek skoroszytu lub dodawanie pinów i łączy zewnętrznych do dzienników.

Aby uzyskać dostęp do **ustawień zaawansowanych,** wybierz ikonę koła zębatego u dołu każdej komórki.

![Ustawienia zaawansowane](media/view-designer-conversion-tasks/advanced-settings.png)

Spowoduje to wyświetlenie menu z różnymi opcjami:

![Ustawienia zaawansowane](media/view-designer-conversion-tasks/advanced-settings-settings.png)

Aby dodać pinezkę i łącze do kwerendy zewnętrznej, zaznacz odpowiednie pola wyboru. Aby dodać tytuł do komórki, wpisz żądany tytuł w sekcji **Tytuł wykresu.**

Domyślnie każda komórka skoroszytów jest ustawiona na całą szerokość strony, ale można ją dostosować, skalując komórkę w dół na karcie **Styl** w menu **Ustawienia zaawansowane**

![Styl ustawień zaawansowanych](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>Dodatkowe parametry
Wybierz **pozycję Dodaj parametr,** aby utworzyć nowy parametr w skoroszycie. 

Aby wybrać subskrypcję, wpisz *subskrypcję* w polu **Nazwa parametru** w menu bocznym i wybierz selektor *subskrypcji* z listy rozwijanej **Typ parametru**

![Menu subskrypcji](media/view-designer-conversion-tasks/subscription-filter.png)

Aby wybrać zasób, wpisz *zasób* w polu **Nazwa parametru** w menu bocznym i wybierz pozycję *Selektor zasobów* z listy rozwijanej **Typ parametru.**

![Menu zasobów](media/view-designer-conversion-tasks/resource-filter.png)

Spowoduje to wstawienie listy rozwijanej, aby umożliwić dostęp do różnych subskrypcji i zasobów.

![Rozwijanie zasobów subskrypcji](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>Następne kroki
- [Konwersje kafelków](view-designer-conversion-tiles.md)
