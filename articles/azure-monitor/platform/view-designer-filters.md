---
title: Filtry w widokach usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Filtr w widoku usługi Azure Monitor umożliwia użytkownikom do filtrowania danych w widoku przez wartość określonej właściwości bez modyfikowania samego widoku.  W tym artykule opisano sposób użycia filtr i dodaj je do widoku niestandardowego.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: 31a902302ba806889854330c6517d9f5745f1c0c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60551722"
---
# <a name="filters-in-azure-monitor-views"></a>Filtry w widokach usługi Azure Monitor
A **filtru** w [widok usługi Azure Monitor](view-designer.md) umożliwia użytkownikom do filtrowania danych w widoku przez wartość określonej właściwości bez modyfikowania samego widoku.  Na przykład możesz zezwolić użytkownikom widoku filtrowanie widoku danych tylko z określonego komputera lub zestawu komputerów.  Można utworzyć wiele filtrów w jednym widoku, który użytkownicy mogą filtrować według wielu właściwości.  W tym artykule opisano sposób użycia filtr i dodaj je do widoku niestandardowego.

## <a name="using-a-filter"></a>Przy użyciu filtru
Kliknij zakres czasu daty u góry widoku, aby otworzyć rozwijanego którym można zmienić zakres czasu daty dla widoku.

![Przykład filtru](media/view-designer-filters/filters-example-time.png)

Kliknij przycisk **+** Aby dodać filtr, za pomocą filtrów niestandardowych, które są zdefiniowane dla widoku. Z listy rozwijanej lub wpisz wartość, albo wybierz wartość dla filtru. Dodaj filtry, klikając przycisk w dalszym ciągu **+** . 


![Przykład filtru](media/view-designer-filters/filters-example-custom.png)

Jeśli usuniesz wszystkie wartości filtru, a następnie z filtrem nie zostaną zastosowane.


## <a name="creating-a-filter"></a>Utworzenie filtru

Utwórz filtr z **filtry** kartę, kiedy [widoku do edycji](view-designer.md).  Filtr jest globalne dla widoku i dotyczy wszystkich części widoku.  

![Ustawienia filtru](media/view-designer-filters/filters-settings.png)

W poniższej tabeli opisano ustawienia filtru.

| Ustawienie | Opis |
|:---|:---|
| Nazwa pola | Nazwa pola używane do filtrowania.  To pole musi odpowiadać polu Podsumowanie w **zapytanie o wartości**. |
| Zapytanie o wartości | Zapytanie, aby uruchomić, aby wypełnić listę rozwijaną Filtr dla użytkownika.  To zapytanie musi użyć [Podsumuj](/azure/kusto/query/summarizeoperator) lub [distinct](/azure/kusto/query/distinctoperator) zapewnienie unikatowe wartości dla określonego pola, a musi być zgodna **nazwę pola**.  Możesz użyć [sortowania](/azure/kusto/query/sortoperator) Aby posortować wartości, które są wyświetlane użytkownikowi. |
| Tag | Nazwa pola, które jest używany w zapytaniach obsługujące filtr i jest wyświetlany użytkownikowi. |

### <a name="examples"></a>Przykłady

Poniższa tabela zawiera kilka przykładów typowych filtrów.  

| Nazwa pola | Zapytanie o wartości | Tag |
|:--|:--|:--|
| Computer (Komputer)   | Puls &#124; distinct Computer &#124; sortowania przez usługę asc komputera | Komputery |
| EventLevelName | Event &#124; distinct EventLevelName | Severity |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | Severity |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Modyfikowanie widoku zapytań

Filtr, aby mieć żadnego efektu należy zmodyfikować wszelkie zapytania w widoku do filtrowania na podstawie wybranych wartości.  Jeśli użytkownik nie należy modyfikować żadnych zapytań w widoku, wszystkie wartości, które użytkownik wybierze odniesie żadnego skutku.

Składnia przy użyciu wartości filtru w zapytaniu jest następująca: 

    where ${filter name}  

Na przykład, jeśli widok ma kwerendę, która zwraca zdarzenia i używa filtru o nazwie _komputerów_, można użyć następującego zapytania.

    Event | where ${Computers} | summarize count() by EventLevelName

Jeśli dodano inny filtr o nazwie ważności można Użyj następującego zapytania, używać obu filtrów.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [części wizualizacji](view-designer-parts.md) można dodać do widoku niestandardowego.
