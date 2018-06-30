---
title: Tworzenie widoków w celu analizowania danych w Azure Log Analytics | Dokumentacja firmy Microsoft
description: Przy użyciu projektanta widoków analizy dzienników, można utworzyć niestandardowe widoki, które są wyświetlane w portalu Azure i zawierają różne wizualizacje danych w obszarze roboczym analizy dzienników. Ten artykuł zawiera omówienie projektanta widoków i przedstawia procedury tworzenia i edytowania widoków niestandardowych.
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
ms.openlocfilehash: d900a778aa003357fb85d3e8ec8e40dee64f0f9c
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131858"
---
# <a name="create-custom-views-by-using-view-designer-in-log-analytics"></a>Tworzenie niestandardowych widoków przy użyciu projektanta widoków w analizy dzienników
Przy użyciu narzędzia Projektant widoku w [Azure Log Analytics](log-analytics-overview.md), możesz utworzyć różne widoki niestandardowe w portalu Azure, które mogą pomóc w wizualizacji danych w obszarze roboczym analizy dzienników. Ten artykuł zawiera omówienie projektanta widoków i procedur tworzenia i edytowania widoków niestandardowych.

Aby uzyskać więcej informacji na temat Projektant widoków zobacz:

* [Kafelek odwołanie](log-analytics-view-designer-tiles.md): zawiera przewodnik odwołanie do ustawienia dla każdego z dostępnych kafelków w niestandardowych widoków.
* [Odwołanie do części wizualizacji](log-analytics-view-designer-parts.md): zawiera przewodnik odwołanie do ustawienia dla części wizualizacji, które są dostępne w niestandardowych widoków.


## <a name="concepts"></a>Pojęcia
Widoki są wyświetlane na **omówienie** strony obszaru roboczego analizy dzienników w portalu Azure. Kafelki w każdym widoku niestandardowym są wyświetlane w kolejności alfabetycznej i zainstalowano Kafelki rozwiązań do tego samego obszaru roboczego.

![Strona przeglądu](media/log-analytics-view-designer/overview-page.png)

Widoki, które można utworzyć z projektanta widoków zawiera elementy, które zostały opisane w poniższej tabeli:

| Część | Opis |
|:--- |:--- |
| Kafelki | Są wyświetlane w obszarze roboczym analizy dzienników **omówienie** strony. Każdego kafelka Wyświetla podsumowanie visual widok niestandardowy, który reprezentuje. Każdy typ kafelka zawiera inny typ wizualizacji rekordy. Należy wybrać Kafelek, aby wyświetlić widok niestandardowy. |
| Widok niestandardowy | Wyświetlane po wybraniu kafelka. Każdy widok zawiera co najmniej jeden części wizualizacji. |
| Części wizualizacji | Przedstawia wizualizacji danych w obszarze roboczym analizy dzienników oparte na co najmniej jeden [dziennika wyszukiwania](log-analytics-log-searches.md). Większość elementów obejmują nagłówka, co zapewnia wizualizacji wysokiego poziomu, i listy są wyświetlane wyniki top. Każdy typ części zawiera inny typ wizualizacji rekordów w obszarze roboczym analizy dzienników. Należy wybrać elementy w element do wyszukiwania dziennika, który udostępnia szczegółowe dane. |


## <a name="work-with-an-existing-view"></a>Praca z istniejącego widoku
Widoki, które zostały utworzone z projektanta widoków do wyświetlenia następujących opcji:

![Menu — omówienie](media/log-analytics-view-designer/overview-menu.png)

W poniższej tabeli opisano opcje:

