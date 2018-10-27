---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 77fc95c53ba89c3905ebd1aec785e22f42339369
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165552"
---
<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Aby zainstalować regularne poprawek za pomocą programu Windows PowerShell dla usługi StorSimple
1. Łączenie z konsolą szeregową urządzenia. Aby uzyskać więcej informacji, zobacz [krok 1: łączenie z konsolą szeregową](../articles/storsimple/storsimple-update-device.md#step1).
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

