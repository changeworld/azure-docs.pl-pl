---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9bd2ee708f7d27cff5d07ca7f86d925ca6d2741d
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165611"
---
<!--author=SharS last changed: 12/01/15-->

#### <a name="to-enter-maintenance-mode"></a>Aby przejść do trybu konserwacji
1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**.
2. Wpisz hasło. Domyślne hasło jest **Password1**.
3. W wierszu polecenia wpisz polecenie
   
     `Enter-HcsMaintenanceMode`
4. Zostanie wyświetlony komunikat ostrzegawczy informujący o trybu konserwacji będzie zakłócać wszystkich żądań We/Wy i sever połączenia klasycznej witrynie Azure portal i zostanie wyświetlony monit o potwierdzenie. Typ **Y** wejścia w tryb konserwacji.
   
    Powoduje ponowne uruchomienie obu kontrolerów. Po zakończeniu ponownego uruchomienia innego pojawi się komunikat wskazujący, że urządzenie jest w trybie konserwacji.

