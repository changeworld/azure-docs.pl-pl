---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183258"
---
#### <a name="to-enter-maintenance-mode"></a>Aby przejść do trybu konserwacji
1. W menu konsoli szeregowej wybierz opcję 1, **zaloguj się z pełnym dostępem**.
2. Wpisz hasło. Domyślnym hasłem jest **Password1**.
3. W wierszu polecenia wpisz
   
     `Enter-HcsMaintenanceMode`
4. Zostanie wyświetlony komunikat ostrzegawczy informujący, że tryb konserwacji zakłóci wszystkie żądania we/wy i oderwie połączenie z klasycznym portalem platformy Azure, a zostanie wyświetlony monit o potwierdzenie. Wpisz **Y,** aby przejść do trybu konserwacji.
   
    Oba kontrolery zostaną ponownie uruchomione. Po zakończeniu ponownego uruchomienia pojawi się kolejny komunikat informujący, że urządzenie jest w trybie konserwacji.

