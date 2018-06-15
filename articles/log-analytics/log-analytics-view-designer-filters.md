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
ms.topic: article
ms.date: 10/31/2017
ms.author: bwren
ms.openlocfilehash: 5c2201292eb085dcc043e4257580c7971dbaffbd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "23945884"
---
# <a name="filters-in-log-analytics-views"></a>Filtry w widokach analizy dzienników
A **filtru** w [wyświetlić analizy dzienników](log-analytics-view-designer.md) umożliwia filtrowanie danych w widoku przez wartość określonej właściwości bez modyfikowania samego widoku.  Na przykład może pozwolić użytkownikom widoku, aby filtrować widok danych tylko z określonego komputera lub zestawu komputerów.  Można utworzyć wiele filtrów w jednym widoku pozwoli użytkownikom na filtrowanie przez wiele właściwości.  W tym artykule opisano sposób używania filtru i dodaj je do widoku niestandardowego.

## <a name="using-a-filter"></a>Przy użyciu filtru
Kliknij przycisk **filtru** aby otworzyć okienko filtru dla widoku.  Dzięki temu można wybrać zakres czasu i wartości dla wszystkie filtry, które są dostępne dla widoku.  Po wybraniu filtru Wyświetla listę dostępnych wartości.  Można wybrać jedną lub więcej wartości lub wpisz je w. Widok jest automatycznie aktualizowany można filtrować według wartości, które określisz. 

Jeśli jest wybrana żadna wartość filtru, filtr ten nie jest stosowana do widoku.  Jeśli usuniesz wszystkie wartości filtru tego filtru nie będzie można zastosować.


![Przykład filtru](media/log-analytics-view-designer/filters-example.png)


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
| Komputer   | Puls &#124; distinct Computer &#124; Sortuj według asc komputera | Komputery |
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