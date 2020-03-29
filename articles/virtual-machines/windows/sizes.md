---
title: Rozmiary maszyn wirtualnych systemu Windows na platformie Azure
description: Wyświetla listę różnych rozmiarów dostępnych dla maszyn wirtualnych z systemem Windows na platformie Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 398575cc5c3dea96aa644533eb6ce8a262d1981c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164495"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure

W tym artykule opisano dostępne rozmiary i opcje maszyn wirtualnych platformy Azure, których można używać do uruchamiania aplikacji i obciążeń systemu Windows. Zawiera również zagadnienia dotyczące wdrażania, o których należy pamiętać, gdy planujesz używać tych zasobów.  Ten artykuł jest również dostępny dla [maszyn wirtualnych systemu Linux](../linux/sizes.md).

| Typ | Rozmiary | Opis |
|------|-------|-------------|
| [Zastosowania ogólne](../sizes-general.md) | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2 | Zrównoważony stosunek procesora do pamięci. Idealne rozwiązanie na potrzeby testowania i wdrażania, małych i średnich baz danych oraz serwerów internetowych o małym lub średnim ruchu. |
| [Optymalizacja pod kątem obliczeń](../sizes-compute.md) | Fsv2 (właśc. | Wysoki współczynnik procesora cpu do pamięci. Dobre dla serwerów sieci web o średnim natężeniu ruchu, urządzeń sieciowych, procesów wsadowych i serwerów aplikacji. |
| [Optymalizacja pod kątem pamięci](../sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Mv2, M, DSv2, Dv2 | Wysoki współczynnik pamięci do procesora. Opcja bardzo dobra w przypadku serwerów relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizowania w pamięci. |
| [Optymalizacja pod kątem magazynu](../sizes-storage.md)  | Lsv2 ( Lsv2 ) | Wysoka przepustowość dysku i IO idealne dla baz danych Big Data, SQL, NoSQL, hurtowni danych i dużych transakcyjnych baz danych.  |
| [Procesor GPU](../sizes-gpu.md) | NC, NCv2, NCv3, ND, NDv2 (wersja zapoznawcza), NV, NVv3, NVv4 | Wyspecjalizowane maszyny wirtualne przeznaczone do intensywnego renderowania grafiki i edycji wideo, a także szkolenia modeli i wnioskowania (ND) z głębokim uczeniem. Dostępne z pojedynczymi lub wieloma procesorami graficznymi. |
| [Obliczenia o wysokiej wydajności](../sizes-hpc.md) | HB, HBv2, HC, H | Nasze najszybsze i najpotężniejsze maszyny wirtualne cpu z opcjonalnymi interfejsami sieciowymi o wysokiej przepustowości (RDMA). |

- Aby uzyskać informacje na temat cen różnych rozmiarów, zobacz [Ceny maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows).
- Aby wyświetlić ogólne limity dotyczące maszyn wirtualnych platformy Azure, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia.](../../azure-subscription-service-limits.md)
- Koszty magazynowania są obliczane osobno na podstawie wykorzystanych stron na koncie magazynu. Aby uzyskać szczegółowe informacje, [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).
- Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](../acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej w jednostkach SKU platformy Azure.

## <a name="rest-api"></a>Interfejs API REST

Aby uzyskać informacje na temat używania interfejsu API REST do wykonywania zapytań o rozmiary maszyn wirtualnych, zobacz następujące informacje:

- [Lista dostępnych rozmiarów maszyn wirtualnych do zmiany rozmiaru](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Lista rozmiarów dostępnych maszyn wirtualnych dla subskrypcji](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Wyświetlanie rozmiarów dostępnych maszyn wirtualnych w zestawie dostępności](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](../acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej w jednostkach SKU platformy Azure.

## <a name="benchmark-scores"></a>Wyniki testów porównawczych

Dowiedz się więcej o wydajności obliczeniowej maszyn wirtualnych z systemem Windows przy użyciu [wyników testów porównawczych CoreMark](compute-benchmark-scores.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o różnych dostępnych rozmiarach maszyn wirtualnych:

- [Zastosowania ogólne](../sizes-general.md)
- [Optymalizacja pod kątem obliczeń](../sizes-compute.md)
- [Optymalizacja pod kątem pamięci](../sizes-memory.md)
- [Optymalizacja pod kątem magazynu](../sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](../sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](../sizes-hpc.md)
- Sprawdź stronę [poprzedniej generacji](../sizes-previous-gen.md) dla serii A Standard, Dv1 (D1-4 i D11-14 v1) i A8-A11
