---
title: Zmienianie trybu urządzenia StorSimple | Dokumentacja firmy Microsoft
description: W tym artykule opisano tryby urządzenia StorSimple i wyjaśniono, jak zmienianie trybu urządzenia za pomocą programu Windows PowerShell dla usługi StorSimple.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576095"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Zmienianie trybu urządzenia na urządzeniu StorSimple

Ten artykuł zawiera krótkie opisy różnych trybach, w których może działać urządzenia StorSimple. Urządzenie StorSimple może działać w trzech trybów: Normalny, obsługi i odzyskiwania.

Po przeczytaniu tego artykułu, oznacza to:

* Jakie są tryby urządzenia StorSimple są
* Jak ustalić, który tryb urządzenia StorSimple jest w
* Jak zmienić w normalnym trybie konserwacji i *na odwrót*

Powyższe zadań zarządzania można wykonać tylko za pośrednictwem interfejsu programu Windows PowerShell urządzenia StorSimple.

## <a name="about-storsimple-device-modes"></a>Informacje o trybach urządzenia StorSimple

Urządzenie StorSimple może działać w trybie normalnym, konserwacji lub odzyskiwania. Obu tych trybach krótko opisano poniżej.

### <a name="normal-mode"></a>Tryb normalny

To jest zdefiniowany jako normalny tryb operacyjny dla urządzenia StorSimple w pełni skonfigurowany. Domyślnie urządzenia powinny być w trybie normalnym.

### <a name="maintenance-mode"></a>Tryb konserwacji

Czasami urządzenia StorSimple może być konieczne umieszczenie w trybie konserwacji. Ten tryb pozwala wykonać konserwacji na urządzeniu i zainstalować aktualizacje wymagają zatrzymania pracy, takich jak te dotyczą oprogramowania układowego dysku.

System można umieścić w tryb konserwacji tylko przy użyciu programu Windows PowerShell dla usługi StorSimple. Wszystkie żądania We/Wy są wstrzymane w tym trybie. Usługi, takie jak trwałej pamięci (NVRAM) lub usługę klastrowania również zostaną zatrzymane. Oba kontrolery zostaną ponownie uruchomione po wprowadzeniu lub opuścić ten tryb. Po wyjściu z trybu konserwacji, wszystkich usług zostanie wznowiona i powinna być w dobrej kondycji. Może to potrwać kilka minut.

> [!NOTE]
> **Tryb konserwacji jest obsługiwana tylko na urządzeniu działa prawidłowo. Nie jest obsługiwana na urządzeniu, w którym co najmniej jeden z kontrolerów nie działają.**


### <a name="recovery-mode"></a>Tryb odzyskiwania

Tryb odzyskiwania można określić jako "Windows"Tryb awaryjny z obsługą sieci. Tryb odzyskiwania angażuje zespołu Microsoft Support i umożliwia ich wykonywania Diagnostyka w systemie. Podstawowym celem tryb odzyskiwania jest pobrać dzienniki systemu.

Jeśli system przechodzi w tryb odzyskiwania, możesz skontaktować się Microsoft Support dalsze czynności. Aby uzyskać więcej informacji, przejdź do [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

> [!NOTE]
> **Nie można umieścić urządzenia w trybie odzyskiwania. Jeśli urządzenie jest w nieprawidłowym stanie, tryb odzyskiwania próbuje pobrać urządzenia do stanu, w którym personel firmy Microsoft Support było go sprawdzić.**

## <a name="determine-storsimple-device-mode"></a>Określanie trybu urządzenia StorSimple

#### <a name="to-determine-the-current-device-mode"></a>Aby określić bieżący tryb urządzenia

1. Zaloguj się do konsoli szeregowej urządzenia, wykonując kroki opisane w [przy użyciu programu PuTTY, aby nawiązać połączenie z konsolą szeregową urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Spójrz na komunikat transparentu w przedstawionym menu konsoli szeregowej urządzenia. Jawnie ten komunikat wskazuje, czy urządzenie jest w trybie konserwacji lub odzyskiwania. Jeśli komunikat nie zawiera żadnych konkretnych informacji o odnoszących się do trybu systemu, urządzenie jest w trybie normalnym.

## <a name="change-the-storsimple-device-mode"></a>Zmienianie trybu urządzenia StorSimple

Do przeprowadzenia konserwacji lub zainstaluj aktualizacje trybu konserwacji, można umieścić urządzenia StorSimple w tryb konserwacji (w trybie normalnym). Wykonaj poniższe procedury, aby wprowadzić lub wyjściu z trybu konserwacji.

> [!IMPORTANT]
> Przed wprowadzeniem trybu konserwacji, sprawdź, czy oba kontrolery urządzeń są w dobrej kondycji, uzyskując dostęp do **ustawienia urządzenia > kondycja sprzętu** dla urządzenia w witrynie Azure portal. Jeśli jeden lub oba kontrolery nie są w dobrej kondycji, skontaktuj się z Microsoft Support następnych kroków. Aby uzyskać więcej informacji, przejdź do [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).
 

#### <a name="to-enter-maintenance-mode"></a>Aby przejść do trybu konserwacji

1. Zaloguj się do konsoli szeregowej urządzenia, wykonując kroki opisane w [przy użyciu programu PuTTY, aby nawiązać połączenie z konsolą szeregową urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**. Po wyświetleniu monitu podaj **hasło administratora urządzenia**. Domyślne hasło jest: `Password1`.
3. W wierszu polecenia wpisz polecenie 
   
    `Enter-HcsMaintenanceMode`
4. Zostanie wyświetlony komunikat ostrzegawczy informujący o trybu konserwacji będzie zakłócać wszystkich żądań We/Wy i sever połączenia do witryny Azure portal i zostanie wyświetlony monit o potwierdzenie. Typ **Y** wejścia w tryb konserwacji.
5. Powoduje ponowne uruchomienie obu kontrolerów. Po zakończeniu ponownego uruchomienia transparent konsoli szeregowej wskazuje, czy urządzenie jest w trybie konserwacji. Poniżej pokazano przykładowe dane wyjściowe.

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

1. Zaloguj się do konsoli szeregowej urządzenia. Sprawdź w komunikacie transparentu, że urządzenie jest w trybie konserwacji.
2. W wierszu polecenia wpisz polecenie:
   
    `Exit-HcsMaintenanceMode`
3. Pojawi się komunikat ostrzegawczy i komunikat z potwierdzeniem. Typ **Y** aby wyjść z trybu konserwacji.
4. Powoduje ponowne uruchomienie obu kontrolerów. Po zakończeniu ponownego uruchomienia transparent konsoli szeregowej wskazuje, czy urządzenie jest w trybie normalnym. Poniżej pokazano przykładowe dane wyjściowe.

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

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [zastosować aktualizacje trybu normalnego i konserwacja](storsimple-update-device.md) na urządzeniu StorSimple.

