---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: bc156b8c18f46cccf6fc775b82f76383b8c43861
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242128"
---
Poniżej przedstawiono listę kont magazynu obsługiwanych typów magazynów dla urządzenia Data Box. Aby uzyskać pełną listę wszystkich różnych typów kont magazynu i ich pełne możliwości, zobacz [typy kont magazynu](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Konto magazynu / obsługiwane typy magazynu** | **Blokowe obiekty blob** |**Obiekt blob typu Page*** |**Usługa pliki Azure** |**Uwagi**|
| --- | --- | -- | -- | -- |
| Standardowa klasycznego | Tak | Tak | Tak |
| Standardowa ogólnego przeznaczenia w wersji 1  | Tak | Tak | Tak | Obsługiwane są zarówno gorąca i chłodna.|
| Ogólnego przeznaczenia w wersji 1 — wersja Premium  |  | Tak| | |
| Standardowa ogólnego przeznaczenia w wersji 2  | Tak | Tak | Tak | Obsługiwane są zarówno gorąca i chłodna.|
| Ogólnego przeznaczenia w wersji 2 — wersja Premium  |  |Tak | | |
| Magazyn obiektów blob Standard |Tak | | |Obsługiwane są zarówno gorąca i chłodna. |

\* *-Danych przekazanych do stronicowych obiektów blob musi być 512 bajtów wyrównane, takie jak wirtualne dyski twarde.*

>[!NOTE]
> Usługa Azure Data Lake Storage Gen 2 konta nie są obsługiwane.
