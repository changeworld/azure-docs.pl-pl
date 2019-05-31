---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244654"
---
Poniżej przedstawiono rozmiary obiektów platformy Azure, które mogą być zapisywane. Upewnij się, że wszystkie pliki, które są przekazywane są zgodne z tych limitów.

| Typ obiektu platformy Azure | Limit domyślny                                             |
|-------------------|-----------------------------------------------------------|
| Blokowy obiekt blob        | ~ 4.75 TiB                                                 |
| Obiekt blob typu Page         | 8 TiB <br> Każdy plik przekazany w formacie obiektu blob strony musi być 512 bajtów wyrównane (całkowitą wielokrotnością), inne przekazywanie nie powiodło się. <br> VHD i VHDX są 512 bajtów wyrównane. |
| Azure Files        | 1 TiB                                                      |
| Dyski zarządzane     | 4 TiB <br> Aby uzyskać więcej informacji o wielkości i ograniczeń zobacz: <li>[Cele skalowalności standardowych dysków SSD](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Cele skalowalności dysków SSD w warstwie Premium](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Cele skalowalności standardowych dysków twardych](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Cennik i rozliczenia z dyskami zarządzanymi](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
