---
title: Tworzenie widoków w celu analizowania danych dziennika w Azure Monitor | Microsoft Docs
description: Za pomocą projektanta widoków w Azure Monitor można utworzyć niestandardowe widoki, które są wyświetlane w Azure Portal i zawierać różne wizualizacje danych w obszarze roboczym Log Analytics. Ten artykuł zawiera omówienie projektanta widoków i prezentuje procedury tworzenia i edytowania widoków niestandardowych.
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
ms.openlocfilehash: 33930823fbeb42011d8e2a368d17c9a21070a243
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035598"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Tworzenie widoków niestandardowych za pomocą projektanta widoków w Azure Monitor
Za pomocą projektanta widoków w Azure Monitor można utworzyć różne widoki niestandardowe w Azure Portal, które mogą ułatwić wizualizację danych w obszarze roboczym Log Analytics. W tym artykule przedstawiono omówienie projektanta widoków i procedur tworzenia i edytowania widoków niestandardowych.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Aby uzyskać więcej informacji na temat projektanta widoków, zobacz:

* [Odwołanie](view-designer-tiles.md)do kafelka: Zawiera Przewodnik referencyjny dotyczący ustawień dla każdego z dostępnych kafelków w widokach niestandardowych.
* [Odwołanie do części wizualizacji](view-designer-parts.md): Zawiera Przewodnik referencyjny dotyczący ustawień części wizualizacji, które są dostępne w niestandardowych widokach.


## <a name="concepts"></a>Pojęcia
Widoki są wyświetlane na stronie **przegląd** Azure Monitor w Azure Portal. Otwórz Tę stronę z menu **Azure monitor** , klikając pozycję **więcej** w sekcji **szczegółowe informacje** . Kafelki w poszczególnych widokach niestandardowych są wyświetlane alfabetycznie, a kafelki rozwiązań do monitorowania są zainstalowane w tym samym obszarze roboczym.

![Strona przeglądu](media/view-designer/overview-page.png)

Widoki tworzone za pomocą projektanta widoków zawierają elementy, które są opisane w poniższej tabeli:

| Części | Opis |
|:--- |:--- |
| Kafelki | Są wyświetlane na stronie **przegląd** Azure monitor. Każdy kafelek zawiera podsumowanie wizualizacji widoku niestandardowego, który reprezentuje. Każdy typ kafelków zawiera inną wizualizację rekordów. Wybierz kafelek, aby wyświetlić widok niestandardowy. |
| Widok niestandardowy | Wyświetlane po wybraniu kafelka. Każdy widok zawiera jedną lub więcej części wizualizacji. |
| Części wizualizacji | Prezentowanie danych w obszarze roboczym Log Analytics w oparciu o co najmniej jedną [kwerendę dzienników](../log-query/log-query-overview.md). Większość części zawiera nagłówek, który zawiera wizualizację wysokiego poziomu i listę, która wyświetla najważniejsze wyniki. Każdy typ części zawiera inną wizualizację rekordów w obszarze roboczym Log Analytics. Wybierz elementy w części, aby wykonać zapytanie dziennika, które zawiera szczegółowe rekordy. |

