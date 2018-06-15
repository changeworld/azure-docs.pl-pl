---
title: Interfejs użytkownika programu StorSimple Snapshot Manager | Dokumentacja firmy Microsoft
description: Zawiera opis interfejsu użytkownika programu StorSimple Snapshot Manager i wyjaśniono, jak przy jego użyciu zarządzać zadania tworzenia kopii zapasowej i kopii zapasowej wykazu.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.custom: ''
ms.openlocfilehash: b48c507e38eb7cadff56259f617e336e4efe5708
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927950"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Interfejs użytkownika użycia StorSimple Snapshot Manager do zarządzania zadania tworzenia kopii zapasowej i kopii zapasowej wykazu

## <a name="overview"></a>Omówienie
StorSimple Snapshot Manager ma intuicyjnego interfejsu użytkownika służącego do podjęcia i zarządzanie kopiami zapasowymi. Ten samouczek zawiera wprowadzenie do interfejsu użytkownika i wyjaśniono, jak używać poszczególnych składników programu. Aby uzyskać szczegółowy opis programu StorSimple Snapshot Manager, zobacz [co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Opis elementu konsoli
Aby wyświetlić interfejs użytkownika, kliknij ikonę StorSimple Snapshot Manager na pulpicie. Zostanie wyświetlone okno konsoli, jak pokazano na poniższej ilustracji.

![Okienka programu StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

W oknie konsoli ma pięć głównych elementów. Kliknij odpowiednie łącze, aby uzyskać pełny opis każdego elementu.

* [Pasek menu](#menu-bar) 
* [Pasek narzędzi](#tool-bar) 
* [Okienko zakresu](#scope-pane) 
* [W okienku wyników](#results-pane) 
* [W okienku Akcje](#actions-pane) 

Ponadto StorSimple Snapshot Manager obsługuje [klawiatury nawigacji i liczbę skrótów](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Ułatwienia dostępu w konsoli
Interfejs użytkownika programu StorSimple Snapshot Manager obsługuje udostępnione przez system operacyjny Windows i programu Microsoft Management Console (MMC), a także niektóre skróty klawiaturowe StorSimple Snapshot Manager specyficzne funkcje ułatwień dostępu. 

* Opis funkcji ułatwień dostępu systemu Windows, przejdź do [skróty klawiaturowe dla systemu Windows](https://support.microsoft.com/kb/126449). 
* Opis funkcji ułatwień dostępu programu MMC, przejdź do [ułatwień dostępu dla programu MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)
* Opis funkcji ułatwień dostępu programu StorSimple Snapshot Manager, przejdź do [klawiatury nawigacji i skróty](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Pasek menu
Na pasku menu w górnej części okna konsoli zawiera [pliku](#file-menu), [akcji](#action-menu), [widoku](#view-menu), [ulubione](#favorites-menu), [okna](#window-menu), i [pomocy](#help-menu) menu.

Kliknij dowolny element widoczny na pasku menu, aby wyświetlić listę dostępnych poleceń tego menu. W poniższym przykładzie przedstawiono **widoku** menu zaznaczone na pasku menu.

![Wybrane menu Widok](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Menu Plik
**Pliku** menu zawiera standardowe polecenia programu Microsoft Management Console (MMC).

#### <a name="menu-access"></a>Dostęp do menu
Aby wyświetlić **pliku** menu, kliknij przycisk **pliku** na pasku menu. Zostanie wyświetlone następujące menu.

![Menu Plik StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Opis elementu menu
W poniższej tabeli opisano elementy, które znajdują się w **pliku** menu.

| Element menu | Opis |
|:--- |:--- |
| Nowy |Kliknij przycisk **nowy** do utworzenia nowej konsoli oparte na StorSimple Snapshot Manager. |
| Otwarty |Kliknij przycisk **Otwórz** otworzyć istniejącej konsoli. |
| Zapisz |Kliknij przycisk **zapisać** Aby zapisać bieżącą konsolę. |
| Zapisz jako |Kliknij przycisk **Zapisz jako** utworzyć nową, zmieniono nazwę wystąpienia bieżącej konsoli. Użyj **Zapisz jako** możliwość dostosowywania widoku i zapisać go do nowszej pobierania. Na przykład można utworzyć przystawki StorSimple Snapshot Manager, które odwołują się do określonych serwerów. |
| Dodaj/Usuń przystawkę |Kliknij przycisk **Dodaj/Usuń przystawkę** Dodawanie lub usuwanie przystawek i organizowanie węzłów w **zakres** okienka. Aby uzyskać więcej informacji, przejdź do [Dodaj, Usuń i organizowanie przystawek i rozszerzeń w programie MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Opcje |Kliknij przycisk **opcje** Aby zmienić ikony konsoli, określ tryby dostępu do użytkownika i uprawnienia, i usuwania plików konsoli można zwiększyć dostępnego miejsca na dysku. |
| Lista ścieżek pliku |Kliknij przycisk ścieżką na liście numerowane, aby ponownie otworzyć plik, który ostatnio otwarty. |
| Zakończ |Kliknij przycisk **zakończenia** zamknąć **pliku** menu. |

### <a name="action-menu"></a>Menu akcji
Użyj **akcji** menu, aby dokonać wyboru spośród dostępnych akcji. Elementy dostępne są zależne od wyboru w **zakres** okienku lub **wyniki** okienka.

#### <a name="menu-access"></a>Dostęp do menu
Aby wyświetlić **akcji** menu, wykonaj jedną z następujących czynności:

* Kliknij prawym przyciskiem myszy element **zakres** okienku lub **wyniki** okienka.
* Wybierz element w **zakres** okienku lub **wyniki** okienka, a następnie kliknij przycisk **akcji** na pasku menu. 

Na przykład zaznacz górny węzeł w **zakres** okienka, a następnie kliknij prawym przyciskiem myszy lub kliknij przycisk **akcji** na pasku menu zostanie wyświetlone następujące menu.

![Menu Akcja StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

**Akcje** okienko (po prawej stronie konsoli) zawiera tę samą listę działań, jak **akcji** menu. Ponadto **akcje** okienko zawiera **widoku** opcji menu, które umożliwiają utworzenie widoku niestandardowego **wyniki** okienka.

![Otwórz w okienku Akcje, z menu Widok](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Opis elementu menu
Poniższa tabela zawiera alfabetyczną listę akcji StorSimple Snapshot Manager. 

* **Akcji** kolumna zawiera akcje, które mogą wykonywać na węzły i wyniki. 
* **Nawigacji** kolumny opisano sposób wyświetlania odpowiednie **akcji** menu, w którym można wybrać akcję. Niektóre akcje są wyświetlane w wielu **akcji** menu. Te akcje, wybierz jedną **nawigacji** opcję z listy punktowanej. 
* **Opis** kolumny zawiera opis sposobu używania każdej akcji na **akcji** menu lub okienka działań oraz wyjaśniono, jak działa.

> [!NOTE]
> **Akcje** okienko i **akcji** menu zawierają dodatkowe opcje, takie jak **widoku**, **nowe okno w tym miejscu**,  **Odśwież**, **wyeksportować listę**, i **pomocy**. Te opcje są dostępne w ramach programu MMC i nie są specyficzne do programu StorSimple Snapshot Manager. Tabela zawiera opisy tych opcji.
> 
> 

| Akcja | Nawigacji | Opis |
|:--- |:--- |:--- |
| Uwierzytelnianie |Kliknij przycisk **urządzeń** węzeł i kliknij prawym przyciskiem myszy urządzenie w **wyniki** okienka. |Kliknij przycisk **Uwierzytelnij** o wprowadzenie hasła skonfigurowanego dla urządzenia. |
| Klonuj |Rozwiń węzeł **katalogu kopii zapasowej**, rozwiń węzeł **migawki w chmurze**, kliknij przycisk z kopii zapasowej, a następnie wybierz wolumin w **wyniki** okienka. |Kliknij przycisk **klonowania** Utwórz kopię migawka w chmurze i zapisz go w lokalizacji, które zostało określone. |
| Konfigurowanie urządzenia |Kliknij prawym przyciskiem myszy **urządzeń** węzła. |Kliknij przycisk **Skonfiguruj urządzenie** do skonfigurowania jednego lub wielu urządzeniach, aby połączyć się z hostem z systemem Windows. |
| Tworzenie zasad tworzenia kopii zapasowej |Wykonaj jedną z następujących czynności:<ul><li>Kliknij prawym przyciskiem myszy **zasady tworzenia kopii zapasowej**.</li><li>Kliknij i rozwiń **grup woluminu**, a następnie kliknij prawym przyciskiem myszy grupę woluminu.</li><li>Kliknij i rozwiń **katalog kopii zapasowej**, a następnie kliknij prawym przyciskiem myszy grupę woluminu.</li></ul> |Kliknij przycisk **Utwórz zasady tworzenia kopii zapasowej** do skonfigurowania zaplanowanego tworzenia kopii zapasowej dla grupy woluminu. |
| Utwórz grupę woluminu |Wykonaj jedną z następujących czynności:<ul><li>Kliknij przycisk **woluminów** węzeł i kliknij prawym przyciskiem myszy wolumin w **wyniki** okienka.</li><li>Kliknij prawym przyciskiem myszy **grup woluminu** węzła.</li></ul> |Kliknij przycisk **Utwórz grupę woluminu** można przypisać do grupy woluminu woluminy. |
| Usuwanie |Kliknij węzeł lub wynik (ten element będzie wyświetlany na wiele **akcji** menu i **akcje** okienka.) |Kliknij przycisk **usunąć** można usunąć węzła lub wynik wybrany. Gdy pojawi się okno dialogowe potwierdzenia, upewnij się, lub Anuluj operację. |
| Szczegóły |Kliknij przycisk **urządzeń** węzeł i kliknij prawym przyciskiem myszy urządzenie w **wyniki** okienka. |Kliknij przycisk **szczegóły** Aby wyświetlić szczegóły konfiguracji dla urządzenia. |
| Edytuj |Kliknij przycisk **zasady tworzenia kopii zapasowej**, a następnie kliknij prawym przyciskiem myszy zasadę w **wyniki** okienka. |Kliknij przycisk **Edytuj** Aby zmienić harmonogram tworzenia kopii zapasowej dla grupy woluminu. |
| Wyeksportuj listę |Kliknij dowolny węzeł lub wynik (ten element będzie wyświetlany we wszystkich **akcji** menu i **akcje** okienka.) |Kliknij przycisk **Eksportuj listę** zapisać listy w pliku wartości rozdzielanych przecinkami (CSV). Następnie można zaimportować ten plik do arkusza kalkulacyjnego aplikacji do analizy. |
| Pomoc |Kliknij dowolny węzeł lub wynik. (Ten element będzie wyświetlany we wszystkich **akcji** menu i **akcje** okienka.) |Kliknij przycisk **pomocy** otworzyć Pomocy online w osobnym oknie przeglądarki. |
| Nowe okno w tym miejscu |Kliknij dowolny węzeł lub wynik (ten element będzie wyświetlany we wszystkich **akcji** menu i **akcje** okienka.) |Kliknij przycisk **nowe okno w tym miejscu** aby otworzyć nowe okno programu StorSimple Snapshot Manager. |
| Odśwież |Kliknij dowolny węzeł lub wynik (ten element będzie wyświetlany we wszystkich **akcji** menu i **akcje** okienka.) |Kliknij przycisk **Odśwież** zaktualizować aktualnie wyświetlane okno StorSimple Snapshot Manager. |
| Odśwież urządzenia |Kliknij przycisk **urządzeń** węzeł i kliknij prawym przyciskiem myszy urządzenie w **wyniki** okienka. |Kliknij przycisk **Odśwież urządzenia** do synchronizacji określonych urządzeń połączonych z StorSimple Snapshot Manager. |
| Odśwież urządzenia |Kliknij prawym przyciskiem myszy **urządzeń** węzła. |Kliknij przycisk **Odśwież urządzeń** można zsynchronizować listę urządzeń połączonych z StorSimple Snapshot Manager. |
| Skanuj woluminów |Kliknij prawym przyciskiem myszy **woluminów** węzła. |Kliknij przycisk **ponownego skanowania woluminów** Aby zaktualizować listę woluminów, w których **wyniki** okienka. |
| Przywracanie |Rozwiń węzeł **katalogu kopii zapasowej**, rozwiń grupę woluminu, rozwiń węzeł **migawki lokalne** lub **migawki w chmurze**, a następnie kliknij prawym przyciskiem myszy kopii zapasowej. |Kliknij przycisk **przywrócić** zamienić bieżące dane grupy woluminu przy użyciu danych z wybranej kopii zapasowej. |
| Wykonać kopii zapasowej |Wykonaj jedną z następujących czynności:<ul><li>Rozwiń węzeł **grup woluminu**, a następnie kliknij prawym przyciskiem myszy grupę woluminu.</li><li>Rozwiń węzeł **katalog kopii zapasowej**, a następnie kliknij prawym przyciskiem myszy grupę woluminu.</li></ul> |Kliknij przycisk **wykonać kopii zapasowej** Aby natychmiast uruchomić zadanie tworzenia kopii zapasowej. |
| Przełącz wyświetlanie importów |Kliknij prawym przyciskiem myszy górny węzeł w **zakres** okienka ( **StorSimple Snapshot Manager** węzła w przykładach). |Kliknij przycisk **Przełącz importów wyświetlanie** aby pokazać lub ukryć wolumin grup i skojarzonych kopii zapasowych, które zostały zaimportowane na pulpicie nawigacyjnym usługi Menedżer StorSimple urządzenia. |

### <a name="view-menu"></a>Menu Widok
Użyj **widoku** menu, aby utworzyć niestandardowy widok **wyniki** zawartość okienka. **Widoku** zawiera menu **Dodaj/Usuń kolumny** i **Dostosuj** opcje.

#### <a name="menu-access"></a>Dostęp do menu
Dostęp można uzyskać **widoku** menu menu paska lub w **akcje** okienka.

![Menu Widok programu StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Opis elementu menu
W poniższej tabeli opisano elementy, które znajdują się w **widoku** menu.

| Element menu | Opis |
|:--- |:--- |
| Dodawanie/usuwanie kolumn |Kliknij przycisk **Dodaj/Usuń kolumny** można dodać ani usunąć kolumn w **wyniki** okienka. |
| Dostosowywanie |Kliknij przycisk **Dostosuj** aby pokazać lub ukryć elementy w oknie konsoli StorSimple Snapshot Manager. |

### <a name="favorites-menu"></a>Menu Ulubione
Użyj **ulubione** menu do dodawania, usuwania i organizowanie strony widoków i zadań, które są często używane. 

#### <a name="menu-access"></a>Dostęp do menu
Dostęp można uzyskać **ulubione** menu na pasku menu.

![Menu Ulubione StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Opis elementu menu
W poniższej tabeli opisano elementy, które znajdują się w **ulubione** menu.

| Element menu | Opis |
|:--- |:--- |
| Dodaj do ulubionych |Kliknij przycisk **Dodaj do ulubionych** do dodania do bieżącego widoku do listy ulubionych. |
| Skróty do ulubionych |Kliknij przycisk **organizowanie ulubionych** organizować zawartość folderu Ulubione. |

### <a name="window-menu"></a>Menu okna
Użyj **okna** menu, aby dodać i rozmieszczanie okien konsoli StorSimple Snapshot Manager.

#### <a name="menu-access"></a>Dostęp do menu
Dostęp można uzyskać **okna** menu na pasku menu.

![Menu okna StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

Lista numerowana u dołu menu pokazuje systemu windows, które są aktualnie otwarte. Kliknij przycisk dowolnego okna na tej liście, aby umożliwić oknie na pierwszym planie. 

#### <a name="menu-description"></a>Opis elementu menu
W poniższej tabeli opisano elementy, które są wyświetlane w menu okna.

| Element menu | Opis |
|:--- |:--- |
| Nowe okno |Kliknij przycisk **nowe okno** aby otworzyć nowe okno konsoli (oprócz istniejące okno). |
| Kaskadowo |Kliknij przycisk **Cascade** do wyświetlenia w kaskadowego stylu Otwórz konsolę systemu windows. |
| Sąsiadująco w poziomie |Kliknij przycisk **sąsiadująco w poziomie** do wyświetlenia w formacie kafelka (lub siatki) otwórz konsolę systemu windows. |
| Rozmieść ikony |Jeśli masz wiele konsoli systemu windows Otwórz i rozproszone na pulpicie zminimalizować je, a następnie kliknij przycisk **Rozmieść ikony** do rozmieszczenia w rzędzie w dolnej części ekranu. |

### <a name="help-menu"></a>Menu Pomoc
Użyj **pomocy** menu, aby wyświetlić dostępne Pomoc online dla programu StorSimple Snapshot Manager i programu MMC. Można również wyświetlić informacje o wersji oprogramowania MMC i StorSimple Snapshot Manager, które są aktualnie zainstalowane w systemie. 

Dostęp można uzyskać **pomocy** menu na pasku menu. Można także przejść do programu StorSimple Snapshot Manager tematy Pomocy z **akcje** okienka.

![Menu Pomoc programu StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Opis elementu menu
W poniższej tabeli opisano elementy wyświetlane w menu Pomoc.

| Element menu | Opis |
|:--- |:--- |
| Pomoc na temat programu StorSimple Snapshot Manager |Kliknij przycisk **pomocy na StorSimple Snapshot Manager** otworzyć StorSimple Snapshot Manager pomocy w osobnym oknie. |
| Tematy pomocy |Kliknij przycisk **tematy Pomocy** otworzyć Pomoc online programu MMC w osobnym oknie. |
| Witryna TechCenter |Kliknij przycisk **witryny TechCenter** aby otworzyć stronę główną Microsoft TechNet Tech Center w osobnym oknie. |
| Informacje programu Microsoft Management Console |Kliknij przycisk **programu Microsoft Management Console** wersji programu Microsoft Management Console jest zainstalowana w Twoim systemie. |
| StorSimple Snapshot Manager |Kliknij przycisk **StorSimple Snapshot Manager** wersję przystawki jest zainstalowana w Twoim systemie. |

## <a name="tool-bar"></a>Pasek narzędzi
Pasek narzędzi, znajdujący się poniżej paska menu zawiera ikony nawigacji oraz zadań. Każda z ikon to skrót do określonego zadania.

### <a name="icon-descriptions"></a>Ikona opisów
W poniższej tabeli opisano ikony, które są wyświetlane na pasku narzędzi. 

| Ikona | Opis |
|:--- |:--- |
| ![Strzałka w lewo](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Kliknij ikonę strzałki w lewo, aby powrócić do poprzedniej strony. |
| ![Strzałka w prawo](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Kliknij strzałkę w prawo, aby przejść do następnej strony (jeśli strzałkę jest szary, akcja jest niedostępna). |
| ![Ikona w dół](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Kliknij ikonę się, aby przejść w górę o jeden poziom w drzewie konsoli ( **zakres** okienko). |
| ![Pokaż/Ukryj drzewo konsoli](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Kliknij ikonę drzewo konsoli Pokaż/Ukryj, aby pokazać lub ukryć **zakres** okienka. |
| ![Wyeksportuj listę](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Kliknij ikonę eksportowania listy, aby wyeksportować listę do pliku CSV, który określisz. |
| ![Ikona pomocy](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Kliknij ikonę Pomoc, aby otworzyć online tematu Pomocy programu MMC. |
| ![Pokaż/Ukryj okienka Akcje](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Kliknij przycisk Pokaż/Ukryj **akcje** ikonę okienko, aby pokazać lub ukryć **akcje** okienka. |

## <a name="scope-pane"></a>Okienko zakresu
**Zakres** okienko jest w okienku po lewej stronie w Interfejsie użytkownika programu StorSimple Snapshot Manager. Zawiera drzewo konsoli (lub węzeł) i jest nawigacji podstawowego mechanizmu StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Zakres okienka struktury
**Zakres** okienko zawiera szereg obiekty aktywne (węzłów) w strukturze drzewa. 

![Okienko zakresu](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Aby rozwinąć lub zwinąć węzeł, kliknij ikonę strzałki obok nazwy węzła.
* Aby wyświetlić stan lub zawartość węzła, kliknij nazwę węzła. Informacje zamieszczone w **wyniki** okienka. 

**Zakres** okienko zawiera następujące węzły: 

* [Węzła urządzenia](#devices-node) 
* [Węzeł woluminów](#volumes-node) 
* [Wolumin węzeł grupy](#volume-groups-node) 
* [Węzeł Zasady tworzenia kopii zapasowej](#backup-policies-node) 
* [Węzeł katalogu kopii zapasowej](#backup-catalog-node) 
* [Węzeł zadania](#jobs-node) 

### <a name="scope-pane-tasks"></a>Zakres okienka zadań
Można użyć **zakres** okienko, aby ukończyć akcji w określonym węźle. Aby wybrać zadanie, wykonaj jedną z następujących czynności:

* Kliknij prawym przyciskiem myszy węzeł, a następnie wybierz zadanie w menu.
* Kliknij węzeł, a następnie kliknij przycisk **akcji** na pasku menu. Wybierz zadanie w menu.
* Kliknij węzeł, a następnie wybierz akcję w **akcje** okienka.

Po wybraniu węzła i użyj jednej z tych metod, aby wyświetlić listę zadań, są wyświetlane tylko akcje, które mogą być wykonywane w tym węźle.

### <a name="devices-node"></a>Węzła urządzenia
**Urządzeń** reprezentuje węzeł urządzenia StorSimple i urządzeń wirtualnych StorSimple, które są podłączone do programu StorSimple Snapshot Manager. Wybierz ten węzeł, aby połączyć i konfigurowanie urządzenia i importowanie jej skojarzone woluminy, grup woluminów i istniejące kopie zapasowe. Wiele urządzeń można podłączyć do jednego hosta.

* Aby rozwinąć węzeł, kliknij ikonę strzałki **urządzeń**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy **urządzeń** węzła lub kliknij prawym przyciskiem myszy dowolny z węzłów, które są wyświetlane w widoku rozszerzonego.
* Aby wyświetlić listę skonfigurowanych urządzeń, kliknij przycisk **urządzeń** w **zakres** okienka. Lista urządzeń, wraz z informacjami o wszystkich urządzeniach, jest wyświetlana w **wyniki** okienka.

### <a name="volumes-node"></a>Węzeł woluminów
**Woluminów** reprezentuje węzeł dyski, które odnoszą się do woluminów zainstalowanych w wyniku hosta, łącznie z tymi odnalezione za pomocą inicjatora iSCSI i te odnalezione przez urządzenie. Użyj tego węzła, aby wyświetlić listę dostępnych woluminów i przypisanie poszczególnych woluminów do grup woluminu.

* Aby rozwinąć węzeł, kliknij ikonę strzałki **woluminów**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy **woluminów** węzła lub kliknij prawym przyciskiem myszy dowolny z węzłów, które są wyświetlane w widoku rozszerzonego.
* Aby wyświetlić listę woluminów, kliknij **woluminów** w **zakres** okienka. Lista woluminów, wraz z informacjami o każdym woluminie, jest wyświetlana w **wyniki** okienka.

### <a name="volume-groups-node"></a>Wolumin węzeł grupy
Grupy woluminu są nazywane również spójności grup. Każda grupa woluminu jest puli woluminów związanych z aplikacją ułatwia zapewnienie spójności aplikacji podczas operacji tworzenia kopii zapasowej. Użyj **grup woluminu** węzeł skonfigurować te grupy i korzystać z kopii zapasowych interakcyjnego lub utworzyć harmonogramy tworzenia kopii zapasowej. 

* Aby rozwinąć węzeł, kliknij ikonę strzałki **grup woluminu**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy **grup woluminu** węzła lub kliknij prawym przyciskiem myszy dowolny z węzłów, które są wyświetlane w widoku rozszerzonego.
* Umożliwia wyświetlenie listy grup wolumin, kliknij przycisk **grup woluminu** w **zakres** okienka. Lista grup woluminu, wraz z informacjami o każdej grupy woluminu jest wyświetlana w **wyniki** okienka.

### <a name="backup-policies-node"></a>Węzeł Zasady tworzenia kopii zapasowej
Zasady tworzenia kopii zapasowej są harmonogramy zadań dla lokalnych i w chmurze migawki. Użyj **zasad tworzenia kopii zapasowych** węzeł, aby określić, jak często jest tworzona kopia zapasowa i czas przechowywania kopii zapasowej powinny być zachowywane. 

* Aby rozwinąć węzeł, kliknij ikonę strzałki **zasady tworzenia kopii zapasowej**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy **zasady tworzenia kopii zapasowej** węzła lub kliknij prawym przyciskiem myszy dowolny z węzłów, które są wyświetlane w widoku rozszerzonego.
* Aby wyświetlić listę zasad tworzenia kopii zapasowych, kliknij przycisk **zasad tworzenia kopii zapasowych** w **zakres** okienka. Lista zasad tworzenia kopii zapasowych, wraz z informacjami na temat poszczególnych zasad, zostanie wyświetlona w **wyniki** okienka.

> [!NOTE]
> Można zachować maksymalnie 64 kopie zapasowe.


### <a name="backup-catalog-node"></a>Węzeł katalogu kopii zapasowej
**Katalog kopii zapasowej** węzła zawiera listy lokalnie i w innej lokalizacji kopii zapasowych woluminów Azure StorSimple. Ten węzeł jest zorganizowana według grupy woluminu, a kontener grupy Każdy wolumin zawiera osobne struktur migawki lokalne ( **migawka lokalna**węzła s) i migawki w chmurze ( **migawki w chmurze** węzła). Po rozwinięciu kontenera grupy Każdy wolumin zawiera listę wszystkich powiodło się tworzenie kopii zapasowych wykonanych interakcyjnego lub skonfigurowanymi zasadami.

* Aby rozwinąć węzeł, kliknij ikonę strzałki **katalog kopii zapasowej**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy **katalog kopii zapasowej** węzła lub kliknij prawym przyciskiem myszy dowolny z węzłów, które są wyświetlane w widoku rozszerzonego.
* Aby wyświetlić listę kopii zapasowych, kliknij przycisk **katalogu kopii zapasowej** w **zakres** okienka. Lista migawki, wraz z informacjami o każdej migawki jest wyświetlana w **wyniki** okienka.

### <a name="local-snapshots-node"></a>Lokalny węzeł migawki
**Migawki lokalne** węzła listy migawki lokalne dla określonego woluminu grupy. Węzeł znajduje się w obszarze **katalog kopii zapasowej** w węźle **zakres** okienka. Migawki lokalne są kopiami w momencie wolumin danych, które są przechowywane na urządzeniu Azure StorSimple. Zazwyczaj ten typ kopii zapasowej można tworzyć i szybko przywrócić. Można użyć migawka lokalna, jak w przypadku lokalnej kopii zapasowej.

* Aby rozwinąć węzeł, kliknij ikonę strzałki **migawki lokalne**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy **migawki lokalne** węzła lub kliknij prawym przyciskiem myszy dowolny z węzłów, które są wyświetlane w widoku rozszerzonego.
* Aby wyświetlić listę migawki lokalne, kliknij przycisk **migawki lokalne** w **zakres** okienka. Lista migawki, wraz z informacjami o każdej migawki jest wyświetlana w **wyniki** okienka.

### <a name="cloud-snapshots-node"></a>Węzeł migawki w chmurze
**Migawki w chmurze** węzła listy migawki w chmurze dla grupy określonego woluminu. Węzeł znajduje się w obszarze **katalog kopii zapasowej** w węźle **zakres** okienka. Migawki w chmurze są w chwili kopii danych woluminu, które są przechowywane w chmurze. Migawka w chmurze jest odpowiednikiem migawki replikowane w systemie magazynu innych, poza nim. Migawki w chmurze są szczególnie przydatne w scenariuszach odzyskiwania po awarii.

* Aby rozwinąć węzeł, kliknij ikonę strzałki **migawki w chmurze**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy **migawki w chmurze** węzła lub kliknij prawym przyciskiem myszy dowolny z węzłów, które są wyświetlane w widoku rozszerzonego.
* Aby wyświetlić listę migawki w chmurze, kliknij przycisk **migawki w chmurze** w **zakres** okienka. Lista migawki, wraz z informacjami o każdej migawki jest wyświetlana w **wyniki** okienka.

### <a name="jobs-node"></a>Węzeł zadania
**Zadania** węzła zawiera informacje o zaplanowanym, uruchamianie i ostatnio wykonanych zadań tworzenia kopii zapasowej. 

* Aby rozwinąć węzeł, kliknij ikonę strzałki **zadania**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy **zadania** węzła lub kliknij prawym przyciskiem myszy dowolny z węzłów, które są wyświetlane w widoku rozszerzonego.
* Aby wyświetlić listę zadań zaplanowanych, rozwiń **zadania** węzeł, a następnie kliknij przycisk **zaplanowane**. Lista wcześniej skonfigurowanych zadań oraz informacje o każdym zadaniem jest wyświetlana w **wyniki** okienka. 
* Aby wyświetlić listę zadań ostatnio wykonanych, rozwiń **zadania** węzeł, a następnie kliknij przycisk **ostatnich 24 godzin**. Zostanie wyświetlona lista zadań, które zostały ukończone w ciągu ostatnich 24 godzin w **wyniki** okienka. **Wyniki** okienko zawiera także informacje o każdym zadanie zostało ukończone.
* Aby wyświetlić listę zadań, które są aktualnie uruchomione, rozwiń **zadania** węzeł, a następnie kliknij przycisk **systemem**. Lista aktualnie uruchomionych zadań i informacje o każdym zadaniem jest wyświetlana w **wyniki** okienka.

## <a name="results-pane"></a>W okienku wyników
**Wyniki** okienko jest w środkowym okienku w Interfejsie użytkownika programu StorSimple Snapshot Manager. Zawiera on wyświetla i szczegółowe informacje o stanie dla węzła wybranego w **zakres** okienka.

### <a name="example"></a>Przykład
Aby wyświetlić w poniższym przykładzie, kliknij **grup woluminu** w węźle **zakres** okienka. **Wyniki** okienku zostanie wyświetlona lista grup woluminu o szczegółowe informacje dotyczące każdej grupy.

![W okienku wyników](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Można skonfigurować szczegółowe informacje wyświetlane w **wyniki** okienko: kliknij prawym przyciskiem myszy węzeł w **zakres** okienku, kliknij przycisk **widoku**, a następnie kliknij przycisk **Dodawanie/usuwanie kolumn** .

## <a name="actions-pane"></a>W okienku Akcje
**Akcje** okienko jest w okienku po prawej stronie w Interfejsie użytkownika programu StorSimple Snapshot Manager. Zawiera menu działań, które mogą wykonywać na węźle, widoku lub dane, które należy wybrać w **zakres** okienku lub **wyniki** okienka. **Akcje** okienko zawiera te same polecenia co **akcji** menu, które są dostępne dla elementów w **zakres** okienko i **wyniki** okienko. Opis akcji, zobacz tabelę w **akcji** części menu.

### <a name="examples"></a>Przykłady
Aby wyświetlić w poniższym przykładzie **zakres** okienku rozwiń **zadania** węzeł i kliknij przycisk **zaplanowane**. **Akcje** okienku wyświetlane są dostępne akcje **zaplanowane** węzła.

![Przykład zaplanowanych zadań w okienku Akcje](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Aby wyświetlić więcej opcji w **zakres** okienku rozwiń **zadania** węzła, kliknij przycisk **zaplanowane**, a następnie kliknij przycisk zaplanowane zadanie w **wyników** okienko. **Akcje** okienku wyświetlane są dostępne akcje zaplanowanego zadania, jak pokazano w poniższym przykładzie.

![Przykład działania zadania w okienku Akcje](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Skróty i nawigacji klawiatury
StorSimple Snapshot Manager zapewnia funkcje ułatwień dostępu systemu operacyjnego i programu Microsoft Management Console (MMC). Zawiera także niektóre funkcje nawigacji klawiatury i skrótów, które są specyficzne do programu StorSimple Snapshot Manager zgodnie z opisem w poniższych sekcjach.

* [Klawisze nawigacji](#keyboard-navigation-keys) 
* [Menu paska klawiszy skrótów](#menu-bar-shortcut-keys) 
* [Klawisze skrótów okienka zakresu](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Klawisze nawigacji
W poniższej tabeli opisano klucze służące do nawigacji w interfejsie użytkownika programu StorSimple Snapshot Manager. 

| Klucz nawigacji | Akcja |
|:--- |:--- |
| Strzałka w dół |Klawisz strzałki w dół umożliwia pionowo Przenieś do następnego elementu menu lub okienka. |
| Wprowadź |Naciśnij klawisz Enter, aby ukończyć akcji, a następnie przejdź do następnego kroku. Można na przykład, naciśnij klawisz Enter, aby wybrać **dalej**, **OK**, lub **Utwórz**, a następnie przejdź do następnego kroku w kreatorze. |
| ESC |Naciśnij klawisz Esc, aby zamknąć menu lub aby anulować i zamknij stronę. |
| F1 |Naciśnij klawisz F1, aby wyświetlić tematu pomocy dla aktualnie aktywnego okna. |
| F5 |Naciśnij klawisz F5, aby odświeżyć węzła. |
| F6 |Naciśnij klawisz F6, aby przenieść z **zakres** okienko, aby **wyniki** okienka. |
| F10 |Naciśnij klawisz F10, aby przejść do paska menu. |
| Strzałka w lewo |Użyj klawisza Strzałka w lewo poruszać się poziomo z opcji paska menu do poprzedniej opcji. W przypadku przenoszenia do poprzedniego elementu na pasku menu zostanie wyświetlone menu Akcja (lub kontekstu) dla poprzedniego elementu. |
| Klawisz strzałki w prawo |Umożliwia przenoszenie w poziomie z jednego menu opcji pasek do następnego klawisz strzałki w prawo. W przypadku przenoszenia do następnego elementu na pasku menu zostanie wyświetlone menu Akcja (lub kontekstu) dla nowego elementu. |
| Klawisz TAB |Użyj klawisza Tab, aby przejść do następnego okienka w konsoli lub do następnego pola wyboru lub tekstu na stronie. |
| Klawisz strzałki w górę |Umożliwia przenoszenie w pionie do poprzedniego elementu menu lub okienku klawisz Strzałka w górę. |

### <a name="menu-bar-shortcut-keys"></a>Menu paska klawiszy skrótów
W poniższej tabeli opisano kombinacje klawiszy skrótu dla paska menu. Po naciśnięciu klawiszy skrótów i otwiera menu, można użyć klawiszy skrótu w menu (podkreślone klawisze menu). Aby uzyskać więcej informacji na temat paska menu, przejdź do [paska Menu](#menu-bar).

| Skrót | wynik | Klawisz skrótu w menu | wynik |
|:--- |:--- |:--- |:--- |
| ALT + F |Otwiera **pliku** menu. |N |Otwiera nowe wystąpienie konsoli. |
|  |O |Otwiera **narzędzia administracyjne** strony. | |
|  |S |Zapisuje konsoli programu StorSimple Snapshot Manager. | |
|  |A |Otwiera **Zapisz jako** strony. | |
|  |M |Otwiera **Dodaj/Usuń przystawkę** strony. | |
|  |P |Otwiera **opcje** strony. | |
|  |H |Otwiera Pomoc online. | |
| ALT + A |Otwiera **akcji** menu. |I |Włącza opcję wyświetlania importu i wyłącza. |
|  |W |Otwiera nową konsolę programu StorSimple Snapshot Manager. | |
|  |F |Aktualizacje konsoli programu StorSimple Snapshot Manager. | |
|  |L |Otwiera **Eksportuj listę** strony. | |
|  |H |Otwiera Pomoc online. | |
| ALT + V |Otwiera **widoku** menu. |A |Otwiera **Dodaj/Usuń kolumny** strony. |
|  |U |Otwiera **Dostosuj widok** strony. | |
| ALT + O |Otwiera **ulubione** menu. |A |Otwiera **Dodaj do ulubionych** strony. |
|  |O |Otwiera **organizowanie ulubionych** strony. | |
| ALT + W |Otwiera **okna** menu. |N |Otwiera inne okno StorSimple Snapshot Manager. |
|  |C |Wyświetla wszystkie okna Otwórz konsolę stylów. | |
|  |T |Wyświetla wszystkie otwarte konsoli systemu windows w układzie siatki. | |
|  |I |Rozmieszcza ikony w rzędzie u dołu ekranu. | |
| ALT + H |Otwiera **pomocy** menu. |H |Otwiera Pomoc online. |
|  |T |Otwiera stronę sieci web Microsoft TechNet Tech Center. | |
|  |A |Otwiera **programu Microsoft Management Console** strony. | |

### <a name="scope-pane-shortcut-keys"></a>Klawisze skrótów okienka zakresu
W poniższych tabelach przedstawiono skrót kombinacje klawiszy dla każdego węzła w **zakres** okienka. 

* [Klawisze skrótów węzła urządzenia](#devices-node-shortcut-keys)
* [Klawisze skrótów węzła woluminów](#volumes-node-shortcut-keys)
* [Klawisze skrótów węzła grupy woluminu](#volume-groups-node-shortcut-keys)
* [Kopia zapasowa klawiszy skrótów węzła zasad](#backup-policies-node-shortcut-keys)
* [Kopia zapasowa klawiszy skrótów węzła katalogu](#backup-catalog-node-shortcut-keys)
* [Klawisze skrótów węzła zadania](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Klawisze skrótów węzła urządzenia
| Menu skrótów | wynik |
|:--- |:--- |
| C |Otwiera **Skonfiguruj urządzenie** strony. |
| D |Odświeża listę urządzeń i szczegóły urządzenia. |
| V |Otwiera **widoku** menu. |
| W |Otwiera nową konsolę programu StorSimple Snapshot Manager koncentruje się na **szczegóły** węzła. |
| F |Aktualizacje konsoli programu StorSimple Snapshot Manager. |
| L |Otwiera **Eksportuj listę** strony. |
| H |Otwiera Pomoc online. |

#### <a name="volumes-node-shortcut-keys"></a>Klawisze skrótów węzła woluminów
| Menu skrótów | wynik |
|:--- |:--- |
| V |Aktualizuje listę woluminów. |
| V (naciśnij dwa razy) |Otwiera **widoku** menu. |
| W |Otwiera nową konsolę programu StorSimple Snapshot Manager koncentruje się na **woluminów** węzła. |
| F |Aktualizacje konsoli programu StorSimple Snapshot Manager. |
| L |Otwiera **Eksportuj listę** strony. |
| H |Otwiera Pomoc online. |

#### <a name="volume-groups-node-shortcut-keys"></a>Klawisze skrótów węzła grupy woluminu
| Menu skrótów | wynik |
|:--- |:--- |
| G |Otwiera **Utwórz grupę woluminu** strony. |
| V |Otwiera **widoku** menu. |
| W |Otwiera nową konsolę programu StorSimple Snapshot Manager koncentruje się na **grup woluminu** węzła. |
| F |Aktualizacje konsoli programu StorSimple Snapshot Manager. |
| L |Otwiera **Eksportuj listę** strony. |
| H |Otwiera Pomoc online. |

#### <a name="backup-policies-node-shortcut-keys"></a>Kopia zapasowa klawiszy skrótów węzła zasad
| Menu skrótów | wynik |
|:--- |:--- |
| B |Otwiera **utworzyć zasadę** strony. |
| V |Otwiera **widoku** menu. |
| W |Otwiera nową konsolę programu StorSimple Snapshot Manager koncentruje się na **grup woluminu** węzła. |
| F |Aktualizacje konsoli programu StorSimple Snapshot Manager. |
| L |Otwiera ** Eksportuj listę ** strony. |
| H |Otwiera Pomoc online. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Kopia zapasowa klawiszy skrótów węzła katalogu
| Menu skrótów | wynik |
|:--- |:--- |
| W |Otwiera nową konsolę programu StorSimple Snapshot Manager koncentruje się na **grup woluminu** węzła. |
| F |Aktualizacje konsoli programu StorSimple Snapshot Manager. |
| H |Otwiera Pomoc online. |

#### <a name="jobs-node-shortcut-keys"></a>Klawisze skrótów węzła zadania
| Menu skrótów | wynik |
|:--- |:--- |
| V |Otwiera **widoku** menu. |
| W |Otwiera nową konsolę programu StorSimple Snapshot Manager koncentruje się na **zadania** węzła. |
| F |Aktualizacje konsoli programu StorSimple Snapshot Manager. |
| L |Otwiera **Eksportuj listę** strony. |
| H |Otwiera Pomoc online |

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zarządzać rozwiązania StorSimple przy użyciu programu StorSimple Snapshot Manager](storsimple-snapshot-manager-admin.md).
* Dowiedz się, jak [StorSimple Snapshot Manager umożliwia łączenie urządzenia i zarządzać nimi](storsimple-snapshot-manager-manage-devices.md).

