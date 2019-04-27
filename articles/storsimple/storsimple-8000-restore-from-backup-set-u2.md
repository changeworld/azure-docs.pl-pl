---
title: Przywracanie woluminu z kopii zapasowej na serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak za pomocą usługi Menedżer urządzeń StorSimple wykaz kopii zapasowych, przywracania woluminu StorSimple z zestawu kopii zapasowych.
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
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: 6a2e022697ced90d968075b7a4abe4163be7a539
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723394"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Przywracanie woluminu StorSimple z zestawu kopii zapasowych

## <a name="overview"></a>Omówienie

W tym samouczku opisano operacji przywracania, wykonywane na urządzenia serii StorSimple 8000 przy użyciu istniejącego zestawu kopii zapasowych. Użyj **katalog kopii zapasowej** bloku przywracania woluminu z dysku lokalnego lub kopia zapasowa w chmurze. **Katalog kopii zapasowej** bloku wyświetlane są wszystkie zestawy kopii zapasowych, które są tworzone podczas ręczne lub automatyczne kopie zapasowe są wykonywane. Operacja przywracania z kopii zapasowej do trybu online dopiero podczas gdy dane są pobierane w tle.

Alternatywna metoda, aby rozpocząć przywracanie jest aby przejść do **urządzeń > [urządzenie] > woluminów**. W **woluminów** bloku, wybierz wolumin, kliknij prawym przyciskiem myszy aby wywołać menu kontekstowe, a następnie wybierz **przywrócić**.

## <a name="before-you-restore"></a>Przed przywróceniem

Przed rozpoczęciem przywracania, należy przejrzeć następujące zastrzeżenia:

