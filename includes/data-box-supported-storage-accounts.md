---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: bc156b8c18f46cccf6fc775b82f76383b8c43861
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "66242128"
---
Oto lista obsługiwanych kont magazynu i typów magazynu dla urządzenia Data Box. Aby uzyskać pełną listę wszystkich typów kont magazynu i ich pełne możliwości, zobacz [Typy kont magazynu](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Konto magazynu / Obsługiwane typy magazynu** | **Blokowy obiekt blob** |**Obiekt blob strony*** |**Pliki platformy Azure** |**Uwagi**|
| --- | --- | -- | -- | -- |
| Klasyczny standard | Tak | Tak | Tak |
| Standard ogólnego przeznaczenia v1  | Tak | Tak | Tak | Zarówno gorące, jak i chłodne są obsługiwane.|
| Uniwersalny v1 Premium  |  | Tak| | |
| Standard ogólnego przeznaczenia v2  | Tak | Tak | Tak | Zarówno gorące, jak i chłodne są obsługiwane.|
| Uniwersalny v2 Premium  |  |Tak | | |
| Standard magazynu obiektów blob |Tak | | |Zarówno gorące, jak i chłodne są obsługiwane. |

\**- Dane przesłane do stronicowych obiektów blob muszą być wyrównane do 512 bajtów, takich jak VHD.*

>[!NOTE]
> Konta usługi Azure Data Lake Storage Gen 2 nie są obsługiwane.
