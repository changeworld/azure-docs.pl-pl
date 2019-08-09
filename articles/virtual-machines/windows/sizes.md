---
title: Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure | Microsoft Docs
description: Wyświetla różne rozmiary dostępne dla maszyn wirtualnych z systemem Windows na platformie Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/08/2019
ms.author: jonbeck
ms.openlocfilehash: 91c6d0446d47e118a4e607ff985e06ff5e43e1f7
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884053"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure

W tym artykule opisano dostępne rozmiary i opcje dla maszyn wirtualnych platformy Azure, za pomocą których można uruchamiać aplikacje i obciążenia systemu Windows. Zawiera również zagadnienia dotyczące wdrażania, które należy wziąć pod uwagę w przypadku planowania użycia tych zasobów.  Ten artykuł jest również dostępny dla [maszyn wirtualnych z systemem Linux](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


| Type                     | Rozmiary           |    Opis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Zastosowania ogólne](sizes-general.md)          | B, Dsv3, Dv3, Dasv3, Dav3, DSv2, Dv2, Av2, DC  | Zrównoważona moc procesora CPU w stosunku do pamięci. Idealne na potrzeby testowania i programowania, małych i średnich baz danych oraz serwerów sieci Web o niewielkim i średnim ruchu. |
| [Optymalizacja pod kątem obliczeń](sizes-compute.md)        | Fsv2           | Duża moc procesora CPU w stosunku do pamięci. Dobrze sprawdzają się w przypadku serwerów sieci Web o średnim ruchu, urządzeń sieciowych, procesów wsadowych i serwerów aplikacji.        |
| [Optymalizacja pod kątem pamięci](sizes-memory.md)         | Esv3, EV3, Easv3, Eav3, Mv2, M, DSv2, Dv2  | Duże proporcje pamięci i procesora CPU. Świetnie sprawdzają się w przypadku serwerów relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizowania w pamięci.                 |
| [Optymalizacja pod kątem magazynu](sizes-storage.md)        | Lsv2                | Wysoka przepływność dysku i doskonałe operacje we/wy dla danych Big Data, baz danych SQL, NoSQL, magazynowania danych i dużych transakcyjnych baz danych.  |
| [Procesor GPU](sizes-gpu.md)            | NC, NCv2, Seria NCV3, ND, NDv2 (wersja zapoznawcza), NV, NVv3 (wersja zapoznawcza) | Wyspecjalizowane maszyny wirtualne przeznaczone do intensywnego renderowania grafiki i edytowania wideo, a także szkolenia modelowe i inferencing (ND) z głębokie uczenie. Dostępne z pojedynczym lub wieloma procesorami GPU.       |
| [Obliczenia o wysokiej wydajności](sizes-hpc.md) | HB, HC, H | Maszyny wirtualne z najszybszymi i najbardziej wydajnymi procesorami CPU oraz, opcjonalnie, interfejsami sieciowymi zapewniającymi wysoką przepływność (RDMA). |


<br> 

- Aby uzyskać informacje o cenach różnych rozmiarów, zobacz [Virtual Machines Cennik](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). 
- Aby wyświetlić ogólne limity dotyczące maszyn wirtualnych platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../../azure-subscription-service-limits.md).
- Koszty magazynowania są obliczane osobno na podstawie wykorzystanych stron na koncie magazynu. Aby uzyskać szczegółowe informacje, [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).
- Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.


## <a name="rest-api"></a>Interfejs API REST

Aby uzyskać informacje na temat używania interfejsu API REST do wysyłania zapytań o rozmiary maszyn wirtualnych, zobacz następujące tematy:

- [Wyświetl dostępne rozmiary maszyn wirtualnych na potrzeby zmiany rozmiaru](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Wyświetlanie listy dostępnych rozmiarów maszyn wirtualnych dla subskrypcji](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Wyświetlanie listy dostępnych rozmiarów maszyn wirtualnych w zestawie dostępności](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.

## <a name="benchmark-scores"></a>Wyniki testów porównawczych

Dowiedz się więcej na temat wydajności obliczeń dla maszyn wirtualnych z systemem Windows przy użyciu [wyników testów porównawczych](compute-benchmark-scores.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o różnych dostępnych rozmiarach maszyn wirtualnych:
- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](../virtual-machines-windows-sizes-memory.md)
- [Optymalizacja pod kątem magazynu](../virtual-machines-windows-sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- Sprawdź [poprzednią generację](sizes-previous-gen.md) strony, aby zapoznać się ze standardem, Dv1 (D1-4 i D11-14 v1) oraz seriami A8-A11




