---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129377"
---
Aby zresetować urządzenie, musisz bezpiecznie usunąć wszystkie dane na dysku danych i dysku rozruchowym urządzenia. 

Użyj `Reset-HcsAppliance` polecenia cmdlet, aby wymazać zarówno dyski z danymi, jak i dysk rozruchowy lub tylko dyski z danymi. `ClearData` Przełączniki `BootDisk` i przełączniki umożliwiają wyczyszczenia odpowiednio dysków z danymi i dysku rozruchowego.

Przełącznik `BootDisk` czyści dysk rozruchowy i sprawia, że urządzenie jest bezużyteczne. Powinien być używany tylko wtedy, gdy urządzenie musi zostać zwrócone do firmy Microsoft. Aby uzyskać więcej informacji, zobacz [Zwracanie urządzenia do firmy Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Jeśli używasz resetowania urządzenia w lokalnym interfejsie użytkownika sieci Web, tylko dyski danych są bezpiecznie czyszczone, ale dysk rozruchowy jest utrzymywany w stanie nienaruszonym. Dysk rozruchowy zawiera konfigurację urządzenia.

1. [Połącz się z interfejsem programu PowerShell](#connect-to-the-powershell-interface).
2. W wierszu polecenia wpisz polecenie:

    `Reset-HcsAppliance -ClearData -BootDisk`

    W poniższym przykładzie pokazano, jak używać tego polecenia cmdlet:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
