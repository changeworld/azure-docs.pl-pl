---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183266"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Aby zainstalować aktualizacje trybu konserwacji za pośrednictwem programu Windows PowerShell for StorSimple
1. Jeśli jeszcze tego nie zrobiłeś, przejdź do konsoli szeregowej urządzenia i wybierz opcję 1, **Zaloguj się z pełnym dostępem**. 
2. Wpisz hasło. Domyślnym hasłem jest **Password1**.
3. W wierszu polecenia wpisz polecenie:
   
     `Get-HcsUpdateAvailability` 
4. Użytkownik zostanie powiadomiony, jeśli dostępne są aktualizacje i czy aktualizacje są destrukcyjne lub nie zakłócające. Aby zastosować aktualizacje zakłócające zakłócenia, należy przełożyć urządzenie w tryb konserwacji. Zobacz [krok 2: Wprowadź tryb konserwacji,](../articles/storsimple/storsimple-update-device.md#step2) aby uzyskać instrukcje.
5. Gdy urządzenie jest w trybie konserwacji, w wierszu polecenia wpisz:`Start-HcsUpdate`
6. Pojawi się monit o potwierdzenie. Po potwierdzeniu aktualizacji zostaną one zainstalowane na kontrolerze, do którego aktualnie uzyskujesz dostęp. Po zainstalowaniu aktualizacji kontroler zostanie ponownie uruchomiony. 
7. Monitoruj stan aktualizacji. Wpisz:
   
    `Get-HcsUpdateStatus`
   
    Jeśli `RunInProgress` jest `True`, aktualizacja jest nadal w toku. Jeśli `RunInProgress` `False`tak , oznacza to, że aktualizacja została zakończona.  
8. Po zainstalowaniu aktualizacji na bieżącym kontrolerze i ponownym uruchomieniu należy połączyć się z innym kontrolerem i wykonać kroki od 1 do 6.
9. Po zaktualizowaniu obu kontrolerów wyjdź z trybu konserwacji. Zobacz [krok 4: Wyjdź z trybu konserwacji, aby](../articles/storsimple/storsimple-update-device.md#step4) uzyskać instrukcje.

