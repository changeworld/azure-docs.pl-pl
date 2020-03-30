---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2d0fd904dd704c704662192e1e92fe403f0971c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183261"
---
#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Aby zainstalować poprawki trybu konserwacji za pośrednictwem programu Windows PowerShell for StorSimple
> [!IMPORTANT]
> W trybie konserwacji należy najpierw zastosować poprawkę na jednym kontrolerze, a następnie na drugim kontrolerze.
> 
> 

1. Umieść urządzenie w trybie konserwacji. Zobacz [krok 2: Wprowadź tryb konserwacji,](../articles/storsimple/storsimple-update-device.md#step2) aby uzyskać instrukcje dotyczące wchodzenia w tryb konserwacji.
2. Aby zastosować poprawkę, wpisz:
   
     `Start-HcsHotfix` 
3. Po wyświetleniu monitu podaj ścieżkę do sieciowego folderu udostępnionego zawierającego pliki poprawek.
4. Pojawi się monit o potwierdzenie. Wpisz **Y,** aby kontynuować instalację poprawki.
5. Po zastosowaniu poprawki na jednym kontrolerze zaloguj się do drugiego kontrolera. Zastosuj poprawkę, tak jak w przypadku poprzedniego kontrolera.
6. Po zastosowaniu poprawek, zakończyć tryb konserwacji. Zobacz [krok 4: Wyjdź z trybu konserwacji, aby](../articles/storsimple/storsimple-update-device.md#step4) uzyskać instrukcje.

