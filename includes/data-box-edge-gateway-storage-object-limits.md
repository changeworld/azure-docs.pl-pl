---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183684"
---
Oto rozmiary obiektów platformy Azure, które mogą być zapisywane. Upewnij się, że wszystkie pliki, które są przekazywane są zgodne z tymi limitami.

| Typ obiektu platformy Azure | Limit przesyłania                                             |
|-------------------|-----------------------------------------------------------|
| Blok blob        | ~ 4,75 TB                                                 |
| Obiekt blob strony         | 1 TB <br> Każdy plik przekazany w formacie stronicowego obiektu blob musi być wyrównany do 512 bajtów (integralna wielokrotność), w przeciwnym razie przekazywanie kończy się niepowodzeniem. <br> VHD i VHDX są wyrównane o 512 bajtów. |
| Azure Files         | 1 TB <br> Każdy plik przekazany w formacie stronicowego obiektu blob musi być wyrównany do 512 bajtów (integralna wielokrotność), w przeciwnym razie przekazywanie kończy się niepowodzeniem. <br> VHD i VHDX są wyrównane o 512 bajtów. |

> [!IMPORTANT]
> Tworzenie plików (niezależnie od typu pamięci masowej) jest dozwolone do 5 TB. Jeśli jednak utworzysz plik, którego rozmiar jest większy niż limit przekazywania zdefiniowany w poprzedniej tabeli, plik nie zostanie przekazany. Musisz ręcznie usunąć plik, aby odzyskać miejsce.