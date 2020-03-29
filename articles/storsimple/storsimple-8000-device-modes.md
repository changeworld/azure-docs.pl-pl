---
title: Zmień tryb urządzenia StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano tryby urządzenia StorSimple i wyjaśniono, jak zmienić tryb urządzenia za pomocą programu Windows PowerShell for StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: e55964beff48df6ce24d99c01975d39b662f1612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60576095"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Zmienianie trybu urządzenia na urządzeniu StorSimple

Ten artykuł zawiera krótki opis różnych trybów, w których urządzenie StorSimple może działać. Urządzenie StorSimple może działać w trzech trybach: normalnym, konserwacji i odzyskiwania.

Po przeczytaniu tego artykułu, będziesz wiedzieć:

* Czym są tryby urządzenia StorSimple
* Jak ustalić, w którym trybie znajduje się urządzenie StorSimple
* Jak zmienić tryb normalny na tryb konserwacji i *odwrotnie*

Powyższe zadania zarządzania można wykonywać tylko za pośrednictwem interfejsu programu Windows PowerShell urządzenia StorSimple.

## <a name="about-storsimple-device-modes"></a>Informacje o trybach urządzenia StorSimple

Urządzenie StorSimple może działać w trybie normalnym, konserwacyjnym lub odzyskiwania. Każdy z tych trybów jest krótko opisany poniżej.

### <a name="normal-mode"></a>Tryb normalny

Jest to zdefiniowane jako normalny tryb pracy dla w pełni skonfigurowanego urządzenia StorSimple. Domyślnie urządzenie powinno być w trybie normalnym.

### <a name="maintenance-mode"></a>Tryb konserwacji

Czasami urządzenie StorSimple może wymagać przełączenia w tryb konserwacji. Ten tryb umożliwia przeprowadzenie konserwacji na urządzeniu i zainstalowanie przełomowych aktualizacji, takich jak te związane z oprogramowaniem układowym dysku.

System można przełożyć w tryb konserwacji tylko za pośrednictwem programu Windows PowerShell for StorSimple. Wszystkie żądania we/wy są wstrzymane w tym trybie. Usługi, takie jak nieulotna pamięć dostępu losowego (NVRAM) lub usługa klastrowania, również są zatrzymywane. Oba kontrolery są ponownie uruchamiane po wejściu lub wyjściu z tego trybu. Po wyjściu z trybu konserwacji wszystkie usługi zostaną wznowione i powinny być w dobrej kondycji. Może to potrwać kilka minut.

> [!NOTE]
> **Tryb konserwacji jest obsługiwany tylko na prawidłowo działającym urządzeniu. Nie jest obsługiwany na urządzeniu, w którym jeden lub oba kontrolery nie działają.**


### <a name="recovery-mode"></a>Tryb odzyskiwania

Tryb odzyskiwania można opisać jako "Tryb awaryjny systemu Windows z obsługą sieci". Tryb odzyskiwania włącza zespół pomocy technicznej firmy Microsoft i umożliwia im wykonywanie diagnostyki w systemie. Głównym celem trybu odzyskiwania jest pobranie dzienników systemowych.

Jeśli system przejdzie w tryb odzyskiwania, należy skontaktować się z pomocą techniczną firmy Microsoft, aby uzyskać następujące kroki. Aby uzyskać więcej informacji, przejdź do [strony Skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

> [!NOTE]
> **Nie można umieścić urządzenia w trybie odzyskiwania. Jeśli urządzenie jest w złym stanie, tryb odzyskiwania próbuje uzyskać urządzenie w stanie, w którym pracownicy pomocy technicznej firmy Microsoft mogą go zbadać.**

## <a name="determine-storsimple-device-mode"></a>Określanie trybu urządzenia StorSimple

#### <a name="to-determine-the-current-device-mode"></a>Aby określić bieżący tryb urządzenia

1. Zaloguj się do konsoli szeregowej urządzenia, wykonując czynności opisane w [aplikacji Użyj putty, aby połączyć się z konsolą szeregową urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Spójrz na komunikat banera w menu konsoli szeregowej urządzenia. Ten komunikat jawnie wskazuje, czy urządzenie jest w trybie konserwacji lub odzyskiwania. Jeśli wiadomość nie zawiera żadnych konkretnych informacji dotyczących trybu systemowego, urządzenie jest w trybie normalnym.

## <a name="change-the-storsimple-device-mode"></a>Zmienianie trybu urządzenia StorSimple

Urządzenie StorSimple można umieścić w trybie konserwacji (z trybu normalnego), aby wykonać konserwację lub zainstalować aktualizacje trybu konserwacji. Wykonaj następujące procedury, aby przejść lub zakończyć tryb konserwacji.

> [!IMPORTANT]
> Przed wejściem w tryb konserwacji sprawdź, czy oba kontrolery urządzeń są w dobrej kondycji, uzyskując dostęp do **ustawień urządzenia > kondycję sprzętu** dla urządzenia w witrynie Azure portal. Jeśli jeden lub oba kontrolery nie są w dobrej kondycji, skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać następujące kroki. Aby uzyskać więcej informacji, przejdź do [strony Skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).
 

#### <a name="to-enter-maintenance-mode"></a>Aby przejść do trybu konserwacji

1. Zaloguj się do konsoli szeregowej urządzenia, wykonując czynności opisane w [aplikacji Użyj putty, aby połączyć się z konsolą szeregową urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. W menu konsoli szeregowej wybierz opcję 1, **zaloguj się z pełnym dostępem**. Po wyświetleniu monitu podaj **hasło administratora urządzenia**. Domyślne hasło `Password1`to: .
3. W wierszu polecenia wpisz 
   
    `Enter-HcsMaintenanceMode`
4. Zostanie wyświetlony komunikat ostrzegawczy informujący, że tryb konserwacji zakłóci wszystkie żądania we/wy i oderwie połączenie z portalem Azure, a zostanie wyświetlony monit o potwierdzenie. Wpisz **Y,** aby przejść do trybu konserwacji.
5. Oba kontrolery zostaną ponownie uruchomione. Po zakończeniu ponownego uruchamiania baner konsoli szeregowej wskazuje, że urządzenie jest w trybie konserwacji. Poniżej pokazano przykładowe dane wyjściowe.

```
    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Controller0>Enter-HcsMaintenanceMode
    Checking device state...

    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>

```

#### <a name="to-exit-maintenance-mode"></a>Aby wyjść z trybu konserwacji

1. Zaloguj się do konsoli szeregowej urządzenia. Sprawdź na podstawie komunikatu banera, czy urządzenie jest w trybie konserwacji.
2. W wierszu polecenia wpisz polecenie:
   
    `Exit-HcsMaintenanceMode`
3. Pojawi się komunikat ostrzegawczy i komunikat potwierdzający. Wpisz **Y,** aby wyjść z trybu konserwacji.
4. Oba kontrolery zostaną ponownie uruchomione. Po zakończeniu ponownego uruchamiania baner konsoli szeregowej wskazuje, że urządzenie jest w trybie normalnym. Poniżej pokazano przykładowe dane wyjściowe.

```
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0
    ---------------------------------------------------------------

    Controller0>Exit-HcsMaintenanceMode
    Checking device state...

    Before exiting maintenance mode, ensure that any updates that are required on both controllers have been applied. Failure to install on each controller could result in data corruption. Exiting maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to exit maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Active
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [zastosować aktualizacje trybu normalnego i konserwacyjnego](storsimple-update-device.md) na urządzeniu StorSimple.

