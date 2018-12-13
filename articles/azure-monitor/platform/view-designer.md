---
title: Tworzenie widoków w celu analizowania danych w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Przy użyciu projektanta widoku w usłudze Log Analytics, można utworzyć niestandardowe widoki, które są wyświetlane w witrynie Azure portal i zawierać wiele wizualizacji danych w obszarze roboczym usługi Log Analytics. Ten artykuł zawiera omówienie Projektant widoków i przedstawiono procedury tworzenia i edytowania widoków niestandardowych.
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
ms.openlocfilehash: ec56e21a989fb0e8db7b8bafb1357c6ed64eae75
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53192264"
---
# <a name="create-custom-views-by-using-view-designer-in-log-analytics"></a>Tworzenie niestandardowych widoków przy użyciu projektanta widoku w usłudze Log Analytics
Przy użyciu projektanta widoku w [usługi Azure Log Analytics](../../azure-monitor/log-query/log-query-overview.md), można tworzyć różne widoki niestandardowe w witrynie Azure portal, która pomoże Ci wizualizować dane w obszarze roboczym usługi Log Analytics. Ten artykuł zawiera omówienie Projektant widoków i procedur tworzenia i edytowania widoków niestandardowych.

Aby uzyskać więcej informacji dotyczących projektanta widoków zobacz:

* [Kafelek odwołanie](view-designer-tiles.md): Przewodnik po odwołanie do ustawienia dla wszystkich dostępnych kafelków w widoki niestandardowe.
* [Dokumentacja części wizualizacji](view-designer-parts.md): Przewodnik po odwołanie do ustawienia dla części wizualizacji, które są dostępne w widoki niestandardowe.


## <a name="concepts"></a>Pojęcia
Widoki są wyświetlane na **Przegląd** stronie obszaru roboczego usługi Log Analytics w witrynie Azure portal. Kafelki w każdym widoku niestandardowego są wyświetlane w kolejności alfabetycznej, a kafelków rozwiązań są zainstalowane tego samego obszaru roboczego.

![Strona przeglądu](media/view-designer/overview-page.png)

Widoki, które tworzysz przy użyciu projektanta widoku zawierają elementy, które są opisane w poniższej tabeli:

| Część | Opis |
|:--- |:--- |
| Kafelki | Są wyświetlane w obszarze roboczym usługi Log Analytics **Przegląd** strony. Każdy Kafelek Wyświetla wizualnego podsumowania widoku niestandardowego, który go reprezentuje. Każdy typ kafelka zawiera inny typ wizualizacji rekordy. Możesz wybrać Kafelek, aby wyświetlić widok niestandardowy. |
| Widok niestandardowy | Wyświetlane po wybraniu kafelka. Każdy widok zawiera jedną lub więcej z części wizualizacji. |
| Części wizualizacji | Prezentowanie wizualizacji danych w obszarze roboczym usługi Log Analytics, w oparciu o co najmniej jeden [dziennikach](../../azure-monitor/log-query/log-query-overview.md). Większość elementów obejmują nagłówka, który zawiera wizualizację wysokiego poziomu, oraz listy, który wyświetla najwyższe wyniki. Każdy typ części zawiera inny typ wizualizacji rekordów w obszarze roboczym usługi Log Analytics. Możesz wybrać elementy w części, aby wykonać wyszukiwanie w dzienniku, który zawiera szczegółową dokumentację. |


## <a name="work-with-an-existing-view"></a>Praca z istniejącego widoku
Widoki, które zostały utworzone przy użyciu projektanta widoku są wyświetlane następujące opcje:

![Menu Przegląd](media/view-designer/overview-menu.png)

W poniższej tabeli opisano opcje:

