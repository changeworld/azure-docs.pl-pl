---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183427"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instalowanie aktualizacji trybu konserwacji za pośrednictwem programu Windows PowerShell dla StorSimple

Po zastosowaniu aktualizacji trybu konserwacji do urządzenia StorSimple wszystkie żądania we/wy są wstrzymane. Usługi, takie jak nieulotna pamięć dostępu losowego (NVRAM) lub usługa klastrowania, są zatrzymywane. Oba kontrolery są ponownie uruchamiane po wejściu lub wyjściu z tego trybu. Po wyjściu z tego trybu wszystkie usługi wznowić i są w dobrej kondycji. (Może to potrwać kilka minut).)

> [!IMPORTANT]
> * Przed wejściem w tryb konserwacji sprawdź, czy oba kontrolery urządzeń są w dobrej kondycji w witrynie Azure portal. Jeśli kontroler nie jest w dobrej kondycji, [skontaktuj się z pomocą techniczną firmy Microsoft,](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) aby uzyskać następujące kroki.
> * Gdy jesteś w trybie konserwacji, należy najpierw zaktualizować jeden kontroler, a następnie drugi kontroler.

1. Użyj PuTTY, aby połączyć się z konsolą szeregową. Postępuj zgodnie ze szczegółowymi instrukcjami w części [Nawiązywanie połączenia z konsolą szeregową urządzenia przy użyciu programu PuTTY](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). W wierszy polecenia naciśnij klawisz **Enter**. Wybierz opcję 1, **zaloguj się z pełnym dostępem**.

2. Aby umieścić sterownik w trybie konserwacji, wpisz:
    
    `Enter-HcsMaintenanceMode`

    Oba kontrolery zostaną ponownie uruchomione w trybie konserwacji.

3. Zainstaluj aktualizacje trybu konserwacji. Wpisz:

    `Start-HcsUpdate`

    Zostanie wyświetlony monit o potwierdzenie. Po potwierdzeniu aktualizacji są one instalowane na kontrolerze, do którego aktualnie uzyskujesz dostęp. Po zainstalowaniu aktualizacji kontroler zostanie ponownie uruchomiony.

4. Monitoruj stan aktualizacji. Zaloguj się do kontrolera równorzędnego, gdy bieżący kontroler jest aktualizowany i nie jest w stanie przetworzyć żadnych innych poleceń. Wpisz:

    `Get-HcsUpdateStatus`

    Jeśli `RunInProgress` jest `True`, aktualizacja jest nadal w toku. Jeśli `RunInProgress` `False`tak , oznacza to, że aktualizacja została zakończona.

5. Po pomyślnym zastosowaniu aktualizacji oprogramowania układowego dysku i ponownym uruchomieniu zaktualizowanego kontrolera sprawdź wersję oprogramowania układowego dysku. Na zaktualizowanym kontrolerze wpisz:

    `Get-HcsFirmwareVersion`
   
    Oczekiwane wersje oprogramowania układowego dysku to:`XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Wyjdź z trybu konserwacji. Wpisz następujące polecenie dla każdego kontrolera urządzenia:

    `Exit-HcsMaintenanceMode`

    Po wyjściu z trybu konserwacji kontrolery zostaną ponownie uruchomione.

7. Wróć do witryny Azure Portal. Portal może nie pokazać, że aktualizacje trybu konserwacji zostały zainstalowane przez 24 godziny.