* **Wolumin należy wykonać w trybie offline** — wykonaj wolumin w tryb offline na hoście i urządzenia, przed rozpoczęciem operacji przywracania. Mimo że operacji przywracania automatycznie do trybu online dopiero na urządzeniu, należy ręcznie przenieść urządzenie w tryb online, na hoście. Można przenieść wolumin w trybie online na hoście, jak wolumin jest w trybie online, na urządzeniu. (Nie trzeba czekać, aż do ukończenia operacji przywracania.) Aby uzyskać procedury, przejdź do [przełączyć wolumin w tryb offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Typ woluminu po przywróceniu** — usunięte woluminy są przywracane na podstawie typu w migawce; czyli woluminów przypiętych lokalnie zostaną przywrócone jako woluminy przypięte lokalnie i woluminów, które zostały warstwowego zostaną przywrócone jako woluminy warstwowe.

    Istniejące woluminy bieżącego typu użycia woluminu zastępuje typ, który jest przechowywany w migawce. Na przykład woluminu w przypadku przywracania z migawki, która została wykonana, gdy typ woluminu został warstwy, a typ woluminu jest teraz przypięty lokalnie (z powodu operacji konwersji, która została wykonana), następnie wolumin zostanie przywrócony jako woluminu przypiętego lokalnie. Podobnie jeśli istniejącego woluminu przypiętego lokalnie została rozwinięta, a następnie przywrócić z starsze migawki gdy wolumin był mniejszy, przywróconych woluminów zachowa bieżący rozmiar po rozwinięciu.

    Nie można przekonwertować woluminu z warstwowego woluminu do woluminu przypiętego lokalnie lub woluminu przypiętego lokalnie do woluminu warstwowego, gdy wolumin jest przywracany. Poczekaj, aż operacja przywracania została zakończona, a następnie przekonwertować wolumin, do innego typu. Aby uzyskać informacje o konwersji woluminu, przejdź do [zmienić typ woluminu](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **Rozmiar woluminu jest odzwierciedlana w woluminie przywróconej** — jest ważną kwestią, Jeśli przywracasz woluminu przypiętego lokalnie, który został usunięty (ponieważ lokalnie przypięte woluminy są w pełni zaaprowizowanym). Upewnij się, że jest wystarczająco dużo miejsca, przed przystąpieniem do przywracania woluminu przypiętego lokalnie, który został wcześniej usunięty.

* **Nie można rozszerzyć woluminu, gdy jest przywracana** — poczekaj na zakończenie operacji przywracania przed przystąpieniem do zwiększenia woluminu. Aby uzyskać informacje o rozszerzaniu woluminu, przejdź do [modyfikowania woluminu](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Można wykonać kopii zapasowej, podczas przywracania woluminu lokalnego** — dla procedur, przejdź do [usługi Menedżer urządzeń StorSimple umożliwia zarządzanie zasadami kopii zapasowych](storsimple-8000-manage-backup-policies-u2.md).

* **Można anulować operacji przywracania** — w przypadku anulowania zadania przywracania, a następnie wolumin zostanie wycofana do stanu sprzed zainicjował operację przywracania. Aby uzyskać procedury, przejdź do [anulować zadanie](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Jak przywrócić pracę

W przypadku urządzeń z aktualizacją Update 4 lub nowszym Przywracanie na podstawie mapy cieplnej jest zaimplementowana. Jako host wysyła żądanie do dostępu do danych nawiązać połączenia z urządzeniem, te żądania są śledzone i utworzeniu mapy cieplnej. Liczba żądań wysokiej powoduje fragmentów danych przy użyciu nowszej ciepła natomiast wolniejszym tempie żądania przekłada się na fragmenty o niższych ciepło. Należy uzyskać dostęp do danych w co najmniej dwa razy być oznaczony jako _gorąca_. Plik, który jest modyfikowana również jest oznaczony jako _gorąca_. Po zainicjowaniu przywracania aktywnego wypełniania danych występuje zależnie od mapy cieplnej. W przypadku wersji starszych niż Update 4 danych jest pobierany podczas przywracania oparte na dostęp tylko do.

Następujące zastrzeżenia dotyczą Przywracanie na podstawie mapy cieplnej:

* Mapy cieplnej śledzenie jest włączone tylko dla woluminów warstwowych, a woluminy przypięte lokalnie nie są obsługiwane.

* Przywracanie na podstawie mapy cieplnej nie jest obsługiwane, podczas klonowania woluminu na innym urządzeniu. 

* W przypadku przywracania w miejscu, a następnie firma Microsoft nie przywrócenia z magazynu trwałego (jak dane są już dostępne w lokalnie) na urządzeniu, istnieje lokalne migawki dla woluminu, który ma zostać przywrócone. 

* Domyślnie podczas przywracania, zadania ponownego wypełniania są inicjowane, które aktywnie przywrócenia z magazynu trwałego danych w oparciu mapy cieplnej. 

W aktualizacji Update 4 poleceń cmdlet programu Windows PowerShell może służyć do zapytania działające zadania ponownego wypełniania, anulowanie zadania ponownego wypełniania i wyświetlić stan zadania ponownego wypełniania.

* `Get-HcsRehydrationJob` — To polecenie cmdlet pobiera stan zadania ponownego wypełniania. Zadanie jednego ponownego wypełniania, zostanie wywołany przez jeden wolumin.

* `Set-HcsRehydrationJob` — To polecenie cmdlet pozwala na wstrzymanie, Zatrzymaj, Wznów zadanie ponownego wypełniania, podczas ponownego wypełniania jest w toku.

Aby uzyskać więcej informacji na temat poleceń cmdlet ponownego wypełniania, przejdź do [Dokumentacja poleceń cmdlet programu Windows PowerShell dla usługi StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Za pomocą automatycznego ponownego wypełniania zwykle wyższa wydajność odczytu przejściowy jest oczekiwany. Wielkość rzeczywistej ulepszenia zależy od różnych czynników, takich jak wzorcu dostępu, współczynnik zmian danych i typ danych. 

Aby anulować zadanie ponownego wypełniania, używając polecenia cmdlet programu PowerShell. Jeśli chcesz trwale wyłączyć zadania ponownego wypełniania dla wszystkich przyszłych przywraca [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>Jak używać wykaz kopii zapasowych

**Wykaz kopii zapasowych** blok zawiera zapytanie, które pomaga w celu zawężenia kopii zapasowej Ustaw zaznaczenie. Można filtrować zestawów kopii zapasowych, które są pobierane w oparciu o następujące parametry:

* **Zakres czasu** — zakres dat i godzin, podczas tworzenia zestawu kopii zapasowych.
* **Urządzenie** — urządzenia, na którym został utworzony zestaw kopii zapasowych.
* **Zasady tworzenia kopii zapasowej** lub **woluminu** — zasady tworzenia kopii zapasowej lub woluminie skojarzonym z tego zestawu kopii zapasowych.

Filtrowane zestawów kopii zapasowych następnie wyszczególniono w oparciu o następujące atrybuty:

* **Nazwa** — Nazwa zasad kopii zapasowych lub woluminie skojarzonym z zestawu kopii zapasowych.
* **Typ** — zestawy kopii zapasowych mogą być lokalne migawki lub migawki w chmurze. Migawka lokalna jest kopię zapasową wszystkich danych woluminów przechowywanych lokalnie na urządzeniu, migawkę w chmurze, który odnosi się do wykonywania kopii zapasowych woluminów danych znajdujących się w chmurze. Migawki lokalne zapewniają szybszy dostęp, dlatego są wybierane migawki w chmurze, aby zachować odporność danych.
* **Rozmiar** — rzeczywisty rozmiar zestawu kopii zapasowych.
* **Utworzone na** — Data i godzina, kiedy zostały utworzone kopie zapasowe. 
* **Woluminy** — liczba woluminów skojarzonych z zestawu kopii zapasowych.
* **Zainicjowano** — kopie zapasowe mogą być inicjowane automatycznie, zgodnie z harmonogramem lub ręcznie przez użytkownika. (Aby zaplanować kopie zapasowe można użyć zasad tworzenia kopii zapasowej. Alternatywnie, można użyć **wykonaj kopię zapasową** opcję, aby wykonać interakcyjnego lub na żądanie kopii zapasowej.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Przywracanie woluminu StorSimple z kopii zapasowej

Możesz użyć **wykaz kopii zapasowych** bloku przywracania woluminu StorSimple z określonej kopii zapasowej. Należy pamiętać, jednak, Przywracanie woluminu zostanie przywrócony woluminu do stanu, w jakim był po wykonaniu kopii zapasowej. Wszelkie dane, które zostały dodane po zakończeniu operacji tworzenia kopii zapasowej zostaną utracone.

> [!WARNING]
> Przywracanie z kopii zapasowej spowoduje zastąpienie istniejących woluminów z kopii zapasowej. Może to spowodować utratę wszystkich danych, które zostały zapisane, po wykonaniu kopii zapasowej.


### <a name="to-restore-your-volume"></a>Aby przywrócić woluminu
1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij przycisk **katalog kopii zapasowej**.

2. Wybierz kopię zapasową, ustaw w następujący sposób:
   
   1. Określ zakres czasu.
   2. Wybierz odpowiednie urządzenie.
   3. Na liście rozwijanej wybierz zasady woluminu lub kopii zapasowej do utworzenia kopii zapasowej, który chcesz wybrać.
   4. Kliknij przycisk **Zastosuj** do wykonywania tej kwerendy.

      Kopie zapasowe skojarzone z wybranego woluminu lub zasad tworzenia kopii zapasowej powinna zostać wyświetlona na liście zestawów kopii zapasowych.
   
      ![Lista zestawu kopii zapasowych](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Rozwiń zestaw, aby wyświetlić skojarzone woluminy kopii zapasowych. Te woluminy muszą przełączony w tryb offline na hoście i urządzenia przed można je przywrócić. Dostęp do woluminów na **woluminów** bloku urządzenia, a następnie wykonaj kroki opisane w [przełączyć wolumin w tryb offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) do przełączenia ich w tryb offline.
   
   > [!IMPORTANT]
   > Upewnij się, czy wykonano woluminy w tryb offline na hoście po pierwsze, zanim zaczną woluminy w tryb offline na urządzeniu. Jeśli nie wykonasz woluminy w tryb offline na hoście, może prowadzić do uszkodzenia danych.
   
4. Przejdź z powrotem do **wykaz kopii zapasowych** karcie, a następnie wybierz zestaw kopii zapasowych. Kliknij prawym przyciskiem myszy, a następnie z menu kontekstowego wybierz pozycję **przywrócić**.

    ![Lista zestawu kopii zapasowych](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Użytkownik jest monitowany o potwierdzenie. Zapoznaj się z informacjami przywracania, a następnie zaznacz pole wyboru potwierdzenia.
   
    ![Strona potwierdzenia](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7. Kliknij przycisk **przywrócić**. Spowoduje to zainicjowanie zadania przywracania, który można wyświetlić, uzyskując dostęp do **zadań** strony.

   ![Strona potwierdzenia](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. Po zakończeniu przywracania Sprawdź, czy zawartość woluminów są zastępowane przez woluminy z kopii zapasowej.


## <a name="if-the-restore-fails"></a>W przypadku niepowodzenia przywracania

Zostanie wyświetlony alert, jeśli operacja przywracania nie powiedzie się z jakiegokolwiek powodu. W takiej sytuacji należy odświeżyć listy kopii zapasowych, aby sprawdzić, czy kopia zapasowa jest nadal ważny. Jeśli kopia zapasowa jest prawidłowa, i przywracasz z chmury, następnie problemy z łącznością mogą być przyczyną problemu.

Aby ukończyć operację przywracania, przełącz go w trybie offline na hoście i spróbuj ponownie wykonać operację przywracania. Należy pamiętać, że wszelkie modyfikacje danych woluminów, które były wykonywane podczas procesu przywracania zostaną utracone.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [woluminów StorSimple zarządzanie](storsimple-8000-manage-volumes-u2.md).
* Dowiedz się, jak [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

