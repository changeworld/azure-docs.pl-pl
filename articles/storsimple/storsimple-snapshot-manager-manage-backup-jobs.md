---
title: Zadania tworzenia kopii zapasowej programu StorSimple Snapshot Manager | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania przystawki MMC programu StorSimple Snapshot Manager i zarządzania zaplanowane, uruchomione i zakończonych zadań tworzenia kopii zapasowej.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 03e306b62250f2bb033cc14e856a59760b5406c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875816"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>StorSimple Snapshot Manager umożliwia wyświetlanie i zarządzanie nimi zadania tworzenia kopii zapasowej

## <a name="overview"></a>Omówienie
**Zadania** w węźle **zakres** pokazuje **zaplanowane**, **ostatnich 24 godzin**, i **systemem** zadania tworzenia kopii zapasowych, które inicjowane interakcyjnego lub skonfigurowanymi zasadami. 

W tym samouczku opisano, jak używasz **zadania** węzeł, aby wyświetlić informacje o zaplanowane, ostatnie i aktualnie uruchomionych zadań tworzenia kopii zapasowej. (Lista zadań i informacji jest wyświetlana w **wyniki** okienko.) Ponadto można kliknij prawym przyciskiem myszy wymienionych zadań i zobacz menu kontekstowego, który zawiera listę dostępnych akcji.

## <a name="view-scheduled-jobs"></a>Wyświetl zaplanowane zadania
Poniższa procedura umożliwia wyświetlenie zaplanowanych zadań kopii zapasowej.

#### <a name="to-view-scheduled-jobs"></a>Aby wyświetlić zaplanowane zadania
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager. 
2. W **zakres** okienku rozwiń **zadania** węzeł, a następnie kliknij przycisk **zaplanowane**. Następujące informacje są wyświetlane w **wyniki** okienka:
   
   * **Nazwa** — nazwa zaplanowaną migawkę
   * **Uruchom** — Data i godzina następną zaplanowaną migawkę
   * **Ostatnie uruchomienie** — Data i godzina ostatniej zaplanowanej migawki
     
     > [!NOTE]
     > Jednorazowe tylko migawek **następnego uruchomienia** i **ostatnie uruchomienie** będą takie same.
     
     ![Zaplanowanych zadań kopii zapasowej](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Aby wykonać dodatkowe akcje w konkretnym zadaniu, kliknij prawym przyciskiem myszy nazwę zadania w **wyniki** okienko i wybierz jedną z opcji menu.

## <a name="view-recent-jobs"></a>Wyświetl ostatnie zadania
Poniższa procedura umożliwia wyświetlanie kopii zapasowej i przywracanie zadania, które zostały ukończone w ciągu ostatnich 24 godzin.

#### <a name="to-view-recent-jobs"></a>Aby wyświetlić ostatnich zadań
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
2. W **zakres** okienku rozwiń **zadania** węzeł, a następnie kliknij przycisk **ostatnich 24 godzin**. **Wyniki** w okienku zostaną wyświetlone zadania tworzenia kopii zapasowej w ciągu ostatnich 24 godzin (maksymalnie 64 zadań). Następujące informacje są wyświetlane w **wyniki** okienko, w zależności od **widoku** możesz określić opcje:
   
   * **Nazwa** — nazwa zaplanowaną migawkę.
   * **Rozpoczęto** — datę i godzinę rozpoczęcia tworzenia migawki.
   * **Zatrzymano** — Data i godzina, kiedy migawki zostało zakończone lub zostało przerwane.
   * **Upłynął** — ilość czasu między **uruchomiono** i **zatrzymane** razy.
   * **Stan** — stan zadania ostatnio wykonanych. **Powodzenie** wskazuje, czy kopia zapasowa została utworzona pomyślnie. **Nie powiodło się** wskazuje, czy zadanie nie zostało uruchomione pomyślnie.
   * **Informacje o** — przyczynę błędu.
   * **Bajty przetwarzane (MB)** — ilość danych z grupy woluminu, który był przetwarzany (w MB). 
     
     ![Zadania, które były uruchamiane w ostatnich 24 godzinach](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Aby wykonać dodatkowe akcje w konkretnym zadaniu, kliknij prawym przyciskiem myszy nazwę zadania w **wyniki** okienko i wybierz jedną z opcji menu.
   
    ![Usuwanie zadania](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Wyświetl uruchomione zadania
Użyj poniższej procedury do widoku zadania, które są aktualnie uruchomione.

#### <a name="to-view-currently-running-jobs"></a>Aby wyświetlić aktualnie wykonywanych zadań
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
2. W **zakres** okienku rozwiń **zadania** węzeł, a następnie kliknij przycisk **systemem**. W zależności od **widoku** opcji, należy określić następujące informacje są wyświetlane w **wyniki** okienka:
   
   * **Nazwa** — nazwa zaplanowaną migawkę.
   * **Rozpoczęto** — datę i godzinę rozpoczęcia tworzenia migawki.
   * **Punkt kontrolny** — bieżącej akcji kopii zapasowej.
   * **Stan** — procentu ukończenia.
   * **Upłynął** — ilość czasu, jaki upłynął od chwili rozpoczęcia tworzenia kopii zapasowej. 
   * **Średnia przepływność (MB)** — współczynnik łączna liczba bajtów danych przetwarzanych w tym całkowity czas poświęcony na przetwarzanie (MB).
   * **Bajty przetwarzane (MB)** — łączna liczba bajtów dane przetworzone (w MB).
   * **Bajty zapisane (MB)** — łączna liczba bajtów danych (w MB). Ona zawiera dane, a także metadanych i dlatego zazwyczaj większe niż przetwarzane bajtów.
     
     ![Obecnie uruchomionych zadań](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Aby wykonać dodatkowe akcje w konkretnym zadaniu, kliknij prawym przyciskiem myszy nazwę zadania w **wyniki** okienko i wybierz jedną z opcji menu.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zarządzać rozwiązania StorSimple przy użyciu programu StorSimple Snapshot Manager](storsimple-snapshot-manager-admin.md).
* Dowiedz się, jak [StorSimple Snapshot Manager umożliwia zarządzanie katalogu kopii zapasowej](storsimple-snapshot-manager-manage-backup-catalog.md).

