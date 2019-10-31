---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/30/2019
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: 3215f5952daef053c94432bc8fdef15e1775047a
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171099"
---
Umieszczanie maszyn wirtualnych w jednym regionie zmniejsza odległość fizyczną między wystąpieniami. Umieszczenie ich w ramach pojedynczej strefy dostępności spowoduje również, że zostaną one fizycznie bliżej siebie. Jednak w miarę zwiększania się rozmiaru platformy Azure jedna strefa dostępności może obejmować wiele fizycznych centrów danych, co może skutkować opóźnieniami sieciowymi wpływającymi na aplikację. 

Aby zapewnić, że maszyny wirtualne będą możliwie jak najbliżej, osiągając najniższe możliwe opóźnienie, należy wdrożyć je w obrębie grupy umieszczania sąsiedztwa.

Grupa umieszczania bliskości jest grupą logiczną używaną w celu upewnienia się, że zasoby obliczeniowe platformy Azure znajdują się fizycznie blisko siebie. Grupy umieszczania zbliżeniowe są przydatne w przypadku obciążeń, w których jest wymagane małe opóźnienia.


- Małe opóźnienia między samodzielnymi maszynami wirtualnymi.
- Małe opóźnienia między maszynami wirtualnymi w jednym zestawie dostępności lub zestawem skalowania maszyn wirtualnych. 
- Małe opóźnienia między samodzielnymi maszynami wirtualnymi, maszynami wirtualnymi w wielu zestawach dostępności lub wieloma zestawami skalowania. W jednej grupie umieszczania można korzystać z wielu zasobów obliczeniowych w celu zebrania aplikacji wielowarstwowej. 
- Małe opóźnienia między wieloma warstwami aplikacji przy użyciu różnych typów sprzętu. Na przykład uruchomienie zaplecza przy użyciu serii M w zestawie dostępności i frontonu w wystąpieniu serii D w zestawie skalowania w pojedynczej grupie umieszczania sąsiedztwa.


![Ilustracja przedstawiająca grupy położenia zbliżeniowe](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Używanie grup umieszczania w sąsiedztwie 

Grupa umieszczania bliskości jest nowym typem zasobów na platformie Azure. Należy utworzyć je przed użyciem z innymi zasobami. Po utworzeniu można go używać z maszynami wirtualnymi, zestawami dostępności lub zestawami skalowania maszyn wirtualnych. Należy określić grupę umieszczania bliskości podczas tworzenia zasobów obliczeniowych, podając identyfikator grupy umieszczania sąsiedztwa. 

Możesz również przenieść istniejący zasób do grupy umieszczania sąsiedztwa. Podczas przenoszenia zasobu do grupy umieszczania w sąsiedztwie należy najpierw zatrzymać (cofnąć przydział) element zawartości, ponieważ zostanie on ponownie wdrożony w innym centrum danych w regionie, tak aby spełniał ograniczenie wspólnej lokalizacji. 

W przypadku zestawów dostępności i zestawów skalowania maszyn wirtualnych należy ustawić grupę umieszczania sąsiedztwa na poziomie zasobu, a nie na poszczególnych maszynach wirtualnych. 

Grupa położenia sąsiedztwa to ograniczenie między lokalizacjami, a nie mechanizm przypinania. Jest przypięty do określonego centrum danych z wdrożeniem pierwszego zasobu, aby go użyć. Po zatrzymaniu lub usunięciu wszystkich zasobów przy użyciu grupy umieszczania w sąsiedztwie nie jest już przypięty. W związku z tym podczas korzystania z grupy umieszczania bliskości z wieloma seriami maszyn wirtualnych ważne jest, aby określić wszystkie wymagane typy z góry w szablonie, gdy jest to możliwe, lub postępować zgodnie z sekwencją wdrożenia, która poprawi szansę na pomyślne wdrożenie. Jeśli wdrożenie nie powiedzie się, uruchom ponownie wdrożenie z rozmiarem maszyny wirtualnej, który nie powiódł się, jako pierwszy rozmiar do wdrożenia.


## <a name="best-practices"></a>Najlepsze praktyki 
- W przypadku najmniejszego opóźnienia należy używać grup umieszczania sąsiedztwa wraz z przyspieszoną siecią. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu przyspieszonej sieci](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu przyspieszonej sieci](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Wdróż wszystkie rozmiary maszyn wirtualnych w jednym szablonie. Aby uniknąć lądowania na sprzęcie, który nie obsługuje wszystkich potrzebnych jednostek SKU i rozmiarów maszyn wirtualnych, należy uwzględnić wszystkie warstwy aplikacji w jednym szablonie, tak aby wszystkie te aplikacje były wdrażane w tym samym czasie.
- Jeśli tworzysz skrypty do wdrożenia przy użyciu programu PowerShell, interfejsu wiersza polecenia lub zestawu SDK, możesz uzyskać błąd alokacji `OverconstrainedAllocationRequest`. W takim przypadku należy zatrzymać/cofnąć przydział wszystkich istniejących maszyn wirtualnych i zmienić sekwencję w skrypcie wdrażania, aby rozpocząć od jednostki SKU/rozmiaru maszyny wirtualnej, która nie powiodła się. 
- Podczas ponownie korzystania z istniejącej grupy umieszczania, z której usunięto maszyny wirtualne, poczekaj na pełne zakończenie operacji usuwania przed dodaniem do niej maszyn wirtualnych.
- Jeśli opóźnienie jest pierwszym priorytetem, umieść maszyny wirtualne w grupie umieszczania bliskości i całe rozwiązanie w strefie dostępności. Ale jeśli odporność ma najwyższy priorytet, rozłożenie wystąpień w wielu strefach dostępności (pojedyncza Grupa położenia sąsiedztwa nie może obejmować stref).