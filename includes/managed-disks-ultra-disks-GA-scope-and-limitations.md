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
ms.openlocfilehash: df488c2a3033015175943f7d092beaaa41e34046
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260826"
---
Na razie Ultra dyski mają dodatkowe ograniczenia, są następujące:

- Są obsługiwane w następujących regionach o różnej liczbie stref dostępności na region:
    - Wschodnie stany USA 2
    - Wschodnie stany USA
    - Zachodnie stany USA 2
    - Azja Południowo-Wschodnia
    - Europa Północna
    - Europa Zachodnia
    - Południowe Zjednoczone Królestwo 
- Może być używany tylko z strefami dostępności (zbiory dostępności i pojedyncze wdrożenia maszyn wirtualnych poza strefami nie będą miały możliwości dołączenia Ultra Disk)
- Są obsługiwane tylko przez następującą serię maszyn wirtualnych:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nie każdy rozmiar maszyny wirtualnej jest dostępny w każdym obsługiwanym regionie przy użyciu Ultra Disks.
- Są dostępne tylko jako dyski danych i obsługują tylko rozmiar sektora fizycznego 4 KB  
- Można utworzyć tylko jako puste dyski  
- Nie obsługują jeszcze migawek dysków, obrazów maszyn wirtualnych, zestawów dostępności i usługi Azure Disk Encryption
- Nie należy jeszcze obsługiwać integracji z programem Azure Backup lub Azure Site Recovery
- Bieżący maksymalny limit liczby operacji we/wy na maszynach wirtualnych "GA" to 80 000.
- Jeśli chcesz wziąć udział w ograniczonej wersji zapoznawczej maszyny wirtualnej, która może wykonywać 160 000 operacji we/wy na bardzo dyskach, Wyślij wiadomość e-mail UltraDiskFeedback@microsoft. com