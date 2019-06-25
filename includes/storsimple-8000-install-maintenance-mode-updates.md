---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183427"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Zainstaluj aktualizacje trybu konserwacji za pomocą programu Windows PowerShell dla usługi StorSimple

Gdy aktualizacje trybu konserwacji zostanie zastosowany do urządzenia StorSimple, wszystkie żądania We/Wy są wstrzymane. Usługi, takie jak trwałej pamięci (NVRAM) lub usługę klastrowania zostaną zatrzymane. Po wprowadzeniu lub opuścić ten tryb, ponowne uruchomienie obu kontrolerów. Podczas zamykania w tym trybie wszystkie usługi wznowić i są w dobrej kondycji. (Może to potrwać kilka minut).

> [!IMPORTANT]
> * Przed wprowadzeniem trybu konserwacji, sprawdź, czy oba kontrolery urządzeń są w dobrej kondycji w witrynie Azure portal. Jeśli kontrolera nie jest w dobrej kondycji, [skontaktuj się z pomocą techniczną firmy Microsoft](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) potrzeby następnych kroków.
> * Podczas pracy w trybie konserwacji, należy najpierw zaktualizować jednego kontrolera, a następnie inny kontroler.

1. Łączenie z konsolą szeregową przy użyciu programu PuTTY. Postępuj zgodnie ze szczegółowymi instrukcjami w części [Nawiązywanie połączenia z konsolą szeregową urządzenia przy użyciu programu PuTTY](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). W wierszy polecenia naciśnij klawisz **Enter**. Wybierz opcję 1 **Zaloguj się przy użyciu pełnego dostępu**.

2. Aby umieścić kontrolera w trybie konserwacji, wpisz:
    
    `Enter-HcsMaintenanceMode`

    Oba kontrolery zostaną ponownie uruchomione w trybie konserwacji.

3. Zainstaluj aktualizacje trybu konserwacji. Wpisz:

    `Start-HcsUpdate`

    Zostanie wyświetlony monit o potwierdzenie. Po potwierdzeniu aktualizacje są zainstalowane na kontrolerze, który obecnie uzyskują dostęp do. Po zainstalowaniu aktualizacji, ponowne uruchomienie kontrolera.

4. Monitoruj stan aktualizacji. Zaloguj się do kontrolera elementu równorzędnego jako bieżący kontroler jest aktualizowany i nie będzie mógł przetwarzać z innymi poleceniami. Wpisz:

    `Get-HcsUpdateStatus`

    Jeśli `RunInProgress` jest `True`, aktualizacja jest nadal w toku. Jeśli `RunInProgress` jest `False`, oznacza to, że aktualizacja została zakończona.

5. Po aktualizacje oprogramowania układowego dysku zostaną pomyślnie zastosowane i zaktualizowano kontroler ponownie uruchomił sprawdź wersję oprogramowania układowego dysku. Na kontrolerze zaktualizowane wpisz:

    `Get-HcsFirmwareVersion`
   
    Dostępne są następujące wersje oprogramowania układowego dysku oczekiwany:  `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Wyjść z trybu konserwacji. Wpisz następujące polecenie dla każdego kontrolera urządzenia:

    `Exit-HcsMaintenanceMode`

    Po wyjściu z trybu konserwacji kontrolery zostaną ponownie uruchomione.

7. Wróć do witryny Azure Portal. W portalu może nie są wyświetlane zainstalowano aktualizacje trybu konserwacji na 24 godziny.