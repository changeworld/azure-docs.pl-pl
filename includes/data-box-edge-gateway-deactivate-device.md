---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: aebb82690a7a49aba071ed64349d37d516208cca
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183692"
---
Aby zresetować urządzenie, musisz bezpiecznie czyszczenia wszystkich danych na dysku danych, a dysk rozruchowy urządzenia. 

Użyj `Reset-HcsAppliance` polecenia cmdlet, aby wymazać z dysków z danymi i dysk rozruchowy lub po prostu dysków z danymi. `ClearData` i `BootDisk` przełączników pozwalają odpowiednio wyczyścić dysk rozruchowy i dysków z danymi.

Jeśli używasz urządzenia resetowania w lokalnym internetowym interfejsie użytkownika, tylko dyski danych są bezpiecznie wyczyszczone, ale dysk rozruchowy pozostaje bez zmian. Dysk rozruchowy zawiera konfigurację urządzenia.

1. [Nawiązać połączenie z interfejsu programu PowerShell](#connect-to-the-powershell-interface).
2. W wierszu polecenia wpisz polecenie:

    `Reset-HcsAppliance -ClearData -BootDisk`

    Poniższy przykład przedstawia sposób użycia tego polecenia cmdlet:
    ```
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk
    
    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
