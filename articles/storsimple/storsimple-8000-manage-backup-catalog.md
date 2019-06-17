---
title: Zarządzanie wykazem kopii zapasowych usługi StorSimple | Dokumentacja firmy Microsoft
description: Opis sposobu użycia strony wykaz kopii zapasowych usługi Menedżer urządzeń StorSimple do listy, wybierz i Usuń zestawy kopii zapasowych.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60319675"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Usługa Menedżer urządzeń StorSimple do zarządzania wykaz kopii zapasowych
## <a name="overview"></a>Omówienie
Usługa Menedżer urządzeń StorSimple **wykaz kopii zapasowych** bloku wyświetlane są wszystkie zestawy kopii zapasowych, które są tworzone podczas ręczne lub zaplanowane kopie zapasowe są wykonywane. Użyj tej strony, aby wyświetlić listę wszystkich kopii zapasowych dla zasad tworzenia kopii zapasowej lub na wolumin, wybierz opcję lub usunąć kopie zapasowe lub użyj kopii zapasowej do przywrócenia lub klonowanie woluminu.

W tym samouczku wyjaśniono, jak wyświetlać, wybierz i usuwanie zestawu kopii zapasowych. Aby dowiedzieć się, jak przywrócić urządzenie z kopii zapasowej, przejdź do [przywrócić urządzenie z zestawu kopii zapasowych](storsimple-8000-restore-from-backup-set-u2.md). Aby dowiedzieć się, jak sklonować woluminu, przejdź do [klonowania woluminu StorSimple](storsimple-8000-clone-volume-u2.md).

