---
title: Samouczek kopii zapasowej tablicy wirtualnej usługi Microsoft Azure StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób utworzenia kopii zapasowej udziałów i woluminów tablicy wirtualnej StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a61dcca1f78b6ba444a2deefcf6b8bb4fd5c5087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60581398"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Łącze zapasowe udziałów lub woluminów w macierzy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

StorSimple Virtual Array to hybrydowe urządzenie wirtualne w chmurze, które można skonfigurować jako serwer plików lub serwer iSCSI. Tablica wirtualna umożliwia użytkownikowi tworzenie zaplanowanych i ręcznych kopii zapasowych wszystkich udziałów lub woluminów na urządzeniu. Po skonfigurowaniu jako serwer plików umożliwia również odzyskiwanie na poziomie elementu. W tym samouczku opisano sposób tworzenia zaplanowanych i ręcznych kopii zapasowych oraz odzyskiwania na poziomie elementu w celu przywrócenia usuniętego pliku w tablicy wirtualnej.

Ten samouczek dotyczy tylko tablic wirtualnych StorSimple. Aby uzyskać informacje na temat serii 8000, przejdź do [tworzenia kopii zapasowej dla urządzenia z serii 8000](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Dziele zapasowe udziałów i wolumenów

Kopie zapasowe zapewniają ochronę punktu w czasie, poprawiają możliwość odzyskiwania i minimalizują czas przywracania udziałów i woluminów. Możesz wywklić kopii zapasowej udziału lub woluminu na urządzeniu StorSimple na dwa sposoby: **Zaplanowany** lub **Ręczny**. Każda z metod jest omówiona w poniższych sekcjach.

## <a name="change-the-backup-start-time"></a>Zmienianie czasu rozpoczęcia tworzenia kopii zapasowej

> [!NOTE]
> W tej wersji zaplanowane kopie zapasowe są tworzone przez domyślną zasadę, która jest uruchamiana codziennie o określonej godzinie i tworzy kopię zapasową wszystkich udziałów lub woluminów na urządzeniu. Obecnie nie można tworzyć niestandardowych zasad dla zaplanowanych kopii zapasowych.


Aplikacja StorSimple Virtual Array ma domyślną zasadę tworzenia kopii zapasowych, która rozpoczyna się o określonej porze dnia (22:30) i jednocześnie jednocześnie jednocześnie współucześnia wszystkich udziałów lub woluminów na urządzeniu. Można zmienić godzinę rozpoczęcia tworzenia kopii zapasowej, ale nie można zmienić częstotliwości i przechowywania (która określa liczbę kopii zapasowych do zachowania). Podczas wykonywania tych kopii zapasowych kopia zapasowa całego urządzenia wirtualnego jest kopią zapasową. Może to potencjalnie wpłynąć na wydajność urządzenia i wpłynąć na obciążenia wdrożone na urządzeniu. W związku z tym zaleca się zaplanowanie tych kopii zapasowych poza godzinami szczytu.

 Aby zmienić domyślny czas rozpoczęcia tworzenia kopii zapasowej, wykonaj następujące czynności w [witrynie Azure portal](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Aby zmienić godzinę rozpoczęcia domyślnej zasady tworzenia kopii zapasowej

1. Przejdź do **urządzenia**. Zostanie wyświetlona lista urządzeń zarejestrowanych w usłudze StorSimple Device Manager. 
   
    ![przechodzenie do urządzeń](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Wybierz i kliknij urządzenie. Zostanie wyświetlony blok **Ustawienia.** Przejdź do **zarządzania > zasady tworzenia kopii zapasowych**.
   
    ![wybierz urządzenie](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. W **bloku Zasady kopii zapasowej** domyślna godzina rozpoczęcia to 22:30. Można określić nową godzinę rozpoczęcia dla dziennego harmonogramu w strefie czasowej urządzenia.
   
    ![przejdź do zasad tworzenia kopii zapasowych](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Kliknij przycisk **Zapisz**.

### <a name="take-a-manual-backup"></a>Wykonywanie ręcznej kopii zapasowej

Oprócz zaplanowanych kopii zapasowych można w dowolnym momencie wykonać ręczną (na żądanie) kopię zapasową danych urządzenia.

#### <a name="to-create-a-manual-backup"></a>Ręczne tworzenie kopii zapasowej

1. Przejdź do **urządzenia**. Wybierz urządzenie i kliknij prawym przyciskiem myszy **...** po prawej stronie wybranego wiersza. Z menu kontekstowego wybierz polecenie **Zrób kopię zapasową**.
   
    ![nawigować, aby wykonać kopię zapasową](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. W bloku **Zrób kopię zapasową** kliknij pozycję **Zrób kopię zapasową**. Spowoduje to kopię zapasową wszystkich udziałów na serwerze plików lub wszystkich woluminów na serwerze iSCSI. 
   
    ![uruchamianie kopii zapasowej](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Rozpoczyna się tworzenie kopii zapasowej na żądanie i widać, że zadanie tworzenia kopii zapasowej zostało rozpoczęte.
   
    ![uruchamianie kopii zapasowej](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Po pomyślnym zakończeniu zadania zostanie ponownie powiadomiony. Następnie rozpoczyna się proces tworzenia kopii zapasowej.
   
    ![utworzone zadanie tworzenia kopii zapasowej](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Aby śledzić postęp tworzenia kopii zapasowych i przeglądać szczegóły zadania, kliknij powiadomienie. Spowoduje to przejście do **szczegółów zadania**.
   
     ![szczegóły zadania tworzenia kopii zapasowej](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. Po zakończeniu wykonywania kopii zapasowej przejdź do **katalogu Zarządzanie > kopia zapasowa**. Zobaczysz migawkę chmury wszystkich udziałów (lub woluminów) na urządzeniu.
   
    ![Ukończona kopia zapasowa](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Wyświetlanie istniejących kopii zapasowych
Aby wyświetlić istniejące kopie zapasowe, wykonaj następujące kroki w witrynie Azure portal.

#### <a name="to-view-existing-backups"></a>Aby wyświetlić istniejące kopie zapasowe

1. Przejdź do **urządzenia** bloku. Wybierz i kliknij urządzenie. W bloku **Ustawienia** przejdź do **pozycji Zarządzanie > katalog kopii zapasowych**.
   
    ![Przejdź do katalogu kopii zapasowych](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Określ następujące kryteria, które mają być używane do filtrowania:
   
   - **Zakres czasu** - może to być **przeszłe 1 godziny**, **Ostatnie 24 godziny,** **Ostatnie 7 dni,** **Ostatnie 30 dni,** **Miniony rok**i **Data niestandardowa.**
    
   - **Urządzenia** — wybierz z listy serwerów plików lub serwerów iSCSI zarejestrowanych w usłudze StorSimple Device Manager.
   
   - **Inicjowane** — mogą być automatycznie **zaplanowane** (przez zasady tworzenia kopii zapasowych) lub **ręcznie** inicjowane (przez Użytkownika).
   
     ![Filtrowanie kopii zapasowych](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Kliknij przycisk **Zastosuj**. Filtrowana lista kopii zapasowych jest wyświetlana w bloku **katalog kopii zapasowej.** Uwaga Tylko 100 elementów kopii zapasowej mogą być wyświetlane w danym czasie.
   
    ![Zaktualizowany katalog kopii zapasowych](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [administrowaniu tablicą wirtualną StorSimple](storsimple-ova-web-ui-admin.md).

