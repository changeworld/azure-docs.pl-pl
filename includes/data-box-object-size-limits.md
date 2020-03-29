---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "66244654"
---
Oto rozmiary obiektów platformy Azure, które mogą być zapisywane. Upewnij się, że wszystkie pliki, które są przekazywane są zgodne z tymi limitami.

| Typ obiektu platformy Azure | Limit domyślny                                             |
|-------------------|-----------------------------------------------------------|
| Blokowy obiekt blob        | ~ 4,75 TiB                                                 |
| Stronicowy obiekt blob         | 8 TiB <br> Każdy plik przekazany w formacie obiektu blob strony musi być wyrównany 512 bajtów (integralną wielokrotność), w przeciwnym razie przekazywanie kończy się niepowodzeniem. <br> VHD i VHDX są wyrównane o 512 bajtów. |
| Azure Files        | 1 TiB                                                      |
| Dyski zarządzane     | 4 TiB <br> Aby uzyskać więcej informacji na temat rozmiaru i limitów, zobacz: <li>[Cele skalowalności standardowych identyfikatorów SSD](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Cele skalowalności najwyższej klasy ssd premium](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Cele skalowalności standardowych dysków twardych](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Ceny i rozliczenia dysków zarządzanych](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
