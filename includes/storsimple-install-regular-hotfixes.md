---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183263"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Aby zainstalować regularne poprawki za pośrednictwem programu Windows PowerShell for StorSimple
1. Połącz się z konsolą szeregową urządzenia. Aby uzyskać więcej informacji, zobacz [Krok 1: Łączenie się z konsolą szeregową](../articles/storsimple/storsimple-update-device.md#step1).
2. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się z pełnym dostępem**. Wpisz hasło. Domyślnym hasłem jest **Password1**.
3. W wierszu polecenia wpisz polecenie:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > To polecenie dotyczy tylko zwykłych poprawek. To polecenie zostało uruchomione tylko na jednym kontrolerze, ale oba kontrolery zostaną zaktualizowane.
    > Podczas procesu aktualizacji można zauważyć proces pracy awaryjnej kontrolera; jednak praca awaryjna nie wpłynie na dostępność systemu lub działanie.

4. Po wyświetleniu monitu podaj ścieżkę do sieciowego folderu udostępnionego zawierającego pliki poprawek.
5. Pojawi się monit o potwierdzenie. Wpisz **Y,** aby kontynuować instalację poprawki.

