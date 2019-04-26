---
title: Zadania tworzenia kopii zapasowej programu StorSimple Snapshot Manager | Dokumentacja firmy Microsoft
description: Opisuje sposób używania przystawki MMC przystawki StorSimple Snapshot Manager do wyświetlania i zarządzania zaplanowane, aktualnie uruchomione i ukończone zadania tworzenia kopii zapasowej.
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
ms.openlocfilehash: c34ff487f03d90b16b6660fbad77c3a16699e165
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303489"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Do przeglądania i zarządzania zadania tworzenia kopii zapasowej za pomocą Menedżera migawek StorSimple

## <a name="overview"></a>Omówienie
**Zadania** w węźle **zakres** pokazuje **zaplanowane**, **ostatnie 24 godziny**, i **systemem** zadania tworzenia kopii zapasowych, które inicjowane interaktywnie lub przy użyciu skonfigurowanych zasad. 

W tym samouczku wyjaśniono, jak można użyć **zadań** węzeł, aby wyświetlić informacje o zaplanowanych, ostatnie i aktualnie uruchomionych zadań tworzenia kopii zapasowej. (Lista zadań i informacji jest wyświetlana w **wyniki** okienka.) Ponadto możesz kliknij prawym przyciskiem myszy na liście zadań i wyświetlić menu kontekstowe, który zawiera listę dostępnych akcji.

## <a name="view-scheduled-jobs"></a>Wyświetl zaplanowane zadania
Poniższa procedura umożliwia wyświetlanie zaplanowanych zadań kopii zapasowej.

#### <a name="to-view-scheduled-jobs"></a>Aby wyświetlić zadania zaplanowane
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager. 
2. W **zakres** okienku rozwiń **zadania** węzeł, a następnie kliknij przycisk **zaplanowane**. Zostaną wyświetlone następujące informacje w **wyniki** okienka:
   
   * **Nazwa** — nazwa zaplanowaną migawkę
   * **Następne uruchomienie** — Data i godzina następną zaplanowaną migawkę
   * **Ostatnie uruchomienie** — Data i godzina najnowszych zaplanowaną migawkę
     
     > [!NOTE]
     > Dla jednorazowej migawki tylko **następnego uruchomienia** i **ostatnie uruchomienie** będą takie same.
     
     ![Zaplanowanych zadań kopii zapasowej](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Aby wykonać dodatkowe akcje dla określonego zadania, kliknij prawym przyciskiem myszy nazwę zadania w **wyniki** okienka, a następnie zaznacz opcje menu.

## <a name="view-recent-jobs"></a>Wyświetl ostatnie zadania
Poniższa procedura umożliwia wyświetlanie kopii zapasowych i przywracanie zadania, które zostały ukończone w ciągu ostatnich 24 godzin.

#### <a name="to-view-recent-jobs"></a>Aby wyświetlić ostatnie zadania
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
2. W **zakres** okienku rozwiń **zadania** węzeł, a następnie kliknij przycisk **ostatnie 24 godziny**. **Wyniki** zadania tworzenia kopii zapasowej w okienku są wyświetlane w ciągu ostatnich 24 godzin (maksymalnie 64 zadań). Zostaną wyświetlone następujące informacje w **wyniki** okienko, w zależności od **widoku** opcji, należy określić:
   
   * **Nazwa** — nazwa zaplanowaną migawkę.
   * **Pracę** — Data i godzina rozpoczęcia migawki.
   * **Zatrzymano** — Data i godzina, kiedy migawki zostało zakończone lub został zakończony.
   * **Upłynęło** — ilość czasu między **uruchomiono** i **zatrzymane** razy.
   * **Stan** — stan niedawno ukończone zadania. **Powodzenie** wskazuje, czy kopia zapasowa została utworzona pomyślnie. **Nie powiodło się** wskazuje, że zadanie nie zostało uruchomione pomyślnie.
   * **Informacje o** — przyczyny niepowodzenia.
   * **Bajty przetwarzane (MB)** — ilość danych z grupy woluminów, która została przetworzona (w MB). 
     
     ![Zadań uruchomionych w ostatnich 24 godzinach](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Aby wykonać dodatkowe akcje dla określonego zadania, kliknij prawym przyciskiem myszy nazwę zadania w **wyniki** okienka, a następnie zaznacz opcje menu.
   
    ![Usuń zadanie](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Wyświetl uruchomione zadania
Użyj poniższej procedury do widoku zadania, które są aktualnie uruchomione.

#### <a name="to-view-currently-running-jobs"></a>Aby wyświetlić aktualnie uruchomionych zadań
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
2. W **zakres** okienku rozwiń **zadania** węzeł, a następnie kliknij przycisk **systemem**. W zależności od **widoku** opcji, należy określić następujące informacje są wyświetlane w **wyniki** okienka:
   
   * **Nazwa** — nazwa zaplanowaną migawkę.
   * **Pracę** — Data i godzina rozpoczęcia migawki.
   * **Punkt kontrolny** — bieżącej akcji tworzenia kopii zapasowej.
   * **Stan** — procentu ukończenia.
   * **Upłynęło** — ilość czasu, jaki upłynął od chwili rozpoczęcia tworzenia kopii zapasowej. 
   * **Średnia przepustowość (MB)** — współczynnik całkowita liczba bajtów danych przetworzonych w tym całkowity czas poświęcony na przetwarzanie (MB).
   * **Bajty przetwarzane (MB)** — łączna liczba bajtów dane przetworzone (w MB).
   * **Bajty zapisane (MB)** — łączna liczba bajtów danych zapisanych (w MB). Ona zawiera dane, a także metadanych i dlatego jest zwykle większy niż przetwarzane bajtów.
     
     ![Obecnie uruchomionych zadań](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Aby wykonać dodatkowe akcje dla określonego zadania, kliknij prawym przyciskiem myszy nazwę zadania w **wyniki** okienka, a następnie zaznacz opcje menu.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [za pomocą Menedżera migawek StorSimple do administrowania rozwiązania StorSimple](storsimple-snapshot-manager-admin.md).
* Dowiedz się, jak [Zarządzanie wykazem kopii zapasowych za pomocą Menedżera migawek StorSimple](storsimple-snapshot-manager-manage-backup-catalog.md).

