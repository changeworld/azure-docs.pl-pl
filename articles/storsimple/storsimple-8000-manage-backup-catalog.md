---
title: Zarządzanie katalogu kopii zapasowej StorSimple | Dokumentacja firmy Microsoft
description: Opisano sposób korzystania na stronie katalogu kopii zapasowej usługi Menedżer StorSimple urządzenie na liście, wybierz i Usuń zestawy kopii zapasowych.
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
ms.openlocfilehash: ac2577c6cd350d6d437d55e61ec73d954cb24893
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874927"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Używanie usługi Menedżer StorSimple urządzenia do zarządzania katalogu kopii zapasowej
## <a name="overview"></a>Omówienie
Usługę Menedżer StorSimple urządzenia **katalogu kopii zapasowej** bloku Wyświetla wszystkie zestawy kopii zapasowych tworzonych podczas ręczne lub zaplanowane kopie zapasowe są pobierane. Ta strona służy do listę wszystkich kopii zapasowych dla zasad tworzenia kopii zapasowej lub woluminem, zaznacz lub usuń kopii zapasowych lub użyj kopii zapasowej do przywrócenia lub sklonować woluminu.

W tym samouczku wyjaśniono, jak listy, wybierz i usuwania zestawu kopii zapasowych. Aby dowiedzieć się, jak przywracanie z kopii zapasowej urządzenia, przejdź do [przywrócenie urządzenia z zestawu kopii zapasowych](storsimple-8000-restore-from-backup-set-u2.md). Aby dowiedzieć się, jak klonowanie woluminu, przejdź do [sklonować woluminu StorSimple](storsimple-8000-clone-volume-u2.md).