| Opcja | Opis |
|:--|:--|
| Odświeżanie   | Odświeża widok przy użyciu najnowszych danych. | 
| Analiza | Otwiera [portal analityka zaawansowane](log-analytics-log-search-portals.md#advanced-analytics-portal) do analizowania danych z dziennika wyszukiwania. |
| Edytuj       | Otwiera widok w Projektancie widoku do edycji jej zawartości i konfiguracji.  |
| Klonuj      | Tworzy nowy widok i otwarcie go w widoku projektanta. Nazwa nowego widoku jest taka sama, jak oryginalna nazwa, ale *kopiowania* dołączone do niego. |
| Zakres dat | Ustaw datę i godzinę Filtr zakresu danych, który znajduje się w widoku. |
| +          | Definiowanie niestandardowego filtru, który jest zdefiniowany dla widoku. |


## <a name="create-a-new-view"></a>Utwórz nowy widok
Możesz utworzyć nowy widok w widoku projektanta, wybierając **Widok projektanta** w menu obszaru roboczego analizy dzienników.

![Widok projektanta kafelka](media/log-analytics-view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Praca z projektanta widoków
Projektant widoków umożliwia tworzenie nowych widoków lub edytować istniejące. 

Widok projektanta ma trzy sekcje: 
* **Projekt**: zawiera widok niestandardowy, który podczas tworzenia lub edycji. 
* **Formanty**: zawiera Kafelki i elementy, które dodajesz do **projekt** okienka. 
* **Właściwości**: Wyświetla właściwości Kafelki lub zaznaczonych części.

![Projektant widoków](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Konfigurowanie widoku kafelków
Widok niestandardowy może mieć tylko jednego kafelka. Aby wyświetlić bieżący Kafelek lub wybierz alternatywny, wybierz **Kafelek** karcie w **kontroli** okienka. **Właściwości** okienku są wyświetlane właściwości bieżącego fragmentu. 

Można skonfigurować właściwości kafelka zgodnie z informacjami w [Kafelek odwołania](log-analytics-view-designer-tiles.md) , a następnie kliknij przycisk **Zastosuj** Aby zapisać zmiany.

### <a name="configure-the-visualization-parts"></a>Skonfiguruj części wizualizacji
Widok może zawierać dowolną liczbę części wizualizacji. Aby dodać elementy do widoku, wybierz **widoku** , a następnie wybierz część wizualizacji. **Właściwości** okienku są wyświetlane właściwości wybranej części. 

Można skonfigurować właściwości widoku zgodnie z informacjami w [odwołanie do części wizualizacji](log-analytics-view-designer-parts.md) , a następnie kliknij przycisk **Zastosuj** Aby zapisać zmiany.

Widoki mają tylko jeden wiersz części wizualizacji. Możesz zmienić kolejność istniejących części, przeciągając je do nowej lokalizacji.

Z widoku można usunąć część wizualizacji, zaznaczając **X** u góry po prawej części.


### <a name="menu-options"></a>Opcje menu
Praca z widokami w trybie edycji opcje są opisane w poniższej tabeli.

![Menu Edycja](media/log-analytics-view-designer/edit-menu.png)

| Opcja | Opis |
|:--|:--|
| Zapisz        | Zapisuje zmiany i zamyka widoku. |
| Cancel      | Odrzuca wszystkie zmiany i zamyka widoku. |
| Usuń widok | Usuwa widok. |
| Eksportowanie      | Eksportuje widok, aby [szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) , który można zaimportować do innego obszaru roboczego. Nazwa pliku jest nazwa widoku i ma *omsview* rozszerzenia. |
| Import      | Importy *omsview* pliku, który został wyeksportowany z innego obszaru roboczego. Ta akcja spowoduje zastąpienie konfigurację istniejącego widoku. |
| Klonuj       | Tworzy nowy widok i otwarcie go w widoku projektanta. Nazwa nowego widoku jest taka sama, jak oryginalna nazwa, ale *kopiowania* dołączone do niego. |

## <a name="next-steps"></a>Kolejne kroki
* Dodaj [Kafelki](log-analytics-view-designer-tiles.md) do widoku niestandardowym.
* Dodaj [części wizualizacji](log-analytics-view-designer-parts.md) do widoku niestandardowym.
