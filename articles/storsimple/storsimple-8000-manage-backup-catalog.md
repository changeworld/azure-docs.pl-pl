---
title: Zarządzanie katalogiem kopii zapasowych StorSimple | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak używać strony katalogu kopii zapasowych usługi StorSimple Device Manager do listy, zaznaczania i usuwania zestawów kopii zapasowych.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 07d9e03f1631ebce88a7a7c2e33be62f21dda522
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60319675"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Zarządzanie katalogiem kopii zapasowych za pomocą usługi StorSimple Device Manager
## <a name="overview"></a>Omówienie
Serwer bloku **Katalog kopii zapasowych** usługi StorSimple Menedżer urządzeń wyświetla wszystkie zestawy kopii zapasowych, które są tworzone podczas wykonywania ręcznych lub zaplanowanych kopii zapasowych. Za pomocą tej strony można wyświetlić listę wszystkich kopii zapasowych dla zasad tworzenia kopii zapasowych lub woluminu, wybrać lub usunąć kopie zapasowe lub użyć kopii zapasowej do przywrócenia lub sklonowania woluminu.

W tym samouczku wyjaśniono, jak wyświetlić listę, zaznaczyć i usunąć zestaw kopii zapasowych. Aby dowiedzieć się, jak przywrócić urządzenie z kopii zapasowej, przejdź do [tematu Przywracanie urządzenia z zestawu kopii zapasowych](storsimple-8000-restore-from-backup-set-u2.md). Aby dowiedzieć się, jak sklonować wolumin, przejdź do [tematu Klonuj wolumin StorSimple](storsimple-8000-clone-volume-u2.md).

