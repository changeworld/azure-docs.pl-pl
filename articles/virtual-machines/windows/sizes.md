---
title: Rozmiary maszyn wirtualnych systemu Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Wyświetla listę różnych rozmiarów dostępnej dla maszyn wirtualnych systemu Windows na platformie Azure.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jonbeck
ms.openlocfilehash: 91cd1cc746952ef8f442165b167f88f626870c70
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Rozmiary maszyn wirtualnych systemu Windows na platformie Azure

W tym artykule opisano dostępne rozmiary i opcje dla maszyn wirtualnych platformy Azure, używanego do uruchamiania aplikacji systemu Windows i obciążeń. Umożliwia także zagadnienia dotyczące wdrażania pod uwagę podczas planowania korzystać z tych zasobów.  W tym artykule jest również dostępny do [maszyn wirtualnych systemu Linux](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


| Typ                     | Rozmiary           |    Opis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Zastosowania ogólne](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7 | Zrównoważona moc procesora CPU w stosunku do pamięci. Idealne na potrzeby testowania i programowania, małych i średnich baz danych oraz serwerów sieci Web o niewielkim i średnim ruchu. |
| [Optymalizacja pod kątem obliczeń](sizes-compute.md)        | F Fsv2, Fs,             | Duża moc procesora CPU w stosunku do pamięci. Dobrze sprawdzają się w przypadku serwerów sieci Web o średnim ruchu, urządzeń sieciowych, procesów wsadowych i serwerów aplikacji.        |
| [Optymalizacja pod kątem pamięci](../virtual-machines-windows-sizes-memory.md)         | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Wysoki współczynnik pamięci do Procesora. Świetnie sprawdzają się w przypadku serwerów relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizowania w pamięci.                 |
| [Optymalizacja pod kątem magazynu](../virtual-machines-windows-sizes-storage.md)        | Ls                | Wysoka przepływność dysku i duża liczba operacji we/wy. Opcja idealna w przypadku danych big data oraz baz danych SQL i NoSQL.                                                         |
| [Procesor GPU](sizes-gpu.md)            | Wirtualizacją sieci, NC, NCv2, NCv3, ND            | Specjalne maszyny wirtualne przeznaczone dla duże Renderowanie grafiki i wideo edycji, a także model szkolenia i inferencing (ND) przy użyciu bezpośrednich learning. Dostępne z jednego lub wielu procesorów graficznych.       |
| [Obliczenia o wysokiej wydajności](sizes-hpc.md) | H, A8-11          | Maszyny wirtualne z najszybszymi i najbardziej wydajnymi procesorami CPU oraz, opcjonalnie, interfejsami sieciowymi zapewniającymi wysoką przepływność (RDMA). 

<br> 

- Aby uzyskać informacje o cenach o różnych rozmiarach, zobacz [cennik maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). 
- Aby wyświetlić ogólne limity na maszynach wirtualnych Azure, zobacz [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](../../azure-subscription-service-limits.md).
- Koszty magazynowania są obliczane osobno na podstawie wykorzystanych stron na koncie magazynu. Aby uzyskać szczegółowe informacje [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).
- Dowiedz się więcej na temat [jednostki (ACU) rozwiązań usługi obliczenia Azure](acu.md) ułatwia porównanie wydajności obliczeniowej różnych jednostki SKU Azure.



## <a name="rest-api"></a>Interfejs API REST

Informacji dotyczących korzystania z interfejsu API REST do zapytania rozmiarów maszyn wirtualnych zobacz następujące tematy:

- [Lista dostępne rozmiary maszyny wirtualnej do zmiany rozmiaru](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-for-resizing)
- [Lista dostępne rozmiary maszyny wirtualnej w ramach subskrypcji](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)
- [Lista dostępne rozmiary maszyny wirtualnej w zestawie dostępności](
https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-availability-set)

## <a name="acu"></a>ACU

Dowiedz się więcej na temat [jednostki (ACU) rozwiązań usługi obliczenia Azure](acu.md) ułatwia porównanie wydajności obliczeniowej różnych jednostki SKU Azure.

## <a name="benchmark-scores"></a>Wyniki testu

Dowiedz się więcej na temat obliczeniowe wydajności dla maszyn wirtualnych systemu Windows przy użyciu [wyniki testów porównawczych CoreMark](compute-benchmark-scores.md).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o różnych rozmiarów maszyn wirtualnych, które są dostępne:
- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](../virtual-machines-windows-sizes-memory.md)
- [Optymalizacja pod kątem magazynu](../virtual-machines-windows-sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)