![Katalog kopii zapasowej](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

**Katalogu kopii zapasowej** blok zawiera zapytanie, aby zawęzić kopii zapasowej zaznaczenia. Można filtrować zestawy kopii zapasowych, które są pobierane na podstawie następujących parametrów:

* **Urządzenie** — urządzenia, na którym utworzono ten zestaw kopii zapasowych.
* **Zasady tworzenia kopii zapasowej lub woluminie** — zasady tworzenia kopii zapasowej lub woluminie skojarzonym z tego zestawu kopii zapasowych.
* **Od i do** — zakres dat i godzin przy tworzeniu zestawu kopii zapasowych.

Następnie filtrowane zestawy kopii zapasowych wyszczególniono na podstawie następujących atrybutów:

* **Nazwa** — Nazwa zasad tworzenia kopii zapasowej lub woluminie skojarzonym z zestawu kopii zapasowych.
* **Rozmiar** — rzeczywisty rozmiar zestawu kopii zapasowych.
* **Utworzone na** — Data i godzina utworzenia kopii zapasowych. 
* **Typ** — zestawy kopii zapasowych można migawki lokalne lub migawki w chmurze. Migawka lokalna jest kopię zapasową wszystkich danych woluminu lokalnie przechowywanych na urządzeniu, migawka w chmurze odnosi się do kopii zapasowej danych woluminu znajdującej się w chmurze. Migawki lokalne zapewnić szybszy dostęp migawki w chmurze są wybrać, aby zachować odporność danych.
* **Zainicjowane przez** — kopie zapasowe można zainicjować automatycznie według harmonogramu lub ręcznie przez użytkownika. Aby zaplanować tworzenie kopii zapasowych, można użyć zasad tworzenia kopii zapasowej. Alternatywnie można użyć **utworzenia kopii zapasowej** opcji, aby utworzyć kopię zapasową ręczne.

## <a name="list-backup-sets-for-a-backup-policy"></a>Zestawy kopii zapasowych listy dla zasad tworzenia kopii zapasowej
Wykonaj poniższe kroki, aby wyświetlić listę wszystkich kopii zapasowych dla zasad tworzenia kopii zapasowej.

#### <a name="to-list-backup-sets"></a>Do listy zestawów kopii zapasowych
1. Przejdź do Menedżera urządzenia StorSimple usługi, a następnie kliknij przycisk **katalog kopii zapasowej**.

2. Filtruj ustawienia w następujący sposób:
   
   1. Określ zakres czasu.
   2. Wybierz odpowiednie urządzenie.
   3. Filtruj według **kopii zapasowej zasad** Aby wyświetlić odpowiednie kopie zapasowe.
   3. Z listy rozwijanej zasad tworzenia kopii zapasowej, wybierz **wszystkie** Aby wyświetlić wszystkie kopie zapasowe wybranego urządzenia.
   4. Kliknij przycisk **Zastosuj** do wykonania tego zapytania.
      
      Tworzenie kopii zapasowych skojarzone z wybranych zasad tworzenia kopii zapasowej powinny być wyświetlane na liście zestawów kopii zapasowych.

      ![Przejdź do katalogu kopii zapasowej](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Wybierz zestaw kopii zapasowych
Wykonaj poniższe kroki, aby wybrać zestaw kopii zapasowych dla woluminu lub zasad tworzenia kopii zapasowej.

#### <a name="to-select-a-backup-set"></a>Aby wybrać zestaw kopii zapasowych
1. Przejdź do Menedżera urządzenia StorSimple usługi, a następnie kliknij przycisk **katalog kopii zapasowej**.
2. Filtruj ustawienia w następujący sposób:
   
   1. Określ zakres czasu. 
   2. Wybierz odpowiednie urządzenie. 
   3. Filtruj według zasad woluminu lub kopii zapasowej do utworzenia kopii zapasowej, który chcesz wybrać.
   4. Kliknij przycisk **Zastosuj** do wykonania tego zapytania.
      
      Tworzenie kopii zapasowych skojarzony z wybranego woluminu lub zasad tworzenia kopii zapasowej powinien zostać wyświetlony na liście zestawów kopii zapasowych.

      ![Przejdź do katalogu kopii zapasowej](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Wybierz i rozwiń zestawu kopii zapasowych. Możesz teraz przeglądać zestawy kopii zapasowych według woluminów, które zawiera. **Przywrócić** i **usunąć** opcje są dostępne za pośrednictwem menu kontekstowe (kliknij prawym przyciskiem myszy) dla zestawu kopii zapasowych. Każda z tych akcji można wykonywać na wybrany zestaw kopii zapasowych.

    ![Przejdź do katalogu kopii zapasowej](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Usuń zestaw kopii zapasowych
Usunięcie kopii zapasowej, jeśli nie chcesz zachować dane skojarzone z nim. Wykonaj poniższe kroki, aby usunąć zestaw kopii zapasowych.

#### <a name="to-delete-a-backup-set"></a>Aby usunąć zestaw kopii zapasowych
 Przejdź do Menedżera urządzenia StorSimple usługi, a następnie kliknij przycisk **katalog kopii zapasowej**.
2. Filtruj ustawienia w następujący sposób:
   
   1. Określ zakres czasu. 
   2. Wybierz odpowiednie urządzenie. 
   3. Filtruj według zasad woluminu lub kopii zapasowej do utworzenia kopii zapasowej, który chcesz wybrać.
   4. Kliknij przycisk **Zastosuj** do wykonania tego zapytania.
      
      Tworzenie kopii zapasowych skojarzony z wybranego woluminu lub zasad tworzenia kopii zapasowej powinien zostać wyświetlony na liście zestawów kopii zapasowych.

      ![Przejdź do katalogu kopii zapasowej](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Wybierz i rozwiń zestawu kopii zapasowych. Możesz teraz przeglądać zestawy kopii zapasowych według woluminów, które zawiera. **Przywrócić** i **usunąć** opcje są dostępne za pośrednictwem menu kontekstowe (kliknij prawym przyciskiem myszy) dla zestawu kopii zapasowych. Kliknij prawym przyciskiem myszy wybrane zestawu kopii zapasowych i z menu kontekstowego wybierz **usunąć**.

    ![Przejdź do katalogu kopii zapasowej](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

4. Po wyświetleniu monitu o potwierdzenie, przejrzyj wyświetlone informacje i kliknij przycisk **usunąć**. Wybranej kopii zapasowej jest trwale usunięte.

    ![Przejdź do katalogu kopii zapasowej](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

5. Użytkownik zostanie powiadomiony, gdy usunięcie jest w toku i kiedy zostało pomyślnie zakończone. Po zakończeniu usuwania Odśwież zapytanie na tej stronie. Usunięto zestaw kopii zapasowych nie będzie dłużej widoczny na liście zestawów kopii zapasowych.

    ![Przejdź do katalogu kopii zapasowej](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [korzystania z katalogu kopii zapasowej do przywrócenia urządzenia z zestawu kopii zapasowych](storsimple-8000-restore-from-backup-set-u2.md).
* Dowiedz się, jak [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).

