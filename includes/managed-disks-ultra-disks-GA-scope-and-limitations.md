---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a5e0e459800e7cb57672518597f3d04a74f53118
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008649"
---
Na razie dyski ultra mają dodatkowe ograniczenia, są następujące:

Jedynymi opcjami nadmiarowości infrastruktury, które są obecnie dostępne dla dysków ultra, są strefy dostępności. Maszyny wirtualne korzystające z innych opcji nadmiarowości nie mogą dołączyć dysku ultra.

W poniższej tabeli przedstawiono regiony, w których są dostępne dyski ultra, a także odpowiednie opcje dostępności:

> [!NOTE]
> Niektóre strefy dostępności w tych regionach nie oferują dysków ultra.

|Regiony  |Brak nadmiarowości infrastruktury  |Strefy dostępności  |
|---------|---------|---------|
|Zachodnie stany USA     |Tak         |Nie         |
|Zachodnie stany USA 2    |Nie         |Tak         |
|Wschodnie stany USA     |Nie         |Tak         |
|Wschodnie stany USA 2     |Nie         |Tak         |
|Azja Południowa     |Nie         |Tak         |
|Europa Północna     |Nie         |Tak         |
|Europa Zachodnia     |Nie         |Tak         |
|Południowe Zjednoczone Królestwo     |Nie         |Tak         |

- Są obsługiwane tylko w następujących seriach maszyn wirtualnych:
    - [ESv3 (właśc.](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2 (właśc.
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2 (właśc.](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nie każdy rozmiar maszyny Wirtualnej jest dostępny w każdym obsługiwanym regionie z dyskami ultra
- Są dostępne tylko jako dyski danych i obsługują tylko rozmiar sektora fizycznego 4k. Ze względu na na rodzimy rozmiar sektora 4K ultradyskusji, istnieją aplikacje, które nie będą kompatybilne z dyskami ultra. Jednym z przykładów może być Oracle Database, która wymaga wydania 12.2 lub nowszego w celu obsługi dysków ultra.  
- Może być tworzony tylko jako puste dyski  
- Obecnie nie obsługuje migawek dysków, obrazów maszyn wirtualnych, zestawów dostępności, dedykowanych hostów platformy Azure ani szyfrowania dysków platformy Azure
- Obecnie nie obsługuje integracji z usługą Azure Backup lub usługa Azure Site Recovery
- Bieżący maksymalny limit dla we/wy na maszynach wirtualnych GA wynosi 80 000.

Dyski ultra platformy Azure domyślnie oferują do 16 TiB na region na subskrypcję, ale dyski ultra obsługują większą pojemność na żądanie. Aby poprosić o zwiększenie pojemności, skontaktuj się z pomocą techniczną platformy Azure.