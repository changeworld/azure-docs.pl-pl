---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129377"
---
Aby zresetować urządzenie, należy bezpiecznie wyczyścić wszystkie dane na dysku danych i dysku rozruchowym urządzenia. 

Użyj polecenia cmdlet `Reset-HcsAppliance`, aby wyczyścić zarówno dyski danych, jak i dysk rozruchowy albo tylko dyski danych. Przełączniki `ClearData` i `BootDisk` umożliwiają odpowiednio wyczyszczenie dysków danych i dysku rozruchowego.

Przełącznik `BootDisk` czyści dysk rozruchowy i uniemożliwia korzystanie z urządzenia. Należy go używać tylko wtedy, gdy urządzenie ma zostać zwrócone firmie Microsoft. Aby uzyskać więcej informacji, zobacz [Zwracanie urządzenia firmie Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Jeśli użyjesz funkcji resetowania urządzenia w lokalnym internetowym interfejsie użytkownika, tylko dyski danych zostaną bezpiecznie wyczyszczone, a dysk rozruchowy pozostanie nienaruszony. Dysk rozruchowy zawiera konfigurację urządzenia.

1. [Nawiązywanie połączenia z interfejsem programu PowerShell](#connect-to-the-powershell-interface).
2. W wierszu polecenia wpisz polecenie:

    `Reset-HcsAppliance -ClearData -BootDisk`

    W poniższym przykładzie pokazano, jak użyć tego polecenia cmdlet:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
