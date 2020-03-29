---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/18/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b819264895e35c6ef4fe9dc5263444dcac17eaa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74935891"
---
Na razie dyski ultra mają dodatkowe ograniczenia, są następujące:

- Są obsługiwane w następujących regionach, z różną liczbą stref dostępności na region:
    - Wschodnie stany USA 2
    - Wschodnie stany USA
    - Zachodnie stany USA 2
    - Azja Południowa
    - Europa Północna
    - Europa Zachodnia
    - Południowe Zjednoczone Królestwo 
- Może być używany tylko ze strefami dostępności (zestawy dostępności i pojedyncze wdrożenia maszyn wirtualnych poza strefami nie będą miały możliwości dołączenia ultramudy)
- Są obsługiwane tylko w następujących seriach maszyn wirtualnych:
    - [ESv3 (właśc.](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2 (właśc.
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2 (właśc.](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nie każdy rozmiar maszyny Wirtualnej jest dostępny w każdym obsługiwanym regionie z dyskami ultra
- Są dostępne tylko jako dyski danych i obsługują tylko rozmiar sektora fizycznego 4k. Ze względu na na rodzimy rozmiar sektora 4K ultradyskusji, istnieją aplikacje, które nie będą kompatybilne z dyskami ultra. Jednym z przykładów może być Oracle Database, która wymaga wydania 12.2 lub nowszego w celu obsługi dysków ultra.  
- Może być tworzony tylko jako puste dyski  
- Nie obsługuje jeszcze migawek dysków, obrazów maszyn wirtualnych, zestawów dostępności i szyfrowania dysków platformy Azure
- Nie obsługuje jeszcze integracji z usługą Azure Backup lub usługi Azure Site Recovery
- Bieżący maksymalny limit dla we/wy na maszynach wirtualnych GA wynosi 80 000.
- Jeśli chcesz wziąć udział w ograniczonym podglądzie maszyny Wirtualnej, która może wykonać 160 000 IOPS z ultra dyskami, wyślij wiadomość e-mail na adres UltraDiskFeedback@microsoft .com