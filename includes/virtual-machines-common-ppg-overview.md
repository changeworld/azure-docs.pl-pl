---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/28/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 9832ae6010f262322328ec22876a35c89e1bedca
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850329"
---
Umieszczanie maszyn wirtualnych w jednym regionie zmniejsza odległość fizyczną między wystąpieniami. Umieszczenie ich w ramach pojedynczej strefy dostępności spowoduje również, że zostaną one fizycznie bliżej siebie. Jednak w miarę zwiększania się rozmiaru platformy Azure jedna strefa dostępności może obejmować wiele fizycznych centrów danych, co może skutkować opóźnieniami sieciowymi wpływającymi na aplikację. 

Aby zapewnić, że maszyny wirtualne będą możliwie jak najbliżej, osiągając najniższe możliwe opóźnienie, należy wdrożyć je w obrębie grupy umieszczania sąsiedztwa.

Grupa umieszczania bliskości jest grupą logiczną używaną w celu upewnienia się, że zasoby obliczeniowe platformy Azure znajdują się fizycznie blisko siebie. Grupy umieszczania zbliżeniowe są przydatne w przypadku obciążeń, w których jest wymagane małe opóźnienia.


- Małe opóźnienia między samodzielnymi maszynami wirtualnymi.
- Małe opóźnienia między maszynami wirtualnymi w jednym zestawie dostępności lub zestawem skalowania maszyn wirtualnych. 
- Małe opóźnienia między samodzielnymi maszynami wirtualnymi, maszynami wirtualnymi w wielu zestawach dostępności lub wieloma zestawami skalowania. W jednej grupie umieszczania można korzystać z wielu zasobów obliczeniowych w celu zebrania aplikacji wielowarstwowej. 
- Małe opóźnienia między wieloma warstwami aplikacji przy użyciu różnych typów sprzętu. Na przykład uruchomienie zaplecza przy użyciu serii M w zestawie dostępności i frontonu w wystąpieniu serii D w zestawie skalowania w pojedynczej grupie umieszczania sąsiedztwa.

> [!IMPORTANT]
> Grupy umieszczania w sąsiedztwie są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Grupy umieszczania w sąsiedztwie nie są dostępne w tych regionach w ramach wersji zapoznawczej: **Japonia Wschodnia**, **Australia Wschodnia** i **Indie Środkowe**.


## <a name="best-practices"></a>Najlepsze praktyki 
- W przypadku najmniejszego opóźnienia należy używać grup umieszczania sąsiedztwa wraz z przyspieszoną siecią. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu przyspieszonej sieci](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu przyspieszonej sieci](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Wdróż wszystkie rozmiary maszyn wirtualnych w jednym szablonie Menedżera zasobów. Aby uniknąć lądowania na sprzęcie, który nie obsługuje wszystkich potrzebnych jednostek SKU i rozmiarów maszyn wirtualnych, należy uwzględnić wszystkie warstwy aplikacji w jednym szablonie, tak aby wszystkie te aplikacje były wdrażane w tym samym czasie.
- Jeśli tworzysz skrypty do wdrożenia przy użyciu programu PowerShell, interfejsu wiersza polecenia lub zestawu SDK, może wystąpić błąd `OverconstrainedAllocationRequest`alokacji. W takim przypadku należy zatrzymać/cofnąć przydział wszystkich istniejących maszyn wirtualnych i zmienić sekwencję w skrypcie wdrażania, aby rozpocząć od jednostki SKU/rozmiaru maszyny wirtualnej, która nie powiodła się. 
- Podczas ponownie korzystania z istniejącej grupy umieszczania, z której usunięto maszyny wirtualne, poczekaj na pełne zakończenie operacji usuwania przed dodaniem do niej maszyn wirtualnych.
- Jeśli opóźnienie jest pierwszym priorytetem, umieść maszyny wirtualne w grupie umieszczania bliskości i całe rozwiązanie w strefie dostępności. Ale jeśli odporność ma najwyższy priorytet, rozłożenie wystąpień w wielu strefach dostępności (pojedyncza Grupa położenia sąsiedztwa nie może obejmować stref).