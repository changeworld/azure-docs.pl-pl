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
ms.openlocfilehash: 2303d36e93cecfca03894a8b0e55458c03b13d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73412994"
---
**Wychodzące transfery danych:** [Wychodzące transfery danych](https://azure.microsoft.com/pricing/details/bandwidth/) (dane wychodzące z centrów danych platformy Azure) obciążają rozliczenia za użycie przepustowości.

**Transakcje:** Naliczane są opłaty za liczbę transakcji wykonywanych na standardowym dysku zarządzanym. W przypadku standardowych ssd każda operacja we/wy mniejsza lub równa 256 KiB przepływności jest uważana za pojedynczą operację we/wy. Operacje we/wy większe niż 256 KiB przepustowości są uważane za wiele operacji we/wy o rozmiarze 256 KiB. W przypadku standardowych dysków twardych każda operacja operacji we/wy jest traktowana jako pojedyncza transakcja, niezależnie od rozmiaru we/wy.

Aby uzyskać szczegółowe informacje na temat cen dysków zarządzanych, w tym kosztów transakcji, zobacz [Ceny dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Opłata za rezerwację maszyny wirtualnej na ultra dysku

Maszyny wirtualne platformy Azure mają możliwość wskazania, czy są one zgodne z dyskami ultra. Maszyna wirtualna zgodna z dyskami ultra przydziela dedykowaną pojemność przepustowości między wystąpieniem maszyny Wirtualnej obliczeniowej a jednostką skalowania magazynu blokowego w celu optymalizacji wydajności i zmniejszenia opóźnień. Dodanie tej funkcji na maszynie wirtualnej powoduje obciążenie rezerwacji, które jest nakładane tylko wtedy, gdy włączono funkcję ultra dysku na maszynie wirtualnej bez podłączania do niej ultramudysu. Gdy dysk ultra jest podłączony do maszyny wirtualnej zgodnej z dyskami ultra, opłata ta nie zostanie zastosowana. Ta opłata jest na procesor wirtualny aprowizowana na maszynie wirtualnej. 

> [!Note]
> W przypadku [podstawowych rozmiarów maszyn wirtualnych](../articles/virtual-machines/linux/constrained-vcpu.md)opłata za rezerwację jest oparta na rzeczywistej liczbie procesorów wirtualnych, a nie na ograniczonych rdzeniach. W przypadku Standard_E32-8s_v3 opłata za rezerwację zostanie pobrana z 32 rdzeni. 

Szczegółowe informacje na temat cenowych [dysków ultradymiej.](https://azure.microsoft.com/pricing/details/managed-disks/)

### <a name="azure-disk-reservation"></a>Rezerwacja dysku platformy Azure

Rezerwacja dysku to opcja zakupu jednego roku pamięci masowej na dysku z wyprzedzeniem ze zniżką, co zmniejsza całkowity koszt. Kupując rezerwację dysku, należy wybrać określoną jednostkę SKU dysku w regionie docelowym, na przykład 10 dysków SSD premium P30 (1TiB) w regionie Wschodnie stany USA 2 na okres jednego roku. Środowisko rezerwacji jest podobne do zarezerwowanych wystąpień maszyny wirtualnej (VM). Możesz spakować rezerwacje maszyn wirtualnych i dysków, aby zmaksymalizować swoje oszczędności. Na razie usługa Azure Disks Reservation oferuje roczny plan zobowiązań dla jednostek SSD premium od P30 (1TiB) do P80 (32 TiB) we wszystkich regionach produkcyjnych. Aby uzyskać więcej informacji na temat cenowania dysków zarezerwowanych, zobacz [stronę cennika dysków platformy Azure](https://azure.microsoft.com/pricing/details/managed-disks/).