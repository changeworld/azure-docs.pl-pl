---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0541b15743cff16ec0d19f8b450150c5430afb8d
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164271"
---
<!--author=alkohli last changed: 02/10/17-->

#### <a name="to-add-a-storsimple-backup-policy"></a>Aby dodać zasady kopii zapasowych danych StorSimple

1. Przejdź do urządzenia StorSimple i kliknij pozycję **Zasady kopii zapasowych**.

2. W bloku **Zasady kopii zapasowych** kliknij pozycję **+ Dodaj zasady** na pasku poleceń.
   
    ![Dodawanie zasad kopii zapasowych](./media/storsimple-8000-add-backup-policy-u2/addbupol1.png)

3. W bloku **Tworzenie zasad kopii zapasowych** wykonaj następujące czynności:
   
   1. Pole **Wybierz urządzenie** jest automatycznie wypełniane na podstawie wybranego urządzenia.
   
   2. Podaj **Nazwę zasad** kopii zapasowych o długości od 3 do 150 znaków. Po utworzeniu zasad nie można zmienić ich nazwy.
       
   3. Aby przypisać woluminy do tych zasad kopii zapasowych, wybierz pozycję **Dodaj woluminy**, a następnie na tabelarycznej liście woluminów kliknij pola wyboru, aby przypisać woluminy do tych zasad kopii zapasowych.

       ![Dodawanie zasad kopii zapasowych](./media/storsimple-8000-add-backup-policy-u2/addbupol2.png)

   4. Aby zdefiniować harmonogram dla tych zasad kopii zapasowych, kliknij pozycję **Pierwszy harmonogram**, a następnie zmodyfikuj następujące parametry:

       ![Dodawanie zasad kopii zapasowych](./media/storsimple-8000-add-backup-policy-u2/addbupol3.png)

       1. Dla pozycji **Typ migawki** wybierz opcję **Chmura** lub **Lokalna**.

       2. Podaj częstotliwość wykonywania kopii zapasowych (określ liczbę, a następnie wybierz z listy rozwijanej pozycję **Dni** lub **Tygodnie**).

       3. Wprowadź harmonogram przechowywania.

       4. Wprowadź datę i godzinę rozpoczęcia dla zasad kopii zapasowych.

       5. Kliknij przycisk **OK**, aby zdefiniować harmonogram.

   5. Kliknij przycisk **Utwórz**, aby utworzyć zasady kopii zapasowych.

       ![Dodawanie zasad kopii zapasowych](./media/storsimple-8000-add-backup-policy-u2/addbupol4.png)
   
   6. Otrzymasz powiadomienie o utworzeniu zasad kopii zapasowych. Nowo dodane zasady będą wyświetlane w widoku tabelarycznym w bloku **Zasady kopii zapasowych**.

       ![Dodawanie zasad kopii zapasowych](./media/storsimple-8000-add-backup-policy-u2/addbupol7.png)

