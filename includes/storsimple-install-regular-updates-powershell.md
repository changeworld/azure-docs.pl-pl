---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 36a014fbff30c32a7149a83907c9586bc3b2f01a
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166187"
---
<!--author=SharS last changed: 11/18/16-->

#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Aby zainstalować regularne aktualizacje za pośrednictwem programu Windows PowerShell dla usługi StorSimple
1. Otwórz okno konsoli szeregowej urządzenia i wybierz opcję 1, **Zaloguj się przy użyciu pełnego dostępu**. Wpisz hasło. Domyślne hasło jest *Password1*. 
2. W wierszu polecenia wpisz polecenie:
   
     `Get-HcsUpdateAvailability`
   
    Jeśli aktualizacje są dostępne i czy aktualizacje są uciążliwe lub niezakłócającego, otrzymasz powiadomienie.
3. W wierszu polecenia wpisz polecenie:
   
     `Start-HcsUpdate`
   
    Rozpocznie się proces aktualizacji.

> [!IMPORTANT]
> * To polecenie dotyczy tylko regularne aktualizacje. Uruchom to polecenie na tylko jeden kontroler, ale oba kontrolery zostaną zaktualizowane. 
> * Można zauważyć praca awaryjna kontrolera podczas procesu aktualizacji; Jednak przełączenie w tryb failover nie wpłynie na dostępność systemu lub operacji.
> 
> 

