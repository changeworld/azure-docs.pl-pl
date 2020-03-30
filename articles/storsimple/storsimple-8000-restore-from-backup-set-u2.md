---
title: Przywracanie woluminu z kopii zapasowej w serii StorSimple 8000 | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak przywrócić wolumin StorSimple z zestawu kopii zapasowych za pomocą usługi StorSimple Device Manager.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60723394"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Przywracanie woluminu StorSimple z zestawu kopii zapasowych

## <a name="overview"></a>Omówienie

W tym samouczku opisano operację przywracania wykonaną na urządzeniu z serii StorSimple 8000 przy użyciu istniejącego zestawu kopii zapasowych. Użyj **bloku katalogu kopii zapasowej,** aby przywrócić wolumin z kopii zapasowej lokalnej lub w chmurze. Blok **katalogu kopii zapasowej** wyświetla wszystkie zestawy kopii zapasowych, które są tworzone podczas wykonywania ręcznych lub automatycznych kopii zapasowych. Operacja przywracania z zestawu kopii zapasowych powoduje natychmiastowe przesunie wolumin w tryb online, gdy dane są pobierane w tle.

Alternatywną metodą uruchamiania przywracania jest przejść do **urządzenia > [Urządzenie] > woluminów**. W bloku **Woluminy** wybierz wolumin, kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe, a następnie wybierz polecenie **Przywróć**.

## <a name="before-you-restore"></a>Przed przywróceniem

Przed rozpoczęciem przywracania zapoznaj się z następującymi zastrzeżeniami:

