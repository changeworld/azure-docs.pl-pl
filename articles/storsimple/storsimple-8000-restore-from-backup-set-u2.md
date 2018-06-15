---
title: Przywracanie z kopii zapasowej w serii StorSimple 8000 woluminu | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak używać usługi Menedżer StorSimple urządzenia katalogu kopii zapasowej do przywrócenia woluminu StorSimple z zestawu kopii zapasowych.
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
ms.openlocfilehash: aff0710ead4f76bb80c38e2d88fe9cd3ce6a7b48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875459"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Przywracania woluminu StorSimple z zestawu kopii zapasowych

## <a name="overview"></a>Omówienie

W tym samouczku opisano operacji przywracania, wykonać na urządzeniu serii StorSimple 8000 przy użyciu istniejącego zestawu kopii zapasowych. Użyj **katalog kopii zapasowej** blok przywracania woluminu z dysku lokalnego lub kopia zapasowa w chmurze. **Katalog kopii zapasowej** bloku Wyświetla wszystkie zestawy kopii zapasowych, które są tworzone podczas ręczne lub automatyczne kopie zapasowe są pobierane. Operacja przywracania z zestawu kopii zapasowych łączy woluminu w trybie online, podczas gdy dane są pobierane w tle.

Jest to alternatywna metoda, aby uruchomić Przywracanie można przejść do **urządzenia > [urządzenia] > woluminów**. W **woluminów** bloku, wybierz wolumin, kliknij prawym przyciskiem myszy można wywołać z menu kontekstowego, a następnie wybierz **przywrócić**.

## <a name="before-you-restore"></a>Przed przywróceniem

Przed rozpoczęciem przywracania, przejrzyj następujące ostrzeżenia:

