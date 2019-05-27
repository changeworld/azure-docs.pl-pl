---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66171868"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Aby zainstalować regularne poprawek za pomocą programu Windows PowerShell dla usługi StorSimple
1. Łączenie z konsolą szeregową urządzenia. Aby uzyskać więcej informacji, zobacz [krok 1: Łączenie z konsolą szeregową](../articles/storsimple/storsimple-update-device.md#step1).
2. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**. Wpisz hasło. Domyślne hasło jest **Password1**.
3. W wierszu polecenia wpisz polecenie:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > To polecenie dotyczy tylko regularnych poprawki. Uruchom to polecenie na tylko jeden kontroler, ale oba kontrolery zostaną zaktualizowane.
    > Można zauważyć praca awaryjna kontrolera podczas procesu aktualizacji; Jednak przełączenie w tryb failover nie wpłynie na dostępność systemu lub operacji.

4. Po wyświetleniu monitu podaj ścieżkę do udostępnionego folderu sieciowego, który zawiera pliki poprawki.
5. Użytkownik jest monitowany o potwierdzenie. Typ **Y** kontynuowanie instalacji poprawki.