* Przed zainicjowaniem operacji przywracania **wolumin należy przetraktować wolumin** w tryb offline — przejmij wolumin w tryb offline zarówno na hoście, jak i na urządzeniu. Mimo że operacja przywracania automatycznie powoduje przesuń wolumin w tryb online na urządzeniu, należy ręcznie przewieźć urządzenie do trybu online na hoście. Wolumin można przewieźć do trybu online na hoście, gdy tylko wolumin zostanie w trybie online na urządzeniu. (Nie trzeba czekać, aż operacja przywracania zostanie zakończona.) Aby uzyskać procedury, przejdź do [ustawienia Przejmuj wolumin w tryb offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Typ woluminu po przywróceniu** — usunięte woluminy są przywracane na podstawie typu w migawce; oznacza to, że woluminy, które zostały przypięte lokalnie są przywracane jako woluminy przypięte lokalnie i woluminy, które zostały warstwowe są przywracane jako woluminy warstwowe.

    W przypadku istniejących woluminów bieżący typ użycia woluminu zastępuje typ, który jest przechowywany w migawce. Na przykład jeśli przywrócisz wolumin z migawki, która została podjęta, gdy typ woluminu był warstwowy, a ten typ woluminu jest teraz przypięty lokalnie (z powodu operacji konwersji, która została wykonana), wolumin zostanie przywrócony jako wolumin przypięty lokalnie. Podobnie jeśli istniejący wolumin przypięty lokalnie został rozszerzony, a następnie przywrócony ze starszej migawki wykonanej, gdy wolumin był mniejszy, przywrócony wolumin zachowa bieżący rozmiar rozwiniętego.

    Nie można przekonwertować woluminu z woluminu warstwowego na wolumin przypięty lokalnie lub z woluminu przypiętego lokalnie na wolumin warstwowy podczas przywracania woluminu. Poczekaj na zakończenie operacji przywracania, a następnie możesz przekonwertować wolumin na inny typ. Aby uzyskać informacje dotyczące konwertowania [woluminu,](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)przejdź do temat Zmień typ woluminu . 

* **Rozmiar woluminu jest odzwierciedlany w przywróconym woluminie** — jest to ważna kwestia w przypadku przywracania woluminu przypiętego lokalnie, który został usunięty (ponieważ woluminy przypięte lokalnie są w pełni aprowizowane). Upewnij się, że masz wystarczająco dużo miejsca przed podjęciem próby przywrócenia lokalnie przypiętego woluminu, który został wcześniej usunięty.

* **Nie można rozszerzyć woluminu podczas jego przywracania** — poczekaj, aż operacja przywracania zostanie zakończona przed próbą rozszerzenia woluminu. Aby uzyskać informacje dotyczące rozszerzania woluminu, przejdź do [tematu Modyfikowanie woluminu](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Kopię zapasową można wykonać podczas przywracania woluminu lokalnego** — w przypadku procedur przejdź do [usługi Menedżer urządzeń StorSimple do zarządzania zasadami tworzenia kopii zapasowych.](storsimple-8000-manage-backup-policies-u2.md)

* **Można anulować operację przywracania** — jeśli anulujesz zadanie przywracania, wolumin zostanie przywrócony do stanu, w który był przed zainicjowaniem operacji przywracania. Aby uzyskać procedury, przejdź do [anuluj zadanie](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Jak działa przywracanie

W przypadku urządzeń z aktualizacją 4 lub nowszą zaimplementowano przywracanie oparte na mapach cieplnych. Gdy host żąda dostępu do danych, dociera do urządzenia, te żądania są śledzone i tworzona jest mapa cieplna. Wysoka szybkość żądania powoduje fragmenty danych o wyższym ogniu, podczas gdy niższa szybkość żądania przekłada się na kawałki o niższym ogniu. Aby uzyskać dostęp do danych, należy uzyskać dostęp co najmniej dwa razy, aby oznaczyć je jako _gorące._ Zmodyfikowany plik jest również oznaczony jako _gorący_. Po zainicjowaniu przywracania następuje proaktywne nawodnienie danych na podstawie mapy cieplnej. W przypadku wersji wcześniejszych niż Aktualizacja 4 dane są pobierane podczas przywracania tylko na podstawie dostępu.

Następujące zastrzeżenia dotyczą przywracania opartego na mapie cieplnej:

* Śledzenie mapy cieplnej jest włączone tylko dla woluminów warstwowych, a woluminy przypięte lokalnie nie są obsługiwane.

* Przywracanie oparte na heatmap nie jest obsługiwane podczas klonowania woluminu na inne urządzenie. 

* Jeśli istnieje przywracanie w miejscu i lokalna migawka dla woluminu do przywrócenia istnieje na urządzeniu, a następnie nie nawadniać (ponieważ dane są już dostępne lokalnie). 

* Domyślnie po przywróceniu są inicjowane zadania nawodnienia, które proaktywnie nawadniają dane na podstawie mapy cieplnej. 

W aktualizacji 4 polecenia cmdlet programu Windows PowerShell mogą służyć do wykonywania zapytań o uruchamianie zadań nawodnienia, anulowania zadania nawodnienia i uzyskania stanu zadania nawadniania.

* `Get-HcsRehydrationJob`- To polecenie cmdlet otrzymuje status zadania nawodnienia. Dla jednego woluminu wyzwalane jest jedno zadanie nawodnienia.

* `Set-HcsRehydrationJob`- To polecenie cmdlet pozwala na wstrzymanie, zatrzymanie, wznowienie pracy nawodnienia, gdy nawodnienie jest w toku.

Aby uzyskać więcej informacji na temat poleceń cmdlet rehydration, przejdź do [odwołania polecenia cmdlet programu Windows PowerShell dla StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

W przypadku automatycznego nawodnienia oczekuje się zazwyczaj wyższej wydajności odczytu przejściowego. Rzeczywista wielkość ulepszeń zależy od różnych czynników, takich jak wzorzec dostępu, zmiany danych i typ danych. 

Aby anulować zadanie nawadniania, można użyć polecenia cmdlet programu PowerShell. Jeśli chcesz trwale wyłączyć zadania nawadniania dla wszystkich przyszłych przywracań, [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>Jak korzystać z katalogu kopii zapasowych

Blok **Wykaz kopii zapasowych** zawiera kwerendę, która pomaga zawęzić wybór zestawu kopii zapasowych. Można filtrować zestawy kopii zapasowych, które są pobierane na podstawie następujących parametrów:

* **Zakres czasu** — zakres daty i godziny podczas tworzenia zestawu kopii zapasowych.
* **Urządzenie** — urządzenie, na którym utworzono zestaw kopii zapasowych.
* **Zasady tworzenia kopii zapasowych** lub **wolumin —** zasady tworzenia kopii zapasowych lub wolumin skojarzony z tym zestawem kopii zapasowych.

Filtrowane zestawy kopii zapasowych są następnie tabelaryczne na podstawie następujących atrybutów:

* **Nazwa** — nazwa zasad tworzenia kopii zapasowej lub woluminu skojarzonego z zestawem kopii zapasowych.
* **Typ** — zestawy kopii zapasowych mogą być migawkami lokalnymi lub migawkami w chmurze. Migawka lokalna to kopia zapasowa wszystkich danych woluminu przechowywanych lokalnie na urządzeniu, podczas gdy migawka w chmurze odnosi się do kopii zapasowej danych woluminów przechowywanych w chmurze. Migawki lokalne zapewniają szybszy dostęp, podczas gdy migawki w chmurze są wybierane dla odporności danych.
* **Rozmiar** — rzeczywisty rozmiar zestawu kopii zapasowych.
* **Utworzony na** — data i godzina utworzenia kopii zapasowych. 
* **Woluminy** — liczba woluminów skojarzonych z zestawem kopii zapasowych.
* **Zainicjowane** — kopie zapasowe mogą być inicjowane automatycznie zgodnie z harmonogramem lub ręcznie przez użytkownika. (Można użyć zasad tworzenia kopii zapasowych, aby zaplanować tworzenie kopii zapasowych. Alternatywnie można użyć opcji **Zrób kopię zapasową,** aby wykonać interaktywną lub na żądanie kopię zapasową).)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Jak przywrócić wolumin StorSimple z kopii zapasowej

Za pomocą bloku **wykaz kopii zapasowej** można przywrócić wolumin StorSimple z określonej kopii zapasowej. Należy jednak pamiętać, że przywracanie woluminu spowoduje przywrócenie woluminu do stanu, w którym znajdował się podczas wykonywania kopii zapasowej. Wszystkie dane dodane po operacji tworzenia kopii zapasowej zostaną utracone.

> [!WARNING]
> Przywracanie z kopii zapasowej zastąpi istniejące woluminy z kopii zapasowej. Może to spowodować utratę wszelkich danych, które zostały zapisane po wykonaniu kopii zapasowej.


### <a name="to-restore-your-volume"></a>Aby przywrócić głośność
1. Przejdź do usługi StorSimple Device Manager, a następnie kliknij pozycję **Katalog kopii zapasowych**.

2. Wybierz zestaw kopii zapasowych w następujący sposób:
   
   1. Określ zakres czasu.
   2. Wybierz odpowiednie urządzenie.
   3. Na liście rozwijanej wybierz zasady woluminu lub kopii zapasowej dla kopii zapasowej, którą chcesz wybrać.
   4. Kliknij przycisk **Zastosuj,** aby wykonać tę kwerendę.

      Kopie zapasowe skojarzone z wybranymi zasadami woluminu lub kopii zapasowej powinny być wyświetlane na liście zestawów kopii zapasowych.
   
      ![Lista zestawów kopii zapasowych](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Rozwiń zestaw kopii zapasowych, aby wyświetlić skojarzone woluminy. Woluminy te muszą zostać przesuń do trybu offline na hoście i urządzeniu, zanim będzie można je przywrócić. Uzyskaj dostęp do woluminów na bloku **Woluminy** urządzenia, a następnie wykonaj kroki opisane w [obszarze Przekszłać wolumin do trybu offline,](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) aby przetraktować je w tryb offline.
   
   > [!IMPORTANT]
   > Przed przełączeniem woluminów do trybu offline na urządzeniu w trybie offline woluminów na hoście należy najpierw przetraktować woluminy w trybie offline. Jeśli woluminy nie przejmą woluminów w tryb offline na hoście, może to potencjalnie doprowadzić do uszkodzenia danych.
   
4. Przejdź wstecz do karty **Wykaz kopii zapasowych** i wybierz zestaw kopii zapasowych. Kliknij prawym przyciskiem myszy, a następnie z menu kontekstowego wybierz polecenie **Przywróć**.

    ![Lista zestawów kopii zapasowych](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Pojawi się monit o potwierdzenie. Przejrzyj informacje o przywracania, a następnie zaznacz pole wyboru potwierdzenia.
   
    ![Strona potwierdzenia](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7. Kliknij **przycisk Przywróć**. Spowoduje to zainicjowanie zadania przywracania, które można wyświetlić, uzyskując dostęp do strony **Zadania.**

   ![Strona potwierdzenia](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. Po zakończeniu przywracania sprawdź, czy zawartość woluminów jest zastępowana woluminami z kopii zapasowej.


## <a name="if-the-restore-fails"></a>Jeśli przywracanie nie powiedzie się

Zostanie wyświetlony alert, jeśli operacja przywracania nie powiedzie się z jakiegokolwiek powodu. W takim przypadku należy odświeżyć listę kopii zapasowych, aby sprawdzić, czy kopia zapasowa jest nadal prawidłowa. Jeśli kopia zapasowa jest prawidłowa i są przywracane z chmury, a następnie problemy z łącznością może być przyczyną problemu.

Aby zakończyć operację przywracania, należy przetraktować wolumin na hoście i ponowić próbę operacji przywracania. Należy zauważyć, że wszelkie modyfikacje danych woluminu, które zostały wykonane podczas procesu przywracania zostaną utracone.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zarządzać woluminami StorSimple](storsimple-8000-manage-volumes-u2.md).
* Dowiedz się, jak [zarządzać urządzeniem StorSimple za pomocą usługi StorSimple Device Manager.](storsimple-8000-manager-service-administration.md)

