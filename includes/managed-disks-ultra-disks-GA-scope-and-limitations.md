---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c847b110cb821ebe3ca7e706fcaec592dcc58fa8
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73600970"
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