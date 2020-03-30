---
title: Tworzenie widoków do analizowania danych dziennika w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Za pomocą projektanta widoku w usłudze Azure Monitor, można utworzyć widoki niestandardowe, które są wyświetlane w witrynie Azure portal i zawierają wiele wizualizacji na danych w obszarze roboczym usługi Log Analytics. Ten artykuł zawiera omówienie projektanta widoku i przedstawia procedury tworzenia i edytowania widoków niestandardowych.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/10/2019
ms.openlocfilehash: 9a7521f61dc59bd954629a05638c159ab0e70556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658493"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Tworzenie widoków niestandardowych przy użyciu projektanta widoku w usłudze Azure Monitor
Korzystając z projektanta widoku w usłudze Azure Monitor, można utworzyć wiele widoków niestandardowych w witrynie Azure portal, które mogą ułatwić wizualizację danych w obszarze roboczym usługi Log Analytics. W tym artykule przedstawiono omówienie projektanta widoku i procedur tworzenia i edytowania widoków niestandardowych.

> [!IMPORTANT]
> Widoki w usłudze Azure Monitor są wycofywane i zastępowane [skoroszytami,](workbooks-overview.md) które zapewniają dodatkowe funkcje. Zobacz [Przewodnik przejścia widoku usługi Azure Monitor do skoroszytów, aby](view-designer-conversion-overview.md) uzyskać szczegółowe informacje na temat konwertowania istniejących widoków na skoroszyty.

Aby uzyskać więcej informacji na temat widoku projektanta, zobacz:

* [Odwołanie do kafelków](view-designer-tiles.md): Zawiera przewodnik po ustawieniach każdego z dostępnych kafelków w widokach niestandardowych.
* [Odwołanie do części wizualizacji:](view-designer-parts.md)Zawiera przewodnik po ustawieniach części wizualizacji, które są dostępne w widokach niestandardowych.


## <a name="concepts"></a>Pojęcia
Widoki są wyświetlane na stronie **Przegląd usługi** Azure Monitor w witrynie Azure portal. Otwórz tę stronę z menu **Monitor platformy Azure,** klikając pozycję **Więcej** w sekcji **Insights.** Kafelki w każdym widoku niestandardowym są wyświetlane alfabetycznie, a kafelki dla rozwiązań monitorowania są instalowane w tym samym obszarze roboczym.

![Strona przeglądowa](media/view-designer/overview-page.png)

Widoki utworzone za pomocą projektanta widoku zawierają elementy opisane w poniższej tabeli:

| Część | Opis |
|:--- |:--- |
| Kafelki | Są wyświetlane na stronie **Przegląd usługi** Azure Monitor. Każdy kafelek wyświetla wizualne podsumowanie widoku niestandardowego, który reprezentuje. Każdy typ kafelka zapewnia inną wizualizację rekordów. Wybierz kafelek, aby wyświetlić widok niestandardowy. |
| Widok niestandardowy | Wyświetlane po wybraniu kafelka. Każdy widok zawiera jedną lub więcej części wizualizacji. |
| Części wizualizacji | Przedstaw wizualizację danych w obszarze roboczym usługi Log Analytics na podstawie co najmniej jednego [zapytania dziennika.](../log-query/log-query-overview.md) Większość części zawiera nagłówek, który zapewnia wizualizację wysokiego poziomu, oraz listę, która wyświetla najlepsze wyniki. Każdy typ części zapewnia inną wizualizację rekordów w obszarze roboczym usługi Log Analytics. Należy wybrać elementy w części, aby wykonać kwerendę dziennika, która zawiera szczegółowe rekordy. |

