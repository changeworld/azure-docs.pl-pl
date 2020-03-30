---
title: StorSimple Snapshot Manager akcje menu programu MMC | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób używania standardowych akcji menu programu Microsoft Management Console (MMC) w Menedżerze migawek StorSimple.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: ced075395c4f2a51308d5d3b0cbe4c0153a17816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931471"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Używanie akcji menu programu MMC w Menedżerze migawek StorSimple

## <a name="overview"></a>Omówienie
W StorSimple Snapshot Manager, zobaczysz następujące akcje wymienione we wszystkich menu akcji i wszystkie odmiany **akcji** okienka.

* Widok
* Nowe okno w tym miejscu 
* Odświeżanie 
* Lista eksportu 
* Pomoc 

Te akcje są częścią programu Microsoft Management Console (MMC) i nie są specyficzne dla StorSimple Snapshot Manager. W tym samouczku opisano te akcje i wyjaśniono, jak używać każdego z nich w StorSimple Snapshot Manager.

## <a name="view"></a>Widok
Za pomocą opcji **Widok** można zmienić widok **okienka Wyniki** i zmienić widok okna konsoli. 

#### <a name="to-change-the-results-pane-view"></a>Aby zmienić widok okienka Wyniki
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** kliknij prawym przyciskiem myszy dowolny węzeł lub rozwiń węzeł, a następnie kliknij prawym przyciskiem myszy element w okienku **Wyniki,** a następnie kliknij polecenie **Widok.** 
3. Aby dodać lub usunąć kolumny wyświetlane w okienku **Wyniki,** kliknij pozycję **Dodaj/Usuń kolumny**. Zostanie wyświetlone okno dialogowe **Dodawanie/usuwanie kolumn.**
   
    ![Dodawanie lub usuwanie kolumn z okienka Wyniki](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Wypełnij formularz w następujący sposób:
   
   * Wybierz elementy z listy **Dostępne** kolumny i kliknij przycisk **Dodaj,** aby dodać je do listy **Wyświetlane kolumny.** 
   * Kliknij pozycję Elementy na liście **Wyświetlane kolumny,** a następnie kliknij pozycję **Usuń,** aby usunąć je z listy. 
   * Zaznacz element na liście **Wyświetlane** kolumny i kliknij pozycję **Przenieś** w górę lub Przenieś **w dół,** aby przenieść element w górę lub w dół listy. 
   * Kliknij **przycisk Przywróć ustawienia domyślne,** aby powrócić do domyślnej konfiguracji okienka **Wyniki.** 
5. Po zakończeniu wyboru kliknij przycisk **OK**. 

#### <a name="to-change-the-console-window-view"></a>Aby zmienić widok okna konsoli
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** kliknij prawym przyciskiem myszy dowolny węzeł, kliknij polecenie **Widok**, a następnie kliknij polecenie **Dostosuj**. Zostanie wyświetlone okno dialogowe **Dostosowywanie.**
   
    ![Dostosowywanie okna konsoli](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Zaznacz lub wyczyść pola wyboru, aby wyświetlić lub ukryć elementy w oknie konsoli. Po zakończeniu wyboru kliknij przycisk **OK**.

## <a name="new-window-from-here"></a>Nowe okno w tym miejscu
Aby otworzyć nowe okno konsoli, można użyć opcji **Nowe okno z tutaj.**

#### <a name="to-open-a-new-console-window"></a>Aby otworzyć nowe okno konsoli
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** kliknij prawym przyciskiem myszy dowolny węzeł, a następnie kliknij polecenie **Nowe okno w oknie tutaj**. 
   
    Zostanie wyświetlane nowe okno z zaznaczonym zakresem. Jeśli na przykład w okienku **Zasady** tworzenia kopii zapasowych zostanie kliknąć prawym przyciskiem myszy węzeł **Zasady tworzenia kopii zapasowych,** w okienku **Wyniki** zostanie **wyświetlona** tylko węzeł Zasady kopii zapasowej oraz lista zdefiniowanych zasad tworzenia kopii zapasowych. Zobacz poniższy przykład.
   
    ![Nowe okno w tym miejscu](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Odświeżanie
Akcji **Odśwież** można zaktualizować okno konsoli.

#### <a name="to-update-the-console-window"></a>Aby zaktualizować okno konsoli
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** kliknij prawym przyciskiem myszy dowolny węzeł lub rozwiń węzeł, a następnie kliknij prawym przyciskiem myszy element w okienku **Wyniki,** a następnie kliknij polecenie **Odśwież**. 

## <a name="export-list"></a>Lista eksportu
Akcji **Eksportuj listę** można zapisać listę w pliku csv (csv). Na przykład można wyeksportować listę zasad tworzenia kopii zapasowych lub katalogu kopii zapasowych. Następnie można zaimportować plik CSV do aplikacji arkusza kalkulacyjnego do analizy.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Aby zapisać listę w pliku csv (CSV)
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple. 
2. W okienku **Zakres** kliknij prawym przyciskiem myszy dowolny węzeł lub rozwiń węzeł, a następnie kliknij prawym przyciskiem myszy element w okienku **Wyniki,** a następnie kliknij polecenie **Eksportuj listę**. 
3. Zostanie wyświetlone okno dialogowe **Lista eksportu.** Wypełnij formularz w następujący sposób: 
   
   1. W polu **Nazwa pliku** wpisz nazwę pliku CSV lub kliknij strzałkę, aby wybrać z listy rozwijanej.
   2. W polu **Zapisz jako typ** kliknij strzałkę i wybierz typ pliku z listy rozwijanej.
   3. Aby zapisać tylko zaznaczone elementy, zaznacz je, a następnie kliknij pole wyboru **Zapisz tylko zaznaczone wiersze.** Aby zapisać wszystkie wyeksportowane listy, wyczyść pole wyboru **Zapisz tylko zaznaczone wiersze.**
   4. Kliknij przycisk **Zapisz**.
      
      ![Eksportowanie listy jako pliku wartości oddzielonego przecinkami](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Pomoc
Za pomocą menu **Pomoc** można wyświetlić dostępną pomoc online dla Menedżera migawek StorSimple i programu MMC.

#### <a name="to-view-available-online-help"></a>Aby wyświetlić dostępną pomoc online
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** kliknij prawym przyciskiem myszy dowolny węzeł lub rozwiń węzeł, a następnie kliknij prawym przyciskiem myszy element w okienku **Wyniki,** a następnie kliknij polecenie **Pomoc**. 

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [interfejsie użytkownika StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
* Dowiedz się więcej o [administrowaniu rozwiązaniem StorSimple za pomocą Menedżera migawek StorSimple.](storsimple-snapshot-manager-admin.md)

