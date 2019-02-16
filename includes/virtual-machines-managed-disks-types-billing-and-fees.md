---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 82bfdfd7481b47c08f76d077585f85763e58b87b
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331221"
---
**Wychodzące transfery danych**: [Wychodzące transfery danych](https://azure.microsoft.com/pricing/details/bandwidth/) (dane wychodzące z centrów danych platformy Azure) Naliczanie opłat za zużycie przepustowości.

**Transakcje**: Są naliczane dla liczby transakcji, które można wykonać na standardowa dysku zarządzanego. Na platformie Azure obowiązuje 0.0036 $ za 100 000 transakcji dla standardowych dysków twardych. Transakcje obejmują operacje odczytu i zapisu związane z magazynem.

Standardowe dyski SSD, użyj rozmiaru jednostki we/wy wynoszący 256 KB. Transferowanych danych jest mniejsza niż 256 KB, jest uznawana za jedną jednostkę operacji We/Wy. Większego rozmiaru operacji We/Wy są liczone jako wiele operacji We/Wy o rozmiarze 256 KB. Na przykład KB 1100 operacji We/Wy jest traktowana jako pięć jednostki we/wy.

Nie ma żadnych kosztów transakcji dla dysku zarządzanego w warstwie premium.

Aby uzyskać szczegółowe informacje na temat cen dla dysków Managed Disks, zobacz [cennika usługi Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disks-vm-reservation-fee"></a>Opłata za rezerwacji maszyny Wirtualnej Ultra dysków

Maszyny wirtualne platformy Azure ma możliwość wskazania, jeśli są one zgodne z dyskami ultra. Największa dysku zgodnej maszyny Wirtualnej przydziela dedykowaną pojemność przepustowości między wystąpieniem maszyny Wirtualnej obliczeniowe i jednostki skali magazynu bloku, aby zoptymalizować wydajność i zmniejszyć opóźnienie. Dodanie tej funkcji na wynikach maszyn wirtualnych w Twoich rękach rezerwacji, która nakłada się tylko włączenie możliwości ultra dysku na maszynie Wirtualnej bez podłączania ultra dysku do niego. Gdy ultra dysk jest podłączony do ultra dysku zgodnej maszyny Wirtualnej, ta opłata nie zostanie zastosowana. Ta opłata jest na procesor wirtualny vCPU, którego obsługę zainicjowano na maszynie Wirtualnej.

Zapoznaj się [Azure Disks stronę z cennikiem](https://azure.microsoft.com/pricing/details/managed-disks/) ultra dysku w szczegóły cennika dysków.