## <a name="required-permissions"></a>Wymagane uprawnienia
Do tworzenia lub modyfikowania widoków wymagane są co najmniej [uprawnienia na poziomie współautora](manage-access.md#manage-access-using-azure-permissions) w obszarze roboczym usługi Log Analytics. Jeśli nie masz tego uprawnienia, opcja Projektant widoku nie będzie wyświetlana w menu.


## <a name="work-with-an-existing-view"></a>Praca z istniejącym widokiem
Widoki utworzone za pomocą projektanta widoku wyświetlają następujące opcje:

![Menu Przegląd](media/view-designer/overview-menu.png)

Opcje są opisane w poniższej tabeli:

| Opcja | Opis |
|:--|:--|
| Odświeżanie   | Odświeża widok z najnowszymi danymi. | 
| Dzienniki      | Otwiera [usługi Log Analytics](../log-query/portals.md) do analizowania danych za pomocą zapytań dziennika. |
| Edytuj       | Otwiera widok w projektancie widoku, aby edytować jego zawartość i konfigurację.  |
| Klonowanie      | Tworzy nowy widok i otwiera go w projektancie widoku. Nazwa nowego widoku jest taka sama jak oryginalna nazwa, ale z *dołączenie do* kopii. |
| Zakres dat | Ustaw filtr zakresu daty i godziny dla danych uwzględnionych w widoku. Ten zakres dat jest stosowany przed wszelkimi zakresami dat ustawionymi w kwerendach w widoku.  |
| +          | Zdefiniuj filtr niestandardowy zdefiniowany dla widoku. |


## <a name="create-a-new-view"></a>Tworzenie nowego widoku
Nowy widok można utworzyć w projektancie widoku, wybierając **pozycję Projektant widoku** w menu obszaru roboczego usługi Log Analytics.

![Wyświetl kafelek projektanta](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Praca z projektantem widoku
Projektant widoku służy do tworzenia nowych widoków lub edytowania istniejących. 

Widok Projektant ma trzy okienka: 
* **Projekt:** Zawiera widok niestandardowy, który tworzysz lub edytujesz. 
* **Formanty**: Zawiera kafelki i części dodawanych do okienka **Projektowanie.** 
* **Właściwości**: Wyświetla właściwości kafelków lub wybranych części.

![Projektant widoków](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Konfigurowanie kafelka widoku
Widok niestandardowy może mieć tylko jeden kafelek. Aby wyświetlić bieżący kafelek lub wybrać alternatywny, wybierz kartę **Kafelek** w okienku **Sterowanie.** W okienku **Właściwości** zostaną wyświetlone właściwości bieżącego kafelka. 

Właściwości kafelków można skonfigurować zgodnie z informacjami w odwołaniu do [kafelków,](view-designer-tiles.md) a następnie kliknąć przycisk **Zastosuj,** aby zapisać zmiany.

### <a name="configure-the-visualization-parts"></a>Konfigurowanie części wizualizacji
Widok może zawierać dowolną liczbę części wizualizacji. Aby dodać części do widoku, wybierz kartę **Widok,** a następnie wybierz część wizualizacji. W okienku **Właściwości** wyświetlane są właściwości wybranej części. 

Właściwości widoku można skonfigurować zgodnie z informacjami w odwołaniu do [części Wizualizacja,](view-designer-parts.md) a następnie kliknąć przycisk **Zastosuj,** aby zapisać zmiany.

Widoki mają tylko jeden wiersz części wizualizacji. Istniejące części można zmienić, przeciągając je w nowe miejsce.

Część wizualizacji można usunąć z widoku, wybierając **x** w prawym górnym rogu części.


### <a name="menu-options"></a>Opcje menu
Opcje pracy z widokami w trybie edycji opisano w poniższej tabeli.

![Menu edycji](media/view-designer/edit-menu.png)

| Opcja | Opis |
|:--|:--|
| Zapisz        | Zapisuje zmiany i zamyka widok. |
| Cancel      | Odrzuca zmiany i zamyka widok. |
| Usuń widok | Usuwa widok. |
| Eksportowanie      | Eksportuje widok do [szablonu usługi Azure Resource Manager,](../../azure-resource-manager/templates/template-syntax.md) który można zaimportować do innego obszaru roboczego. Nazwa pliku jest nazwą widoku i ma rozszerzenie *omsview.* |
| Import      | Importuje plik *omsview* wyeksportowany z innego obszaru roboczego. Ta akcja zastępuje konfigurację istniejącego widoku. |
| Klonowanie       | Tworzy nowy widok i otwiera go w projektancie widoku. Nazwa nowego widoku jest taka sama jak oryginalna nazwa, ale z *dołączenie do* kopii. |

## <a name="next-steps"></a>Następne kroki
* Dodawanie [kafelków](view-designer-tiles.md) do widoku niestandardowego.
* Dodaj [części wizualizacji](view-designer-parts.md) do widoku niestandardowego.
