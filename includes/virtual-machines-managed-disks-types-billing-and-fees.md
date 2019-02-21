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
ms.openlocfilehash: 0d771f03f9f71151ef25140148d4dd4daf3d46ec
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443344"
---
**Wychodzące transfery danych**: [Wychodzące transfery danych](https://azure.microsoft.com/pricing/details/bandwidth/) (dane wychodzące z centrów danych platformy Azure) Naliczanie opłat za zużycie przepustowości.

**Transakcje**: Są naliczane dla liczby transakcji, które można wykonać na standardowa dysku zarządzanego. Na platformie Azure obowiązuje 0.0036 $ za 100 000 transakcji dla standardowych dysków twardych. Transakcje obejmują operacje odczytu i zapisu związane z magazynem.

Standardowe dyski SSD, użyj rozmiaru jednostki we/wy wynoszący 256 KB. Transferowanych danych jest mniejsza niż 256 KB, jest uznawana za jedną jednostkę operacji We/Wy. Większego rozmiaru operacji We/Wy są liczone jako wiele operacji We/Wy o rozmiarze 256 KB. Na przykład KB 1100 operacji We/Wy jest traktowana jako pięć jednostki we/wy.

Nie ma żadnych kosztów transakcji dla dysku zarządzanego w warstwie premium.

Aby uzyskać szczegółowe informacje na temat cen dla dysków Managed Disks, zobacz [cennika usługi Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Największa opłata rezerwacji maszyny Wirtualnej z dysków SSD

Maszyny wirtualne platformy Azure mają możliwość do wskazania, jeśli są one zgodne z ultra dysków SSD. Największa dysku zgodnej maszyny Wirtualnej przydziela dedykowaną pojemność przepustowości między wystąpieniem maszyny Wirtualnej obliczeniowe i jednostki skali magazynu bloku, aby zoptymalizować wydajność i zmniejszyć opóźnienie. Dodanie tej funkcji na wynikach maszyn wirtualnych w Twoich rękach rezerwacji, która nakłada się tylko włączenie możliwości ultra dysku na maszynie Wirtualnej bez podłączania ultra dysku do niego. Gdy ultra dysk jest podłączony do ultra dysku zgodnej maszyny Wirtualnej, ta opłata nie zostanie zastosowana. Ta opłata jest na procesor wirtualny vCPU, którego obsługę zainicjowano na maszynie Wirtualnej.

Zapoznaj się [Azure Disks stronę z cennikiem](https://azure.microsoft.com/pricing/details/managed-disks/) ultra dysku w szczegóły cennika dysków.