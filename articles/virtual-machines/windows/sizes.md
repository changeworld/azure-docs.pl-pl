---
title: Rozmiary maszyn wirtualnych Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Wyświetla listę różnych rozmiarów dostępnymi dla maszyn wirtualnych Windows na platformie Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/14/2018
ms.author: jonbeck
ms.openlocfilehash: b9a81fb451164aa58ca4ba943ed000c14d9f87cc
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873579"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Rozmiary maszyn wirtualnych Windows na platformie Azure

W tym artykule opisano dostępne rozmiary i opcje dla maszyn wirtualnych platformy Azure używane do uruchamiania obciążeń i aplikacji Windows. Zapewnia również zagadnienia dotyczące wdrażania pod uwagę podczas planowania tych zasobów.  W tym artykule jest także dostępna dla [maszyn wirtualnych systemu Linux](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


| Type                     | Rozmiary           |    Opis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Zastosowania ogólne](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, Av2, DC | Zrównoważona moc procesora CPU w stosunku do pamięci. Idealne na potrzeby testowania i programowania, małych i średnich baz danych oraz serwerów sieci Web o niewielkim i średnim ruchu. |
| [Optymalizacja pod kątem obliczeń](sizes-compute.md)        | Fsv2          | Duża moc procesora CPU w stosunku do pamięci. Dobrze sprawdzają się w przypadku serwerów sieci Web o średnim ruchu, urządzeń sieciowych, procesów wsadowych i serwerów aplikacji.        |
| [Optymalizacja pod kątem pamięci](../virtual-machines-windows-sizes-memory.md)         | Esv3, Ev3, M, GS, G, DSv2, Dv2  | Wysoki współczynnik pamięci na Procesor. Świetnie sprawdzają się w przypadku serwerów relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizowania w pamięci.                 |
| [Optymalizacja pod kątem magazynu](../virtual-machines-windows-sizes-storage.md)        | Lsv2, Ls                | Wysoka przepływność dysku i we/wy idealne rozwiązanie w przypadku obsługi dużych ilości danych, SQL, NoSQL baz danych, magazynowanie danych i dużych transakcyjnych baz danych.  |
| [Procesor GPU](sizes-gpu.md)            | NV NVv2, NC, NCv2, NCv3, ND, NDv2 (wersja zapoznawcza)            | Wyspecjalizowane maszyny wirtualne przeznaczone dla intensywnego renderowania grafiki i edytowania materiałów wideo, a także modelowanie szkolenia oraz wnioskowania (ND) przy użyciu uczenia głębokiego. Dostępne za pomocą jednego lub wielu procesorów GPU.       |
| [Obliczenia o wysokiej wydajności](sizes-hpc.md) | H       | Maszyny wirtualne z najszybszymi i najbardziej wydajnymi procesorami CPU oraz, opcjonalnie, interfejsami sieciowymi zapewniającymi wysoką przepływność (RDMA). |


<br> 

- Aby uzyskać informacje o cenach poszczególnych rozmiarów, zobacz [cennik usługi Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). 
- Aby wyświetlić ogólne limity na maszynach wirtualnych platformy Azure, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../../azure-subscription-service-limits.md).
- Koszty magazynowania są obliczane osobno na podstawie wykorzystanych stron na koncie magazynu. Aby uzyskać szczegółowe informacje [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).
- Dowiedz się więcej o tym, jak [usługi Azure compute jednostki (ACU)](acu.md) pozwalają porównać wydajności obliczeń w jednostkach SKU platformy Azure.


## <a name="rest-api"></a>Interfejs API REST

Instrukcje dotyczące rozmiarów maszyn wirtualnych przy użyciu interfejsu API REST do wykonywania zapytań zobacz następujące tematy:

- [Lista dostępne rozmiary maszyny wirtualnej do zmiany rozmiaru](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Lista dostępne rozmiary maszyny wirtualnej dla subskrypcji](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Lista dostępne rozmiary maszyny wirtualnej w zestawie dostępności](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Dowiedz się więcej o tym, jak [usługi Azure compute jednostki (ACU)](acu.md) pozwalają porównać wydajności obliczeń w jednostkach SKU platformy Azure.

## <a name="benchmark-scores"></a>Wyniki testów porównawczych

Dowiedz się więcej na temat obliczeń wydajność przy użyciu maszyn wirtualnych Windows [wyniki testów porównawczych CoreMark](compute-benchmark-scores.md).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat różnych rozmiarów maszyn wirtualnych, które są dostępne:
- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](../virtual-machines-windows-sizes-memory.md)
- [Optymalizacja pod kątem magazynu](../virtual-machines-windows-sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- Sprawdź [poprzednia generacja](sizes-previous-gen.md) strona w wersji Standard, Dv1 (D1 – 4 i D11-14 v1) i serii A8 – A11




