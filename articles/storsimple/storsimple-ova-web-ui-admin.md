---
title: StorSimple Virtual Array web administracji interfejsu użytkownika | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób wykonywania podstawowych zadań administracyjnych urządzeń za pośrednictwem interfejsu użytkownika sieci Web StorSimple Virtual Array.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254731"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Administrowanie tablicą wirtualną StorSimple za pomocą interfejsu użytkownika sieci Web
![przepływ procesu instalacji](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Omówienie
Samouczki w tym artykule dotyczą tablicy wirtualnej Microsoft Azure StorSimple (znanej również jako lokalne urządzenie wirtualne StorSimple) z uruchomioną wersją ogólnej dostępności (GA) z marca 2016 r. W tym artykule opisano niektóre złożone przepływy pracy i zadania zarządzania, które można wykonać w tablicy wirtualnej StorSimple. Można zarządzać StorSimple Virtual Array przy użyciu interfejsu użytkownika usługi StorSimple Manager (dalej interfejsu użytkownika portalu) i lokalnego interfejsu użytkownika sieci web dla urządzenia. W tym artykule skupiono się na zadaniach, które można wykonywać przy użyciu interfejsu użytkownika sieci Web.

W tym artykule zawarto następujące samouczki:

* Pobierz klucz szyfrowania danych usługi
* Rozwiązywanie problemów z błędami konfiguracji interfejsu użytkownika sieci Web
* Generowanie pakietu dziennika
* Zamykanie lub ponowne uruchamianie urządzenia

## <a name="get-the-service-data-encryption-key"></a>Pobierz klucz szyfrowania danych usługi
Klucz szyfrowania danych usługi jest generowany podczas rejestrowania pierwszego urządzenia w usłudze StorSimple Manager. Ten klucz jest następnie wymagany przy przy tym z kluczem rejestracji usługi, aby zarejestrować dodatkowe urządzenia w usłudze StorSimple Manager.

Jeśli zgubiłeś klucz szyfrowania danych usługi i musisz go pobrać, wykonaj następujące kroki w lokalnym interfejsie użytkownika sieci Web urządzenia zarejestrowanego w usłudze.

#### <a name="to-get-the-service-data-encryption-key"></a>Aby uzyskać klucz szyfrowania danych usługi
1. Połącz się z lokalnym interfejsem użytkownika sieci Web. Przejdź do **ustawień chmury konfiguracji** > **Cloud Settings**.
2. U dołu strony kliknij pozycję **Pobierz klucz szyfrowania danych usługi**. Pojawi się klucz. Skopiuj i zapisz ten klucz.
   
    ![pobierz klucz szyfrowania danych usługi 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Rozwiązywanie problemów z błędami konfiguracji interfejsu użytkownika sieci Web
W niektórych przypadkach podczas konfigurowania urządzenia za pośrednictwem lokalnego interfejsu użytkownika sieci web mogą wystąpić błędy. Aby zdiagnozować i rozwiązać takie błędy, można uruchomić testy diagnostyczne.

#### <a name="to-run-the-diagnostic-tests"></a>Aby uruchomić testy diagnostyczne
1. W lokalnym interfejsie użytkownika sieci Web przejdź do strony **Rozwiązywanie problemów** > **z testami diagnostycznymi**.
   
    ![uruchamianie diagnostyki 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. U dołu strony kliknij pozycję **Uruchom testy diagnostyczne**. Spowoduje to zainicjowanie testów w celu zdiagnozowania ewentualnych problemów z ustawieniami sieci, urządzenia, serwera proxy, czasu lub chmury. Zostaniesz powiadomiony, że urządzenie jest uruchomione testy.
3. Po zakończeniu testów zostaną wyświetlone wyniki. W poniższym przykładzie przedstawiono wyniki testów diagnostycznych. Należy zauważyć, że ustawienia serwera proxy sieci web nie zostały skonfigurowane na tym urządzeniu i dlatego test serwera proxy sieci web nie został uruchomiony. Wszystkie inne testy ustawień sieciowych, serwera DNS i ustawień czasu zostały pomyślnie przeprowadzone.
   
    ![uruchamianie diagnostyki 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Generowanie pakietu dziennika
Pakiet dziennika składa się ze wszystkich odpowiednich dzienników, które mogą pomóc pomocy technicznej firmy Microsoft w rozwiązywaniu problemów z urządzeniem. W tej wersji pakiet dziennika mogą być generowane za pośrednictwem lokalnego interfejsu użytkownika sieci web.

#### <a name="to-generate-the-log-package"></a>Aby wygenerować pakiet dziennika
1. W lokalnym interfejsie użytkownika sieci Web przejdź do witryny **Rozwiązywanie problemów** > **z dziennikami systemu**.
   
    ![generowanie pakietu dziennika 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. U dołu strony kliknij pozycję **Utwórz pakiet dziennika**. Zostanie utworzony pakiet dzienników systemowych. Zajmie to kilka minut.
   
    ![generowanie pakietu dziennika 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Zostaniesz powiadomiony po pomyślnym utworzeniu pakietu, a strona zostanie zaktualizowana, aby wskazać godzinę i datę utworzenia pakietu.
   
    ![generowanie pakietu dziennika 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Kliknij **pozycję Pobierz pakiet dziennika**. Spakowany pakiet zostanie pobrany z twojego systemu.
   
    ![generowanie pakietu dziennika 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Możesz rozpakować pobrany pakiet dziennika i wyświetlić pliki dziennika systemowego.

## <a name="shut-down-and-restart-your-device"></a>Wyłącz i uruchom ponownie urządzenie
Można wyłączyć lub ponownie uruchomić urządzenie wirtualne przy użyciu lokalnego interfejsu użytkownika sieci Web. Zaleca się, aby przed ponownym uruchomieniem, należy wziąć woluminy lub udziały w trybie offline na hoście, a następnie urządzenia. Zminimalizuje to możliwość uszkodzenia danych. 

#### <a name="to-shut-down-your-virtual-device"></a>Aby wyłączyć urządzenie wirtualne
1. W lokalnym interfejsie użytkownika sieci Web przejdź do**ustawień zasilania** **konserwacji** > .
2. U dołu strony kliknij przycisk **Zamknij**.
   
    ![wyłączenie urządzenia 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Pojawi się ostrzeżenie informujące, że wyłączenie urządzenia spowoduje przerwanie wszystkich we/wy, które były w toku, co spowoduje przestój. Kliknij ikonę znacznika wyboru, ![ikona znacznika wyboru](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![ostrzeżenie o wyłączeniu urządzenia](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Zostaniesz powiadomiony, że zamknięcie zostało zainicjowane.
   
    ![rozpoczęto zamykanie urządzenia](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Urządzenie zostanie teraz wyłączone. Jeśli chcesz uruchomić urządzenie, musisz to zrobić za pośrednictwem Menedżera funkcji Hyper-V.

#### <a name="to-restart-your-virtual-device"></a>Aby ponownie uruchomić urządzenie wirtualne
1. W lokalnym interfejsie użytkownika sieci Web przejdź do**ustawień zasilania** **konserwacji** > .
2. U dołu strony kliknij pozycję **Uruchom ponownie**.
   
    ![ponowne uruchomienie urządzenia](./media/storsimple-ova-web-ui-admin/image36.png)
3. Pojawi się ostrzeżenie informujące, że ponowne uruchomienie urządzenia spowoduje przerwanie wszystkich uruchomionych we/wy, co spowoduje przestój. Kliknij ikonę znacznika wyboru, ![ikona znacznika wyboru](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![ostrzeżenie o ponownym uruchomieniu](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Zostaniesz powiadomiony, że uruchomiono ponowne uruchomienie.
   
    ![zainicjowane ponowne uruchomienie](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Podczas ponownego uruchamiania jest w toku, utracisz połączenie z interfejsem użytkownika. Można monitorować ponowne uruchomienie, okresowo odświeżając interfejs użytkownika. Alternatywnie można monitorować stan ponownego uruchomienia urządzenia za pośrednictwem Menedżera funkcji Hyper-V.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [zarządzać urządzeniem za pomocą usługi StorSimple Manager.](storsimple-virtual-array-manager-service-administration.md)

