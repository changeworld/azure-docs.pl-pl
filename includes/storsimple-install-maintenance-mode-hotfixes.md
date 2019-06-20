---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2d0fd904dd704c704662192e1e92fe403f0971c5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183261"
---
#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Aby zainstalować poprawki przeznaczone do trybu konserwacji za pomocą programu Windows PowerShell dla usługi StorSimple
> [!IMPORTANT]
> W trybie konserwacji należy najpierw zastosować poprawkę na jeden kontroler, a następnie na drugim kontrolerze.
> 
> 

1. Umieść je w tryb konserwacji. Zobacz [krok 2: Przejście do trybu konserwacji](../articles/storsimple/storsimple-update-device.md#step2) w jaki sposób, aby przejść do trybu konserwacji.
2. Aby zastosować poprawkę, wpisz:
   
     `Start-HcsHotfix` 
3. Po wyświetleniu monitu podaj ścieżkę do udostępnionego folderu sieciowego, który zawiera pliki poprawki.
4. Użytkownik jest monitowany o potwierdzenie. Typ **Y** kontynuowanie instalacji poprawki.
5. Po zastosowaniu poprawki na jednym kontrolerze Zaloguj się do innego kontrolera. Zastosuj poprawkę, tak jak dla poprzedniego kontrolera.
6. Po zastosowaniu poprawki, wyjść z trybu konserwacji. Zobacz [krok 4: Wyjdź z trybu konserwacji](../articles/storsimple/storsimple-update-device.md#step4) instrukcje.

