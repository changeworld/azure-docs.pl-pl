---
title: Filtry w widokach monitora platformy Azure | Dokumenty firmy Microsoft
description: Filtr w widoku usługi Azure Monitor umożliwia użytkownikom filtrowanie danych w widoku według wartości określonej właściwości bez modyfikowania samego widoku.  W tym artykule opisano sposób używania filtru i dodawania go do widoku niestandardowego.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: b4840ed30eb1f6dc8d6e6cef47da17807f9644d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658578"
---
# <a name="filters-in-azure-monitor-views"></a>Filtry w widokach monitora platformy Azure
**Filtr** w [widoku usługi Azure Monitor](view-designer.md) umożliwia użytkownikom filtrowanie danych w widoku według wartości określonej właściwości bez modyfikowania samego widoku.  Można na przykład zezwolić użytkownikom widoku na filtrowanie widoku danych tylko z określonego komputera lub zestawu komputerów.  Można utworzyć wiele filtrów w jednym widoku, aby umożliwić użytkownikom filtrowanie według wielu właściwości.  W tym artykule opisano sposób używania filtru i dodawania go do widoku niestandardowego.

## <a name="using-a-filter"></a>Korzystanie z filtru
Kliknij zakres daty w górnej części widoku, aby otworzyć rozwijaną, w której można zmienić zakres dat dla widoku.

![Przykład filtru](media/view-designer-filters/filters-example-time.png)

Kliknij, **+** aby dodać filtr przy użyciu filtrów niestandardowych zdefiniowanych dla widoku. Wybierz wartość filtru z listy rozwijanej lub wpisz wartość. Kontynuuj dodawanie filtrów, klikając **+** przycisk . 


![Przykład filtru](media/view-designer-filters/filters-example-custom.png)

Jeśli usuniesz wszystkie wartości filtru, ten filtr nie będzie już stosowany.


## <a name="creating-a-filter"></a>Tworzenie filtru

Podczas [edytowania widoku](view-designer.md)należy utworzyć filtr na karcie **Filtry** .  Filtr jest globalny dla widoku i ma zastosowanie do wszystkich części w widoku.  

![Ustawienia filtru](media/view-designer-filters/filters-settings.png)

W poniższej tabeli opisano ustawienia filtru.

| Ustawienie | Opis |
|:---|:---|
| Nazwa pola | Nazwa pola używanego do filtrowania.  To pole musi być zgodne z polem podsumowania w **polu Kwerenda dla wartości**. |
| Kwerenda dla wartości | Kwerenda do uruchomienia w celu wypełnienia listy rozwijanej filtru dla użytkownika.  Ta kwerenda musi używać [podsumowywania](/azure/kusto/query/summarizeoperator) lub [wyróżniania,](/azure/kusto/query/distinctoperator) aby zapewnić unikatowe wartości dla określonego pola i musi być zgodna z **nazwą pola**.  Sortowania [sort](/azure/kusto/query/sortoperator) można używać do sortowania wartości wyświetlanych użytkownikowi. |
| Tag | Nazwa pola, które jest używane w kwerendach obsługujących filtr i jest również wyświetlana użytkownikowi. |

### <a name="examples"></a>Przykłady

Poniższa tabela zawiera kilka przykładów typowych filtrów.  

| Nazwa pola | Kwerenda dla wartości | Tag |
|:--|:--|:--|
| Computer (Komputer)   | Bicie serca &#124; różne &#124; komputerowe sortowane przez komputer asc | Komputery |
| Nazwa eventlevelname | Zdarzenie &#124; różne EventLevelName | Ważność |
| WażnośćPoziom | Syslog &#124; różne SeverityLevel | Ważność |
| SvcChangeType (SvcChangeType) | ConfigurationChange &#124; distinct svcChangeType | Changetype |


## <a name="modify-view-queries"></a>Modyfikowanie kwerend widoku

Aby filtr miał jakikolwiek wpływ, należy zmodyfikować wszystkie kwerendy w widoku, aby filtrować wybrane wartości.  Jeśli nie zmodyfikujesz żadnych zapytań w widoku, wszelkie wartości wybrane przez użytkownika nie będą miały wpływu.

Składnia używania wartości filtru w kwerendzie jest: 

    where ${filter name}  

Na przykład jeśli w widoku jest kwerenda, która zwraca zdarzenia i używa filtru o nazwie _Komputery,_ można użyć następującej kwerendy.

    Event | where ${Computers} | summarize count() by EventLevelName

Jeśli dodano inny filtr o nazwie Ważność, można użyć następującej kwerendy, aby użyć obu filtrów.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [częściach wizualizacji,](view-designer-parts.md) które można dodać do widoku niestandardowego.
