---
title: Zadania tworzenia kopii zapasowych Menedżera migawek StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób używania przystawki PROGRAMU MMC Menedżera migawek StorSimple do wyświetlania zaplanowanych, aktualnie uruchomionych i ukończonych zadań tworzenia kopii zapasowych oraz zarządzania nimi.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 094b996cd3227903995c7a74ef14ed8c0561f59d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933350"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Użyj Menedżera migawek StorSimple, aby wyświetlać zadania tworzenia kopii zapasowych i zarządzać nimi

## <a name="overview"></a>Omówienie
Węzeł **Zadania** w okienku **Zakres** pokazuje **zaplanowane**, **Ostatnie 24 godziny**i **Uruchamianie** zadań tworzenia kopii zapasowej, które zostały zainicjowane interaktywnie lub przez skonfigurowaną zasadę. 

W tym samouczku wyjaśniono, jak za pomocą węzła **Zadania** można wyświetlać informacje o zaplanowanych, ostatnich i aktualnie uruchomionych zadaniach tworzenia kopii zapasowych. (Lista zadań i odpowiadające im informacje są wyświetlane w okienku **Wyniki).** Ponadto można kliknąć prawym przyciskiem myszy wymienione zadanie i wyświetlić menu kontekstowe z listą dostępnych akcji.

## <a name="view-scheduled-jobs"></a>Wyświetlanie zaplanowanych zadań
Poniższa procedura służy do wyświetlania zaplanowanych zadań tworzenia kopii zapasowych.

#### <a name="to-view-scheduled-jobs"></a>Aby wyświetlić zaplanowane zadania
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple. 
2. W okienku **Zakres** rozwiń węzeł **Zadania** i kliknij pozycję **Zaplanowane**. W okienku **Wyniki** są wyświetlane następujące informacje:
   
   * **Nazwa** — nazwa zaplanowanej migawki
   * **Następny bieg** — data i godzina następnej zaplanowanej migawki
   * **Ostatnie uruchomienie** — data i godzina ostatniej zaplanowanej migawki
     
     > [!NOTE]
     > Dla migawek tylko jednorazowo **Next Run** i **Last Run** będą takie same.
     
     ![Zaplanowane zadania tworzenia kopii zapasowych](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Aby wykonać dodatkowe akcje na określonym zadaniu, kliknij prawym przyciskiem myszy nazwę zadania w okienku **Wyniki** i wybierz z opcji menu.

## <a name="view-recent-jobs"></a>Wyświetlanie ostatnich ofert pracy
Poniższa procedura służy do wyświetlania zadań tworzenia kopii zapasowych i przywracania, które zostały ukończone w ciągu ostatnich 24 godzin.

#### <a name="to-view-recent-jobs"></a>Aby wyświetlić ostatnie oferty pracy
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** rozwiń węzeł **Zadania** i kliknij pozycję **Ostatnie 24 godziny**. W okienku **Wyniki** są wyświetlane zadania tworzenia kopii zapasowych z ostatnich 24 godzin (maksymalnie 64 zadania). W okienku **Wyniki** są wyświetlane następujące informacje, w zależności od określonych opcji **widoku:**
   
   * **Nazwa** — nazwa zaplanowanej migawki.
   * **Rozpoczęto** — datę i godzinę rozpoczęcia migawki.
   * **Zatrzymano** — datę i godzinę zakończenia migawki lub jej zakończenia.
   * **Upłynął** — czas między **godzinami rozpoczęcia** i **zatrzymania.**
   * **Status** — stan niedawno ukończonego zadania. **Powodzenie** wskazuje, że kopia zapasowa została utworzona pomyślnie. **Nie powiodło się** wskazuje, że zadanie nie zostało pomyślnie uruchomione.
   * **Informacje** – przyczyna awarii.
   * **Bajty przetworzone (MB)** – ilość danych z grupy woluminów, która została przetworzona (w MB). 
     
     ![Oferty pracy, które działały w ciągu ostatnich 24 godzin](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Aby wykonać dodatkowe akcje na określonym zadaniu, kliknij prawym przyciskiem myszy nazwę zadania w okienku **Wyniki** i wybierz z opcji menu.
   
    ![Usuwanie zadania](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Wyświetlanie aktualnie uruchomionych zadań
Poniższa procedura służy do wyświetlania aktualnie uruchomionych zadań.

#### <a name="to-view-currently-running-jobs"></a>Aby wyświetlić aktualnie uruchomione zadania
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** rozwiń węzeł **Zadania** i kliknij pozycję **Uruchomiona**. W zależności od określonych opcji **widoku** w okienku **Wyniki** pojawiają się następujące informacje:
   
   * **Nazwa** — nazwa zaplanowanej migawki.
   * **Rozpoczęto** — datę i godzinę rozpoczęcia migawki.
   * **Checkpoint** — bieżąca akcja kopii zapasowej.
   * **Status** – procent ukończenia.
   * **Upłynął** — czas, który upłynął od rozpoczęcia tworzenia kopii zapasowej. 
   * **Średnia przepustowość (MB)** — stosunek całkowitych bajtów przetwarzanych danych do całkowitego czasu przetwarzania (MB).
   * **Bajty przetworzone (MB)** – całkowita liczba bajtów przetwarzanych danych (w MB).
   * **Bajty zapisane (MB)** – całkowita liczba bajtów zapisanych danych (w MB). Zawiera dane, jak również metadane i dlatego jest zazwyczaj większa niż bajty przetwarzane.
     
     ![Zadania aktualnie uruchomione](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Aby wykonać dodatkowe akcje na określonym zadaniu, kliknij prawym przyciskiem myszy nazwę zadania w okienku **Wyniki** i wybierz z opcji menu.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zarządzać rozwiązaniem StorSimple, za pomocą Menedżera migawek StorSimple.](storsimple-snapshot-manager-admin.md)
* Dowiedz się, jak [zarządzać katalogiem kopii zapasowych za pomocą Menedżera migawek StorSimple.](storsimple-snapshot-manager-manage-backup-catalog.md)

