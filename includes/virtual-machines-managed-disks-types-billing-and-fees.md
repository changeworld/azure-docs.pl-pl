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
ms.openlocfilehash: 42ab8be45d4086589f0793531003700e7552a440
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64744417"
---
**Wychodzące transfery danych**: [Wychodzące transfery danych](https://azure.microsoft.com/pricing/details/bandwidth/) (dane wychodzące z centrów danych platformy Azure) Naliczanie opłat za zużycie przepustowości.

**Transakcje**: Są naliczane dla liczby transakcji, które można wykonać na standardowa dysku zarządzanego. Dla standardowych dysków SSD rozmiar jednostki we/wy 256 KiB służy do księgowość liczby transakcji. Większego rozmiaru operacji We/Wy są liczone jako wiele operacji We/Wy o rozmiarze 256 KiB. Dla standardowych dysków twardych każda operacja We/Wy jest traktowany jako pojedynczą transakcję, bez względu na rozmiar operacji We/Wy.

Szczegółowe informacje na temat cennika usługi Managed Disks, w tym koszty transakcji, zobacz [cennika usługi Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Największa opłata rezerwacji maszyny Wirtualnej z dysków SSD

Maszyny wirtualne platformy Azure mają możliwość do wskazania, jeśli są one zgodne z ultra dysków SSD. Największa dysku zgodnej maszyny Wirtualnej przydziela dedykowaną pojemność przepustowości między wystąpieniem maszyny Wirtualnej obliczeniowe i jednostki skali magazynu bloku, aby zoptymalizować wydajność i zmniejszyć opóźnienie. Dodanie tej funkcji na wynikach maszyn wirtualnych w Twoich rękach rezerwacji, która nakłada się tylko włączenie możliwości ultra dysku na maszynie Wirtualnej bez podłączania ultra dysku do niego. Gdy ultra dysk jest podłączony do ultra dysku zgodnej maszyny Wirtualnej, ta opłata nie zostanie zastosowana. Ta opłata jest na procesor wirtualny vCPU, którego obsługę zainicjowano na maszynie Wirtualnej.

Zapoznaj się [Azure Disks stronę z cennikiem](https://azure.microsoft.com/pricing/details/managed-disks/) ultra dysku w szczegóły cennika dysków.