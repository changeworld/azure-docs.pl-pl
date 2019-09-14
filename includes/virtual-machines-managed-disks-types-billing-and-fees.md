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
ms.openlocfilehash: a416d1c6e813be558f034e15576c57efa6073788
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968601"
---
**Wychodzące transfery danych**: [Wychodzące transfery danych](https://azure.microsoft.com/pricing/details/bandwidth/) (dane wychodzące z centrów danych platformy Azure) naliczane są rozliczanie za użycie przepustowości.

**Transakcje**: Opłaty są naliczane za liczbę transakcji wykonywanych na dysku zarządzanym w warstwie Standardowa. W przypadku standardowej dysków SSD każda operacja we/wy mniejsza lub równa 256 KiB przepływności jest uznawana za pojedynczą operację we/wy. Operacje we/wy o rozmiarze większym niż 256 KiB przepływności są uważane za wiele systemów I/OS o rozmiarach 256 KiB. W przypadku standardowej HDD każda operacja we/wy jest uznawana za pojedynczą transakcję, niezależnie od rozmiaru wejścia/wyjścia.

Aby uzyskać szczegółowe informacje na temat cen Managed Disks, w tym kosztów transakcji, zobacz [Managed disks cennika](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Opłata rezerwacji na maszynę wirtualną Ultra Disk

Maszyny wirtualne platformy Azure mają możliwość wskazywania, czy są one zgodne z Ultra Disks. Maszyna wirtualna zgodne z dyskami jest przydzielona dedykowanej przepustowości między wystąpieniem maszyny wirtualnej obliczeniowej a jednostką skali magazynu blokowego w celu zoptymalizowania wydajności i skrócenia opóźnień. Dodanie tej funkcji na maszynie wirtualnej spowoduje naliczenie opłaty za rezerwację, która jest nałożona tylko wtedy, gdy na maszynie wirtualnej jest włączona funkcja Ultra Disk bez dołączania do niej dysku. Gdy dysk jest podłączony do maszyny wirtualnej zgodnej z dyskiem Ultra, ta opłata nie zostanie zastosowana. Ta opłata jest naliczana za vCPU na maszynie wirtualnej. 

> [!Note]
> W przypadku [ograniczonych rozmiarów maszyn wirtualnych](../articles/virtual-machines/linux/constrained-vcpu.md)opłata za rezerwację jest oparta na rzeczywistej liczbie procesorów wirtualnych vCPU, a nie w ograniczonych rdzeniach. W przypadku Standard_E32-8s_v3 opłata za rezerwację będzie oparta na 32 rdzeniach. 

Zapoznaj się ze [stroną cennika usługi Azure disks](https://azure.microsoft.com/pricing/details/managed-disks/) , aby uzyskać szczegółowe informacje o cenach.