| Opcja | Opis |
|:--|:--|
| Odświeżanie   | Odświeża widok przy użyciu najnowszych danych. | 
| Analiza | Otwiera [portalu analizy zaawansowanej](../../azure-monitor/log-query/portals.md) do analizowania danych za pomocą zapytań log. |
| Edytuj       | Otwiera widok w Projektancie widoku do edycji jej zawartość i konfigurację.  |
| Klonowanie      | Tworzy nowy widok, a zostanie on otwarty w Projektancie widoku. Nazwa nowego widoku jest taka sama, jak oryginalna nazwa, ale z *kopiowania* dołączone do niego. |
| Zakres dat | Ustaw datę i godzinę filtru zakresu danych, który znajduje się w widoku. Ten zakres dat jest stosowany przed wszystkie zakresy dat w zapytaniach w widoku.  |
| +          | Definiowanie niestandardowego filtru, który jest zdefiniowany dla widoku. |


## <a name="create-a-new-view"></a>Tworzenie nowego widoku
Można utworzyć nowy widok w Projektancie widoku, wybierając **Projektant widoków** w menu obszaru roboczego usługi Log Analytics.

![Wyświetlanie kafelka projektanta](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Praca z projektanta widoków
Projektant widoków umożliwia tworzenie nowych widoków lub edytować istniejące. 

Projektant widoków ma trzy okienka: 
* **Projekt**: Zawiera widok niestandardowy, który podczas tworzenia lub edycji. 
* **Formanty**: Zawiera Kafelki i części, które dodajesz do **projektowania** okienka. 
* **Właściwości**: Wyświetla właściwości kafelków lub zaznaczonych części.

![Projektant widoków](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Konfiguruj Kafelek widoku
Widok niestandardowy może mieć tylko pojedynczego kafelka. Aby wyświetlić bieżący Kafelek, lub wybierz jeden z alternatywnych, wybierz **Kafelek** karcie **kontroli** okienka. **Właściwości** okienko wyświetla właściwości bieżącego fragmentu. 

Można skonfigurować właściwości kafelka, zgodnie z informacjami w [Kafelek odwołania](view-designer-tiles.md) a następnie kliknij przycisk **Zastosuj** Aby zapisać zmiany.

### <a name="configure-the-visualization-parts"></a>Konfigurowanie części wizualizacji
Widok może zawierać dowolną liczbę części wizualizacji. Aby dodać elementy do widoku, wybierz **widoku** kartę, a następnie wybierz element wizualizacji. **Właściwości** okienko wyświetla właściwości wybranej części. 

Można skonfigurować właściwości widoku zgodnie z informacjami w [dokumentacja części wizualizacji](view-designer-parts.md) a następnie kliknij przycisk **Zastosuj** Aby zapisać zmiany.

Widoki mają tylko jeden wiersz części wizualizacji. Możesz zmienić kolejność istniejące składniki, przeciągając je do nowej lokalizacji.

Możesz usunąć część wizualizacji z widoku, wybierając **X** u góry po prawej części.


### <a name="menu-options"></a>Opcje menu
Praca z widokami w trybie edycji opcje są opisane w poniższej tabeli.

![Menu Edycja](media/view-designer/edit-menu.png)

| Opcja | Opis |
|:--|:--|
| Zapisz        | Zapisuje zmiany i zamyka widoku. |
| Cancel      | Odrzuca wszystkie zmiany i zamyka widoku. |
| Usuń widok | Usuwa widok. |
| Eksportowanie      | Eksportuje widoku na potrzeby [szablonu usługi Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) który można zaimportować do innego obszaru roboczego. Nazwa pliku jest nazwa widoku i ma *omsview* rozszerzenia. |
| Import      | Importy *omsview* pliku, który został wyeksportowany z innym obszarem roboczym. Ta akcja zastępuje konfigurację istniejącego widoku. |
| Klonowanie       | Tworzy nowy widok, a zostanie on otwarty w Projektancie widoku. Nazwa nowego widoku jest taka sama, jak oryginalna nazwa, ale z *kopiowania* dołączone do niego. |

## <a name="next-steps"></a>Kolejne kroki
* Dodaj [Kafelki](view-designer-tiles.md) do widoku niestandardowego.
* Dodaj [części wizualizacji](view-designer-parts.md) do widoku niestandardowego.
