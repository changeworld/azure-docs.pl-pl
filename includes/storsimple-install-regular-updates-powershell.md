---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183264"
---
#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Aby zainstalować regularne aktualizacje za pośrednictwem programu Windows PowerShell for StorSimple
1. Otwórz konsolę szeregową urządzenia i wybierz opcję 1, **Zaloguj się z pełnym dostępem**. Wpisz hasło. Domyślnym hasłem jest *Password1*. 
2. W wierszu polecenia wpisz polecenie:
   
     `Get-HcsUpdateAvailability`
   
    Użytkownik zostanie powiadomiony, jeśli dostępne są aktualizacje i czy aktualizacje są destrukcyjne lub nie zakłócające.
3. W wierszu polecenia wpisz polecenie:
   
     `Start-HcsUpdate`
   
    Rozpocznie się proces aktualizacji.

> [!IMPORTANT]
> * To polecenie dotyczy tylko regularnych aktualizacji. To polecenie zostało uruchomione tylko na jednym kontrolerze, ale oba kontrolery zostaną zaktualizowane. 
> * Podczas procesu aktualizacji można zauważyć proces pracy awaryjnej kontrolera; jednak praca awaryjna nie wpłynie na dostępność systemu lub działanie.
> 
> 

