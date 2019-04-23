---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn;davberg
ms.custom: include file
ms.openlocfilehash: 366dec6c687c0b9f40d61e0debb7c15910b9fa99
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60187776"
---
Pojęcia z usługi Azure Compute jednostki (ACU) umożliwia porównanie wydajności obliczeniowej (procesora CPU) w jednostkach SKU platformy Azure. Ten parametr pomoże łatwo zidentyfikować jednostkę SKU, która najprawdopodobniej spełni określone potrzeby związane z wydajnością.  Jednostka ACU jest obecnie standaryzowana na małej maszynie wirtualnej (Standardowa_A1) jako równa 100, a wszystkie pozostałe jednostki SKU reprezentują w przybliżeniu, o ile szybciej dana jednostka SKU może uruchomić standardowy test porównawczy. 

> [!IMPORTANT]
> Wartość ACU jest tylko wskazówką.  Wyniki dla konkretnego obciążenia mogą się różnić. 
> 
> 

<br>

| Rodzina SKU | ACU/procesor wirtualny vCPU | Procesor wirtualny: Rdzeń |
| --- | --- |---|
| [A0](../articles/virtual-machines/windows/sizes-general.md) |50 | 1:1 |
| [A1 - A4](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A5 — A7](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A1_v2 - A8_v2](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A2m_v2 - A8m_v2](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A8 – A11](../articles/virtual-machines/windows/sizes-hpc.md) |225* | 1:1 |
| [D1 - D14](../articles/virtual-machines/windows/sizes-general.md) |160 - 250 | 1:1 |
| [D1_v2 - D15_v2](../articles/virtual-machines/windows/sizes-general.md) |210 - 250* | 1:1 |
| [DS1 - DS14](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160 - 250 | 1:1 |
| [DS1_v2 - DS15_v2](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |210 - 250* | 1:1 |
| [D_v3](../articles/virtual-machines/virtual-machines-windows-sizes-general.md) |160 - 190* | 2:1\*\*\* |
| [Ds_v3](../articles/virtual-machines/virtual-machines-windows-sizes-general.md) |160 - 190* | 2:1\*\*\* |
| [E_v3](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160 - 190* | 2:1\*\*\*|
| [Es_v3](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160 - 190* | 2:1\*\*\* |
| [F2s_v2 - F72s_v2](../articles/virtual-machines/windows/sizes-compute.md) |195 - 210* | 2:1\*\*\* |
| [F1 — F16](../articles/virtual-machines/windows/sizes-compute.md) |210 - 250* | 1:1 |
| [F1s - F16s](../articles/virtual-machines/windows/sizes-compute.md) |210 - 250* | 1:1 |
| [G1 — 5. GENERACJI](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 - 240* | 1:1 |
| [GS1 — GS5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 - 240* | 1:1 |
| [H](../articles/virtual-machines/windows/sizes-hpc.md) |290 - 300* | 1:1 |
| [L4s - L32s](../articles/virtual-machines/windows/sizes-storage.md) |180 - 240* | 1:1 |
| [L8s_v2 w warstwie - L80s_v2](../articles/virtual-machines/windows/sizes-storage.md) |150 - 175** | 2:1 |
| [M](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) | 160 - 180 | 2:1\*\*\* |

* ACU wykorzystują technologię Intel® Turbo zwiększyć częstotliwość procesora CPU i zapewnić wzrost wydajności.  Ilość wzrost wydajności może się różnić w zależności od rozmiaru maszyny Wirtualnej, obciążenia i innych obciążeń uruchomionych na tym samym hoście.

** ACU wykorzystują technologię AMD® Boost, aby zwiększyć częstotliwość procesora CPU i zapewnić wzrost wydajności.  Ilość wzrost wydajności może się różnić w zależności od rozmiaru maszyny Wirtualnej, obciążenia i innych obciążeń uruchomionych na tym samym hoście.

Funkcja Hyper Threading i umożliwiający uruchomienie wirtualizacji zagnieżdżonej