![Katalog kopii zapasowych](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

Blok **Wykaz kopii zapasowych** zawiera kwerendę, aby zawęzić wybór zestawu kopii zapasowych. Można filtrować zestawy kopii zapasowych, które są pobierane, na podstawie następujących parametrów:

* **Urządzenie** — urządzenie, na którym utworzono zestaw kopii zapasowych.
* **Zasady tworzenia kopii zapasowych lub wolumin —** zasady tworzenia kopii zapasowych lub wolumin skojarzony z tym zestawem kopii zapasowych.
* **Od i Do** — zakres daty i godziny podczas tworzenia zestawu kopii zapasowych.

Filtrowane zestawy kopii zapasowych są następnie tabelaryczne na podstawie następujących atrybutów:

* **Nazwa** — nazwa zasad tworzenia kopii zapasowej lub woluminu skojarzonego z zestawem kopii zapasowych.
* **Rozmiar** — rzeczywisty rozmiar zestawu kopii zapasowych.
* **Utworzone na** — data i godzina utworzenia kopii zapasowych. 
* **Typ** — zestawy kopii zapasowych mogą być migawkami lokalnymi lub migawkami w chmurze. Migawka lokalna to kopia zapasowa wszystkich danych woluminu przechowywanych lokalnie na urządzeniu, podczas gdy migawka w chmurze odnosi się do kopii zapasowej danych woluminów przechowywanych w chmurze. Migawki lokalne zapewniają szybszy dostęp, podczas gdy migawki w chmurze są wybierane dla odporności danych.
* **Zainicjowane przez** — kopie zapasowe mogą być inicjowane automatycznie przez harmonogram lub ręcznie przez użytkownika. Za pomocą zasad tworzenia kopii zapasowych można zaplanować tworzenie kopii zapasowych. Alternatywnie można użyć opcji **Zrób kopię zapasową,** aby wykonać ręczną kopię zapasową.

## <a name="list-backup-sets-for-a-backup-policy"></a>Wyświetlanie zestawów kopii zapasowych dla zasad tworzenia kopii zapasowych
Wykonaj następujące kroki, aby wyświetlić listę wszystkich kopii zapasowych dla zasad tworzenia kopii zapasowych.

#### <a name="to-list-backup-sets"></a>Aby wyświetlić listę zestawów kopii zapasowych
1. Przejdź do usługi StorSimple Device Manager i kliknij pozycję **Katalog kopii zapasowych**.

2. Filtruj wybory w następujący sposób:
   
   1. Określ zakres czasu.
   2. Wybierz odpowiednie urządzenie.
   3. Filtruj według **zasad kopii zapasowej,** aby wyświetlić odpowiednie kopie zapasowe.
   3. Z listy rozwijanej zasad tworzenia kopii zapasowych wybierz pozycję **Wszystkie,** aby wyświetlić wszystkie kopie zapasowe na wybranym urządzeniu.
   4. Kliknij przycisk **Zastosuj,** aby wykonać tę kwerendę.
      
      Kopie zapasowe skojarzone z wybranymi zasadami tworzenia kopii zapasowych powinny pojawić się na liście zestawów kopii zapasowych.

      ![Przejdź do katalogu kopii zapasowych](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Wybieranie zestawu kopii zapasowych
Wykonaj następujące kroki, aby wybrać zestaw kopii zapasowych dla woluminu lub zasady tworzenia kopii zapasowej.

#### <a name="to-select-a-backup-set"></a>Aby wybrać zestaw kopii zapasowych
1. Przejdź do usługi StorSimple Device Manager i kliknij pozycję **Katalog kopii zapasowych**.
2. Filtruj wybory w następujący sposób:
   
   1. Określ zakres czasu. 
   2. Wybierz odpowiednie urządzenie. 
   3. Filtruj według woluminów lub zasad tworzenia kopii zapasowych dla kopii zapasowej, którą chcesz wybrać.
   4. Kliknij przycisk **Zastosuj,** aby wykonać tę kwerendę.
      
      Kopie zapasowe skojarzone z wybranymi zasadami woluminu lub kopii zapasowej powinny być wyświetlane na liście zestawów kopii zapasowych.

      ![Przejdź do katalogu kopii zapasowych](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Wybierz i rozwiń zestaw kopii zapasowych. Teraz można zobaczyć zestawy kopii zapasowych w podziale według woluminów, które zawiera. Opcje **Przywracanie** i **usuwanie** są dostępne za pomocą menu kontekstowego (kliknij prawym przyciskiem myszy) dla zestawu kopii zapasowych. Jedną z tych akcji można wykonać na wybranym zestawie kopii zapasowych.

    ![Przejdź do katalogu kopii zapasowych](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Usuwanie zestawu kopii zapasowych
Usuń kopię zapasową, gdy nie chcesz już zachowywać skojarzonych z nią danych. Wykonaj następujące czynności, aby usunąć zestaw kopii zapasowych.

#### <a name="to-delete-a-backup-set"></a>Aby usunąć zestaw kopii zapasowych
 Przejdź do usługi StorSimple Device Manager i kliknij pozycję **Katalog kopii zapasowych**.
1. Filtruj wybory w następujący sposób:
   
   1. Określ zakres czasu. 
   2. Wybierz odpowiednie urządzenie. 
   3. Filtruj według woluminów lub zasad tworzenia kopii zapasowych dla kopii zapasowej, którą chcesz wybrać.
   4. Kliknij przycisk **Zastosuj,** aby wykonać tę kwerendę.
      
      Kopie zapasowe skojarzone z wybranymi zasadami woluminu lub kopii zapasowej powinny być wyświetlane na liście zestawów kopii zapasowych.

      ![Przejdź do katalogu kopii zapasowych](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

1. Wybierz i rozwiń zestaw kopii zapasowych. Teraz można zobaczyć zestawy kopii zapasowych w podziale według woluminów, które zawiera. Opcje **Przywracanie** i **usuwanie** są dostępne za pomocą menu kontekstowego (kliknij prawym przyciskiem myszy) dla zestawu kopii zapasowych. Kliknij prawym przyciskiem myszy wybrany zestaw kopii zapasowych i z menu kontekstowego wybierz polecenie **Usuń**.

    ![Przejdź do katalogu kopii zapasowych](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

1. Po wyświetleniu monitu o potwierdzenie przejrzyj wyświetlane informacje i kliknij przycisk **Usuń**. Wybrana kopia zapasowa zostanie trwale usunięta.

    ![Przejdź do katalogu kopii zapasowych](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

1. Zostaniesz powiadomiony, gdy usunięcie jest w toku i po pomyślnym zakończeniu. Po zakończeniu usuwania odśwież zapytanie na tej stronie. Usunięty zestaw kopii zapasowych nie będzie już wyświetlany na liście zestawów kopii zapasowych.

    ![Przejdź do katalogu kopii zapasowych](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [przywrócić urządzenie z zestawu kopii zapasowych za pomocą katalogu kopii zapasowych.](storsimple-8000-restore-from-backup-set-u2.md)
* Dowiedz się, jak [zarządzać urządzeniem StorSimple za pomocą usługi StorSimple Device Manager.](storsimple-8000-manager-service-administration.md)

