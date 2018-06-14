---
title: Samouczek tworzenia kopii zapasowej Microsoft Azure StorSimple wirtualnego tablicy | Dokumentacja firmy Microsoft
description: Opisuje sposób wykonywania kopii zapasowej woluminów i udziałów tablicy wirtualne StorSimple.
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
ms.openlocfilehash: c926f0c80ce56cac3106ad97ec3ec2e18a8e2cc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875648"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Tworzyć kopie zapasowe udziały lub woluminy na tablica wirtualnego StorSimple

## <a name="overview"></a>Omówienie

Tablica wirtualne StorSimple jest hybrydowe chmury magazynu lokalnego urządzenia wirtualnego skonfigurowanego jako serwer plików lub serwera iSCSI. Tablica wirtualnego zezwala użytkownikowi na tworzenie kopii zapasowych zaplanowanych, jak i ręczne udziały lub woluminy na urządzeniu. Gdy skonfigurowany jako serwer plików, również umożliwia odzyskiwanie na poziomie elementu. Ten przewodnik opisuje sposób tworzenia kopii zapasowych zaplanowanych, jak i ręczne i wykonać odzyskiwanie na poziomie elementu do plików w sieci wirtualnej macierzy.

Ten samouczek dotyczy StorSimple wirtualnego tablic tylko. Informacje w serii 8000, przejdź do [tworzenia kopii zapasowej urządzenie 8000 series](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Wykonaj kopię zapasową woluminów i udziałów

Kopie zapasowe zapewnia ochronę w momencie, ułatwia odzyskiwanie i zminimalizować czas przywracania dla woluminów i udziałów. Można utworzyć kopię zapasową udziału lub wolumin w urządzeniu StorSimple na dwa sposoby: **zaplanowane** lub **ręcznego**. W poniższych sekcjach omówiono każdej z metod.

## <a name="change-the-backup-start-time"></a>Zmiana czasu rozpoczęcia tworzenia kopii zapasowej

> [!NOTE]
> W tej wersji zaplanowane kopie zapasowe są tworzone przez zasady domyślne, który jest uruchamiany codziennie o określonej godzinie i tworzy kopię zapasową wszystkich udziały lub woluminy na urządzeniu. Nie jest możliwe tworzenie niestandardowych zasad dla zaplanowanych kopii zapasowych w tej chwili.


Tablica wirtualnego StorSimple ma domyślne zasady tworzenia kopii zapasowej, który rozpoczyna się o określonej godzinie dnia (22:30) i tworzy kopię zapasową wszystkich udziały lub woluminy na urządzeniu raz dziennie. Czas, w którym nie można zmienić kopii zapasowej zostanie uruchomiony, ale częstotliwość i przechowywania (który określa liczbę kopii zapasowych, aby zachować), można zmienić. Podczas te kopie zapasowe wszystkich danych z urządzenia wirtualnego kopia zapasowa jest tworzona. To może potencjalnie wpłynąć na wydajność urządzenia i wpływać na obciążeń wdrożonych na urządzeniu. Dlatego zaleca się zaplanować te kopie zapasowe dla poza godzinami szczytu.

 Aby zmienić domyślny czas rozpoczęcia tworzenia kopii zapasowych, wykonaj następujące kroki w [portalu Azure](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Aby zmienić czas rozpoczęcia domyślne zasady tworzenia kopii zapasowej

1. Przejdź do **urządzeń**. Zostanie wyświetlona lista urządzeń zarejestrowanych w usłudze usługi Menedżer StorSimple urządzenia. 
   
    ![Przejdź do urządzeń](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Wybierz, a następnie kliknij urządzenie. **Ustawienia** zostanie wyświetlony blok. Przejdź do **Zarządzaj > zasady tworzenia kopii zapasowej**.
   
    ![Wybierz urządzenie](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. W **zasady tworzenia kopii zapasowej** bloku, domyślny czas rozpoczęcia jest 22:30. Można określić nową godzinę rozpoczęcia harmonogramu dziennego w strefie czasowej urządzenia.
   
    ![Przejdź do zasad tworzenia kopii zapasowych](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Kliknij pozycję **Zapisz**.

### <a name="take-a-manual-backup"></a>Wykonaj kopię zapasową ręczne

Oprócz zaplanowanych kopii zapasowych można wykonać ręcznie (na żądanie) kopii zapasowych danych urządzenia w dowolnym momencie.

#### <a name="to-create-a-manual-backup"></a>Ręczne tworzenie kopii zapasowej

1. Przejdź do **urządzeń**. Wybierz urządzenie, a następnie kliknij prawym przyciskiem myszy **...**  z prawej zaznaczonego wiersza. Wybierz z menu kontekstowego **utworzenia kopii zapasowej**.
   
    ![Przejdź do utworzenia kopii zapasowej](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. W **utworzenia kopii zapasowej** bloku, kliknij przycisk **utworzenia kopii zapasowej**. Zostanie utworzona kopia zapasowa, wszystkie udziały na serwerze plików lub wszystkie woluminy na serwerze iSCSI. 
   
    ![Uruchamianie tworzenia kopii zapasowej](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Rozpoczęcia tworzenia kopii zapasowej na żądanie i zobacz, czy zadanie tworzenia kopii zapasowej została uruchomiona.
   
    ![Uruchamianie tworzenia kopii zapasowej](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Po pomyślnym ukończeniu zadania zostanie wyświetlony ponownie. Następnie uruchamia proces tworzenia kopii zapasowej.
   
    ![Utworzono zadanie kopii zapasowej](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Aby śledzić postęp kopii zapasowych i przyjrzyj się szczegóły zadania, kliknij powiadomienie. Powoduje to przejście do **szczegóły zadania**.
   
     ![Szczegóły zadania kopii zapasowej](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. Po wykonaniu kopii zapasowej przejdź do **zarządzania > katalog kopii zapasowej**. Zobaczysz migawkę chmury dla wszystkich udziałów (lub woluminów) na urządzeniu.
   
    ![Zakończono tworzenie kopii zapasowej](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Wyświetlanie istniejących kopii zapasowych
Aby wyświetlić istniejące kopie zapasowe, wykonaj następujące kroki w portalu Azure.

#### <a name="to-view-existing-backups"></a>Aby wyświetlić istniejące kopie zapasowe

1. Przejdź do **urządzeń** bloku. Wybierz, a następnie kliknij urządzenie. W **ustawienia** bloku, przejdź do **zarządzania > katalog kopii zapasowej**.
   
    ![Przejdź do katalogu kopii zapasowej](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Określ następujące kryteria do użycia w celu filtrowania:
   
    - **Zakres czasu** — może być **ostatnich 1 godzina**, **ostatnich 24 godzin**, **ostatnie 7 dni**, **w ciągu ostatnich 30 dni**, **ciągu ostatniego roku**, i **Data niestandardowa**.
    
    - **Urządzenia** — wybierz z listy serwerów plików lub serwery iSCSI zarejestrowana przy użyciu usługi Menedżer StorSimple urządzenia.
   
    - **Zainicjowano** — może być automatycznie **zaplanowane** (przy użyciu zasad tworzenia kopii zapasowej) lub **ręcznie** zainicjujesz ().
   
    ![Kopie zapasowe filtru](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Kliknij przycisk **Zastosuj**. Filtrowane listy kopii zapasowych jest wyświetlany w **katalog kopii zapasowej** bloku. Uwaga tylko 100 elementów kopii zapasowej mogą być wyświetlane w danym momencie.
   
    ![Zaktualizowano katalogu kopii zapasowej](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [administrowanie tablica wirtualnego StorSimple](storsimple-ova-web-ui-admin.md).

