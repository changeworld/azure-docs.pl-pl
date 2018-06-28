---
title: Rozmiar maszyny Wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Wyświetla listę różnych rozmiarów dostępnej dla maszyn wirtualnych systemu Linux na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/22/2018
ms.author: jonbeck
ms.openlocfilehash: 3d5311bda4fd0133bea57fee245386d451deb5c4
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "34653896"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Rozmiary maszyn wirtualnych systemu Linux na platformie Azure
W tym artykule opisano dostępne rozmiary i opcje dla maszyn wirtualnych platformy Azure, który służy do uruchamiania aplikacji dla systemu Linux i obciążeń. Umożliwia także zagadnienia dotyczące wdrażania pod uwagę podczas planowania korzystać z tych zasobów. W tym artykule jest również dostępny do [maszyn wirtualnych Windows](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


| Typ                     | Rozmiary           |    Opis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Zastosowania ogólne](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, Av2  | Zrównoważona moc procesora CPU w stosunku do pamięci. Idealne na potrzeby testowania i programowania, małych i średnich baz danych oraz serwerów sieci Web o niewielkim i średnim ruchu. |
| [Optymalizacja pod kątem obliczeń](sizes-compute.md)        | F Fsv2, Fs,             | Duża moc procesora CPU w stosunku do pamięci. Dobrze sprawdzają się w przypadku serwerów sieci Web o średnim ruchu, urządzeń sieciowych, procesów wsadowych i serwerów aplikacji.        |
| [Optymalizacja pod kątem pamięci](sizes-memory.md)         | Esv3, Ev3, M, GS, G, DSv2, Dv2  | Wysoki współczynnik pamięci do Procesora. Świetnie sprawdzają się w przypadku serwerów relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizowania w pamięci.                 |
| [Optymalizacja pod kątem magazynu](sizes-storage.md)        | Ls                | Wysoka przepływność dysku i duża liczba operacji we/wy. Opcja idealna w przypadku danych big data oraz baz danych SQL i NoSQL.                                                         |
| [Procesor GPU](sizes-gpu.md)            | Wirtualizacją sieci, NC, NCv2, NCv3, ND            | Specjalne maszyny wirtualne przeznaczone dla duże Renderowanie grafiki i wideo edycji, a także model szkolenia i inferencing (ND) przy użyciu bezpośrednich learning. Dostępne z jednego lub wielu procesorów graficznych.       |
| [Obliczenia o wysokiej wydajności](sizes-hpc.md) | H       | Maszyny wirtualne z najszybszymi i najbardziej wydajnymi procesorami CPU oraz, opcjonalnie, interfejsami sieciowymi zapewniającymi wysoką przepływność (RDMA). 

<br>

- Aby uzyskać informacje o cenach o różnych rozmiarach, zobacz [cennik maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Dostępność rozmiarów maszyn wirtualnych w regionach platformy Azure, zobacz [produkty, które są dostępne w regionie](https://azure.microsoft.com/regions/services/).
- Aby wyświetlić ogólne limity na maszynach wirtualnych Azure, zobacz [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](../../azure-subscription-service-limits.md).
- Dowiedz się więcej na temat [jednostki (ACU) rozwiązań usługi obliczenia Azure](acu.md) ułatwia porównanie wydajności obliczeniowej różnych jednostki SKU Azure.


## <a name="rest-api"></a>Interfejs API REST

Informacji dotyczących korzystania z interfejsu API REST do zapytania rozmiarów maszyn wirtualnych zobacz następujące tematy:

- [Lista dostępne rozmiary maszyny wirtualnej do zmiany rozmiaru](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Lista dostępne rozmiary maszyny wirtualnej w ramach subskrypcji](https://docs.microsoft.com/rest/api/compute/virtualmachines/listall)
- [Lista dostępne rozmiary maszyny wirtualnej w zestawie dostępności](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Dowiedz się więcej na temat [jednostki (ACU) rozwiązań usługi obliczenia Azure](acu.md) ułatwia porównanie wydajności obliczeniowej różnych jednostki SKU Azure.

## <a name="benchmark-scores"></a>Wyniki testów porównawczych

Dowiedz się więcej na temat obliczeniowe wydajności dla maszyn wirtualnych systemu Linux przy użyciu [wyniki testów porównawczych CoreMark](compute-benchmark-scores.md).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o różnych rozmiarów maszyn wirtualnych, które są dostępne:
- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Procesor GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- Sprawdź [generowania Wstecz](sizes-previous-gen.md) strony dla standardowych A Dv1 D1 4 i D11 14 v1 i serii A8 A11



