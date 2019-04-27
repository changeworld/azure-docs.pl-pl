---
title: Rozwiązania StorSimple Virtual Array sieci web interfejsu użytkownika administracji | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób wykonywania zadań administracyjnych urządzenia podstawowe za pośrednictwem interfejsu użytkownika sieci web macierzy wirtualnej StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 92671206a4171ca838423f55b526191ef30e5c35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630464"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Administrowanie rozwiązania StorSimple Virtual Array przy użyciu interfejsu użytkownika sieci Web
![przepływ procesu instalacji](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Omówienie
Samouczki, w tym artykule mają zastosowanie do programu Microsoft Azure StorSimple Virtual Array (znany także jako lokalne urządzenie wirtualne StorSimple) wersji ogólnodostępnej (GA) marca 2016 r. W tym artykule opisano niektóre złożonych przepływów pracy i zadania zarządzania, które mogą być wykonywane w ramach macierzy wirtualnej StorSimple. Można zarządzać macierzy wirtualnej StorSimple przy użyciu usługi StorSimple Manager service (nazywane interfejsu użytkownika portalu) interfejsu użytkownika i lokalnego internetowego interfejsu użytkownika dla tego urządzenia. Ten artykuł koncentruje się na zadaniach, że można wykonać przy użyciu interfejsu użytkownika sieci web.

W tym artykule zawarto następujące samouczki:

* Pobierz klucz szyfrowania danych usługi
* Rozwiązywanie problemów z błędami ustawień interfejsu użytkownika sieci web
* Wygeneruj pakiet dziennika
* Zamykanie lub ponowne uruchamianie urządzenia

## <a name="get-the-service-data-encryption-key"></a>Pobierz klucz szyfrowania danych usługi
Klucz szyfrowania danych usługi jest generowany podczas rejestrowania pierwszego urządzenia z usługą StorSimple Manager. Ten klucz będzie wymagany przy użyciu klucza rejestracji usługi w celu rejestracji dodatkowych urządzeń w usłudze StorSimple Manager.

Jeśli zostały Zagubione Twojego klucza szyfrowania danych usługi i trzeba go pobrać, wykonaj następujące kroki w lokalnym internetowym interfejsie użytkownika, urządzenia zarejestrowane w usłudze.

#### <a name="to-get-the-service-data-encryption-key"></a>Aby uzyskać klucz szyfrowania danych usługi
1. Nawiązać połączenia lokalnego Interfejsu w przeglądarce. Przejdź do **konfiguracji** > **ustawienia chmury**.
2. W dolnej części strony kliknij **Pobierz klucz szyfrowania danych usługi**. Zostanie wyświetlony klucz. Skopiuj i Zapisz ten klucz.
   
    ![Pobierz klucz szyfrowania danych usługi 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Rozwiązywanie problemów z błędami ustawień interfejsu użytkownika sieci web
W niektórych przypadkach podczas konfigurowania urządzenia za pomocą lokalnego internetowego interfejsu użytkownika, możesz napotkać błędy. Aby zdiagnozować i rozwiązać błędy takie, można uruchomić testy diagnostyczne.

#### <a name="to-run-the-diagnostic-tests"></a>Aby uruchomić testy diagnostyczne
1. W lokalnym internetowym interfejsie użytkownika, przejdź do **Rozwiązywanie problemów** > **badania diagnostyczne**.
   
    ![Uruchom diagnostykę 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. W dolnej części strony kliknij **Uruchom testy diagnostyczne**. Spowoduje to zainicjowanie testów w celu zdiagnozowania możliwych problemów z Twojej sieci, urządzenia, serwera proxy sieci web, czasu lub ustawienia chmury. Powiadomimy Cię, czy urządzenie ma zainstalowany testów.
3. Po zakończeniu testów, będą wyświetlane wyniki. Poniższy przykład pokazuje wyniki testów diagnostycznych. Należy pamiętać, że ustawienia serwera proxy sieci web nie zostały skonfigurowane na tym urządzeniu i w związku z tym, test serwera proxy sieci web nie zostało uruchomione. Wszystkie testy dla ustawień sieci, serwer DNS i ustawień czasu zakończyły się pomyślnie.
   
    ![Uruchom diagnostykę 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Wygeneruj pakiet dziennika
Pakiet dziennika składa się z wszystkie odpowiednie dzienniki, które mogą pomóc Support firmy Microsoft w rozwiązywaniu wszelkich problemów z urządzeniami. W tej wersji pakietu dziennika można wygenerować za pomocą lokalnego Interfejsu w przeglądarce.

#### <a name="to-generate-the-log-package"></a>Aby wygenerować pakiet dziennika
1. W lokalnym internetowym interfejsie użytkownika, przejdź do **Rozwiązywanie problemów** > **dzienniki systemu**.
   
    ![Generowanie dziennika pakietu 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. W dolnej części strony kliknij **Tworzenie dziennika pakietu**. Zostanie utworzony pakiet dzienniki systemu. To może potrwać kilka minut.
   
    ![Generowanie dziennika pakietu 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Otrzymasz powiadomienie po pomyślnym utworzeniu pakietu, a strona zostanie zaktualizowana w celu wskazania Data i godzina utworzenia pakietu.
   
    ![Generowanie dziennika pakietu 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Kliknij przycisk **pakiet do pobrania dziennika**. Pakiet skompresowany zostaną pobrane w Twoim systemie.
   
    ![Generowanie dziennika pakietu 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Można Rozpakuj pakiet dziennika pobrane i wyświetlać w plikach dziennika systemowego.

## <a name="shut-down-and-restart-your-device"></a>Wyłączenie i ponowne uruchomienie urządzenia
Można zamknąć lub ponownie uruchomić urządzenie wirtualne przy użyciu lokalnego Interfejsu w przeglądarce. Firma Microsoft zaleca się prowadzące przed ponownym uruchomieniem, woluminy i udziały w tryb offline na hoście, a następnie urządzenia. Pozwoli to zminimalizować możliwości uszkodzenie danych. 

#### <a name="to-shut-down-your-virtual-device"></a>Aby wyłączyć urządzenie wirtualne
1. W lokalnym internetowym interfejsie użytkownika, przejdź do **konserwacji** > **ustawienia zasilania**.
2. W dolnej części strony kliknij **zamknięcia**.
   
    ![zamknięcie urządzenia 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Zostanie wyświetlone ostrzeżenie, stwierdzający, że wyłączenie urządzenia spowoduje przerwanie wszelkich operacji wejścia/wyjścia w toku, co spowoduje Przestój. Kliknij ikonę znacznika wyboru ![ikona znacznika wyboru](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![Ostrzeżenie zamknięcie urządzenia](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Użytkownik będzie powiadamiany, zainicjowano zamknięcia systemu.
   
    ![zamknięcie urządzenia pracę](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Urządzenie zostanie teraz zamknięty. Jeśli chcesz uruchomić urządzenie, należy to zrobić za pomocą Menedżera funkcji Hyper-V.

#### <a name="to-restart-your-virtual-device"></a>Aby ponownie uruchomić urządzenie wirtualne
1. W lokalnym internetowym interfejsie użytkownika, przejdź do **konserwacji** > **ustawienia zasilania**.
2. W dolnej części strony kliknij **ponowne uruchomienie**.
   
    ![ponowne uruchomienie urządzenia](./media/storsimple-ova-web-ui-admin/image36.png)
3. Zostanie wyświetlone ostrzeżenie, stwierdzający, że ponowne uruchomienie urządzenia spowoduje przerwanie wszelkich dla systemu IOs, które były w toku, co spowoduje Przestój. Kliknij ikonę znacznika wyboru ![ikona znacznika wyboru](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![Ostrzeżenie o ponownym uruchomieniu](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Powiadomimy Cię, że zainicjowano ponowne uruchomienie.
   
    ![Rozpoczęto ponowne uruchamianie](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Ponowne uruchomienie jest w toku, spowoduje utratę połączenia w interfejsie użytkownika. Aby monitorować ponowne uruchomienie, okresowo odświeżanie interfejsu użytkownika. Alternatywnie można monitorować stan ponownego uruchomienia urządzenia za pomocą Menedżera funkcji Hyper-V.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [korzystać z usługi StorSimple Manager do zarządzania urządzeniem](storsimple-virtual-array-manager-service-administration.md).

