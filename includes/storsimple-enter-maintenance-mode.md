---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171864"
---
#### <a name="to-enter-maintenance-mode"></a>Aby przejść do trybu konserwacji
1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**.
2. Wpisz hasło. Domyślne hasło jest **Password1**.
3. W wierszu polecenia wpisz polecenie
   
     `Enter-HcsMaintenanceMode`
4. Zostanie wyświetlony komunikat ostrzegawczy informujący o trybu konserwacji będzie zakłócać wszystkich żądań We/Wy i sever połączenia klasycznej witrynie Azure portal i zostanie wyświetlony monit o potwierdzenie. Typ **Y** wejścia w tryb konserwacji.
   
    Powoduje ponowne uruchomienie obu kontrolerów. Po zakończeniu ponownego uruchomienia innego pojawi się komunikat wskazujący, że urządzenie jest w trybie konserwacji.