![wykaz kopii zapasowych](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

**Wykaz kopii zapasowych** blok zawiera zapytanie, aby zawęzić kopii zapasowej Ustaw zaznaczenie. Można filtrować zestawów kopii zapasowych, które są pobierane na podstawie następujących parametrów:

* **Urządzenie** — urządzenia, na którym został utworzony zestaw kopii zapasowych.
* **Zasady tworzenia kopii zapasowej lub woluminu** — zasady tworzenia kopii zapasowej lub woluminie skojarzonym z tego zestawu kopii zapasowych.
* **Od i do** — zakres dat i godzin, podczas tworzenia zestawu kopii zapasowych.

Filtrowane zestawów kopii zapasowych następnie wyszczególniono w oparciu o następujące atrybuty:

* **Nazwa** — Nazwa zasad kopii zapasowych lub woluminie skojarzonym z zestawu kopii zapasowych.
* **Rozmiar** — rzeczywisty rozmiar zestawu kopii zapasowych.
* **Data utworzenia** — Data i godzina, kiedy zostały utworzone kopie zapasowe. 
* **Typ** — zestawy kopii zapasowych mogą być lokalne migawki lub migawki w chmurze. Migawka lokalna jest kopię zapasową wszystkich danych woluminów przechowywanych lokalnie na urządzeniu, migawkę w chmurze, który odnosi się do wykonywania kopii zapasowych woluminów danych znajdujących się w chmurze. Migawki lokalne zapewniają szybszy dostęp, dlatego są wybierane migawki w chmurze, aby zachować odporność danych.
* **Zainicjowane przez** — kopie zapasowe mogą być inicjowane automatycznie, zgodnie z harmonogramem lub ręcznie przez użytkownika. Aby zaplanować kopie zapasowe, można użyć zasad tworzenia kopii zapasowej. Alternatywnie, można użyć **wykonaj kopię zapasową** opcję, aby wykonać kopię zapasową ręczne.

## <a name="list-backup-sets-for-a-backup-policy"></a>Kopia zapasowa listy zestawów dla zasad tworzenia kopii zapasowej
Wykonaj poniższe kroki, aby wyświetlić listę wszystkich kopii zapasowych dla zasad kopii zapasowych.

#### <a name="to-list-backup-sets"></a>Na liście zestawów kopii zapasowych
1. Przejdź do Menedżera urządzeń StorSimple, usługi, a następnie kliknij przycisk **katalog kopii zapasowej**.

2. Filtruj wyborów w następujący sposób:
   
   1. Określ zakres czasu.
   2. Wybierz odpowiednie urządzenie.
   3. Filtruj według **zasady tworzenia kopii zapasowej** do wyświetlania odpowiadającego kopii zapasowych.
   3. Wybierz z listy rozwijanej zasad tworzenia kopii zapasowej, **wszystkich** Aby wyświetlić wszystkie kopie zapasowe na wybranym urządzeniu.
   4. Kliknij przycisk **Zastosuj** do wykonywania tej kwerendy.
      
      Kopie zapasowe, skojarzone z wybranych zasad tworzenia kopii zapasowej powinna zostać wyświetlona na liście zestawów kopii zapasowych.

      ![Przejdź do katalogu kopii zapasowej](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Wybierz zestaw kopii zapasowych
Wykonaj poniższe kroki, aby wybrać zestaw kopii zapasowych dla woluminu lub zasad tworzenia kopii zapasowej.

#### <a name="to-select-a-backup-set"></a>Aby wybrać zestaw kopii zapasowych
1. Przejdź do Menedżera urządzeń StorSimple, usługi, a następnie kliknij przycisk **katalog kopii zapasowej**.
2. Filtruj wyborów w następujący sposób:
   
   1. Określ zakres czasu. 
   2. Wybierz odpowiednie urządzenie. 
   3. Filtruj według zasad woluminu lub kopii zapasowej do utworzenia kopii zapasowej, który chcesz wybrać.
   4. Kliknij przycisk **Zastosuj** do wykonywania tej kwerendy.
      
      Kopie zapasowe skojarzone z wybranego woluminu lub zasad tworzenia kopii zapasowej powinna zostać wyświetlona na liście zestawów kopii zapasowych.

      ![Przejdź do katalogu kopii zapasowej](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Wybierz i rozwiń węzeł zestawu kopii zapasowych. Teraz widać zestawów kopii zapasowych z podziałem na woluminach, które zawiera. **Przywrócić** i **Usuń** opcje są dostępne za pośrednictwem menu kontekstowe (kliknij prawym przyciskiem myszy) dla zestawu kopii zapasowych. Każda z tych akcji można wykonywać na zestaw kopii zapasowych, które wybrano.

    ![Przejdź do katalogu kopii zapasowej](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Usuwanie zestawu kopii zapasowych
Jeśli nie chcesz już być przechowywane dane skojarzone z nim, należy usunąć kopii zapasowej. Wykonaj poniższe kroki, aby usunąć zestawu kopii zapasowych.

#### <a name="to-delete-a-backup-set"></a>Można usunąć zestawu kopii zapasowych
 Przejdź do Menedżera urządzeń StorSimple, usługi, a następnie kliknij przycisk **katalog kopii zapasowej**.
1. Filtruj wyborów w następujący sposób:
   
   1. Określ zakres czasu. 
   2. Wybierz odpowiednie urządzenie. 
   3. Filtruj według zasad woluminu lub kopii zapasowej do utworzenia kopii zapasowej, który chcesz wybrać.
   4. Kliknij przycisk **Zastosuj** do wykonywania tej kwerendy.
      
      Kopie zapasowe skojarzone z wybranego woluminu lub zasad tworzenia kopii zapasowej powinna zostać wyświetlona na liście zestawów kopii zapasowych.

      ![Przejdź do katalogu kopii zapasowej](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

1. Wybierz i rozwiń węzeł zestawu kopii zapasowych. Teraz widać zestawów kopii zapasowych z podziałem na woluminach, które zawiera. **Przywrócić** i **Usuń** opcje są dostępne za pośrednictwem menu kontekstowe (kliknij prawym przyciskiem myszy) dla zestawu kopii zapasowych. Kliknij prawym przyciskiem myszy wybrany zestaw kopii zapasowych, a następnie z menu kontekstowego wybierz **Usuń**.

    ![Przejdź do katalogu kopii zapasowej](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

1. Po wyświetleniu monitu o potwierdzenie Przejrzyj wyświetlone informacje i kliknij przycisk **Usuń**. Wybranej kopii zapasowej jest trwale usunięte.

    ![Przejdź do katalogu kopii zapasowej](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

1. Użytkownik będzie powiadamiany, gdy usuwanie jest w trakcie wykonywania i po jej pomyślnym zakończeniu pracy. Po zakończeniu usuwania, należy odświeżyć zapytanie na tej stronie. Usunięto zestaw kopii zapasowych nie jest już pojawi się na liście zestawów kopii zapasowych.

    ![Przejdź do katalogu kopii zapasowej](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [Użyj wykaz kopii zapasowych, aby przywrócić urządzenie z zestawu kopii zapasowych](storsimple-8000-restore-from-backup-set-u2.md).
* Dowiedz się, jak [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

