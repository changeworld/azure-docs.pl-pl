---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 5e4921be3116754f146ed0845513010f2642c97b
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164462"
---
<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Aby zainstalować poprawki przeznaczone do trybu konserwacji za pomocą programu Windows PowerShell dla usługi StorSimple
> [!IMPORTANT]
> W trybie konserwacji należy najpierw zastosować poprawkę na jeden kontroler, a następnie na drugim kontrolerze.
> 
> 

1. Umieść je w tryb konserwacji. Zobacz [krok 2: tryb konserwacji wprowadź](../articles/storsimple/storsimple-update-device.md#step2) w jaki sposób, aby przejść do trybu konserwacji.
2. Aby zastosować poprawkę, wpisz:
   
     `Start-HcsHotfix` 
3. Po wyświetleniu monitu podaj ścieżkę do udostępnionego folderu sieciowego, który zawiera pliki poprawki.
4. Użytkownik jest monitowany o potwierdzenie. Typ **Y** kontynuowanie instalacji poprawki.
5. Po zastosowaniu poprawki na jednym kontrolerze Zaloguj się do innego kontrolera. Zastosuj poprawkę, tak jak dla poprzedniego kontrolera.
6. Po zastosowaniu poprawki, wyjść z trybu konserwacji. Zobacz [krok 4: tryb konserwacji zakończenia](../articles/storsimple/storsimple-update-device.md#step4) instrukcje.

