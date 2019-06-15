---
title: Interfejs użytkownika programu StorSimple Snapshot Manager | Dokumentacja firmy Microsoft
description: W tym artykule opisano interfejs użytkownika programu StorSimple Snapshot Manager i wyjaśniono, jak przy jego użyciu zarządzać zadania tworzenia kopii zapasowych i wykazem kopii zapasowych.
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
ms.openlocfilehash: 46225e5a332e035e4d1cc256e71c4b5d8686fd47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60845277"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Użyj przystawki StorSimple Snapshot Manager interfejsu użytkownika do zarządzania zadania tworzenia kopii zapasowych i wykazem kopii zapasowych

## <a name="overview"></a>Omówienie
StorSimple Snapshot Manager ma intuicyjnego interfejsu użytkownika, można użyć, aby przejąć kontrolę i zarządzanie kopiami zapasowymi. Ten samouczek zawiera wprowadzenie do interfejsu użytkownika i wyjaśniono, jak używać każdego składnika. Aby uzyskać szczegółowy opis programu StorSimple Snapshot Manager, zobacz [co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Opis konsoli
Aby wyświetlić interfejs użytkownika, kliknij ikonę programu StorSimple Snapshot Manager na pulpicie. Zostanie wyświetlone okno konsoli, jak pokazano na poniższej ilustracji.

![Okienka programu StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

W oknie konsoli zawiera pięć elementów głównych. Kliknij odpowiednie łącze, aby uzyskać pełny opis każdego elementu.

* [Pasek menu](#menu-bar) 
* [Pasek narzędzi](#tool-bar) 
* [Okienko zakresu](#scope-pane) 
* [Okienko wyników](#results-pane) 
* [Okienko akcji](#actions-pane) 

Ponadto programu StorSimple Snapshot Manager obsługuje [za pomocą klawiatury w nawigacji i liczbę skrótów](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Ułatwienia dostępu w konsoli
Interfejs użytkownika programu StorSimple Snapshot Manager obsługuje funkcje ułatwień dostępu, dostarczone przez system operacyjny Windows i program Microsoft Management Console (MMC), a także niektóre skróty klawiaturowe specyficzne dla programu StorSimple Snapshot Manager. 

* Aby uzyskać opis funkcji ułatwień dostępu Windows, przejdź do [skróty klawiaturowe dla Windows](https://support.microsoft.com/kb/126449). 
* Aby uzyskać opis funkcji ułatwień dostępu programu MMC, przejdź do [ułatwienia dostępu dla programu MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)
* Aby uzyskać opis funkcji ułatwień dostępu programu StorSimple Snapshot Manager, przejdź do [za pomocą klawiatury w nawigacji i skróty](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Pasek menu
Na pasku menu w górnej części okna konsoli zawiera [pliku](#file-menu), [akcji](#action-menu), [widoku](#view-menu), [ulubione](#favorites-menu), [okna](#window-menu), i [pomocy](#help-menu) menu.

Kliknij dowolny element widoczny na pasku menu, aby wyświetlić listę dostępnych poleceń tym menu. W poniższym przykładzie przedstawiono **widoku** menu zaznaczone na pasku menu.

![Menu Widok wybrane](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>menu Plik
**Pliku** menu zawiera standardowe polecenia programu Microsoft Management Console (MMC).

#### <a name="menu-access"></a>Dostęp do menu
Aby wyświetlić **pliku** menu, kliknij przycisk **pliku** na pasku menu. Zostanie wyświetlone następujące menu.

![Menu Plik programu StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Opis elementu menu
W poniższej tabeli opisano elementy, które pojawiają się na **pliku** menu.

| Element menu | Opis |
|:--- |:--- |
| Nowa |Kliknij przycisk **New** do utworzenia nowej konsoli, na podstawie Menedżera migawek StorSimple. |
| Otwarty |Kliknij przycisk **Otwórz** otworzyć istniejącej konsoli. |
| Zapisanie |Kliknij przycisk **Zapisz** można zapisać bieżącą konsolę. |
| Zapisz jako |Kliknij przycisk **Zapisz jako** do utworzenia wystąpienia nowego, których nazwy zostały zmienione z bieżącej konsoli. Użyj **Zapisz jako** opcję, aby dostosować widok i zapisać go do nowszej pobierania. Na przykład można utworzyć przystawki StorSimple Snapshot Manager, które odwołują się do określonych serwerów. |
| Dodawanie/Usuwanie przystawki |Kliknij przycisk **Dodaj/Usuń przystawkę** Dodawanie lub usuwanie przystawek i organizowania węzłów na **zakres** okienka. Aby uzyskać więcej informacji, przejdź do [Dodaj, Usuń oraz Organizuj przystawki i rozszerzenia w programie MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Opcje |Kliknij przycisk **opcje** Aby zmienić ikonę konsoli, określ tryby dostępu do użytkowników i uprawnienia lub usunąć pliki konsoli w celu zwiększenia dostępnego miejsca na dysku. |
| Lista ścieżek plików |Kliknij na ścieżkę w listę numerowaną, aby ponownie otworzyć plik, który niedawno został otwarty. |
| Zakończ |Kliknij przycisk **zakończenia** zamknąć **pliku** menu. |

### <a name="action-menu"></a>Menu akcji
Użyj **akcji** menu, aby dokonać wyboru spośród dostępnych akcji. Dostępne elementy są zależne od wyboru w **zakres** okienka lub **wyniki** okienka.

#### <a name="menu-access"></a>Dostęp do menu
Aby wyświetlić **akcji** menu, wykonaj jedną z następujących czynności:

* Kliknij prawym przyciskiem myszy element w **zakres** okienka lub **wyniki** okienka.
* Wybierz element w **zakres** okienka lub **wyniki** okienku, a następnie kliknij przycisk **akcji** na pasku menu. 

Na przykład, jeśli zaznacz górny węzeł w **zakres** okienka, a następnie kliknij prawym przyciskiem myszy lub kliknij **akcji** na pasku menu, zostanie wyświetlone następujące menu.

![Menu akcji programu StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

**Akcje** panelu (po prawej stronie konsoli) zawiera listę tych samych czynności, jak **akcji** menu. Ponadto **akcje** okienko zawiera **widoku** opcje menu, które umożliwiają utworzenie widoku niestandardowego **wyniki** okienka.

![Otwórz okienko akcji z menu Widok](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Opis elementu menu
Poniższa tabela zawiera alfabetyczną listę akcji programu StorSimple Snapshot Manager. 

* **Akcji** kolumna zawiera listę działań, które można wykonać na węzłach, a wyniki. 
* **Nawigacji** kolumny wyjaśnia sposób wyświetlania odpowiednie **akcji** menu, w którym można wybrać akcję. Niektóre akcje pojawią się w wielu **akcji** menu. Do wykonywania tych akcji, wybierz jedną **nawigacji** opcję z listy punktowanej. 
* **Opis** kolumnie opisano sposób użycia każdej akcji na **akcji** menu lub okienka działań oraz wyjaśniono, jak działa.

> [!NOTE]
> **Akcje** okienka i **akcji** menu zawierają dodatkowe opcje, takie jak **widoku**, **nowe okno, w tym miejscu**,  **Odśwież**, **Eksportuj listę**, i **pomocy**. Te opcje są dostępne jako część programu MMC, a nie są specyficzne do programu StorSimple Snapshot Manager. Tabela zawiera opisy tych opcji.
> 
> 

| Akcja | Nawigacja | Opis |
|:--- |:--- |:--- |
| Uwierzytelnianie |Kliknij przycisk **urządzeń** węzeł i kliknij prawym przyciskiem myszy urządzenie w **wyniki** okienka. |Kliknij przycisk **Uwierzytelnij** wprowadzenia hasła, który został skonfigurowany dla tego urządzenia. |
| Klonowanie |Rozwiń **wykaz kopii zapasowych**, rozwiń węzeł **migawki w chmurze**, kliknij przycisk z kopii zapasowej, a następnie wybierz wolumin w **wyniki** okienka. |Kliknij przycisk **klonowania** utworzyć kopii migawki w chmurze i zapisz go w lokalizacji, które zostało określone. |
| Konfigurowanie urządzenia |Kliknij prawym przyciskiem myszy **urządzeń** węzła. |Kliknij przycisk **skonfigurować urządzenie** do skonfigurowania jednego lub wielu urządzeń, aby połączyć się z hostem Windows. |
| Tworzenie zasad kopii zapasowych |Wykonaj jedną z następujących czynności:<ul><li>Kliknij prawym przyciskiem myszy **zasady tworzenia kopii zapasowych**.</li><li>Kliknij przycisk lub rozszerzeniu **grupami woluminów**, a następnie kliknij prawym przyciskiem myszy grupę woluminu.</li><li>Kliknij przycisk lub rozszerzeniu **katalog kopii zapasowej**, a następnie kliknij prawym przyciskiem myszy grupę woluminu.</li></ul> |Kliknij przycisk **Tworzenie zasad kopii zapasowych** skonfigurować zaplanowane tworzenie kopii zapasowej dla grupy woluminu. |
| Tworzenie grupy woluminów |Wykonaj jedną z następujących czynności:<ul><li>Kliknij przycisk **woluminów** węzła, a następnie kliknij prawym przyciskiem myszy wolumin, w **wyniki** okienka.</li><li>Kliknij prawym przyciskiem myszy **grupami woluminów** węzła.</li></ul> |Kliknij przycisk **Utwórz grupę woluminu** Aby przypisać woluminy do grupy woluminu. |
| Usuwanie |Kliknij węzeł lub wynik (ten element będzie wyświetlany na wielu **akcji** menu i **akcje** okienek.) |Kliknij przycisk **Usuń** można usunąć węzła lub wynik, który wybrano. Gdy pojawi się okno dialogowe potwierdzenia, upewnij się, lub Anuluj operację. |
| Szczegóły |Kliknij przycisk **urządzeń** węzła, a następnie kliknij prawym przyciskiem myszy urządzenie w **wyniki** okienka. |Kliknij przycisk **szczegóły** Aby wyświetlić szczegóły konfiguracji urządzenia. |
| Edytuj |Kliknij przycisk **zasady tworzenia kopii zapasowych**, a następnie kliknij prawym przyciskiem myszy zasadę w **wyniki** okienka. |Kliknij przycisk **Edytuj** Aby zmienić harmonogram tworzenia kopii zapasowej grupy woluminu. |
| Eksportuj listę |Kliknij dowolny węzeł lub wynik (ten element będzie wyświetlany na wszystkich **akcji** menu i **akcje** okienek.) |Kliknij przycisk **Eksportuj listę** można zapisać listę w pliku wartości rozdzielanych przecinkami (CSV). Następnie można zaimportować ten plik do arkusza kalkulacyjnego aplikacji do analizy. |
| Help |Kliknij dowolny węzeł lub wynik. (Ten element będzie wyświetlany na wszystkich **akcji** menu i **akcje** okienek.) |Kliknij przycisk **pomocy** otworzyć Pomoc online w osobnym oknie przeglądarki. |
| Nowe okno, w tym miejscu |Kliknij dowolny węzeł lub wynik (ten element będzie wyświetlany na wszystkich **akcji** menu i **akcje** okienek.) |Kliknij przycisk **nowe okno, w tym miejscu** otworzyć nowe okno programu StorSimple Snapshot Manager. |
| Odśwież |Kliknij dowolny węzeł lub wynik (ten element będzie wyświetlany na wszystkich **akcji** menu i **akcje** okienek.) |Kliknij przycisk **Odśwież** można zaktualizować aktualnie wyświetlane okna programu StorSimple Snapshot Manager. |
| Odśwież urządzenie |Kliknij przycisk **urządzeń** węzeł i kliknij prawym przyciskiem myszy urządzenie w **wyniki** okienka. |Kliknij przycisk **Odśwież urządzenie** do synchronizacji określonego urządzenia połączone za pomocą przystawki StorSimple Snapshot Manager. |
| Odśwież urządzenia |Kliknij prawym przyciskiem myszy **urządzeń** węzła. |Kliknij przycisk **Odśwież urządzeń** do synchronizacji listy urządzeń połączonych z programu StorSimple Snapshot Manager. |
| Skanuj ponownie woluminów |Kliknij prawym przyciskiem myszy **woluminów** węzła. |Kliknij przycisk **Skanuj ponownie woluminy** można zaktualizować listę woluminów, które pojawia się w **wyniki** okienka. |
| Przywracanie |Rozwiń **wykaz kopii zapasowych**, rozwiń grupę woluminu, rozwiń węzeł **migawki lokalne** lub **migawki w chmurze**, a następnie kliknij prawym przyciskiem myszy kopii zapasowej. |Kliknij przycisk **przywrócić** zastąpić bieżące dane woluminu w grupie przy użyciu danych z wybranej kopii zapasowej. |
| Wykonaj kopię zapasową |Wykonaj jedną z następujących czynności:<ul><li>Rozwiń **grupami woluminów**, a następnie kliknij prawym przyciskiem myszy grupę woluminu.</li><li>Rozwiń **katalog kopii zapasowej**, a następnie kliknij prawym przyciskiem myszy grupę woluminu.</li></ul> |Kliknij przycisk **wykonać kopii zapasowej** Aby natychmiast uruchomić zadanie tworzenia kopii zapasowej. |
| Przełącz wyświetlanie Importy |Kliknij prawym przyciskiem myszy najwyższy węzeł w **zakres** okienko ( **programu StorSimple Snapshot Manager** węzła w przykładach). |Kliknij przycisk **Przełącz wyświetlanie Importy** pokazać lub ukryć wolumin grup i skojarzonych kopii zapasowych, które zostały zaimportowane na pulpicie nawigacyjnym usługi Menedżer urządzeń StorSimple. |

### <a name="view-menu"></a>Menu Widok
Użyj **widoku** menu, aby utworzyć niestandardowy widok **wyniki** w okienku zawartości. **Widoku** menu zawiera **Dodaj/Usuń kolumny** i **Dostosuj** opcje.

#### <a name="menu-access"></a>Dostęp do menu
Możesz uzyskać dostęp **widoku** menu w menu, pasek lub w **akcje** okienka.

![Menu Widok programu StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Opis elementu menu
W poniższej tabeli opisano elementy, które pojawiają się na **widoku** menu.

| Element menu | Opis |
|:--- |:--- |
| Dodawanie/usuwanie kolumn |Kliknij przycisk **Dodaj/Usuń kolumny** Dodawanie lub usuwanie kolumn w **wyniki** okienka. |
| Dostosuj |Kliknij przycisk **Dostosuj** pokazać lub ukryć elementy w oknie konsoli programu StorSimple Snapshot Manager. |

### <a name="favorites-menu"></a>Menu Ulubione
Użyj **ulubione** menu dodawania, usuwania i organizowanie wyświetleń stron i zadania, które są często używane. 

#### <a name="menu-access"></a>Dostęp do menu
Możesz uzyskać dostęp **ulubione** menu na pasku menu.

![Menu Ulubione programu StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Opis elementu menu
W poniższej tabeli opisano elementy, które pojawiają się na **ulubione** menu.

| Element menu | Opis |
|:--- |:--- |
| Dodaj do ulubionych |Kliknij przycisk **Dodaj do ulubionych** dodać bieżący widok do listy ulubionych. |
| Skróty do ulubionych |Kliknij przycisk **organizowanie ulubionych** organizować zawartość folderu Ulubione. |

### <a name="window-menu"></a>Menu okna
Użyj **okna** menu, aby dodać i rozmieszczanie okna konsoli programu StorSimple Snapshot Manager.

#### <a name="menu-access"></a>Dostęp do menu
Możesz uzyskać dostęp **okna** menu na pasku menu.

![Menu okna programu StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

Przedstawia listę numerowaną w dolnej części menu otwarte okna, które są obecnie dostępne. Kliknij przycisk dowolnego okna na tej liście, aby zapewnić w oknie na pierwszym planie. 

#### <a name="menu-description"></a>Opis elementu menu
W poniższej tabeli opisano elementy, które pojawiają się w menu Okno.

| Element menu | Opis |
|:--- |:--- |
| Nowe okno |Kliknij przycisk **nowe okno** otworzyć nowe okno konsoli (jako uzupełnienie istniejących okno). |
| Kaskadowe |Kliknij przycisk **Cascade** do wyświetlenia okna konsoli otwórz w stylów. |
| Sąsiadująco w poziomie |Kliknij przycisk **sąsiadująco w poziomie** do wyświetlenia okna konsoli otwórz w formacie Kafelek (lub siatki). |
| Rozmieść ikony |Jeśli masz wiele konsoli systemu windows Otwórz i rozproszone na pulpicie, zminimalizować je, a następnie kliknij przycisk **Rozmieść ikony** rozmieścić je w rzędzie w dolnej części ekranu. |

### <a name="help-menu"></a>Menu Pomoc
Użyj **pomocy** menu, aby wyświetlić dostępne Pomoc online dla programu StorSimple Snapshot Manager i programu MMC. Można również wyświetlić informacje o konsoli MMC i przystawki StorSimple Snapshot Manager wersje oprogramowania, które są aktualnie zainstalowane w systemie. 

Możesz uzyskać dostęp **pomocy** menu na pasku menu. Można także przejść do programu StorSimple Snapshot Manager tematy Pomocy z **akcje** okienka.

![Menu Pomoc programu StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Opis elementu menu
W poniższej tabeli opisano elementy, które są wyświetlane w menu Pomoc.

| Element menu | Opis |
|:--- |:--- |
| Pomoc na temat programu StorSimple Snapshot Manager |Kliknij przycisk **pomocy na programu StorSimple Snapshot Manager** do otwierania przystawki StorSimple Snapshot Manager pomocy w osobnym oknie. |
| Tematy pomocy |Kliknij przycisk **tematy Pomocy** otworzyć Pomoc online programu MMC w osobnym oknie. |
| Witryny sieci Web Centrum TechCenter oprogramowania |Kliknij przycisk **witryny sieci Web Centrum TechCenter oprogramowania** aby otworzyć stronę główną Microsoft TechNet Tech Center w osobnym oknie. |
| O programie Microsoft Management Console |Kliknij przycisk **Microsoft Management Console** która wersja programu Microsoft Management Console jest zainstalowana w Twoim systemie. |
| StorSimple Snapshot Manager |Kliknij przycisk **StorSimple Snapshot Manager** wersji przystawki jest zainstalowana w Twoim systemie. |

## <a name="tool-bar"></a>Pasek narzędzi
Pasek narzędzi, znajdujący się poniżej paska menu zawiera ikony nawigacji oraz zadań. Każda ikona to skrót do konkretnego zadania.

### <a name="icon-descriptions"></a>Ikona opisów
W poniższej tabeli opisano ikon, które są wyświetlane na pasku narzędzi. 

| Ikona | Opis |
|:--- |:--- |
| ![Strzałka w lewo](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Kliknij ikonę strzałki w lewo, aby powrócić do poprzedniej strony. |
| ![Strzałka w prawo](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Kliknij strzałkę w prawo, aby przejść do następnej strony (jeśli strzałkę jest szary, akcja jest niedostępna). |
| ![Ikona w dół](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Kliknij ikonę się do góry o jeden poziom w drzewie konsoli ( **zakres** okienko). |
| ![Pokaż/Ukryj drzewo konsoli](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Kliknij ikonę Pokaż/Ukryj konsoli drzewa pokazać lub ukryć **zakres** okienka. |
| ![Eksportuj listę](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Kliknij ikonę eksportowania listy, aby wyeksportować listę do pliku CSV, który określisz. |
| ![Ikona pomocy](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Kliknij ikonę Pomoc, aby otworzyć tematu Pomocy online programu MMC. |
| ![Pokaż/Ukryj okienko akcji](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Kliknij pozycję Pokaż/Ukryj **akcje** ikonę w okienku, aby pokazać lub ukryć **akcje** okienka. |

## <a name="scope-pane"></a>Okienko zakresu
**Zakres** okienko jest skrajnie po lewej stronie okienka w Interfejsie programu StorSimple Snapshot Manager. Zawiera drzewo konsoli (lub węzeł) i jest mechanizm podstawowa Nawigacja do programu StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Zakres okienka struktury
**Zakres** okienko zawiera szereg możesz klikać obiektów (węzły) strukturę drzewa. 

![Okienko zakresu](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Aby rozwinąć lub zwinąć węzeł, kliknij ikonę strzałki obok nazwy węzła.
* Aby wyświetlić stan lub zawartość węzła, kliknij nazwę węzła. Informacje zamieszczone w **wyniki** okienka. 

**Zakres** okienko zawiera następujące węzły: 

* [Węzła urządzenia](#devices-node) 
* [Węzeł woluminów](#volumes-node) 
* [Węzeł grupy woluminów](#volume-groups-node) 
* [Węzeł Zasady tworzenia kopii zapasowej](#backup-policies-node) 
* [Węzeł katalogu kopii zapasowej](#backup-catalog-node) 
* [Węzeł zadań](#jobs-node) 

### <a name="scope-pane-tasks"></a>Zakres okienka zadań
Możesz użyć **zakres** okienko, aby ukończyć akcji w określonym węźle. Aby wybrać zadanie, wykonaj jedną z następujących czynności:

* Kliknij prawym przyciskiem myszy węzeł, a następnie wybierz zadanie z wyświetlonego menu.
* Kliknij węzeł, a następnie kliknij przycisk **akcji** na pasku menu. Wybierz zadanie z wyświetlonego menu.
* Kliknij węzeł, a następnie wybierz akcję w **akcje** okienka.

Po wybraniu węzła i użyć dowolnej z tych metod umożliwia wyświetlenie listy zadań, są wyświetlane tylko akcje, które mogą być wykonywane w tym węźle.

### <a name="devices-node"></a>Węzła urządzenia
**Urządzeń** reprezentuje węzeł urządzenia StorSimple i urządzeń wirtualnych StorSimple, które są podłączone do programu StorSimple Snapshot Manager. Wybierz ten węzeł, aby nawiązać połączenie i skonfiguruj urządzenie, a następnie zaimportować jego skojarzone woluminy, grupy woluminów i istniejące kopie zapasowe. Wiele urządzeń można połączyć z jednego hosta.

* Aby rozwinąć węzeł, kliknij ikonę strzałki **urządzeń**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy **urządzeń** węzła lub kliknij prawym przyciskiem myszy dowolnego z węzłów, które są wyświetlane w widoku rozszerzonym.
* Aby wyświetlić listę skonfigurowanych urządzeń, kliknij przycisk **urządzeń** w **zakres** okienka. Zostanie wyświetlona lista urządzeń, wraz z informacjami o wszystkich urządzeniach w **wyniki** okienka.

### <a name="volumes-node"></a>Węzeł woluminów
**Woluminów** reprezentuje węzeł dysków, które odnoszą się do woluminów zainstalowanych w wyniku hosta, łącznie z tymi odnalezione za pomocą iSCSI i te odnalezione przez urządzenie. Aby wyświetlić listę dostępnych woluminów i przypisać jeden z woluminów do grupami woluminów za pomocą tego węzła.

* Aby rozwinąć węzeł, kliknij ikonę strzałki **woluminów**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy **woluminów** węzła lub kliknij prawym przyciskiem myszy dowolnego z węzłów, które są wyświetlane w widoku rozszerzonym.
* Aby wyświetlić listę woluminów, kliknij **woluminów** w **zakres** okienka. Zostanie wyświetlona lista woluminów, wraz z informacjami na temat każdego woluminu w **wyniki** okienka.

### <a name="volume-groups-node"></a>Węzeł grupy woluminów
Grupami woluminów są również nazywane grupy spójności. Każda grupa woluminu jest puli woluminy związane z aplikacją, która pomaga w zapewnieniu spójności aplikacji podczas wykonywania operacji tworzenia kopii zapasowej. Użyj **grupami woluminów** węzła skonfigurować te grupy, a także Twórz kopie zapasowe interakcyjnego lub utworzyć harmonogramów tworzenia kopii zapasowych. 

* Aby rozwinąć węzeł, kliknij ikonę strzałki **grupami woluminów**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy **grupami woluminów** węzła lub kliknij prawym przyciskiem myszy dowolnego z węzłów, które są wyświetlane w widoku rozszerzonym.
* Aby wyświetlić listę grup wolumin, kliknij przycisk **grupami woluminów** w **zakres** okienka. Zostanie wyświetlona lista grup woluminu, wraz z informacjami o każdej grupy woluminu w **wyniki** okienka.

### <a name="backup-policies-node"></a>Węzeł Zasady tworzenia kopii zapasowej
Zasady tworzenia kopii zapasowych są harmonogramy zadań lokalne i migawki w chmurze. Użyj **zasady tworzenia kopii zapasowych** węzeł, aby określić częstotliwość tworzenia kopii zapasowej i jak długo kopia zapasowa ma być przechowywana. 

* Aby rozwinąć węzeł, kliknij ikonę strzałki **zasady tworzenia kopii zapasowych**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy **zasady tworzenia kopii zapasowych** węzła lub kliknij prawym przyciskiem myszy dowolnego z węzłów, które są wyświetlane w widoku rozszerzonym.
* Aby wyświetlić listę zasad tworzenia kopii zapasowych, kliknij przycisk **zasady tworzenia kopii zapasowych** w **zakres** okienka. Zostanie wyświetlona lista zasad tworzenia kopii zapasowych, wraz z informacjami o poszczególnych zasad w **wyniki** okienka.

> [!NOTE]
> Może przechowywać maksymalnie 64 kopie zapasowe.


### <a name="backup-catalog-node"></a>Węzeł katalogu kopii zapasowej
**Katalog kopii zapasowej** węzła zawiera listy lokalnych i przechowywanych poza siedzibą firmy kopii zapasowych woluminów StorSimple systemu Azure. Ten węzeł jest zorganizowana według grupy woluminów, a każdy kontener grupy wolumin zawiera osobne struktury dla lokalnych migawek ( **migawka lokalna**węzeł) i migawki w chmurze ( **migawki w chmurze** węzła). Po rozwinięciu kontener grupy Każdy wolumin zawiera listę wszystkich pomyślnie tworzyć kopie zapasowe, które zostały wykonane interaktywnie lub przy użyciu skonfigurowanych zasad.

* Aby rozwinąć węzeł, kliknij ikonę strzałki **katalog kopii zapasowej**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy **katalog kopii zapasowej** węzła lub kliknij prawym przyciskiem myszy dowolnego z węzłów, które są wyświetlane w widoku rozszerzonym.
* Aby wyświetlić listę migawek kopii zapasowych, kliknij przycisk **wykaz kopii zapasowych** w **zakres** okienka. Zostanie wyświetlona lista migawki, wraz z informacjami o każdej migawki w **wyniki** okienka.

### <a name="local-snapshots-node"></a>Lokalny węzeł migawek
**Migawki lokalne** węzła zawiera lokalne migawki dla grupy określonego woluminu. Węzeł znajduje się w folderze **katalog kopii zapasowej** w węźle **zakres** okienka. Migawki lokalne są kopiami w momencie woluminu danych, które są przechowywane na urządzeniu StorSimple systemu Azure. Zazwyczaj ten typ kopii zapasowej można tworzyć i przywracane szybko. Możesz użyć lokalnych migawek, jak w przypadku lokalnej kopii zapasowej.

* Aby rozwinąć węzeł, kliknij ikonę strzałki **migawki lokalne**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy **migawki lokalne** węzła lub kliknij prawym przyciskiem myszy dowolnego z węzłów, które są wyświetlane w widoku rozszerzonym.
* Aby wyświetlić listę migawek lokalnych, kliknij przycisk **migawki lokalne** w **zakres** okienka. Zostanie wyświetlona lista migawki, wraz z informacjami o każdej migawki w **wyniki** okienka.

### <a name="cloud-snapshots-node"></a>Węzeł migawki w chmurze
**Migawki w chmurze** węzła wymieniono migawki w chmurze dla grupy określonego woluminu. Węzeł znajduje się w folderze **katalog kopii zapasowej** w węźle **zakres** okienka. Migawki w chmurze są kopiami w momencie woluminu danych, które są przechowywane w chmurze. Migawka w chmurze jest równoznaczne z migawki replikowane w systemie magazynu inną, poza siedzibą firmy. Migawki w chmurze są szczególnie przydatne w przypadku scenariuszy odzyskiwania po awarii.

* Aby rozwinąć węzeł, kliknij ikonę strzałki **migawki w chmurze**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy **migawki w chmurze** węzła lub kliknij prawym przyciskiem myszy dowolnego z węzłów, które są wyświetlane w widoku rozszerzonym.
* Aby wyświetlić listę migawek w chmurze, kliknij przycisk **migawki w chmurze** w **zakres** okienka. Zostanie wyświetlona lista migawki, wraz z informacjami o każdej migawki w **wyniki** okienka.

### <a name="jobs-node"></a>Węzeł zadań
**Zadań** węzła zawiera informacje o zaplanowanym, uruchamianie i ostatnio wykonanych zadań tworzenia kopii zapasowej. 

* Aby rozwinąć węzeł, kliknij ikonę strzałki **zadań**.
* Aby wyświetlić menu dostępne akcje, kliknij prawym przyciskiem myszy **zadań** węzła lub kliknij prawym przyciskiem myszy dowolnego z węzłów, które są wyświetlane w widoku rozszerzonym.
* Aby wyświetlić listę zadań zaplanowanych, rozwiń **zadania** węzłem, a następnie kliknij przycisk **zaplanowane**. Zostanie wyświetlona lista skonfigurowanych wcześniej zadania i informacji na temat każdego zadania w **wyniki** okienka. 
* Aby wyświetlić listę ostatnio wykonanych zadań, rozwiń **zadania** węzłem, a następnie kliknij przycisk **ostatnie 24 godziny**. Zostanie wyświetlona lista zadań, które zostały ukończone w ciągu ostatnich 24 godzin w **wyniki** okienka. **Wyniki** okienko zawiera także informacje o każdym ukończone zadanie.
* Aby wyświetlić listę zadań, które są aktualnie uruchomione, rozwiń **zadania** węzłem, a następnie kliknij przycisk **systemem**. Zostanie wyświetlona lista aktualnie uruchomionych zadań i informacje dotyczące każdego zadania w **wyniki** okienka.

## <a name="results-pane"></a>Okienko wyników
**Wyniki** okienko jest w środkowym okienku w Interfejsie programu StorSimple Snapshot Manager. Zawiera listy i szczegółowe informacje o stanie dla węzła wybranego w **zakres** okienka.

### <a name="example"></a>Przykład
Aby wyświetlić w poniższym przykładzie, kliknij **grupami woluminów** w węźle **zakres** okienka. **Wyniki** okienko Wyświetla listę grup woluminu ze szczegółami dla każdej grupy.

![Okienko wyników](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Można skonfigurować szczegółowe informacje wyświetlane w **wyniki** okienka: kliknij prawym przyciskiem myszy węzeł w **zakres** okienku kliknij **widoku**, a następnie kliknij przycisk **Dodaj/Usuń kolumny** .

## <a name="actions-pane"></a>Okienko akcji
**Akcje** okienko jest w okienku po prawej stronie w Interfejsie programu StorSimple Snapshot Manager. Zawiera menu operacji, które można wykonywać na węzeł, widoku lub dane wybrane w **zakres** okienka lub **wyniki** okienka. **Akcje** okienko zawiera te same polecenia co **akcji** menu, które są dostępne dla elementów w **zakres** okienka i **wyniki** okienko. Aby uzyskać opis każdej akcji, zobacz tabelę w **akcji** sekcji menu.

### <a name="examples"></a>Przykłady
Aby zobaczyć, w poniższym przykładzie **zakres** okienku rozwiń **zadania** węzła i kliknij przycisk **zaplanowane**. **Akcje** okienko wyświetla Akcje dostępne dla **zaplanowane** węzła.

![Przykład zaplanowanych zadań w okienku Akcje](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Aby wyświetlić więcej opcji w **zakres** okienku rozwiń **zadania** węzła, kliknij przycisk **zaplanowane**, a następnie kliknij przycisk zaplanowanego zadania w **wyniki** okienko. **Akcje** okienko wyświetla Akcje dostępne dla zaplanowanego zadania, jak pokazano w poniższym przykładzie.

![Przykład akcji zadań w okienku Akcje](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Nawigowanie przy użyciu klawiatury i skróty
Menedżer migawek StorSimple umożliwia funkcje ułatwień dostępu systemu operacyjnego Windows i program Microsoft Management Console (MMC). Zawiera także niektóre funkcje nawigacji klawiatury i skróty, które są specyficzne do programu StorSimple Snapshot Manager, zgodnie z opisem w poniższych sekcjach.

* [Klawisze nawigacji](#keyboard-navigation-keys) 
* [Menu paska klawiszy skrótów](#menu-bar-shortcut-keys) 
* [Klawisze skrótów okienka zakresu](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Klawisze nawigacji
W poniższej tabeli opisano klucze, które można użyć do nawigacji interfejsu użytkownika programu StorSimple Snapshot Manager. 

| Klucz nawigacji | Akcja |
|:--- |:--- |
| Strzałka w dół |Klawisz strzałki w dół umożliwia przeniesienie w pionie do następnego elementu w menu lub okienka. |
| Enter |Naciśnij klawisz Enter, aby ukończyć akcji, a następnie przejść do następnego kroku. Można na przykład, naciśnij klawisz Enter, aby wybrać **dalej**, **OK**, lub **Utwórz**, a następnie przejdź do następnego kroku w kreatorze. |
| Esc |Naciśnij klawisz Esc, aby zamknąć menu lub Anuluj i zamknij stronę. |
| F1 |Naciśnij klawisz F1, aby wyświetlić tematu pomocy dla aktualnie aktywnego okna. |
| F5 |Naciśnij klawisz F5, aby odświeżyć węzeł. |
| F6 |Naciśnij klawisz F6, aby przenieść z **zakres** okienku **wyniki** okienka. |
| F10 |Naciśnij klawisz F10, aby przejść do paska menu. |
| Strzałka w lewo |Użyj klawisza Strzałka w lewo do poprzedniej opcji, a następnie przenieść w poziomie z poziomu opcji paska menu. Po przeniesieniu do poprzedniego elementu na pasku menu, zostanie wyświetlone menu akcji (lub kontekstu), dla poprzedniego elementu. |
| Strzałka w prawo |Użyj klawisz strzałki w prawo do następnej, a następnie przenieść w poziomie z poziomu jednego menu paska. Przejdzie do następnego elementu na pasku menu, zostanie wyświetlone menu akcji (lub kontekstu), dla nowego elementu. |
| Klawisz TAB |Użyj klawisza Tab Aby przenieść do następnego okienka w konsoli lub następne pole wyboru lub tekstu na stronie. |
| Klawisz Strzałka w górę |Użyj klawisz Strzałka w górę można przenieść w pionie do poprzedniego elementu menu lub okienka. |

### <a name="menu-bar-shortcut-keys"></a>Menu paska klawiszy skrótów
W poniższej tabeli opisano kombinacji klawiszy skrótów dla paska menu. Po naciśnięciu klawiszy skrótów i otwiera menu, można użyć menu skrótów (podkreślony klucze menu). Aby uzyskać więcej informacji na temat paska menu, przejdź do [pasek Menu](#menu-bar).

| Skrót | Wynik | Klawisz skrótu w menu | Wynik |
|:--- |:--- |:--- |:--- |
| ALT+F |Otwiera **pliku** menu. |Nie |Zostanie otwarte nowe wystąpienie konsoli. |
|  |O |Otwiera **narzędzia administracyjne** strony. | |
|  |S |Zapisuje konsolę programu StorSimple Snapshot Manager. | |
|  |A |Otwiera **Zapisz jako** strony. | |
|  |M |Otwiera **Dodaj/Usuń przystawkę** strony. | |
|  |P |Otwiera **opcje** strony. | |
|  |H |Otwiera Pomoc online. | |
| ALT+A |Otwiera **akcji** menu. |I |Włącza opcję wyświetlania importowania i wyłącza. |
|  |W |Otwiera nową konsolę programu StorSimple Snapshot Manager. | |
|  |F |Aktualizacje konsoli programu StorSimple Snapshot Manager. | |
|  |L |Otwiera **Eksportuj listę** strony. | |
|  |H |Otwiera Pomoc online. | |
| ALT+V |Otwiera **widoku** menu. |A |Otwiera **Dodaj/Usuń kolumny** strony. |
|  |U |Otwiera **Dostosowywanie widoku** strony. | |
| ALT+O |Otwiera **ulubione** menu. |A |Otwiera **Dodaj do ulubionych** strony. |
|  |O |Otwiera **organizowanie ulubionych** strony. | |
| ALT+W |Otwiera **okna** menu. |Nie |Otwiera inne okno przystawki StorSimple Snapshot Manager. |
|  |C |Wyświetla wszystkie systemy windows Otwórz konsolę stylów. | |
|  |T |Wyświetla wszystkie systemy windows Otwórz konsolę we wzorcu siatki. | |
|  |I |Rozmieszcza ikony na poziomie wiersza w dolnej części ekranu. | |
| ALT+H |Otwiera **pomocy** menu. |H |Otwiera Pomoc online. |
|  |T |Otwiera stronę sieci web Microsoft TechNet Tech Center. | |
|  |A |Otwiera **Microsoft Management Console** strony. | |

### <a name="scope-pane-shortcut-keys"></a>Klawisze skrótów okienka zakresu
W poniższych tabelach przedstawiono skrót kombinacje klawiszy dla każdego węzła w **zakres** okienka. 

* [Klawisze skrótów węzła urządzenia](#devices-node-shortcut-keys)
* [Klawisze skrótów węzła woluminów](#volumes-node-shortcut-keys)
* [Klawisze skrótów węzła grupy woluminów](#volume-groups-node-shortcut-keys)
* [Klawisze skrótów węzła w usłudze kopii zapasowej zasad](#backup-policies-node-shortcut-keys)
* [Klawisze skrótów węzła w usłudze kopii zapasowej katalogu](#backup-catalog-node-shortcut-keys)
* [Klawisze skrótów węzła zadania](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Klawisze skrótów węzła urządzenia
| Menu skrótów | Wynik |
|:--- |:--- |
| C |Otwiera **skonfigurować urządzenie** strony. |
| D |Odświeża listę urządzeń i szczegóły urządzeń. |
| V |Otwiera **widoku** menu. |
| W |Otwiera nową konsolę programu StorSimple Snapshot Manager koncentruje się na **szczegóły** węzła. |
| F |Aktualizacje konsoli programu StorSimple Snapshot Manager. |
| L |Otwiera **Eksportuj listę** strony. |
| H |Otwiera Pomoc online. |

#### <a name="volumes-node-shortcut-keys"></a>Klawisze skrótów węzła woluminów
| Menu skrótów | Wynik |
|:--- |:--- |
| V |Aktualizuje listę woluminów. |
| V (naciśnij dwa razy) |Otwiera **widoku** menu. |
| W |Otwiera nową konsolę programu StorSimple Snapshot Manager koncentruje się na **woluminów** węzła. |
| F |Aktualizacje konsoli programu StorSimple Snapshot Manager. |
| L |Otwiera **Eksportuj listę** strony. |
| H |Otwiera Pomoc online. |

#### <a name="volume-groups-node-shortcut-keys"></a>Klawisze skrótów węzła grupy woluminów
| Menu skrótów | Wynik |
|:--- |:--- |
| G |Otwiera **Utwórz grupę woluminu** strony. |
| V |Otwiera **widoku** menu. |
| W |Otwiera nową konsolę programu StorSimple Snapshot Manager koncentruje się na **grupami woluminów** węzła. |
| F |Aktualizacje konsoli programu StorSimple Snapshot Manager. |
| L |Otwiera **Eksportuj listę** strony. |
| H |Otwiera Pomoc online. |

#### <a name="backup-policies-node-shortcut-keys"></a>Klawisze skrótów węzła w usłudze kopii zapasowej zasad
| Menu skrótów | Wynik |
|:--- |:--- |
| B |Otwiera **Utwórz zasadę** strony. |
| V |Otwiera **widoku** menu. |
| W |Otwiera nową konsolę programu StorSimple Snapshot Manager koncentruje się na **grupami woluminów** węzła. |
| F |Aktualizacje konsoli programu StorSimple Snapshot Manager. |
| L |Otwiera ** Eksportuj listę ** strony. |
| H |Otwiera Pomoc online. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Klawisze skrótów węzła w usłudze kopii zapasowej katalogu
| Menu skrótów | Wynik |
|:--- |:--- |
| W |Otwiera nową konsolę programu StorSimple Snapshot Manager koncentruje się na **grupami woluminów** węzła. |
| F |Aktualizacje konsoli programu StorSimple Snapshot Manager. |
| H |Otwiera Pomoc online. |

#### <a name="jobs-node-shortcut-keys"></a>Klawisze skrótów węzła zadania
| Menu skrótów | Wynik |
|:--- |:--- |
| V |Otwiera **widoku** menu. |
| W |Otwiera nową konsolę programu StorSimple Snapshot Manager koncentruje się na **zadań** węzła. |
| F |Aktualizacje konsoli programu StorSimple Snapshot Manager. |
| L |Otwiera **Eksportuj listę** strony. |
| H |Otwiera Pomoc online |

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [za pomocą Menedżera migawek StorSimple do administrowania rozwiązania StorSimple](storsimple-snapshot-manager-admin.md).
* Dowiedz się, jak [do nawiązywania połączeń i zarządzanie urządzeniami za pomocą Menedżera migawek StorSimple](storsimple-snapshot-manager-manage-devices.md).

