---
title: Samouczek tworzenia kopii zapasowych Microsoft Azure StorSimple Virtual Array | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia kopii zapasowej rozwiązania StorSimple Virtual Array udziały i woluminy.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60581398"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Tworzenie kopii zapasowej udziałów lub woluminów na rozwiązania StorSimple Virtual Array

## <a name="overview"></a>Omówienie

Macierz wirtualna StorSimple jest hybrydowe urządzenie magazynujące w chmurze w środowisku lokalnym wirtualnego skonfigurowanego jako serwer plików lub serwera iSCSI. Macierz wirtualna umożliwia użytkownikowi utworzenie zaplanowanych, jak i ręczne tworzenie kopii zapasowych udziałów lub woluminów na urządzeniu. Po skonfigurowaniu serwera jako serwera plików także umożliwia odzyskiwanie na poziomie elementu. W tym samouczku opisano sposób tworzenia zaplanowanych, jak i ręczne tworzenie kopii zapasowych i odzyskiwanie na poziomie elementu można przywrócić usuniętego pliku na macierz wirtualna.

Ten samouczek dotyczy macierze wirtualne StorSimple tylko. Aby uzyskać informacji na temat serii 8000, przejdź do [tworzenie kopii zapasowej dla urządzeń z serii 8000](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Tworzenie kopii zapasowej udziały i woluminy

Tworzenie kopii zapasowych zapewnia ochronę w momencie, odzyskiwanie i zminimalizować czas przywracania, udziały i woluminy. Można tworzyć kopie zapasowe udział lub wolumin w urządzeniu StorSimple na dwa sposoby: **Zaplanowane** lub **ręczne**. W poniższych sekcjach omówiono każdej z metod.

## <a name="change-the-backup-start-time"></a>Zmiana czasu rozpoczęcia tworzenia kopii zapasowych

> [!NOTE]
> W tej wersji zaplanowane kopie zapasowe są tworzone przez zasady domyślne, które jest uruchamiane codziennie o określonej godzinie i tworzy kopię zapasową wszystkich udziałów lub woluminów na urządzeniu. Nie jest możliwe utworzyć zasady niestandardowe dla zaplanowanych kopii zapasowych w tej chwili.


Rozwiązania StorSimple Virtual Array ma domyślne zasady tworzenia kopii zapasowej, który rozpoczyna się o określonej godzinie dnia (22:30) i tworzy kopię zapasową wszystkich udziałów lub woluminów na urządzeniu, raz dziennie. Możesz zmienić czas, w którym nie można zmienić kopii zapasowej jest uruchamiana, ale częstotliwość i okres przechowywania (który określa liczbę kopii zapasowych, aby zachować). Podczas te kopie zapasowe całego urządzenia wirtualnego jest wykonywana kopia zapasowa. To może potencjalnie wpłynąć na wydajność urządzenia i wpływa na obciążeń wdrożonych na urządzeniu. Dlatego zaleca się zaplanować te kopie zapasowe poza godzinami pracy.

 Aby zmienić domyślny czas rozpoczęcia tworzenia kopii zapasowych, wykonaj następujące kroki w [witryny Azure portal](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Aby zmienić czas rozpoczęcia domyślnymi zasadami kopii zapasowych

1. Przejdź do **urządzeń**. Zostanie wyświetlona lista urządzeń zarejestrowanych za pomocą usługi Menedżer urządzeń StorSimple. 
   
    ![Przejdź do urządzenia](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Wybierz i kliknij swoje urządzenie. **Ustawienia** zostanie wyświetlony blok. Przejdź do **Zarządzaj > zasady kopii zapasowych**.
   
    ![Wybierz urządzenie](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. W **zasady tworzenia kopii zapasowych** bloku, domyślny czas rozpoczęcia to 22:30. Można określić nową wartość czasu rozpoczęcia dla harmonogramu dziennego w strefie czasowej urządzenia.
   
    ![Przejdź do zasad tworzenia kopii zapasowych](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Kliknij pozycję **Zapisz**.

### <a name="take-a-manual-backup"></a>Utwórz kopię zapasową ręczne

Oprócz zaplanowanych kopii zapasowych możesz wykonać ręcznie (na żądanie) tworzenie kopii zapasowych danych urządzenia w dowolnym momencie.

#### <a name="to-create-a-manual-backup"></a>Ręczne tworzenie kopii zapasowej

1. Przejdź do **urządzeń**. Wybierz swoje urządzenie, a następnie kliknij prawym przyciskiem myszy **...**  po prawej stronie w zaznaczonym wierszu. Z menu kontekstowego wybierz **wykonaj kopię zapasową**.
   
    ![Przejdź do wykonania kopii zapasowej](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. W **wykonaj kopię zapasową** bloku kliknij **wykonaj kopię zapasową**. Będzie wykonywać kopie zapasowe, wszystkie udziały na serwerze plików lub wszystkie woluminy na serwerze iSCSI. 
   
    ![Początkowa kopia zapasowa](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Uruchamia kopii zapasowej na żądanie, i zobaczysz, że zadanie tworzenia kopii zapasowej została uruchomiona.
   
    ![Początkowa kopia zapasowa](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Gdy zadanie zostanie pomyślnie zakończony, otrzymasz powiadomienie, ponownie. Następnie rozpoczyna się proces tworzenia kopii zapasowej.
   
    ![Utworzono zadanie kopii zapasowej](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Aby śledzić postęp wykonywania kopii zapasowych i przyjrzyj się szczegóły zadania, kliknij powiadomienie. Spowoduje to przejście do **szczegóły zadania**.
   
     ![Szczegóły zadania kopii zapasowej](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. Po wykonaniu kopii zapasowej przejdź do **zarządzania > katalog kopii zapasowej**. Migawki w chmurze wszystkich akcji (lub woluminów) zostanie wyświetlony na urządzeniu.
   
    ![Zakończono tworzenie kopii zapasowej](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Wyświetl istniejące kopie zapasowe
Aby wyświetlić istniejące kopie zapasowe, wykonaj następujące kroki w witrynie Azure portal.

#### <a name="to-view-existing-backups"></a>Aby wyświetlić istniejące kopie zapasowe

1. Przejdź do **urządzeń** bloku. Wybierz i kliknij swoje urządzenie. W **ustawienia** przejdź do bloku **zarządzania > katalog kopii zapasowej**.
   
    ![Przejdź do katalogu kopii zapasowej](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Określ następujące kryteria, które ma być używany do filtrowania:
   
   - **Zakres czasu** — może być **ostatnich 1 godziny**, **ostatnich 24 godzin**, **ostatnie 7 dni**, **w ciągu ostatnich 30 dni**, **ubiegły rok** , i **Data niestandardowa**.
    
   - **Urządzenia** — wybierz z listy serwerów plików lub serwery iSCSI rejestrowania przy użyciu usługi Menedżer urządzeń StorSimple.
   
   - **Zainicjowano** — mogą być automatycznie **zaplanowane** (przy użyciu zasad tworzenia kopii zapasowej) lub **ręcznie** zainicjujesz ().
   
     ![Filtrować kopie zapasowe](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Kliknij przycisk **Zastosuj**. Filtrowana lista kopii zapasowych jest wyświetlany w **katalog kopii zapasowej** bloku. Uwaga tylko 100 elementów kopii zapasowej może być wyświetlany w danym momencie.
   
    ![Zaktualizowano wykaz kopii zapasowych](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [administrowanie rozwiązania StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

