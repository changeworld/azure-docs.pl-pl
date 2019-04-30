---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61410009"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Aby zainstalować aktualizacje trybu konserwacji za pomocą programu Windows PowerShell dla usługi StorSimple
1. Jeśli nie zrobiono tego wcześniej, dostęp do konsoli szeregowej urządzenia i wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**. 
2. Wpisz hasło. Domyślne hasło jest **Password1**.
3. W wierszu polecenia wpisz polecenie:
   
     `Get-HcsUpdateAvailability` 
4. Jeśli aktualizacje są dostępne i czy aktualizacje są uciążliwe lub niezakłócającego, otrzymasz powiadomienie. Aby zastosować aktualizacje wymagają zatrzymania pracy, należy umieścić na urządzeniu w trybie konserwacji. Zobacz [krok 2: Przejście do trybu konserwacji](../articles/storsimple/storsimple-update-device.md#step2) instrukcje.
5. Gdy urządzenie jest w trybie konserwacji, w wierszu polecenia wpisz: `Start-HcsUpdate`
6. Użytkownik jest monitowany o potwierdzenie. Po potwierdzeniu aktualizacje zostaną one zainstalowane na kontrolerze, który obecnie uzyskują dostęp do. Po zainstalowaniu aktualizacji kontroler zostanie uruchomiony ponownie. 
7. Monitoruj stan aktualizacji. Wpisz:
   
    `Get-HcsUpdateStatus`
   
    Jeśli `RunInProgress` jest `True`, aktualizacja jest nadal w toku. Jeśli `RunInProgress` jest `False`, oznacza to, że aktualizacja została zakończona.  
8. Gdy aktualizacja jest zainstalowana na bieżący kontroler i ponownym uruchomieniu, połączyć się z innym kontrolerem i wykonaj kroki od 1 do 6.
9. Po zaktualizowaniu obu kontrolerów są wyjść z trybu konserwacji. Zobacz [krok 4: Wyjdź z trybu konserwacji](../articles/storsimple/storsimple-update-device.md#step4) instrukcje.

