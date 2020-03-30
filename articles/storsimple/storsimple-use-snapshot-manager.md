---
title: Interfejs użytkownika StorSimple Snapshot Manager | Dokumenty firmy Microsoft
description: W tym artykule opisano interfejs użytkownika StorSimple Snapshot Manager i wyjaśniono, jak go używać do zarządzania zadaniami tworzenia kopii zapasowych i katalogu kopii zapasowych.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.custom: ''
ms.openlocfilehash: 56771d2e62289485017f34c6a9ab26e1d53610ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933972"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Interfejs użytkownika StorSimple Snapshot Manager umożliwia zarządzanie zadaniami tworzenia kopii zapasowych i katalogiem kopii zapasowych

## <a name="overview"></a>Omówienie
Menedżer migawek StorSimple ma intuicyjny interfejs użytkownika, którego można używać do wykonywania kopii zapasowych i zarządzania nimi. Ten samouczek zawiera wprowadzenie do interfejsu użytkownika, a następnie wyjaśniono, jak korzystać z każdego ze składników. Aby uzyskać szczegółowy opis Menedżera migawek StorSimple, zobacz [Co to jest Menedżer migawek StorSimple?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Opis konsoli
Aby wyświetlić interfejs użytkownika, kliknij ikonę StorSimple Snapshot Manager na pulpicie. Zostanie wyświetlone okno konsoli, jak pokazano na poniższej ilustracji.

![Okienka Menedżera migawek StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

Okno konsoli ma pięć głównych elementów. Kliknij odpowiednie łącze, aby uzyskać pełny opis każdego elementu.

* [Pasek menu](#menu-bar) 
* [Pasek narzędzi](#tool-bar) 
* [Okienko zakresu](#scope-pane) 
* [Okienko Wyniki](#results-pane) 
* [Okienko Akcje](#actions-pane) 

Ponadto Menedżer migawek StorSimple obsługuje [nawigację za pomocą klawiatury i szereg skrótów](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Ułatwienia dostępu do konsoli
Interfejs użytkownika StorSimple Snapshot Manager obsługuje funkcje ułatwień dostępu udostępniane przez system operacyjny Windows i program Microsoft Management Console (MMC), a także niektóre skróty klawiaturowe specyficzne dla Menedżera migawek StorSimple. 

* Aby uzyskać opis funkcji ułatwień dostępu systemu Windows, przejdź do [skrótów klawiaturowych dla systemu Windows](https://support.microsoft.com/kb/126449). 
* Opis funkcji ułatwień dostępu programu MMC można uzyskać, przejdź do [aplikacji Accessibility for MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)
* Aby uzyskać opis funkcji ułatwień dostępu Menedżera migawek StorSimple, przejdź do [nawigacji po klawiaturze i skrótów](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Pasek menu
Pasek menu w górnej części okna konsoli zawiera menu [Plik,](#file-menu) [Akcja,](#action-menu) [Widok,](#view-menu) [Ulubione,](#favorites-menu) [Okno](#window-menu)i [Pomoc.](#help-menu)

Kliknij dowolny element na pasku menu, aby wyświetlić listę dostępnych poleceń w tym menu. W poniższym przykładzie przedstawiono menu **Widok** wybrane na pasku menu.

![Wybrane menu Widok](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Menu Plik
Menu **Plik** zawiera standardowe polecenia programu Microsoft Management Console (MMC).

#### <a name="menu-access"></a>Dostęp do menu
Aby wyświetlić menu **Plik,** kliknij pozycję **Plik** na pasku menu. Pojawi się następujące menu.

![Menu StorSimple Snapshot Manager File menu StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Opis menu
W poniższej tabeli opisano elementy wyświetlane w menu **Plik.**

| Pozycja menu | Opis |
|:--- |:--- |
| Nowa |Kliknij **przycisk Nowy,** aby utworzyć nową konsolę na podstawie Menedżera migawek StorSimple. |
| Otwarcie |Kliknij **przycisk Otwórz,** aby otworzyć istniejącą konsolę. |
| Zapisz |Kliknij **przycisk Zapisz,** aby zapisać bieżącą konsolę. |
| Zapisz jako |Kliknij **przycisk Zapisz jako,** aby utworzyć nowe, zmienione wystąpienie bieżącej konsoli. Użyj opcji **Zapisz jako,** aby dostosować widok i zapisać go do późniejszego pobrania. Na przykład można utworzyć StorSimple Snapshot Manager przystawek, które wskazują na określone serwery. |
| Dodawanie/usuwanie przystawki |Kliknij **pozycję Dodaj/Usuń przystawkę,** aby dodać lub usunąć przystawki i uporządkować węzły w okienku **Zakres.** Aby uzyskać więcej informacji, przejdź do [dodawania, usuwania i organizowania przystawek i rozszerzeń w programie MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Opcje |Kliknij **pozycję Opcje,** aby zmienić ikonę konsoli, określić tryby i uprawnienia dostępu użytkownika lub usunąć pliki konsoli, aby zwiększyć dostępne miejsce na dysku. |
| Lista ścieżek plików |Kliknij ścieżkę na liście numerowanych, aby ponownie otworzyć ostatnio otwarty plik. |
| Zakończ |Kliknij **przycisk Wyjdź,** aby zamknąć menu **Plik.** |

### <a name="action-menu"></a>Menu Akcji
Użyj menu **Akcja,** aby wybrać spośród dostępnych akcji. Dostępne elementy zależą od zaznaczenia w okienku **Zakres** lub **Wyniki.**

#### <a name="menu-access"></a>Dostęp do menu
Aby wyświetlić menu **Akcja,** wykonaj jedną z następujących czynności:

* Kliknij prawym przyciskiem myszy element w okienku **Zakres** lub **Wyniki.**
* Zaznacz element w okienku **Zakres** lub **Wyniki,** a następnie kliknij pozycję **Akcja** na pasku menu. 

Jeśli na przykład wybierzesz górny węzeł w okienku **Zakres,** a następnie kliknij prawym przyciskiem myszy lub kliknij polecenie **Akcja** na pasku menu, zostanie wyświetlene następujące menu.

![StorSimple Migawka Manager Menu akcji](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

Okienko **Akcje** (po prawej stronie konsoli) zawiera tę samą listę akcji, co menu **Akcja.** Ponadto okienko **Akcje** zawiera opcje menu **Widok,** które umożliwiają utworzenie widoku niestandardowego okienka **Wyniki.**

![Okienko Akcje z otwartym menu Widok](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Opis menu
Poniższa tabela zawiera alfabetyczną listę akcji Menedżera migawek StorSimple. 

* Kolumna **Akcja** zawiera listę akcji, które można wykonywać w węzłach i wynikach. 
* W kolumnie **Nawigacja** wyjaśniono, jak wyświetlić odpowiednie menu **Akcja,** aby można było wybrać akcję. Niektóre akcje są wyświetlane w wielu menu **akcji.** W przypadku tych akcji wybierz jedną opcję **Nawigacji** z listy punktowanej. 
* W kolumnie **Opis** opisano sposób używania każdej akcji w menu **Akcja** lub okienku Akcje i wyjaśniono, co robi.

> [!NOTE]
> Okienko **Akcje** i menu **Akcja** zawierają dodatkowe opcje, takie jak **Widok,** **Nowe okno w tym miejscu**, **Odśwież,** **Lista eksportu**i **Pomoc**. Te opcje są dostępne jako część programu MMC i nie są specyficzne dla StorSimple Snapshot Manager. Tabela zawiera opisy tych opcji.
> 
> 

| Akcja | Nawigacja | Opis |
|:--- |:--- |:--- |
| Uwierzytelnianie |Kliknij węzeł **Urządzenia,** a następnie kliknij prawym przyciskiem myszy urządzenie w okienku **Wyniki.** |Kliknij **przycisk Uwierzytelnij,** aby wprowadzić hasło skonfigurowane dla urządzenia. |
| Klonowanie |Rozwiń **pozycję Wykaz kopii zapasowych**, rozwiń węzeł **Migawki w chmurze,** kliknij datę kopii zapasowej, a następnie wybierz wolumin w okienku **Wyniki.** |Kliknij **przycisk Klonuj,** aby utworzyć kopię migawki w chmurze i przechowywać ją w wyznaczonej lokalizacji. |
| Konfigurowanie urządzenia |Kliknij prawym przyciskiem myszy węzeł **Urządzenia.** |Kliknij **pozycję Konfiguruj urządzenie,** aby skonfigurować jedno urządzenie lub wiele urządzeń do łączenia się z hostem systemu Windows. |
| Tworzenie zasad tworzenia kopii zapasowych |Wykonaj jedną z następujących czynności:<ul><li>Kliknij prawym przyciskiem myszy pozycję **Zasady tworzenia kopii zapasowych**.</li><li>Kliknij lub rozwiń **grupy woluminów**, a następnie kliknij prawym przyciskiem myszy grupę woluminów.</li><li>Kliknij lub rozwiń **pozycję Wykaz kopii zapasowych**, a następnie kliknij prawym przyciskiem myszy grupę woluminów.</li></ul> |Kliknij **pozycję Utwórz zasady tworzenia kopii zapasowych,** aby skonfigurować zaplanowaną kopię zapasową dla grupy woluminów. |
| Tworzenie grupy woluminów |Wykonaj jedną z następujących czynności:<ul><li>Kliknij węzeł **Woluminy,** a następnie kliknij prawym przyciskiem myszy wolumin w okienku **Wyniki.**</li><li>Kliknij prawym przyciskiem myszy węzeł **Grupy woluminów.**</li></ul> |Kliknij **pozycję Utwórz grupę woluminów,** aby przypisać woluminy do grupy woluminów. |
| Usuń |Kliknij węzeł lub wynik (ten element jest wyświetlany w wielu menu **akcji** i okienkach **Akcje).** |Kliknij **przycisk Usuń,** aby usunąć wybrany węzeł lub wynik. Po wyświetleniu okna dialogowego potwierdzenia potwierdź lub anuluj usunięcie. |
| Szczegóły |Kliknij węzeł **Urządzenia,** a następnie kliknij prawym przyciskiem myszy urządzenie w okienku **Wyniki.** |Kliknij **przycisk Szczegóły,** aby wyświetlić szczegóły konfiguracji urządzenia. |
| Edytuj |Kliknij **pozycję Zasady tworzenia kopii zapasowych**, a następnie kliknij prawym przyciskiem myszy zasadę w okienku **Wyniki.** |Kliknij **przycisk Edytuj,** aby zmienić harmonogram tworzenia kopii zapasowych dla grupy woluminów. |
| Lista eksportu |Kliknij dowolny węzeł lub wynik (ten element jest wyświetlany we wszystkich menu **akcji** i okienkach **Akcje).** |Kliknij **pozycję Eksportuj listę,** aby zapisać listę w pliku csv (skomp. Następnie można zaimportować ten plik do aplikacji arkusza kalkulacyjnego do analizy. |
| Pomoc |Kliknij dowolny węzeł lub wynik. (Ten element jest wyświetlany we wszystkich menu **akcji** i okienkach **Akcje).** |Kliknij **przycisk Pomoc,** aby otworzyć Pomoc online w osobnym oknie przeglądarki. |
| Nowe okno w tym miejscu |Kliknij dowolny węzeł lub wynik (ten element jest wyświetlany we wszystkich menu **akcji** i okienkach **Akcje).** |Kliknij **pozycję Nowe okno w tym miejscu,** aby otworzyć nowe okno Menedżera migawek StorSimple. |
| Odświeżanie |Kliknij dowolny węzeł lub wynik (ten element jest wyświetlany we wszystkich menu **akcji** i okienkach **Akcje).** |Kliknij **przycisk Odśwież,** aby zaktualizować aktualnie wyświetlane okno Menedżera migawek StorSimple. |
| Urządzenie odświeżania |Kliknij węzeł **Urządzenia,** a następnie kliknij prawym przyciskiem myszy urządzenie w okienku **Wyniki.** |Kliknij **przycisk Odśwież urządzenie,** aby zsynchronizować określone podłączone urządzenie z Menedżerem migawek StorSimple. |
| Odświeżanie urządzeń |Kliknij prawym przyciskiem myszy węzeł **Urządzenia.** |Kliknij **przycisk Odśwież urządzenia,** aby zsynchronizować listę połączonych urządzeń z Menedżerem migawek StorSimple. |
| Ponowne przeskanowywki |Kliknij prawym przyciskiem myszy węzeł **Woluminy.** |Kliknij **pozycję Ponownie przeskanuj woluminy,** aby zaktualizować listę woluminów wyświetlanych w okienku **Wyniki.** |
| Przywracanie |Rozwiń **pozycję Wykaz kopii zapasowych,** rozwiń grupę woluminów, rozwiń węzeł **Migawki lokalne** lub **Migawki w chmurze,** a następnie kliknij prawym przyciskiem myszy kopię zapasową. |Kliknij **przycisk Przywróć,** aby zastąpić bieżące dane grupy woluminów danymi z wybranej kopii zapasowej. |
| Wykonaj kopię zapasową |Wykonaj jedną z następujących czynności:<ul><li>Rozwiń **pozycję Grupy woluminów**, a następnie kliknij prawym przyciskiem myszy grupę woluminów.</li><li>Rozwiń **pozycję Wykaz kopii zapasowych**, a następnie kliknij prawym przyciskiem myszy grupę woluminów.</li></ul> |Kliknij **przycisk Zrób kopię zapasową,** aby natychmiast rozpocząć zadanie tworzenia kopii zapasowej. |
| Przełączanie wyświetlania importów |Kliknij prawym przyciskiem myszy górny węzeł w okienku **Zakres** (węzeł **Menedżer migawek StorSimple** w przykładach). |Kliknij **pozycję Przełącz wyświetlanie importu,** aby wyświetlić lub ukryć grupy woluminów i skojarzone kopie zapasowe zaimportowane z pulpitu nawigacyjnego usługi Menedżer urządzeń StorSimple. |

### <a name="view-menu"></a>Menu Widok
Użyj menu **Widok,** aby utworzyć widok niestandardowy zawartości okienka **Wyniki.** Menu **Widok** zawiera opcje **Dodaj/Usuń kolumny** i **Dostosuj.**

#### <a name="menu-access"></a>Dostęp do menu
Dostęp do menu **Widok** można uzyskać na pasku menu lub w okienku **Akcje.**

![Menu Widok menedżera migawek StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Opis menu
W poniższej tabeli opisano elementy wyświetlane w menu **Widok.**

| Pozycja menu | Opis |
|:--- |:--- |
| Dodawanie/usuwanie kolumn |Kliknij **pozycję Dodaj/Usuń kolumny,** aby dodać lub usunąć kolumny w okienku **Wyniki.** |
| Dostosowywanie |Kliknij **przycisk Dostosuj,** aby wyświetlić lub ukryć elementy w oknie Konsoli Menedżera migawek StorSimple. |

### <a name="favorites-menu"></a>Menu Ulubione
Menu **Ulubione** służy do dodawania, usuwania i organizowania często używanych widoków stron i zadań. 

#### <a name="menu-access"></a>Dostęp do menu
Dostęp do menu **Ulubione** można uzyskać na pasku menu.

![StorSimple Migawka Manager Ulubione menu](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Opis menu
W poniższej tabeli opisano elementy wyświetlane w menu **Ulubione.**

| Pozycja menu | Opis |
|:--- |:--- |
| Dodaj do ulubionych |Kliknij **pozycję Dodaj do ulubionych,** aby dodać bieżący widok do listy ulubionych. |
| Organizowanie ulubionych |Kliknij **pozycję Organizuj ulubione,** aby uporządkować zawartość folderu Ulubione. |

### <a name="window-menu"></a>Menu okna
Użyj menu **Okno,** aby dodać i zmienić kolejność okien konsoli Menedżera migawek StorSimple.

#### <a name="menu-access"></a>Dostęp do menu
Dostęp do menu **Okno** można uzyskać na pasku menu.

![Menu okna menedżera migawek StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

Lista numerowana u dołu menu pokazuje okna, które są aktualnie otwarte. Kliknij dowolne okno na tej liście, aby przenieść okno na pierwszy plan. 

#### <a name="menu-description"></a>Opis menu
W poniższej tabeli opisano elementy, które pojawiają się w menu Okno.

| Pozycja menu | Opis |
|:--- |:--- |
| Nowe okno |Kliknij **pozycję Nowe okno,** aby otworzyć nowe okno konsoli (oprócz istniejącego okna). |
| Kaskadowo |Kliknij **przycisk Kaskada,** aby wyświetlić otwarte okna konsoli w stylu kaskadowym. |
| Kafelek w poziomie |Kliknij **pozycję Kafelek w poziomie,** aby wyświetlić otwarte okna konsoli w formacie kafelka (lub siatki). |
| Rozmieszczanie ikon |Jeśli masz wiele otwartych i rozproszonych okien konsoli na pulpicie, zminimalizuj je, a następnie kliknij pozycję **Rozmieszcz ikony,** aby rozmieścić je w wierszu poziomym u dołu ekranu. |

### <a name="help-menu"></a>Menu Pomoc
Użyj menu **Pomoc,** aby wyświetlić dostępną pomoc online dla Menedżera migawek StorSimple i programu MMC. Można również wyświetlić informacje o wersjach oprogramowania PROGRAMU MMC i StorSimple Snapshot Manager, które są aktualnie zainstalowane w systemie. 

Dostęp do menu **Pomoc** można uzyskać na pasku menu. Można również uzyskać dostęp do tematów pomocy StorSimple Snapshot Manager z panelu **Akcje.**

![Menu Pomocy Menedżera migawek StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Opis menu
W poniższej tabeli opisano elementy wyświetlane w menu Pomoc.

| Pozycja menu | Opis |
|:--- |:--- |
| Pomoc dotycząca Menedżera migawek StorSimple |Kliknij **przycisk Pomoc w Menedżerze migawek StorSimple,** aby otworzyć pomoc Menedżera migawek StorSimple w osobnym oknie. |
| Tematy Pomocy |Kliknij pozycję **Tematy Pomocy,** aby otworzyć pomoc online programu MMC w osobnym oknie. |
| Witryna sieci Web usługi TechCenter |Kliknij pozycję **Witryna sieci Web TechCenter,** aby otworzyć stronę główną centrum technologicznego microsoft technet w osobnym oknie. |
| Informacje o programie Microsoft Management Console |Kliknij pozycję **Microsoft Management Console** — aby zobaczyć, która wersja programu Microsoft Management Console jest zainstalowana w systemie. |
| Menedżer migawek StorSimple – informacje |Kliknij pozycję **StorSimple Snapshot Manager,** aby zobaczyć, która wersja przystawki jest zainstalowana w systemie. |

## <a name="tool-bar"></a>Pasek narzędzi
Pasek narzędzi znajdujący się pod paskiem menu zawiera ikony nawigacji i zadań. Każda ikona jest skrótem do określonego zadania.

### <a name="icon-descriptions"></a>Opisy ikon
W poniższej tabeli opisano ikony wyświetlane na pasku narzędzi. 

| Ikona | Opis |
|:--- |:--- |
| ![Strzałka w lewo](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Kliknij ikonę strzałki w lewo, aby powrócić do poprzedniej strony. |
| ![Strzałka w prawo](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Kliknij strzałkę w prawo, aby przejść do następnej strony (jeśli strzałka jest szara, akcja jest niedostępna). |
| ![Ikona W górę](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Kliknij ikonę w górę, aby przejść o jeden poziom w drzewie konsoli (okienko **Zakres).** |
| ![Pokaż/ukryj drzewo konsoli](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Kliknij ikonę drzewa pokaż/ukryj konsolę, aby wyświetlić lub ukryć okienko **Zakres.** |
| ![Lista eksportu](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Kliknij ikonę listy eksportu, aby wyeksportować listę do określonego pliku CSV. |
| ![Ikona Pomocy](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Kliknij ikonę pomocy, aby otworzyć temat pomocy programu MMC online. |
| ![Pokaż/ukryj okienko Akcje](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Kliknij ikonę okienka **Pokaż/ukryj akcje,** aby wyświetlić lub ukryć okienko **Akcje.** |

## <a name="scope-pane"></a>Okienko zakresu
Okienko **Zakres** jest okienkiem po lewej stronie w interfejsie użytkownika Menedżera migawek StorSimple. Zawiera ono drzewo konsoli (lub węzła) i jest podstawowym mechanizmem nawigacyjnym dla Menedżera migawek StorSimple. 

### <a name="scope-pane-structure"></a>Struktura okienka zakresów
**Okienko Zakres** zawiera serię klikalnych obiektów (węzłów) zorganizowanych w strukturze drzewa. 

![Okienko zakresu](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Aby rozwinąć lub zwinąć węzeł, kliknij ikonę strzałki obok nazwy węzła.
* Aby wyświetlić stan lub zawartość węzła, kliknij nazwę węzła. Informacje zostaną wyświetlene w okienku **Wyniki.** 

Okienko **Zakres** zawiera następujące węzły: 

* [Węzeł Urządzenia](#devices-node) 
* [Węzeł woluminów](#volumes-node) 
* [Węzeł Grupy woluminów](#volume-groups-node) 
* [Węzeł Zasady tworzenia kopii zapasowych](#backup-policies-node) 
* [Węzeł wykazu kopii zapasowych](#backup-catalog-node) 
* [Węzeł Zadania](#jobs-node) 

### <a name="scope-pane-tasks"></a>Zadania okienka zakresu
Za pomocą okienka **Zakres** można wykonać akcję w określonym węźle. Aby wybrać zadanie, wykonaj jedną z następujących czynności:

* Kliknij prawym przyciskiem myszy węzeł, a następnie wybierz zadanie z wyświetlanego menu.
* Kliknij węzeł, a następnie kliknij pozycję **Akcja** na pasku menu. Wybierz zadanie z wyświetlanego menu.
* Kliknij węzeł, a następnie wybierz akcję w okienku **Akcje.**

Po wybraniu węzła i użyciu dowolnej z tych metod, aby wyświetlić listę zadań, wyświetlane są tylko te akcje, które można wykonać w tym węźle.

### <a name="devices-node"></a>Węzeł Urządzenia
**Węzeł Urządzenia** reprezentuje urządzenia StorSimple i urządzenia wirtualne StorSimple, które są podłączone do Menedżera migawek StorSimple. Wybierz ten węzeł, aby połączyć i skonfigurować urządzenie, a także zaimportować skojarzone z nim woluminy, grupy woluminów i istniejące kopie zapasowe. Do jednego hosta można podłączyć wiele urządzeń.

* Aby rozwinąć węzeł, kliknij ikonę strzałki obok **pozycji Urządzenia**.
* Aby wyświetlić menu dostępnych akcji, kliknij prawym przyciskiem myszy węzeł **Urządzenia** lub kliknij prawym przyciskiem myszy dowolny z węzłów wyświetlanych w widoku rozwiniętym.
* Aby wyświetlić listę skonfigurowanych urządzeń, kliknij pozycję **Urządzenia** w okienku **Zakres.** Lista urządzeń wraz z informacjami o każdym urządzeniu zostanie wyświetlona w okienku **Wyniki.**

### <a name="volumes-node"></a>Węzeł woluminów
Węzeł **Woluminy** reprezentuje dyski, które odpowiadają woluminom zamontowanym przez hosta, w tym woluminom wykrytym za pośrednictwem interfejsu iSCSI i dyskom wykrytym za pośrednictwem urządzenia. Ten węzeł służy do wyświetlania listy dostępnych woluminów i przypisywania poszczególnych woluminów do grup woluminów.

* Aby rozwinąć węzeł, kliknij ikonę strzałki obok **pozycji Woluminy**.
* Aby wyświetlić menu dostępnych akcji, kliknij prawym przyciskiem myszy węzeł **Woluminy** lub kliknij prawym przyciskiem myszy dowolny z węzłów wyświetlanych w widoku rozwiniętym.
* Aby wyświetlić listę woluminów, kliknij pozycję **Woluminy** w okienku **Zakres.** Lista woluminów wraz z informacjami o każdym woluminie jest wyświetlana w okienku **Wyniki.**

### <a name="volume-groups-node"></a>Węzeł Grupy woluminów
Grupy woluminów są również nazywane grupami spójności. Każda grupa woluminów jest pulą woluminów związanych z aplikacją, która pomaga zapewnić spójność aplikacji podczas operacji tworzenia kopii zapasowych. Węzeł **Grupy woluminów** służy do konfigurowania tych grup i wykonywania interaktywnych kopii zapasowych lub tworzenia harmonogramów tworzenia kopii zapasowych. 

* Aby rozwinąć węzeł, kliknij ikonę strzałki obok pozycji **Grupy woluminów**.
* Aby wyświetlić menu dostępnych akcji, kliknij prawym przyciskiem myszy węzeł **Grupy woluminów** lub kliknij prawym przyciskiem myszy dowolny z węzłów wyświetlanych w widoku rozwiniętym.
* Aby wyświetlić listę grup woluminów, kliknij pozycję **Grupy woluminów** w okienku **Zakres.** Lista grup woluminów wraz z informacjami o każdej grupie woluminów jest wyświetlana w okienku **Wyniki.**

### <a name="backup-policies-node"></a>Węzeł Zasady tworzenia kopii zapasowych
Zasady tworzenia kopii zapasowych to harmonogramy zadań dla migawek lokalnych i w chmurze. Użyj **węzła Zasady tworzenia kopii zapasowych,** aby określić, jak często jest tworzona kopia zapasowa i jak długo kopia zapasowa powinna być zachowywana. 

* Aby rozwinąć węzeł, kliknij ikonę strzałki obok **pozycji Zasady kopii zapasowej**.
* Aby wyświetlić menu dostępnych akcji, kliknij prawym przyciskiem myszy węzeł **Zasady tworzenia kopii zapasowych** lub kliknij prawym przyciskiem myszy dowolny z węzłów wyświetlanych w widoku rozwiniętym.
* Aby wyświetlić listę zasad tworzenia kopii zapasowych, kliknij pozycję **Zasady kopii zapasowej** w okienku **Zakres.** Lista zasad tworzenia kopii zapasowych wraz z informacjami o poszczególnych zasadach jest wyświetlana w okienku **Wyniki.**

> [!NOTE]
> Można zachować maksymalnie 64 kopie zapasowe.


### <a name="backup-catalog-node"></a>Węzeł wykazu kopii zapasowych
Węzeł **wykazu kopii zapasowych** zawiera listy kopii zapasowych woluminów usługi Azure StorSimple w miejscu i poza siedzibą firmy. Ten węzeł jest zorganizowany według grupy woluminów, a każdy kontener grupy woluminów zawiera oddzielne struktury dla migawek lokalnych (węzeł **Migawka lokalna**s) i migawek w chmurze (węzeł **Migawki chmury).** Po rozwinięciu każdy kontener grupy woluminów wyświetla listę wszystkich pomyślnych kopii zapasowych, które zostały wykonane interaktywnie lub przez skonfigurowane zasady.

* Aby rozwinąć węzeł, kliknij ikonę strzałki obok **pozycji Wykaz kopii zapasowych**.
* Aby wyświetlić menu dostępnych akcji, kliknij prawym przyciskiem myszy węzeł **Wykaz kopii zapasowych** lub kliknij prawym przyciskiem myszy dowolny z węzłów wyświetlanych w widoku rozwiniętym.
* Aby wyświetlić listę migawek kopii zapasowych, kliknij pozycję **Wykaz kopii zapasowych** w okienku **Zakres.** Lista migawek wraz z informacjami o każdej migawce pojawia się w okienku **Wyniki.**

### <a name="local-snapshots-node"></a>Węzeł Migawki lokalne
Węzeł **Migawki lokalne** zawiera listę migawek lokalnych dla określonej grupy woluminów. Węzeł znajduje się w węźle **Wykaz kopii zapasowych** w okienku **Zakres.** Migawki lokalne są kopie w czasie danych woluminu, które są przechowywane na urządzeniu Usługi Azure StorSimple. Zazwyczaj ten typ kopii zapasowej można szybko utworzyć i przywrócić. Można użyć migawki lokalnej, tak jak lokalnej kopii zapasowej.

* Aby rozwinąć węzeł, kliknij ikonę strzałki obok **pozycji Migawki lokalne**.
* Aby wyświetlić menu dostępnych akcji, kliknij prawym przyciskiem myszy węzeł **Migawki lokalne** lub kliknij prawym przyciskiem myszy dowolny z węzłów wyświetlanych w widoku rozwiniętym.
* Aby wyświetlić listę migawek lokalnych, kliknij pozycję **Migawki lokalne** w okienku **Zakres.** Lista migawek wraz z informacjami o każdej migawce pojawia się w okienku **Wyniki.**

### <a name="cloud-snapshots-node"></a>Węzeł Migawki w chmurze
Węzeł **Migawki w chmurze** wyświetla listę migawek w chmurze dla określonej grupy woluminów. Węzeł znajduje się w węźle **Wykaz kopii zapasowych** w okienku **Zakres.** Migawki chmury są kopie w czasie danych woluminów, które są przechowywane w chmurze. Migawka w chmurze jest odpowiednikiem migawki replikowanej w innym systemie magazynu poza siedzibą firmy. Migawki chmury są szczególnie przydatne w scenariuszach odzyskiwania po awarii.

* Aby rozwinąć węzeł, kliknij ikonę strzałki obok **pozycji Migawki w chmurze**.
* Aby wyświetlić menu dostępnych akcji, kliknij prawym przyciskiem myszy węzeł **Migawki w chmurze** lub kliknij prawym przyciskiem myszy dowolny z węzłów wyświetlanych w widoku rozwiniętym.
* Aby wyświetlić listę migawek w chmurze, kliknij pozycję **Migawki** w chmurze w okienku **Zakres.** Lista migawek wraz z informacjami o każdej migawce pojawia się w okienku **Wyniki.**

### <a name="jobs-node"></a>Węzeł Zadania
Węzeł **Zadania** zawiera informacje o zaplanowanych, uruchomionych i niedawno zakończonych zadaniach tworzenia kopii zapasowych. 

* Aby rozwinąć węzeł, kliknij ikonę strzałki obok **pozycji Zadania**.
* Aby wyświetlić menu dostępnych akcji, kliknij prawym przyciskiem myszy węzeł **Zadania** lub kliknij prawym przyciskiem myszy dowolny z węzłów wyświetlanych w widoku rozwiniętym.
* Aby wyświetlić listę zaplanowanych zadań, rozwiń węzeł **Zadania,** a następnie kliknij przycisk **Zaplanowane**. Lista wcześniej skonfigurowanych zadań i informacje o każdym zadaniu są wyświetlane w okienku **Wyniki.** 
* Aby wyświetlić listę ostatnio ukończonych zadań, rozwiń węzeł **Zadania,** a następnie kliknij przycisk **Ostatnie 24 godziny**. W okienku **Wyniki** zostanie wyświetlona lista zadań ukończonych w ciągu ostatnich 24 godzin. **Okienko Wyniki** zawiera również informacje o każdym wykonanym zadaniu.
* Aby wyświetlić listę aktualnie uruchomionych zadań, rozwiń węzeł **Zadania,** a następnie kliknij przycisk **Uruchomiona**. Lista aktualnie uruchomionych zadań i informacje o każdym zadaniu są wyświetlane w okienku **Wyniki.**

## <a name="results-pane"></a>Okienko Wyniki
**Okienko Wyniki** jest centralnym okienkiem w interfejsie użytkownika Menedżera migawek StorSimple. Zawiera listy i szczegółowe informacje o stanie węzła wybranego w okienku **Zakres.**

### <a name="example"></a>Przykład
Aby wyświetlić poniższy przykład, kliknij węzeł **Grupy woluminów** w okienku **Zakres.** W okienku **Wyniki** jest wyświetlana lista grup woluminów ze szczegółami dotyczącymi każdej grupy.

![Okienko Wyniki](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Można skonfigurować szczegóły wyświetlane w okienku **Wyniki:** kliknij prawym przyciskiem myszy węzeł w okienku **Zakres,** kliknij polecenie **Widok**, a następnie kliknij polecenie **Dodaj/Usuń kolumny**.

## <a name="actions-pane"></a>Okienko Akcje
Okienko **Akcje** jest prawym okienkiem w interfejsie użytkownika Menedżera migawek StorSimple. Zawiera menu operacji, które można wykonać w węźle, widoku lub danych, które można wybrać w okienku **zakres** lub **wyniki** okienka. Okienko **Akcje** zawiera te same polecenia, co menu **Akcja,** które są dostępne dla elementów w okienku **Zakres** i **Wyniki.** Opis każdej akcji można znaleźć w tabeli w menu **Akcja.**

### <a name="examples"></a>Przykłady
Aby wyświetlić poniższy przykład, w okienku **Zakres** rozwiń węzeł **Zadania** i kliknij pozycję **Zaplanowane**. W okienku **Akcje** są wyświetlane dostępne akcje dla **węzła Zaplanowane.**

![Przykład zaplanowanych zadań okienka akcje](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Aby wyświetlić więcej opcji, w okienku **Zakres** rozwiń węzeł **Zadania,** kliknij pozycję **Zaplanowane**, a następnie kliknij zaplanowane zadanie w okienku **Wyniki.** W okienku **Akcje** są wyświetlane dostępne akcje dla zaplanowanego zadania, jak pokazano w poniższym przykładzie.

![Przykład akcji w okienku akcji](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Nawigacja za pomocą klawiatury i skróty
StorSimple Snapshot Manager umożliwia funkcje ułatwień dostępu systemu operacyjnego Windows i programu Microsoft Management Console (MMC). Zawiera również niektóre funkcje nawigacji klawiatury i skróty, które są specyficzne dla StorSimple Snapshot Manager, jak opisano w poniższych sekcjach.

* [Klawisze nawigacyjne klawiatury](#keyboard-navigation-keys) 
* [Klawisze skrótów paska menu](#menu-bar-shortcut-keys) 
* [Klawisze skrótów okienka zakresu](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Klawisze nawigacyjne klawiatury
W poniższej tabeli opisano klucze, których można używać do poruszania się po interfejsie użytkownika Menedżera migawek StorSimple. 

| Klucz nawigacyjny | Akcja |
|:--- |:--- |
| Klawisz strzałki w dół |Użyj klawisza strzałki w dół, aby przejść w pionie do następnego elementu w menu lub okienku. |
| Enter |Naciśnij klawisz Enter, aby wykonać akcję, a następnie przejdź do następnego kroku. Można na przykład nacisnąć klawisz Enter, aby wybrać pozycję **Dalej**, **OK**lub **Utwórz**, a następnie przejść do następnego kroku kreatora. |
| Esc |Naciśnij klawisz Esc, aby zamknąć menu lub anulować i zamknąć stronę. |
| F1 |Naciśnij klawisz F1, aby wyświetlić temat pomocy dla aktualnie aktywnego okna. |
| F5 |Naciśnij klawisz F5, aby odświeżyć węzeł. |
| F6 |Naciśnij klawisz F6, aby przejść z okienka **Zakres** do okienka **Wyniki.** |
| F10 |Naciśnij klawisz F10, aby przejść do paska menu. |
| Klawisz ze strzałką w lewo |Użyj klawisza strzałki w lewo, aby przejść w poziomie z opcji paska menu do poprzedniej opcji. Po przejściu do poprzedniego elementu na pasku menu zostanie wyświetlene menu akcji (lub kontekstu) dla poprzedniego elementu. |
| Klawisz ze strzałką w prawo |Użyj klawisza strzałki w prawo, aby przejść poziomo z jednej opcji paska menu do następnej. Po przejściu do następnego elementu na pasku menu pojawi się menu akcji (lub kontekstu) dla nowego elementu. |
| Klawisz tab |Użyj klawisza Tab, aby przejść do następnego okienka konsoli lub do następnego zaznaczenia lub pola tekstowego na stronie. |
| Strzałka w górę |Użyj klawisza strzałki w górę, aby przejść w pionie do poprzedniego elementu w menu lub okienku. |

### <a name="menu-bar-shortcut-keys"></a>Klawisze skrótów paska menu
W poniższej tabeli opisano kombinacje klawiszy skrótów dla paska menu. Po naciśnięciu klawiszy skrótów i otwarciu menu można użyć klawiszy skrótów menu (podkreślonych klawiszy w menu). Aby uzyskać więcej informacji na temat paska menu, przejdź do [paska menu](#menu-bar).

| Skrót | Wynik | Klawisz skrótu menu | Wynik |
|:--- |:--- |:--- |:--- |
| KLAWISZE ALT+F |Otwiera menu **Plik.** |Nie |Otwiera nowe wystąpienie konsoli. |
|  |O |Otwiera stronę **Narzędzia administracyjne.** | |
|  |S |Zapisuje konsolę Menedżera migawek StorSimple. | |
|  |A |Otwiera stronę **Zapisz jako.** | |
|  |M |Otwiera stronę **Dodaj/Usuń przystawkę.** | |
|  |P |Otwiera stronę **Opcje.** | |
|  |H |Otwiera pomoc online. | |
| ALT+A |Otwiera menu **Akcja.** |I |Włącza i wyłącza opcję wyświetlania importu. |
|  |W |Otwiera nową konsolę StorSimple Snapshot Manager. | |
|  |F |Aktualizuje konsolę Menedżera migawek StorSimple. | |
|  |L |Otwiera stronę **Eksportuj listę.** | |
|  |H |Otwiera pomoc online. | |
| KLAWISZE ALT+V |Otwiera menu **Widok.** |A |Otwiera stronę **Dodaj/Usuń kolumny.** |
|  |U |Otwiera stronę **Dostosuj widok.** | |
| ALT+O |Otwiera menu **Ulubione.** |A |Otwiera stronę **Dodaj do ulubionych.** |
|  |O |Otwiera stronę **Organizowanie ulubionych.** | |
| ALT+W |Otwiera menu **Okno.** |Nie |Otwiera kolejne okno Menedżera migawek StorSimple. |
|  |C |Wyświetla wszystkie otwarte okna konsoli w stylu kaskadowym. | |
|  |T |Wyświetla wszystkie otwarte okna konsoli w szyku siatki. | |
|  |I |Rozmieszcza ikony w poziomym wierszu u dołu ekranu. | |
| ALT+H |Otwiera menu **Pomoc.** |H |Otwiera pomoc online. |
|  |T |Otwiera stronę internetową centrum technicznego microsoft technet. | |
|  |A |Otwiera stronę **Konsoli zarządzania microsoft** — informacje. | |

### <a name="scope-pane-shortcut-keys"></a>Klawisze skrótów okienka zakresu
W poniższych tabelach przedstawiono kombinacje klawiszy skrótów dla każdego węzła w okienku **Zakres.** 

* [Klawisze skrótów węzła Urządzenia](#devices-node-shortcut-keys)
* [Klawisze skrótów węzła woluminów](#volumes-node-shortcut-keys)
* [Klawisze skrótów węzła Grupy woluminów](#volume-groups-node-shortcut-keys)
* [Klucze skrótów węzła Zasady kopii zapasowej](#backup-policies-node-shortcut-keys)
* [Klawisze skrótów węzła wykazu kopii zapasowych](#backup-catalog-node-shortcut-keys)
* [Klawisze skrótów węzła zadania](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Klawisze skrótów węzła Urządzenia
| Skrót menu | Wynik |
|:--- |:--- |
| C |Otwiera stronę **Konfigurowanie urządzenia.** |
| D |Odświeża listę urządzeń i szczegóły urządzenia. |
| V |Otwiera menu **Widok.** |
| W |Otwiera nową konsolę Menedżera migawek StorSimple skupioną na węźle **Szczegóły.** |
| F |Aktualizuje konsolę Menedżera migawek StorSimple. |
| L |Otwiera stronę **Eksportuj listę.** |
| H |Otwiera pomoc online. |

#### <a name="volumes-node-shortcut-keys"></a>Klawisze skrótów węzła woluminów
| Skrót menu | Wynik |
|:--- |:--- |
| V |Aktualizuje listę woluminów. |
| V (naciśnij dwukrotnie) |Otwiera menu **Widok.** |
| W |Otwiera nową konsolę StorSimple Snapshot Manager skupioną na węźle **Woluminy.** |
| F |Aktualizuje konsolę Menedżera migawek StorSimple. |
| L |Otwiera stronę **Eksportuj listę.** |
| H |Otwiera pomoc online. |

#### <a name="volume-groups-node-shortcut-keys"></a>Klawisze skrótów węzła Grupy woluminów
| Skrót menu | Wynik |
|:--- |:--- |
| G |Otwiera stronę **Tworzenie grupy woluminów.** |
| V |Otwiera menu **Widok.** |
| W |Otwiera nową konsolę StorSimple Snapshot Manager skupioną na węźle **Grupy woluminów.** |
| F |Aktualizuje konsolę Menedżera migawek StorSimple. |
| L |Otwiera stronę **Eksportuj listę.** |
| H |Otwiera pomoc online. |

#### <a name="backup-policies-node-shortcut-keys"></a>Klucze skrótów węzła Zasady kopii zapasowej
| Skrót menu | Wynik |
|:--- |:--- |
| B |Otwiera stronę **Utwórz zasady.** |
| V |Otwiera menu **Widok.** |
| W |Otwiera nową konsolę StorSimple Snapshot Manager skupioną na węźle **Grupy woluminów.** |
| F |Aktualizuje konsolę Menedżera migawek StorSimple. |
| L |Otwiera stronę **Lista eksportu **. |
| H |Otwiera pomoc online. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Klawisze skrótów węzła wykazu kopii zapasowych
| Skrót menu | Wynik |
|:--- |:--- |
| W |Otwiera nową konsolę StorSimple Snapshot Manager skupioną na węźle **Grupy woluminów.** |
| F |Aktualizuje konsolę Menedżera migawek StorSimple. |
| H |Otwiera pomoc online. |

#### <a name="jobs-node-shortcut-keys"></a>Klawisze skrótów węzła zadania
| Skrót menu | Wynik |
|:--- |:--- |
| V |Otwiera menu **Widok.** |
| W |Otwiera nową konsolę Menedżera migawek StorSimple skoncentrowaną na węźle **Zadania.** |
| F |Aktualizuje konsolę Menedżera migawek StorSimple. |
| L |Otwiera stronę **Eksportuj listę.** |
| H |Otwiera pomoc online |

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zarządzać rozwiązaniem StorSimple, za pomocą Menedżera migawek StorSimple.](storsimple-snapshot-manager-admin.md)
* Dowiedz się, jak [używać Menedżera migawek StorSimple do łączenia urządzeń i zarządzania nimi.](storsimple-snapshot-manager-manage-devices.md)

