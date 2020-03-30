---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09fa612e7e5c681da16bf19e94c626ae14a3b8a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590732"
---
Podczas dodawania dysków z danymi do maszyny Wirtualnej systemu Linux mogą wystąpić błędy, jeśli dysk nie istnieje w jednostce LUN 0. Jeśli dodajesz dysk ręcznie za `az vm disk attach -new` pomocą polecenia i określisz jednostkę LUN (`--lun`), zamiast zezwalać platformie Azure na określenie odpowiedniej jednostki LUN, należy zadbać o to, aby dysk już istniał / będzie istniał w jednostce LUN 0. 

Rozważmy poniższy przykład przedstawiający fragment danych `lsscsi`wyjściowych z:

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

Dwa dyski danych istnieją w jednostkach LUN 0 i `lsscsi` LUN `[host:channel:target:lun]`1 (pierwsza kolumna w szczegółach danych wyjściowych). Oba dyski powinny być dostępne z poziomu maszyny Wirtualnej. Jeśli ręcznie określono pierwszy dysk, który ma zostać dodany w jednostce LUN 1, a drugi dysk w jednostce LUN 2, dyski mogą nie być poprawnie widoczne z poziomu maszyny Wirtualnej.

> [!NOTE]
> Wartość `host` platformy Azure jest 5 w tych przykładach, ale może się różnić w zależności od typu magazynu, który można wybrać.
> 
> 

To zachowanie dysku nie jest problemem platformy Azure, ale sposób, w jaki jądro systemu Linux jest zgodne ze specyfikacjami SCSI. Gdy jądro Linuksa skanuje magistrala SCSI w poszukiwaniu podłączonych urządzeń, urządzenie musi znajdować się w jednostce LUN 0, aby system kontynuował skanowanie w poszukiwaniu dodatkowych urządzeń. W związku z tym:

* Przejrzyj `lsscsi` dane wyjściowe po dodaniu dysku danych, aby sprawdzić, czy masz dysk w jednostce LUN 0.
* Jeśli dysk nie jest poprawnie widoczny w maszynie wirtualnej, sprawdź, czy dysk istnieje w jednostce LUN 0.