## <a name="required-permissions"></a>Wymagane uprawnienia
Musisz mieć co najmniej [uprawnienia poziomu współautor](manage-access.md#manage-access-using-azure-permissions) w obszarze roboczym log Analytics, aby tworzyć lub modyfikować widoki. Jeśli nie masz tego uprawnienia, opcja Projektant widoków nie zostanie wyświetlona w menu.


## <a name="work-with-an-existing-view"></a>Pracuj z istniejącym widokiem
Widoki, które zostały utworzone za pomocą projektanta widoków, wyświetlają następujące opcje:

![Menu przegląd](media/view-designer/overview-menu.png)

Opcje są opisane w poniższej tabeli:

| Opcja | Opis |
|:--|:--|
| Odśwież   | Odświeża widok przy użyciu najnowszych danych. | 
| Dzienniki      | Otwiera [log Analytics](../log-query/portals.md) , aby analizować dane przy użyciu zapytań dzienników. |
| Edytuj       | Otwiera widok w projektancie widoków, aby edytować jego zawartość i konfigurację.  |
| Klonowanie      | Tworzy nowy widok i otwiera go w projektancie widoków. Nazwa nowego widoku jest taka sama jak oryginalna nazwa, ale z dołączoną do niej *kopią* . |
| Zakres dat | Ustaw filtr zakresu daty i godziny dla danych uwzględnionych w widoku. Ten zakres dat jest stosowany przed wszystkimi zakresami dat ustawionymi w zapytaniach w widoku.  |
| +          | Zdefiniuj filtr niestandardowy, który jest zdefiniowany dla widoku. |


## <a name="create-a-new-view"></a>Tworzenie nowego widoku
Nowy widok można utworzyć w projektancie widoku, wybierając pozycję **Widok projektanta** w menu obszaru roboczego log Analytics.

![Kafelek projektanta widoków](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Pracuj z projektantem widoku
Za pomocą projektanta widoków można tworzyć nowe widoki lub edytować istniejące. 

Projektant widoków ma trzy okienka: 
* **Projekt**: Zawiera niestandardowy widok, który tworzysz lub edytujesz. 
* **Kontrolki**: Zawiera kafelki i części dodawane do okienka **projektowania** . 
* **Właściwości**: Wyświetla właściwości kafelków lub wybranych części.

![Pokaż projektanta](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Konfigurowanie kafelka widoku
Widok niestandardowy może mieć tylko jeden kafelek. Aby wyświetlić bieżący kafelek lub wybrać alternatywę, wybierz kartę **kafelek** w okienku **sterowania** . W okienku **Właściwości** zostaną wyświetlone właściwości bieżącego kafelka. 

Właściwości kafelka można skonfigurować zgodnie z informacjami w [Kompendium kafelka](view-designer-tiles.md) , a następnie kliknąć przycisk **Zastosuj** , aby zapisać zmiany.

### <a name="configure-the-visualization-parts"></a>Konfigurowanie części wizualizacji
Widok może zawierać dowolną liczbę części wizualizacji. Aby dodać części do widoku, wybierz kartę **Widok** , a następnie wybierz część wizualizacji. W okienku **Właściwości** zostaną wyświetlone właściwości wybranej części. 

Można skonfigurować właściwości widoku zgodnie z informacjami w [Kompendium części wizualizacji](view-designer-parts.md) , a następnie kliknąć przycisk **Zastosuj** , aby zapisać zmiany.

Widoki mają tylko jeden wiersz części wizualizacji. Istniejące części można zmienić, przeciągając je do nowej lokalizacji.

Możesz usunąć część wizualizacji z widoku, wybierając **symbol X** w prawym górnym rogu części.


### <a name="menu-options"></a>Opcje menu
W poniższej tabeli opisano opcje pracy z widokami w trybie edycji.

![Menu Edycja](media/view-designer/edit-menu.png)

| Opcja | Opis |
|:--|:--|
| Zapisanie        | Zapisuje zmiany i zamyka widok. |
| Cancel      | Odrzuca zmiany i zamyka widok. |
| Usuń widok | Usuwa widok. |
| Eksportowanie      | Eksportuje widok do [szablonu Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) , który można zaimportować do innego obszaru roboczego. Nazwa pliku to nazwa widoku, która ma rozszerzenie *omsview* . |
| Import      | Importuje plik *omsview* wyeksportowany z innego obszaru roboczego. Ta akcja zastępuje konfigurację istniejącego widoku. |
| Klonowanie       | Tworzy nowy widok i otwiera go w projektancie widoków. Nazwa nowego widoku jest taka sama jak oryginalna nazwa, ale z dołączoną do niej *kopią* . |

## <a name="next-steps"></a>Następne kroki
* Dodaj [kafelki](view-designer-tiles.md) do widoku niestandardowego.
* Dodaj [części wizualizacji](view-designer-parts.md) do widoku niestandardowego.