* **Wolumin należy przełączyć w tryb offline** — zająć woluminu w trybie offline na hoście i urządzenia przed rozpoczęciem operacji przywracania. Mimo że operacji przywracania automatycznie go przywrócić do trybu online na urządzeniu, należy ręcznie przełączyć urządzenia w trybie online na hoście. Wolumin w trybie online można przełączyć na hoście, jak wolumin jest w trybie online na urządzeniu. (Nie trzeba czekać do momentu zakończenia operacji przywracania.) Procedury, przejdź do [Przełącz do trybu offline wolumin](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Typ woluminu po przywróceniu** — usunięte woluminy są przywracane na podstawie typu w migawce; to, że woluminy, które zostały przypięty lokalnie zostaną przywrócone jako woluminów przypiętych lokalnie i woluminów, które zostały do warstwy zostaną przywrócone jako woluminy warstwowe.

    Istniejące woluminy bieżący typ użycia woluminu zastępuje typ, który jest przechowywany w migawce. Na przykład woluminu w przypadku przywracania z migawki, która została podjęta, jeśli typ woluminu został warstwowy i typ woluminu jest teraz przypięty lokalnie (z powodu operacji konwersji, która została wykonana), następnie wolumin zostanie przywrócony jako woluminu przypiętego lokalnie. Podobnie jeśli rozszerzona istniejącego woluminu przypiętego lokalnie, a następnie przywrócone z starsze migawką gdy wolumin był mniejszy, przywróconej woluminu zachowuje bieżący rozmiar po rozwinięciu.

    Nie można przekonwertować woluminu z wolumin warstwowy do woluminu przypiętego lokalnie lub na wolumin warstwowy woluminu przypiętego lokalnie, podczas gdy wolumin jest przywracana. Poczekaj na zakończenie operacji przywracania, a następnie przekonwertować wolumin do innego typu. Aby uzyskać informacje o konwersji woluminu, przejdź do [zmienić typ woluminu](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **Rozmiar woluminu jest widoczny w przywróconej woluminu** — to jest ważną kwestią w przypadku przywracania woluminu przypiętego lokalnie, która została usunięta (ponieważ woluminów przypiętych lokalnie są w pełni zaaprowizowanym). Upewnij się, że masz wystarczającą ilość miejsca na przed próbą przywrócenia woluminu przypiętego lokalnie, który został wcześniej usunięty.

* **Nie można rozszerzyć woluminu, gdy jest przywracana** — poczekaj na zakończenie operacji przywracania przed przystąpieniem do zwiększenia woluminu. Aby uzyskać informacje o rozszerzaniu woluminu, przejdź do [zmodyfikować woluminu](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Podczas przywracania woluminu lokalnego można wykonywać kopii zapasowej** — dla procedury przejdź do [Zarządzanie zasadami tworzenia kopii zapasowej przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manage-backup-policies-u2.md).

* **Możesz anulować operacji przywracania** — Jeśli anulujesz zadania przywracania, a następnie wolumin zostanie cofnięta do stanu sprzed zainicjował operację przywracania. Procedury, przejdź do [anulować zadanie](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Jak przywrócić pracę

Dla urządzeń z systemem aktualizacji 4 lub nowszym systemem heatmap przywracania jest zaimplementowana. Jak host żądania do dostępu do danych połączyć się z urządzeniem, te żądania są śledzone, i heatmap zostanie utworzony. Wysoka częstość powoduje fragmentów danych z wyższej cieplna natomiast mniejszej szybkości żądania przekłada się na fragmenty o niższych interakcji. Muszą uzyskać dostęp do co najmniej danych dwa razy może być oznaczony jako _gorących_. Plik jest modyfikowany jest również oznaczona jako _gorących_. Po rozpoczęciu przywracania aktywnego dodawaniem danych występuje oparte na heatmap. W wersjach wcześniejszych niż aktualizacja 4, danych zostanie pobrana podczas przywracania oparte na dostęp tylko.

Następujące zastrzeżenia dotyczą na podstawie heatmap przywracania:

* Heatmap śledzenie jest włączone tylko dla woluminów warstwowych i woluminów przypiętych lokalnie nie są obsługiwane.

* Przywracanie na podstawie Heatmap nie jest obsługiwana w przypadku klonowania woluminu na innym urządzeniu. 

* Jeśli istnieje przywracania w miejscu i firma Microsoft nie rehydrate (jak dane są już dostępne w lokalnie), a następnie na urządzeniu, istnieje lokalne migawki woluminu, który ma zostać przywrócony. 

* Domyślnie podczas przywracania, Preparaty nawadniające zadań są inicjowane z wyprzedzeniem rehydrate dane oparte na heatmap. 

W pakiecie Update 4 poleceń cmdlet programu Windows PowerShell można zbadać uruchomionych zadań Preparaty nawadniające anulować zadanie Preparaty nawadniające i Pobierz stan zadania Preparaty nawadniające.

* `Get-HcsRehydrationJob`— To polecenie cmdlet pobiera stan zadania Preparaty nawadniające. Zadanie pojedynczego Preparaty nawadniające zostanie wywołany dla jednego woluminu.

* `Set-HcsRehydrationJob`— To polecenie cmdlet pozwala na wstrzymanie, Zatrzymaj, Wznów zadanie Preparaty nawadniające, gdy trwa Preparaty nawadniające.

Aby uzyskać więcej informacji dotyczących poleceń cmdlet Preparaty nawadniające, przejdź do [Dokumentacja poleceń cmdlet programu Windows PowerShell dla StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Z automatycznego rehdyration zwykle wyższa wydajność odczytu przejściowej oczekuje. Rzeczywiste magniutde ulepszeń zależy od różnych czynników, takich jak wzorca dostępu, przenoszenia danych i typ danych. 

Aby anulować zadanie Preparaty nawadniające, używając polecenia cmdlet programu PowerShell. Jeśli chcesz trwale wyłączyć Preparaty nawadniające zadania dla wszystkich przyszłych przywraca [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>Jak używać katalogu kopii zapasowej

**Katalogu kopii zapasowej** blok zawiera zapytanie, które pomaga ograniczyć kopii zapasowej zaznaczenia. Można filtrować zestawy kopii zapasowych, które są pobierane na podstawie następujących parametrów:

* **Zakres czasu** — zakres dat i godzin przy tworzeniu zestawu kopii zapasowych.
* **Urządzenie** — urządzenia, na którym utworzono ten zestaw kopii zapasowych.
* **Wykonaj kopię zapasową zasad** lub **woluminu** — zasady tworzenia kopii zapasowej lub woluminie skojarzonym z tego zestawu kopii zapasowych.

Następnie filtrowane zestawy kopii zapasowych wyszczególniono na podstawie następujących atrybutów:

* **Nazwa** — Nazwa zasad tworzenia kopii zapasowej lub woluminie skojarzonym z zestawu kopii zapasowych.
* **Typ** — zestawy kopii zapasowych można migawki lokalne lub migawki w chmurze. Migawka lokalna jest kopię zapasową wszystkich danych woluminu lokalnie przechowywanych na urządzeniu, migawka w chmurze odnosi się do kopii zapasowej danych woluminu znajdującej się w chmurze. Migawki lokalne zapewnić szybszy dostęp migawki w chmurze są wybrać, aby zachować odporność danych.
* **Rozmiar** — rzeczywisty rozmiar zestawu kopii zapasowych.
* **Utworzony na** — Data i godzina utworzenia kopii zapasowych. 
* **Woluminy** — liczba woluminów skojarzone z zestawu kopii zapasowych.
* **Zainicjowano** — tworzenie kopii zapasowych może zostać zainicjowane automatycznie, zgodnie z harmonogramem lub ręcznie przez użytkownika. (Aby zaplanować tworzenie kopii zapasowych można użyć zasad tworzenia kopii zapasowej. Alternatywnie można użyć **utworzenia kopii zapasowej** opcję, aby wykonać interakcyjnego lub na żądanie kopii zapasowej.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Jak przywrócić z kopii zapasowej woluminu StorSimple

Można użyć **katalogu kopii zapasowej** bloku do przywracania woluminu StorSimple z określonej kopii zapasowej. Należy jednak pamiętać, przywracanie wolumin zostanie przywrócony do stanu, w jakim był po wykonaniu kopii zapasowej woluminu. Dane, które zostały dodane po operacji tworzenia kopii zapasowej zostaną utracone.

> [!WARNING]
> Przywracanie z kopii zapasowej spowoduje zastąpienie istniejących woluminów z kopii zapasowej. Może to spowodować utratę wszystkich danych, który został zapisany po wykonaniu kopii zapasowej.


### <a name="to-restore-your-volume"></a>Aby przywrócić woluminu
1. Przejdź do usługi Menedżer StorSimple urządzenia, a następnie kliknij przycisk **katalog kopii zapasowej**.

2. Wybierz kopię zapasową ustawione w następujący sposób:
   
   1. Określ zakres czasu.
   2. Wybierz odpowiednie urządzenie.
   3. Na liście rozwijanej wybierz zasady woluminu lub kopii zapasowej do utworzenia kopii zapasowej, który chcesz wybrać.
   4. Kliknij przycisk **Zastosuj** do wykonania tego zapytania.

    Tworzenie kopii zapasowych skojarzony z wybranego woluminu lub zasad tworzenia kopii zapasowej powinien zostać wyświetlony na liście zestawów kopii zapasowych.
   
    ![Listy zestawu kopii zapasowych](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Rozwiń zestaw, aby wyświetlić skojarzone woluminy kopii zapasowych. Te woluminy muszą być pobierane w trybie offline na hoście i urządzenia, zanim będzie można ich przywrócić. Dostęp do woluminów na **woluminów** bloku urządzenia, a następnie postępuj zgodnie z instrukcjami [Przełącz do trybu offline wolumin](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) ich przełączenia do trybu offline.
   
   > [!IMPORTANT]
   > Upewnij się, że została wykonana woluminy w tryb offline na hoście najpierw, przed podjęciem dalszych woluminy w tryb offline na urządzeniu. Jeśli nie przyjmują woluminy w tryb offline na hoście, może prowadzić do uszkodzenia danych.
   
4. Przejdź z powrotem do **katalogu kopii zapasowej** i wybierz zestaw kopii zapasowych. Kliknij prawym przyciskiem myszy, a następnie z menu kontekstowego wybierz **przywrócić**.

    ![Listy zestawu kopii zapasowych](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Pojawi się monit o potwierdzenie. Przejrzyj informacje przywracania, a następnie zaznacz pole wyboru potwierdzenia.
   
    ![Strona potwierdzenia](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7.  Kliknij przycisk **przywrócić**. Powoduje to zainicjowanie zadania przywracania, które można wyświetlić, uzyskując dostęp do **zadania** strony.

    ![Strona potwierdzenia](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. Po zakończeniu przywracania Sprawdź zawartość woluminów zastępuje woluminy z kopii zapasowej.


## <a name="if-the-restore-fails"></a>Jeśli jej nie powiedzie się.

Jeśli operacja przywracania nie powiedzie się z jakiegokolwiek powodu, zostanie wyświetlony alert. W takim przypadku należy odświeżyć listy kopii zapasowych, aby sprawdzić, czy kopia zapasowa jest nadal ważny. Jeśli kopia zapasowa jest prawidłowa i że jest przywracana z chmury, następnie problemy z połączeniem mogą być przyczyną problemu.

Aby ukończyć operację przywracania, zająć woluminu w trybie offline na hoście i spróbuj ponownie wykonać operację przywracania. Należy pamiętać, że wszelkie modyfikacje danych woluminu, które zostały wykonane podczas procesu przywracania zostaną utracone.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [woluminów StorSimple zarządzanie](storsimple-8000-manage-volumes-u2.md).
* Dowiedz się, jak [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).

