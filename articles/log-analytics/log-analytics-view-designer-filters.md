---
title: Filtry w widokach w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Filtr w widoku do usługi Log Analytics umożliwia użytkownikom do filtrowania danych w widoku przez wartość określonej właściwości bez modyfikowania samego widoku.  W tym artykule opisano sposób użycia filtr i dodaj je do widoku niestandardowego.
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
ms.openlocfilehash: dcdc84f100ce534ea517f0201b0c090c3059a318
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47160933"
---
# <a name="filters-in-log-analytics-views"></a>Filtry w widokach usługi Log Analytics
A **filtru** w [usługi Log Analytics przeglądać](log-analytics-view-designer.md) umożliwia użytkownikom do filtrowania danych w widoku przez wartość określonej właściwości bez modyfikowania samego widoku.  Na przykład możesz zezwolić użytkownikom widoku filtrowanie widoku danych tylko z określonego komputera lub zestawu komputerów.  Można utworzyć wiele filtrów w jednym widoku, który użytkownicy mogą filtrować według wielu właściwości.  W tym artykule opisano sposób użycia filtr i dodaj je do widoku niestandardowego.

## <a name="using-a-filter"></a>Przy użyciu filtru
Kliknij zakres czasu danych, u góry widoku, aby otworzyć rozwijanego którym można zmienić zakres czasu danych widoku.

![Przykład filtru](media/log-analytics-view-designer/filters-example-time.png)

Kliknij przycisk **+** Aby dodać filtr, za pomocą filtrów niestandardowych, które są zdefiniowane dla widoku. Z listy rozwijanej lub wpisz wartość, albo wybierz wartość dla filtru. Dodaj filtry, klikając przycisk w dalszym ciągu **+**. 


![Przykład filtru](media/log-analytics-view-designer/filters-example-custom.png)

Jeśli usuniesz wszystkie wartości filtru, a następnie z filtrem nie zostaną zastosowane.


## <a name="creating-a-filter"></a>Utworzenie filtru

Utwórz filtr z **filtry** kartę, kiedy [widoku do edycji](log-analytics-view-designer.md).  Filtr jest globalne dla widoku i dotyczy wszystkich części widoku.  

![Ustawienia filtra](media/log-analytics-view-designer/filters-settings.png)

W poniższej tabeli opisano ustawienia filtru.

| Ustawienie | Opis |
|:---|:---|
| Nazwa pola | Nazwa pola używane do filtrowania.  To musi odpowiadać polu Podsumowanie w **zapytanie o wartości**. |
| Zapytanie o wartości | Zapytanie, aby uruchomić, aby wypełnić listę rozwijaną Filtr dla użytkownika.  To należy użyć [Podsumuj](/azure/kusto/query/summarizeoperator) lub [distinct](/azure/kusto/query/distinctoperator) zapewnienie unikatowe wartości dla określonego pola, a musi być zgodna **nazwę pola**.  Możesz użyć [sortowania](/azure/kusto/query/sortoperator) Aby posortować wartości, które są wyświetlane użytkownikowi. |
| Tag | Nazwa pola, które jest używany w zapytaniach obsługujące filtr i jest wyświetlany użytkownikowi. |

### <a name="examples"></a>Przykłady

Poniższa tabela zawiera kilka przykładów typowych filtrów.  

| Nazwa pola | Zapytanie o wartości | Tag |
|:--|:--|:--|
| Computer (Komputer)   | Puls &#124; distinct Computer &#124; sortowania przez usługę asc komputera | Komputery |
| EventLevelName | Zdarzenie &#124; EventLevelName odrębne | Ważność |
| SeverityLevel | SYSLOG &#124; SeverityLevel odrębne | Ważność |
| SvcChangeType | Zmianakonfiguracji &#124; svcChangeType odrębne | ChangeType |


## <a name="modify-view-queries"></a>Modyfikowanie widoku zapytań

Filtr, aby mieć żadnego efektu należy zmodyfikować wszelkie zapytania w widoku do filtrowania na podstawie wybranych wartości.  Jeśli użytkownik nie należy modyfikować żadnych zapytań w widoku wszystkie wartości, które użytkownik wybierze odniesie żadnego skutku.

Składnia przy użyciu wartości filtru w zapytaniu jest następująca: 

    where ${filter name}  

Na przykład jeśli widok ma zapytanie zwraca zdarzeń, używa filtru o nazwie komputery można następujące czynności.

    Event | where ${Computers} | summarize count() by EventLevelName

Jeśli dodano inny filtr o nazwie ważności można Użyj następującego zapytania, używać obu filtrów.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [części wizualizacji](log-analytics-view-designer-parts.md) można dodać do widoku niestandardowego.