---
title: Tablica wirtualnego StorSimple sieci web administracji interfejsu użytkownika | Dokumentacja firmy Microsoft
description: Opisuje sposób wykonywania zadań administracyjnych urządzenia podstawowe za pomocą interfejsu użytkownika sieci web tablicy wirtualne StorSimple.
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
ms.openlocfilehash: 989e7b697f9b527df549fb32be18edd1d3c8d224
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875886"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Użyj interfejsu użytkownika sieci Web do administrowania tablica wirtualnego StorSimple
![przepływ procesu instalacji](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Omówienie
Samouczki, w tym artykule dotyczą Microsoft Azure StorSimple wirtualnego tablicy (znanej także jako lokalnej urządzenia wirtualnego StorSimple) z wersji ogólnodostępnej (GA) marca 2016 r. W tym artykule opisano niektóre złożonych przepływów pracy i zadania zarządzania, które można wykonywać w tablicy wirtualne StorSimple. Można zarządzać za pomocą Menedżera StorSimple tablicy wirtualnego StorSimple usługi lokalnej sieci web interfejsu użytkownika urządzenia i interfejsu użytkownika (określanych jako interfejsu użytkownika portalu). Ten artykuł skupia się na zadaniach, czy można wykonać przy użyciu interfejsu użytkownika sieci web.

Ten artykuł zawiera następujące samouczki:

* Pobierz klucz szyfrowania danych usługi
* Rozwiąż problemy z błędami ustawień interfejsu użytkownika sieci web
* Generowanie pakietu dziennika
* Zamknięcie lub ponowne uruchomienie urządzenia

## <a name="get-the-service-data-encryption-key"></a>Pobierz klucz szyfrowania danych usługi
Klucz szyfrowania danych usługi jest generowany podczas rejestrowania pierwszego urządzenia w usłudze Menedżer StorSimple. Ten klucz jest wymagany przy użyciu klucza rejestracji usługi można zarejestrować dodatkowych urządzeń w usłudze Menedżer StorSimple.

Jeśli ma zgubiony Twojego klucza szyfrowania danych usługi i należy go pobrać, wykonaj poniższe kroki w lokalnej sieci web interfejsu użytkownika, urządzenia zarejestrowane przy użyciu usługi.

#### <a name="to-get-the-service-data-encryption-key"></a>Aby uzyskać klucz szyfrowania danych usługi
1. Podłącz do lokalnego interfejsu użytkownika sieci web. Przejdź do **konfiguracji** > **ustawienia funkcji Cloud**.
2. W dolnej części strony kliknij **klucza szyfrowania danych usługi Get**. Zostanie wyświetlony klucz. Skopiuj i Zapisz ten klucz.
   
    ![Pobierz klucz szyfrowania danych usługi 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Rozwiąż problemy z błędami ustawień interfejsu użytkownika sieci web
W niektórych przypadkach po skonfigurowaniu urządzenia przy użyciu lokalnej sieci web interfejsu użytkownika, możesz napotkać błędy. Aby zdiagnozować i rozwiązać błędy takie, można uruchomić testów diagnostycznych.

#### <a name="to-run-the-diagnostic-tests"></a>Do uruchomienia testów diagnostycznych
1. W lokalnej sieci web interfejsu użytkownika, przejdź do **Rozwiązywanie problemów** > **testów diagnostycznych**.
   
    ![Uruchom funkcję diagnostyki 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. W dolnej części strony kliknij **uruchamiania testów diagnostycznych**. Spowoduje to zainicjowanie testy w celu zdiagnozowania możliwych problemy z Twojej sieci, urządzenia, serwer proxy sieci web, czas lub ustawienia chmury. Użytkownik zostanie poinformowany, czy urządzenie działa testy.
3. Po zakończeniu testów, wyniki będą wyświetlane. W poniższym przykładzie przedstawiono wyniki testów diagnostycznych. Należy pamiętać, że ustawienia serwera proxy sieci web nie zostały skonfigurowane na tym urządzeniu i w związku z tym testu serwera proxy sieci web nie została uruchomiona. Wszystkie testy dla ustawień sieci, serwer DNS i ustawienia czasu zostały zakończone pomyślnie.
   
    ![Uruchom funkcję diagnostyki 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Generowanie pakietu dziennika
Pakiet dziennika składa się z wszystkich odpowiednich dzienniki, które może pomóc Microsoft Support żadnych rozwiązywania problemów z urządzeniami. W tej wersji pakietu dziennika można wygenerować za pomocą lokalnego interfejsu użytkownika sieci web.

#### <a name="to-generate-the-log-package"></a>Aby wygenerować pakietu dziennika
1. W lokalnej sieci web interfejsu użytkownika, przejdź do **Rozwiązywanie problemów** > **dzienniki systemu**.
   
    ![Generowanie pakietu dziennika 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. W dolnej części strony kliknij **Utwórz pakiet dziennika**. Zostanie utworzony pakiet dzienniki systemu. To może potrwać kilka minut.
   
    ![Generowanie pakietu dziennika 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Po pomyślnym utworzeniu pakietu, a strona zostanie zaktualizowana do wskazuje godzinę i datę utworzenia pakietu, otrzymasz powiadomienie.
   
    ![Generowanie pakietu dziennika 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Kliknij przycisk **pakiet dziennika**. Pakiet ZIP zostanie pobrana w tym systemie.
   
    ![Generowanie pakietu dziennika 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Można Rozpakuj pakiet pobrany dziennika i wyświetlać w plikach dziennika systemowego.

## <a name="shut-down-and-restart-your-device"></a>Zamykanie i ponowne uruchomienie urządzenia
Możesz zamknąć lub ponownie uruchomić urządzenie wirtualne za pomocą lokalnego interfejsu użytkownika sieci web. Firma Microsoft zalecamy, aby przed ponownym uruchomieniem, przełączyć woluminy lub udziały w tryb offline na hoście, a następnie urządzenia. Pozwoli to zminimalizować możliwości uszkodzenie danych. 

#### <a name="to-shut-down-your-virtual-device"></a>Aby wyłączyć urządzenie wirtualne
1. W lokalnej sieci web interfejsu użytkownika, przejdź do **konserwacji** > **ustawienia zasilania**.
2. W dolnej części strony kliknij **zamknięcia**.
   
    ![zamknięcie urządzenia 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Zostanie wyświetlone ostrzeżenie, informujący, że wyłączenie urządzenia spowoduje przerwanie wszelkich we/wy, które były w toku, co spowoduje Przestój. Kliknij ikonę znacznika wyboru ![ikona znacznika wyboru](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![Ostrzeżenie zamknięcia urządzenia](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Otrzymasz powiadomienie zainicjowano zamknięcia systemu.
   
    ![Rozpoczęto zamknięcia urządzenia](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Urządzenie zostanie teraz zamknięty. Jeśli chcesz zacząć od urządzenia, należy to zrobić za pomocą Menedżera funkcji Hyper-V.

#### <a name="to-restart-your-virtual-device"></a>Aby ponownie uruchomić urządzenie wirtualne
1. W lokalnej sieci web interfejsu użytkownika, przejdź do **konserwacji** > **ustawienia zasilania**.
2. W dolnej części strony kliknij **ponownego uruchomienia**.
   
    ![ponowne uruchomienie urządzenia](./media/storsimple-ova-web-ui-admin/image36.png)
3. Zostanie wyświetlone ostrzeżenie, informujący, że ponowne uruchomienie urządzenia spowoduje przerwanie wszelkich IOs, które były w toku, co spowoduje Przestój. Kliknij ikonę znacznika wyboru ![ikona znacznika wyboru](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![Ostrzeżenie o ponownym uruchomieniu](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Otrzymasz powiadomienie zainicjowano ponowne uruchomienie.
   
    ![zainicjowano ponowne uruchomienie](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Gdy trwa ponowne uruchomienie, utracisz połączenia do interfejsu użytkownika. Ponowne uruchomienie można monitorować, odświeżając okresowo interfejsu użytkownika. Alternatywnie można monitorować stan ponowne uruchomienie urządzenia za pomocą Menedżera funkcji Hyper-V.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [korzystania z usługi Menedżer StorSimple do zarządzania urządzeniem](storsimple-virtual-array-manager-service-administration.md).

