---
title: Zmień tryb urządzenia StorSimple | Dokumentacja firmy Microsoft
description: W tym artykule opisano tryby urządzenia StorSimple i wyjaśniono, jak zmienić tryb urządzenia za pomocą programu Windows PowerShell dla urządzenia StorSimple.
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
ms.openlocfilehash: dd160ede1189b0de544c8cf5db3b13228d212419
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875018"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Zmień tryb urządzenia na urządzeniu StorSimple

Ten artykuł zawiera krótki opis różnych trybach, w których może działać urządzenia StorSimple. Urządzenia StorSimple może działać w trzech trybów: Normalny, obsługi i odzyskiwania.

Po przeczytaniu tego artykułu, oznacza to:

* Jakie tryby urządzenia StorSimple są
* Znajduje się w sposób dowiedzieć się, który tryb urządzenia StorSimple
* Jak zmienić z normalnym i tryb konserwacji i *na odwrót*

Powyżej zadań zarządzania można wykonać tylko za pośrednictwem interfejsu programu Windows PowerShell urządzenia StorSimple.

## <a name="about-storsimple-device-modes"></a>Temat trybów urządzenia StorSimple

Urządzenia StorSimple może działać w trybie normalnym, konserwacji lub odzyskiwania. Każdy z tych trybów krótko opisano poniżej.

### <a name="normal-mode"></a>Tryb normalny

To jest zdefiniowana jako normalne tryb operacyjnych urządzenie StorSimple w pełni skonfigurowany. Domyślnie urządzenia powinny być w trybie normalnym.

### <a name="maintenance-mode"></a>Tryb konserwacji

Czasami urządzenia StorSimple może być konieczne umieszczenie w trybie konserwacji. Ten tryb służy do przeprowadzania konserwacji na urządzeniu i zainstaluj aktualizacje destrukcyjne, takich jak powiązane z oprogramowania układowego dysku.

System można umieścić w trybie konserwacji tylko przy użyciu programu Windows PowerShell dla urządzenia StorSimple. Wszystkie żądania We/Wy są wstrzymane w tym trybie. Usługi, takie jak trwałej pamięci (NVRAM) lub usługę klastrowania również zostały zatrzymane. Zarówno kontrolery są ponownie uruchamiane po wprowadzeniu lub opuścić ten tryb. Po wyjściu z trybu konserwacji, wszystkie usługi zostanie wznowiona i powinna być w dobrej kondycji. Może to potrwać kilka minut.

> [!NOTE]
> **Tryb konserwacji jest obsługiwana tylko na urządzeniu działa prawidłowo. Nie jest obsługiwane na urządzeniu, w którym jednego lub obu kontrolerów nie działają.**


### <a name="recovery-mode"></a>Tryb odzyskiwania

Tryb odzyskiwania można określić jako "Tryb awaryjny dla systemu Windows z obsługą sieci". Tryb odzyskiwania angażujący zespołu Support firmy Microsoft i umożliwia im wykonywanie diagnostyki w systemie. Podstawowym celem trybu odzyskiwania jest pobrać dzienniki systemu.

System przechodzi do trybu odzyskiwania, należy skontaktować się Microsoft Support dalsze czynności. Aby uzyskać więcej informacji, przejdź do [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

> [!NOTE]
> **Nie można umieścić urządzenia w trybie odzyskiwania. Jeśli urządzenie jest w złym stanie, tryb odzyskiwania próbuje pobrać urządzenia do stanu, w której personel firmy Microsoft Support można ją zbadać.**

## <a name="determine-storsimple-device-mode"></a>Określić tryb urządzenia StorSimple

#### <a name="to-determine-the-current-device-mode"></a>Aby określić bieżący tryb urządzenia

1. Zaloguj się do konsoli szeregowej urządzenia, wykonując kroki opisane w [przy użyciu programu PuTTY można nawiązać połączenia z konsolą szeregową urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Sprawdź komunikat transparentu, w menu konsoli szeregowej urządzenia. Ten komunikat oznacza jawnie, czy urządzenie jest w trybie konserwacji lub odzyskiwania. Jeśli komunikat nie zawiera żadnych określonych informacji dotyczących trybu systemu, urządzenie jest w trybie normalnym.

## <a name="change-the-storsimple-device-mode"></a>Zmień tryb urządzenia StorSimple

Urządzenia StorSimple można umieścić w tryb konserwacji (w trybie normalnym) do wykonania konserwacji lub instalowania aktualizacji tryb konserwacji. Wykonaj poniższe procedury, aby wprowadzić lub wyjść z trybu konserwacji.

> [!IMPORTANT]
> Przed trybu konserwacji, sprawdź, czy zarówno kontrolery urządzeń są w dobrej kondycji po zalogowaniu się do **ustawienia urządzenia > kondycji sprzętu** urządzenia w portalu Azure. Jeśli jeden lub oba kontrolery nie są w dobrej kondycji, skontaktuj się z Microsoft Support następnych kroków. Aby uzyskać więcej informacji, przejdź do [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).
 

#### <a name="to-enter-maintenance-mode"></a>Aby przejść do trybu konserwacji

1. Zaloguj się do konsoli szeregowej urządzenia, wykonując kroki opisane w [przy użyciu programu PuTTY można nawiązać połączenia z konsolą szeregową urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**. Po wyświetleniu monitu podaj **hasło administratora urządzenia**. Domyślne hasło jest: `Password1`.
3. W wierszu polecenia wpisz 
   
    `Enter-HcsMaintenanceMode`
4. Zobaczysz komunikat ostrzegawczy informujący o trybu konserwacji będzie zakłócać wszystkich żądań We/Wy i sever połączenia do portalu Azure i zostanie wyświetlony monit o potwierdzenie. Typ **Y** do trybu konserwacji.
5. Zarówno kontrolery zostanie uruchomiony ponownie. Po zakończeniu ponownego uruchomienia transparent konsoli szeregowej wskazuje, czy urządzenie jest w trybie konserwacji. Poniżej pokazano przykładowe dane wyjściowe.

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

1. Zaloguj się do konsoli szeregowej urządzenia. Sprawdź z komunikat transparentu, że urządzenie jest w trybie konserwacji.
2. W wierszu polecenia wpisz:
   
    `Exit-HcsMaintenanceMode`
3. Zostanie wyświetlony komunikat ostrzegawczy i komunikat potwierdzenia. Typ **Y** aby wyjść z trybu konserwacji.
4. Zarówno kontrolery zostanie uruchomiony ponownie. Po ponownym uruchomieniu komputera, na transparencie konsoli szeregowej wskazuje, czy urządzenie jest w trybie normalnym. Poniżej pokazano przykładowe dane wyjściowe.

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

Dowiedz się, jak [stosowania aktualizacji tryb normalny i konserwacja](storsimple-update-device.md) na urządzeniu StorSimple.

