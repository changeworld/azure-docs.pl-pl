---
title: Zagadnienia dotyczące projektu dla zestawów skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się więcej o zagadnieniach projektowych dla zestawów skalowania maszyny wirtualnej platformy Azure. Porównaj operacje zestawów skalowania z funkcjami maszyny Wirtualnej.
keywords: linuxowa maszyna wirtualna, zestawy skalowania maszyny wirtualnej
author: mayanknayar
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: vm-linux
ms.topic: conceptual
ms.date: 06/01/2017
ms.author: manayar
ms.openlocfilehash: b427319fdba634ea3c61681baa30547450709dc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250779"
---
# <a name="design-considerations-for-scale-sets"></a>Zagadnienia dotyczące projektu dla zestawów skalowania
W tym artykule omówiono zagadnienia dotyczące projektu dla zestawów skalowania maszyny wirtualnej. Aby uzyskać informacje o tym, czym są zestawy skalowania maszyny wirtualnej, zobacz [Omówienie zestawów skalowania maszyn wirtualnych](virtual-machine-scale-sets-overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Kiedy używać zestawów skalowania zamiast maszyn wirtualnych?
Ogólnie rzecz biorąc zestawy skalowania są przydatne do wdrażania infrastruktury o wysokiej dostępności, gdzie zestaw maszyn ma podobną konfigurację. Jednak niektóre funkcje są dostępne tylko w zestawach skalowania, podczas gdy inne funkcje są dostępne tylko w maszynach wirtualnych. Aby podjąć świadomą decyzję o tym, kiedy korzystać z każdej technologii, należy najpierw przyjrzeć się niektórym często używanym funkcjom, które są dostępne w zestawach skalowania, ale nie w maszynach wirtualnych:

### <a name="scale-set-specific-features"></a>Funkcje specyficzne dla skalowania

- Po określeniu konfiguracji zestawu skalowania można zaktualizować właściwość *pojemności,* aby równolegle wdrażać więcej maszyn wirtualnych. Ten proces jest lepszy niż pisanie skryptu do organizowania wdrażania wielu pojedynczych maszyn wirtualnych równolegle.
- Za [pomocą skalowania automatycznego platformy Azure można automatycznie skalować zestaw skalowania,](./virtual-machine-scale-sets-autoscale-overview.md) ale nie pojedyncze maszyny wirtualne.
- Można [ponownie ustawić skalę maszyn wirtualnych,](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage) ale [nie pojedyncze maszyny wirtualne](https://docs.microsoft.com/rest/api/compute/virtualmachines).
- Można [overprovision](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning) skalowanie zestaw maszyn wirtualnych dla zwiększenia niezawodności i krótszy czas wdrażania. Nie można przeprosować poszczególnych maszyn wirtualnych, chyba że piszesz kod niestandardowy, aby wykonać tę akcję.
- Można określić [zasady uaktualniania,](./virtual-machine-scale-sets-upgrade-scale-set.md) aby ułatwić wdrażanie uaktualnień na maszynach wirtualnych w zestawie skalowania. W poszczególnych maszynach wirtualnych należy samodzielnie zorganizować aktualizacje.

### <a name="vm-specific-features"></a>Funkcje specyficzne dla maszyny Wirtualnej

Niektóre funkcje są obecnie dostępne tylko w maszynach wirtualnych:

- Obraz można przechwycić z pojedynczej maszyny Wirtualnej, ale nie z maszyny Wirtualnej w zestawie skalowania.
- Można migrować pojedynczą maszynę wirtualną z dysków macierzystych na dyski zarządzane, ale nie można migrować wystąpień maszyn wirtualnych w zestawie skalowania.
- Publiczne adresy IP IPv6 można przypisać do poszczególnych kart interfejsu sieci wirtualnej maszyny Wirtualnej( NIC), ale nie można tego zrobić w przypadku wystąpień maszyn wirtualnych w zestawie skalowania. Publiczne adresy IP protokołu IPv6 można przypisać do modułów równoważenia obciążenia przed poszczególnymi maszynami wirtualnymi lub zestawem skalowania maszyn wirtualnych.

## <a name="storage"></a>Magazyn

### <a name="scale-sets-with-azure-managed-disks"></a>Zestawy skalowania z dyskami zarządzanymi platformy Azure
Zestawy skalowania można tworzyć za pomocą [dysków zarządzanych platformy Azure](../virtual-machines/windows/managed-disks-overview.md) zamiast tradycyjnych kont magazynu platformy Azure. Dyski zarządzane zapewniają następujące korzyści:
- Nie trzeba wstępnie utworzyć zestaw kont magazynu platformy Azure dla maszyn wirtualnych zestawu skalowania.
- Można zdefiniować [dołączone dyski danych](virtual-machine-scale-sets-attached-disks.md) dla maszyn wirtualnych w zestawie skalowania.
- Zestawy skalowania można skonfigurować tak, aby [obsługiwały do 1000 maszyn wirtualnych w zestawie.](virtual-machine-scale-sets-placement-groups.md) 

Jeśli masz istniejący szablon, możesz również [zaktualizować szablon, aby używał dysków zarządzanych](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Magazyn zarządzany przez użytkownika
Zestaw skalowania, który nie jest zdefiniowany za pomocą dysków zarządzanych platformy Azure, opiera się na kontach magazynu utworzonych przez użytkownika do przechowywania dysków systemu operacyjnego maszyn wirtualnych w zestawie. Współczynnik 20 maszyn wirtualnych na konto magazynu lub mniej jest zalecane w celu osiągnięcia maksymalnej we/wy, a także skorzystać z _nadmiernej obsługi administracyjnej_ (patrz poniżej). Zaleca się również rozmieszczenie początkowych znaków nazw kont magazynu w alfabecie. Pomaga to rozłożyć obciążenie na różne systemy wewnętrzne. 


## <a name="overprovisioning"></a>Nadmierna aprowizowanie
Skalowanie zestawów obecnie domyślnie "overprovisioning" maszyn wirtualnych. Po włączeniu nadmiernej obsługi, zestaw skalowania faktycznie obraca się więcej maszyn wirtualnych niż żądano, a następnie usuwa dodatkowe maszyny wirtualne po pomyślnym zainicjowaniu żądanej liczby maszyn wirtualnych. Nadmierna aprowisja zwiększa wskaźniki powodzenia inicjowania obsługi administracyjnej i skraca czas wdrażania. Nie są naliczane za dodatkowe maszyny wirtualne i nie są wliczane do limitów przydziału.

Podczas overprovisioning poprawia wskaźniki sukcesu inicjowania obsługi administracyjnej, może to spowodować mylące zachowanie dla aplikacji, która nie jest przeznaczona do obsługi dodatkowych maszyn wirtualnych pojawiających się, a następnie znikają. Aby wyłączyć nadawanie, upewnij się, że w `"overprovision": "false"`szablonie jest następujący ciąg znaków: . Więcej szczegółów można znaleźć w [dokumentacji interfejsu API Zestawu skalowania REST](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Jeśli zestaw skalowania używa magazynu zarządzanego przez użytkownika i wyłączysz nadmierne aprowizowanie, możesz mieć więcej niż 20 maszyn wirtualnych na konto magazynu, ale nie zaleca się przekraczania 40 ze względu na wydajność we/wy. 

## <a name="limits"></a>Limity
Zestaw skalowania zbudowany na obrazie portalu Marketplace (nazywanym również obrazem platformy) i skonfigurowany do używania dysków zarządzanych platformy Azure obsługuje pojemność do 1000 maszyn wirtualnych. Jeśli skonfigurować zestaw skalowania do obsługi więcej niż 100 maszyn wirtualnych, nie wszystkie scenariusze działają tak samo (na przykład równoważenia obciążenia). Aby uzyskać więcej informacji, zobacz [Praca z dużymi zestawami skalowania maszyn wirtualnych](virtual-machine-scale-sets-placement-groups.md). 

Zestaw skalowania skonfigurowany z kontami magazynu zarządzanymi przez użytkownika jest obecnie ograniczony do 100 maszyn wirtualnych (a dla tej skali zalecane jest 5 kont magazynu).

Zestaw skalowania zbudowany na obrazie niestandardowym (zbudowany przez Ciebie) może mieć pojemność do 600 maszyn wirtualnych po skonfigurowaniu z dyskami zarządzanymi platformy Azure. Jeśli zestaw skalowania jest skonfigurowany z kontami magazynu zarządzanymi przez użytkownika, musi utworzyć wszystkie dyski VHD dysku systemu operacyjnego w ramach jednego konta magazynu. W rezultacie maksymalna zalecana liczba maszyn wirtualnych w zestawie skalowania zbudowany na obrazie niestandardowym i magazynie zarządzanym przez użytkownika wynosi 20. Jeśli wyłączysz nadawanie, możesz przejść do 40.

Aby uzyskać więcej maszyn wirtualnych niż pozwalają na to te limity, należy wdrożyć wiele zestawów skalowania, jak pokazano w [tym szablonie](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

