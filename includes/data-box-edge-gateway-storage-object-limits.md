---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183684"
---
Poniżej przedstawiono rozmiary obiektów platformy Azure, które mogą być zapisywane. Upewnij się, że wszystkie pliki, które są przekazywane są zgodne z tych limitów.

| Typ obiektu platformy Azure | Limit przekazywania                                             |
|-------------------|-----------------------------------------------------------|
| Blokowe obiekty Blob        | ~ 4.75 TB                                                 |
| Obiekt blob typu Page         | 1 TB <br> Każdy plik przekazany w formacie stronicowy obiekt Blob musi być 512 bajtów wyrównane (całkowitą wielokrotnością), inne przekazywanie nie powiodło się. <br> VHD i VHDX są 512 bajtów wyrównane. |
| Azure Files         | 1 TB <br> Każdy plik przekazany w formacie stronicowy obiekt Blob musi być 512 bajtów wyrównane (całkowitą wielokrotnością), inne przekazywanie nie powiodło się. <br> VHD i VHDX są 512 bajtów wyrównane. |

> [!IMPORTANT]
> Tworzenie plików (niezależnie od typu magazynu) jest dozwolony do 5 TB. Jednak jeśli tworzysz plik, którego rozmiar jest większy niż limit przekazywania zdefiniowane w powyższej tabeli, pliku nie zostać przekazane. Należy ręcznie usunąć plik, aby odzyskać miejsce.