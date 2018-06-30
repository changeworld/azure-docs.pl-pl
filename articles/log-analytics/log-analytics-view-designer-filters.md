---
title: Filtry w widokach Azure Log Analytics | Dokumentacja firmy Microsoft
description: Filtr w widoku analizy dzienników umożliwia filtrowanie danych w widoku przez wartość określonej właściwości bez modyfikowania samego widoku.  W tym artykule opisano sposób używania filtru i dodaj je do widoku niestandardowego.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 0ad22562bd1f36bba7c0ab99fe504e82645033d3
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131414"
---
# <a name="filters-in-log-analytics-views"></a>Filtry w widokach analizy dzienników
A **filtru** w [wyświetlić analizy dzienników](log-analytics-view-designer.md) umożliwia filtrowanie danych w widoku przez wartość określonej właściwości bez modyfikowania samego widoku.  Na przykład może pozwolić użytkownikom widoku, aby filtrować widok danych tylko z określonego komputera lub zestawu komputerów.  Można utworzyć wiele filtrów w jednym widoku pozwoli użytkownikom na filtrowanie przez wiele właściwości.  W tym artykule opisano sposób używania filtru i dodaj je do widoku niestandardowego.

## <a name="using-a-filter"></a>Przy użyciu filtru
Kliknij zakres czasu danych u góry widoku, aby otworzyć listy rozwijanej, którym można zmienić zakres czasu danych widoku.

![Przykład filtru](media/log-analytics-view-designer/filters-example-time.png)

Kliknij przycisk **+** Aby dodać filtr przy użyciu filtrów niestandardowych, które są zdefiniowane dla widoku. Z listy rozwijanej lub wpisz wartość, albo wybrać wartość filtru. Dodaj filtry, klikając w dalszym ciągu **+**. 


![Przykład filtru](media/log-analytics-view-designer/filters-example-custom.png)

Jeśli usuniesz wszystkie wartości filtru tego filtru nie będzie można zastosować.


## <a name="creating-a-filter"></a>Utworzenie filtru

Utwórz filtr z **filtry** karcie kiedy [Edytowanie widoku](log-analytics-view-designer.md).  Filtr jest globalne dla widoku i ma zastosowanie do wszystkich części w widoku.  

![Ustawienia filtra](media/log-analytics-view-designer/filters-settings.png)

W poniższej tabeli opisano ustawienia filtru.

| Ustawienie | Opis |
|:---|:---|
| Nazwa pola | Nazwa pola używanego podczas filtrowania.  Musi on być zgodny z pola podsumowanie w **zapytanie o wartości**. |
| Zapytanie o wartości | Kwerendy do wypełnienia listy rozwijanej filtru dla użytkownika.  To należy użyć [Podsumuj](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) lub [różne](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator) zapewniają unikatowe wartości określonego pola, a musi być zgodna **Nazwa pola**.  Można użyć [sortowania](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator) Aby posortować wartości, które są wyświetlane dla użytkownika. |
| Tag | Nazwa pola, które jest używane w zapytaniach obsługujące filtr i jest wyświetlany użytkownikowi. |

### <a name="examples"></a>Przykłady

W poniższej tabeli przedstawiono kilka przykładów typowych filtrów.  

| Nazwa pola | Zapytanie o wartości | Tag |
|:--|:--|:--|
| Computer (Komputer)   | Puls &#124; distinct Computer &#124; Sortuj według asc komputera | Komputery |
| EventLevelName | Zdarzenia &#124; EventLevelName różne | Ważność |
| Poziom ważności | SYSLOG &#124; różne poziom ważności | Ważność |
| SvcChangeType | Zmianakonfiguracji &#124; svcChangeType różne | ChangeType |


## <a name="modify-view-queries"></a>Zmodyfikuj widok kwerendy

Filtr, aby zostały uwzględnione należy zmodyfikować żadnych zapytań w widoku, aby filtrować według wybranych wartości.  Jeśli nie zmodyfikujesz żadnych zapytań w widoku wartości, które użytkownik wybierze odniesie żadnego skutku.

Składnia przy użyciu wartości filtru w zapytaniu jest: 

    where ${filter name}  

Na przykład jeśli widok ma zapytanie zwraca zdarzeń i używa filtru o nazwie komputery, można użyć następujących czynności.

    Event | where ${Computers} | summarize count() by EventLevelName

Jeśli dodano inny filtr o nazwie ważność można Użyj następującego zapytania, używać obu filtrów.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [części wizualizacji](log-analytics-view-designer-parts.md) można dodać do niego.