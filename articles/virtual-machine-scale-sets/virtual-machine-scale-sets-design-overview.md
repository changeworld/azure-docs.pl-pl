---
title: Zagadnienia dotyczące projektu zestawów skalowania maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zagadnień projektowych dotyczących usługi Azure Virtual Machine Scale Sets
keywords: zestawy skalowania maszyn wirtualnych systemu Linux maszyny wirtualnej,
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: manayar
ms.openlocfilehash: 67bbad7e73f33d73d4c3f1d4f7e5599d2ef914e3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60618476"
---
# <a name="design-considerations-for-scale-sets"></a>Zagadnienia dotyczące projektowania dla zestawów skalowania
W tym artykule omówiono zagadnienia dotyczące projektowania dla zestawów skalowania maszyn wirtualnych. Aby dowiedzieć się, jak co to są zestawy skalowania maszyn wirtualnych, zapoznaj się [omówienie zestawów skalowania maszyn wirtualnych](virtual-machine-scale-sets-overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Kiedy należy używać skalowania zestawy zamiast maszyn wirtualnych?
Ogólnie rzecz biorąc zestawy skalowania są przydatne w przypadku wdrożenia o wysokiej dostępności infrastruktury gdzie zestawu maszyn ma podobnej konfiguracji. Jednak niektóre funkcje są dostępne tylko w zestawach skalowania, podczas gdy inne funkcje są dostępne tylko w maszynach wirtualnych. Aby można było podjąć świadomą decyzję o tym, kiedy należy używać tych technologii, należy najpierw zapoznaj się niektóre z najczęściej używanych funkcji, które są dostępne w zestawach skalowania, ale nie maszyn wirtualnych:

### <a name="scale-set-specific-features"></a>Funkcje specyficzne dla zestawu skalowania

- Po określeniu konfiguracji zestawu skalowania, możesz zaktualizować *pojemności* właściwości, aby wdrożyć więcej maszyn wirtualnych w sposób równoległy. Ten proces jest lepsza niż pisania skryptu do organizowania wdrożenie wielu maszyn wirtualnych z poszczególnych równolegle.
- Możesz [Użyj skalowania automatycznego platformy Azure, aby automatycznie skalować zestaw skalowania](./virtual-machine-scale-sets-autoscale-overview.md) , ale nie do poszczególnych maszyn wirtualnych.
- Możesz [maszyn wirtualnych w zestawie skalowania odtworzenia z obrazu](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage) , ale [nie do poszczególnych maszyn wirtualnych](https://docs.microsoft.com/rest/api/compute/virtualmachines).
- Możesz [overprovision](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning) zestawu skalowania maszyn wirtualnych do zwiększenia niezawodności i szybciej czas wdrażania. Nie można overprovision poszczególne maszyny wirtualne, chyba że napisany został kod niestandardowy do wykonania tej akcji.
- Można określić [zasad uaktualniania](./virtual-machine-scale-sets-upgrade-scale-set.md) ułatwia wdrażanie uaktualnień na maszynach wirtualnych w zestawie skalowania. Z poszczególnymi maszynami wirtualnymi użytkownik musi organizować aktualizacje samodzielnie.

### <a name="vm-specific-features"></a>Funkcje specyficzne dla maszyny Wirtualnej

Niektóre funkcje są obecnie dostępne tylko na maszynach wirtualnych:

- Można przechwycić obrazu z pojedynczej maszyny Wirtualnej, ale nie z maszyny Wirtualnej w zestawie skalowania.
- Można przeprowadzić migrację poszczególnych maszyn wirtualnych z natywnych dysków do usługi managed disks, ale nie można migrować wystąpień maszyn wirtualnych w zestawie skalowania.
- Można przydzielić publiczne adresy IP protokołu IPv6 poszczególnych maszyn wirtualnych wirtualnych kart interfejsu sieciowego (NIC), ale nie można to zrobić dla wystąpień maszyn wirtualnych w zestawie skalowania. Możesz przypisać publiczne adresy IP protokołu IPv6 można załadować równoważenia przed poszczególne maszyny wirtualne lub zestawu skalowania maszyn wirtualnych.

## <a name="storage"></a>Magazyn

### <a name="scale-sets-with-azure-managed-disks"></a>Zestawy skalowania z usługą Azure Managed Disks
Zestawy skalowania mogą być tworzone za pomocą [usługi Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) zamiast kont tradycyjnego magazynu platformy Azure. Dyski zarządzane zapewniają następujące korzyści:
- Nie trzeba wstępnie utworzyć zbiór kont magazynów platformy Azure dla maszyn wirtualnych w zestawie skalowania.
- Można zdefiniować [dołączone dyski danych](virtual-machine-scale-sets-attached-disks.md) dla maszyn wirtualnych w zestawie skalowania jest ustawiony.
- Zestawy skalowania można skonfigurować w celu [obsługuje maksymalnie 1000 maszyn wirtualnych w zestawie](virtual-machine-scale-sets-placement-groups.md). 

Jeśli masz istniejący szablon, możesz również [zaktualizować szablon, aby używać usługi Managed Disks](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Magazyn zarządzany przez użytkownika
Zestaw skalowania, która nie jest zdefiniowana za pomocą usługi Azure Managed Disks opiera się na kontach magazynu utworzonych przez użytkownika do przechowywania dysków systemu operacyjnego maszyn wirtualnych w zestawie. Stosunek 20 maszyn wirtualnych na jedno konto magazynu lub mniej jest zalecane w celu osiągnięcia maksymalnej we/wy, a także korzystać z _celi_ (patrz poniżej). Zalecane jest również rozprzestrzeniać się znaki początkowe nazw kont magazynu w alfabetu. Robi pomaga rozkładu obciążenia w różnych systemach wewnętrznych. 


## <a name="overprovisioning"></a>Celi
Zestawy skalowania obecnie domyślnie "nadmiarową aprowizację" maszyn wirtualnych. Za pomocą nadmiarową aprowizację, włączone skalowania Ustaw faktycznie obroty więcej maszyn wirtualnych, niż prosili o, a następnie usuwa niepotrzebne maszyny wirtualne po żądana liczba maszyn wirtualnych są pomyślnie aprowizowane. Zwiększa inicjowania obsługi administracyjnej współczynniki sukcesu oraz skraca czas wdrażania. Nie są rozliczane dla dodatkowych maszyn wirtualnych i uwzględniane limitów przydziału.

Chociaż celi poprawić współczynnikach pomyślnego inicjowania obsługi administracyjnej, może to spowodować mylące zachowanie dla aplikacji, która nie jest przeznaczony do obsługi dodatkowych maszyn wirtualnych znajdujących się, a następnie znikaniu. Aby włączyć celi, upewnij się, masz następujące parametry w szablonie: `"overprovision": "false"`. Więcej szczegółów można znaleźć w [dokumentację interfejsu API REST zestawu skalowania](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Jeśli zestaw skalowania korzysta z magazynu zarządzanych przez użytkowników, a następnie wyłącz celi, może mieć więcej niż 20 maszyn wirtualnych na jedno konto magazynu, ale nie zaleca się do ponad 40 ze względu na wydajność operacji We/Wy. 

## <a name="limits"></a>Limits
Zestaw skalowania, zbudowany na podstawie obrazu z witryny Marketplace (nazywane również obrazu platformy) i skonfigurowany do używania usługi Azure Managed Disks obsługuje pojemności do 1000 maszyn wirtualnych. Jeśli skonfigurujesz skalowania jest ustawiana na obsługuje ponad 100 maszyn wirtualnych, nie wszystkie scenariusze działać tak samo, (na przykład Równoważenie obciążenia). Aby uzyskać więcej informacji, zobacz [Praca z zestawami skalowania dużą maszynę wirtualną](virtual-machine-scale-sets-placement-groups.md). 

Zestaw skonfigurowany z użyciem kont magazynu zarządzanych przez użytkowników skalowania jest obecnie ograniczona do 100 maszyn wirtualnych (i 5 kont magazynu są zalecane w przypadku tej skali).

Zestaw skalowania oparty na obraz niestandardowy (po jednym utworzone przez użytkownika) może mieć pojemności do 600 maszyny wirtualne skonfigurowane przy użyciu usługi Azure Managed disks. Jeśli zestaw skalowania jest skonfigurowany z kontami magazynu zarządzanych przez użytkowników, musi utworzyć wszystkie dyski VHD dysku systemu operacyjnego w ramach jednego konta magazynu. W rezultacie zaleca się maksymalną liczbę maszyn wirtualnych w zestawie skalowania, oparta na obrazach niestandardowych i magazynu zarządzanych przez użytkowników wynosi 20. Jeśli wyłączysz celi, możesz przejść do 40.

Aby uzyskać więcej maszyn wirtualnych nie zezwolić na te limity, należy wdrożyć wiele zestawów skalowania, jak pokazano w [ten szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

