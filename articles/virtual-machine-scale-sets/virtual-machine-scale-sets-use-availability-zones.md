---
title: Tworzenie zestawu skalowania platformy Azure, który używa stref dostępności
description: Dowiedz się, jak utworzyć zestawy skalowania maszyny wirtualnej platformy Azure, które używają stref dostępności w celu zwiększenia nadmiarowości w przypadku awarii
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: cynthn
ms.openlocfilehash: 11695eb889a10dc689b00399a37382a3b9772eae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274420"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Tworzenie zestawu skalowania maszyny wirtualnej, który używa stref dostępności

Aby chronić zestawy skalowania maszyny wirtualnej przed awariami na poziomie centrum danych, można utworzyć zestaw skalowania w strefach dostępności. Regiony platformy Azure obsługujące strefy dostępności mają co najmniej trzy oddzielne strefy, z których każda ma własne niezależne źródło zasilania, sieć i chłodzenie. Aby uzyskać więcej informacji, zobacz [Omówienie stref dostępności](../availability-zones/az-overview.md).

## <a name="availability-considerations"></a>Zagadnienia dotyczące dostępności

Po wdrożeniu skali ustawionej w co najmniej jednej strefie od wersji interfejsu API *2017-12-01*można wdrożyć z "maksymalnym rozmieszczeniem" lub "statycznym rozrzutem domeny błędów 5". Przy maksymalnym rozmieszczeniu zestaw skalowania rozkłada maszyny wirtualne na jak najwięcej domen błędów w każdej strefie. To rozprzestrzenianie może być większa lub mniejsza niż pięć domen błędów na strefę. W celu "rozprzestrzeniania się domeny błędów statycznych 5" zestaw skalowania rozkłada maszyny wirtualne na dokładnie pięć domen błędów na strefę. Jeśli zestaw skalowania nie może znaleźć pięciu różnych domen błędów na strefę, aby spełnić żądanie alokacji, żądanie nie powiedzie się.

**Zaleca się wdrażanie z maksymalnym rozmieszczeniem dla większości obciążeń,** ponieważ takie podejście zapewnia najlepsze rozłożenie w większości przypadków. Jeśli potrzebujesz replik, które mają być rozłożone na różne jednostki izolacji sprzętu, zalecamy rozmieszczenie w strefach dostępności i wykorzystanie maksymalnego rozrzucania w każdej strefie.

Z maksymalnym rozrzucaniem widzisz tylko jedną domenę błędów w widoku wystąpienia zestawu skalowania i w metadanych wystąpienia, niezależnie od tego, ile domen błędów maszyny wirtualne są rozłożone na. Rozprzestrzenianie się w każdej strefie jest niejawne.

Aby użyć maksymalnego rozrzucania, ustaw *platformfaultDomainCount* na *1*. Aby użyć statycznego rozrzucania domen usterek, należy ustawić *platformFaultDomainCount* na *5*. W wersji interfejsu API *2017-12-01* *, platformaFaultDomainCount* domyślnie *1* dla jednostrefowych i międzystrefowych zestawów skalowania. Obecnie tylko statyczne rozprzestrzenianie domen usterek jest obsługiwane dla regionalnych (nieprzekątowych) zestawów skalowania.

### <a name="placement-groups"></a>Grupy umieszczania

Podczas wdrażania zestawu skalowania, masz również możliwość wdrożenia z jednej [grupy miejsc docelowych](./virtual-machine-scale-sets-placement-groups.md) na dostępność strefy lub z wielu na strefę. W przypadku regionalnych (nieprzezłowych) zestawów skalowania należy wybierać, aby mieć jedną grupę miejsc docelowych w regionie lub mieć wiele w regionie. W przypadku większości obciążeń zalecamy wiele grup miejsc docelowych, co pozwala na większą skalę. W wersji interfejsu API *2017-12-01*skala ustawia domyślnie wiele grup miejsc docelowych dla zestawów skalowania jednostrefowego i między strefowego, ale domyślnie jest to grupa pojedynczych miejsc docelowych dla regionalnych (niestonalnych) zestawów skalowania.

> [!NOTE]
> Jeśli używasz maksymalnego rozrzucania, musisz użyć wielu grup miejsc docelowych.

### <a name="zone-balancing"></a>Równoważenie stref

Na koniec w przypadku zestawów skalowania rozmieszczonych w wielu strefach można również wybrać opcję "najlepsze saldo strefy" lub "ścisłe saldo strefy". Zestaw skalowania jest uważany za "zrównoważony", jeśli każda strefa\\ma taką samą liczbę maszyn wirtualnych lub + - 1 maszynę wirtualną we wszystkich innych strefach dla zestawu skalowania. Przykład:

- Zestaw skalowania z 2 maszynami wirtualnymi w strefie 1, 3 maszyny wirtualne w strefie 2 i 3 maszyny wirtualne w strefie 3 jest uważany za zrównoważony. Istnieje tylko jedna strefa z inną liczbą maszyn wirtualnych i jest tylko 1 mniej niż inne strefy. 
- Zestaw skalowania z 1 maszyną wirtualną w strefie 1, 3 maszyny wirtualne w strefie 2 i 3 maszyny wirtualne w strefie 3 jest uważany za niezrównoważony. Strefa 1 ma 2 mniej maszyn wirtualnych niż strefy 2 i 3.

Jest możliwe, że maszyny wirtualne w zestawie skalowania są pomyślnie tworzone, ale rozszerzenia na tych maszynach wirtualnych nie można wdrożyć. Te maszyny wirtualne z błędami rozszerzenia są nadal liczone podczas określania, czy zestaw skalowania jest zrównoważony. Na przykład zestaw skalowania z 3 maszynami wirtualnymi w strefie 1, 3 maszyny wirtualne w strefie 2 i 3 maszyny wirtualne w strefie 3 jest uważany za zrównoważony, nawet jeśli wszystkie rozszerzenia nie powiodło się w strefie 1 i wszystkie rozszerzenia powiodło się w strefach 2 i 3.

Dzięki najlepszemu bilansowi strefy zestaw skalowania próbuje skalować się i wyskalować przy zachowaniu równowagi. Jeśli jednak z jakiegoś powodu nie jest to możliwe (na przykład jeśli jedna strefa ustępuje, zestaw skalowania nie może utworzyć nowej maszyny Wirtualnej w tej strefie), zestaw skalowania umożliwia tymczasowemu nierównowadze pomyślne skalowanie w lub w poziomie. W kolejnych próbach skalowania w poziomie zestaw skalowania dodaje maszyny wirtualne do stref, które potrzebują więcej maszyn wirtualnych dla zestawu skalowania, które mają być zrównoważone. Podobnie w kolejnych skalach w próbach zestaw skalowania usuwa maszyny wirtualne ze stref, które potrzebują mniejszej liczby maszyn wirtualnych, aby zestaw skalowania był zrównoważony. Z "ścisłego balansu strefy", zestaw skalowania nie kończy żadnych prób skalowania w lub w poziomie, jeśli spowoduje to niewyważenie.

Aby użyć równowagi strefy najwyższego wysiłku, ustaw *zoneBalance* na *false*. To ustawienie jest ustawieniem domyślnym w wersji interfejsu API *2017-12-01*. Aby użyć ścisłego balansu strefy, ustaw *zoneBalance* na *true*.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Zestawy skali jednostrefowej i strefowo nadmiarowej

Podczas wdrażania zestawu skalowania maszyny wirtualnej można użyć jednej strefy dostępności w regionie lub wielu stref.

Podczas tworzenia zestawu skalowania w jednej strefie, można kontrolować, która strefa wszystkie te wystąpienia maszyn wirtualnych uruchomić w, a zestaw skalowania jest zarządzany i skalowanie automatyczne tylko w tej strefie. Zestaw skali nadmiarowej strefy umożliwia utworzenie pojedynczego zestawu skalowania obejmującego wiele stref. Jak są tworzone wystąpienia maszyn wirtualnych, domyślnie są one równomiernie zrównoważone między strefami. W przypadku wystąpienia przerwy w jednej ze stref zestaw skalowania nie jest automatycznie skalowany w poziomie w celu zwiększenia pojemności. Najlepszym rozwiązaniem byłoby skonfigurowanie reguł skalowania automatycznego na podstawie użycia procesora CPU lub pamięci. Reguły skalowania automatycznego pozwoli skalowania zestaw odpowiedzieć na utratę wystąpień maszyny Wirtualnej w tej jednej strefie przez skalowanie w dół nowych wystąpień w pozostałych strefach operacyjnych.

Aby korzystać ze stref dostępności, zestaw skalowania musi zostać utworzony w [obsługiwanym regionie platformy Azure.](../availability-zones/az-overview.md#services-support-by-region) Można utworzyć zestaw skalowania, który używa stref dostępności za pomocą jednej z następujących metod:

- [Portal Azure](#use-the-azure-portal)
- Interfejs wiersza polecenia platformy Azure
- [Azure PowerShell](#use-azure-powershell)
- [Szablony usługi Azure Resource Manager](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Proces tworzenia zestawu skalowania korzystającego ze strefy dostępności jest taki sam, jak opisano w [artykule Wprowadzenie.](quick-create-portal.md) Po wybraniu obsługiwanego regionu platformy Azure można utworzyć zestaw skalowania w jednej lub kilku dostępnych strefach, jak pokazano w poniższym przykładzie:

![Tworzenie zestawu skalowania w jednej strefie dostępności](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

Zestaw skalowania i zasoby pomocnicze, takie jak moduł równoważenia obciążenia platformy Azure i publiczny adres IP, są tworzone w jednej strefie, którą określisz.

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

Proces tworzenia zestawu skalowania korzystającego ze strefy dostępności jest taki sam, jak opisano w [artykule Wprowadzenie.](quick-create-cli.md) Aby korzystać ze stref dostępności, należy utworzyć zestaw skalowania w obsługiwanym regionie platformy Azure.

Dodaj `--zones` parametr do polecenia [az vmss create](/cli/azure/vmss) i określ, której strefy użyć (na przykład strefa *1,* *2*lub *3*). Poniższy przykład tworzy zestaw skali pojedynczej strefy o nazwie *myScaleSet* w strefie *1:*

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

Pełny przykład zestawu skalowania jednostrefowego i zasobów sieciowych można znaleźć w [tym przykładowym skrypcie interfejsu wiersza polecenia](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh)

### <a name="zone-redundant-scale-set"></a>Zestaw skalowania nadmiarowego strefy

Aby utworzyć zestaw skalowania nadmiarowego strefy, należy użyć publicznego adresu IP *jednostki SKU standardu* i modułu równoważenia obciążenia. Aby zwiększyć nadmiarowość, *standardowa* jednostka SKU tworzy nadmiarowe zasoby sieciowe strefy. Aby uzyskać więcej informacji, zobacz [Omówienie standardu równoważenia obciążenia platformy Azure](../load-balancer/load-balancer-standard-overview.md) oraz [standardowe strefy równoważenia obciążenia i dostępności](../load-balancer/load-balancer-standard-availability-zones.md).

Aby utworzyć zestaw skali nadmiarowej strefy, należy `--zones` określić wiele stref z parametrem. Poniższy przykład tworzy zestaw skali nadmiarowej strefy o nazwie *myScaleSet* w różnych strefach *1,2,3:*

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1 2 3
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych w określonych strefach zajmuje kilka minut. Pełny przykład zestawu skalowania nadmiarowego strefy i zasobów sieciowych można znaleźć w [tym przykładowym skrypcie interfejsu wiersza polecenia](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Aby korzystać ze stref dostępności, należy utworzyć zestaw skalowania w obsługiwanym regionie platformy Azure. Dodaj `-Zone` parametr do polecenia [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) i określ, której strefy użyć (na przykład strefa *1,* *2*lub *3*).

Poniższy przykład tworzy zestaw skali pojedynczej strefy o nazwie *myScaleSet* w *strefie 1 wschodnie stany USA* *2*. Zasoby sieciowe platformy Azure dla sieci wirtualnej, publiczny adres IP i moduł równoważenia obciążenia są tworzone automatycznie. Po wyświetleniu monitu podaj własne odpowiednie poświadczenia administracyjne dla wystąpień maszyn wirtualnych w zestawie skalowania:

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1"
```

### <a name="zone-redundant-scale-set"></a>Zestaw skalowania nadmiarowego strefy

Aby utworzyć zestaw skali nadmiarowej strefy, należy `-Zone` określić wiele stref z parametrem. Poniższy przykład tworzy strefowy zestaw skalowania o nazwie *myScaleSet* w strefach *2 wschodnich* stanów USA *1, 2, 3*. Automatycznie tworzone są zasoby sieci platformy Azure dla sieci wirtualnej, publicznego adresu IP i modułu równoważenia obciążenia. Po wyświetleniu monitu podaj własne odpowiednie poświadczenia administracyjne dla wystąpień maszyn wirtualnych w zestawie skalowania:

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1", "2", "3"
```

## <a name="use-azure-resource-manager-templates"></a>Korzystanie z szablonów usługi Azure Resource Manager

Proces tworzenia zestawu skalowania, który używa strefy dostępności, jest taki sam, jak opisano w artykule wprowadzenie dla [systemu Linux](quick-create-template-linux.md) lub [Windows](quick-create-template-windows.md). Aby korzystać ze stref dostępności, należy utworzyć zestaw skalowania w obsługiwanym regionie platformy Azure. Dodaj `zones` właściwość do typu zasobu *Microsoft.Compute/virtualMachineScaleSets* w szablonie i określ, której strefy użyć (na przykład strefa *1*, *2*lub *3*).

Poniższy przykład tworzy linux jednostrefowy zestaw skali o nazwie *myScaleSet* w *east US 2* strefa *1:*

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

Pełny przykład zestawu skalowania jednostrefowego i zasobów sieciowych można znaleźć w [tym przykładowym szablonie Menedżera zasobów](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Zestaw skalowania nadmiarowego strefy

Aby utworzyć zestaw skali nadmiarowej strefy, `zones` należy określić wiele wartości we właściwości dla typu zasobu *Microsoft.Compute/virtualMachineScaleSets.* Poniższy przykład tworzy zestaw skali nadmiarowej strefy o nazwie *myScaleSet* w wschodnich strefach *US 2* *1,2,3:*

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

Jeśli utworzysz publiczny adres IP lub moduł równoważenia obciążenia, określ właściwość *"sku": { "name": "Standard" }",* aby utworzyć nadmiarowe zasoby sieciowe strefy. Należy również utworzyć grupę zabezpieczeń sieci i reguły zezwalają na ruch. Aby uzyskać więcej informacji, zobacz [Omówienie standardu równoważenia obciążenia platformy Azure](../load-balancer/load-balancer-standard-overview.md) oraz [standardowe strefy równoważenia obciążenia i dostępności](../load-balancer/load-balancer-standard-availability-zones.md).

Pełny przykład zestawu skalowania nadmiarowego strefy i zasobów sieciowych można znaleźć w [tym przykładowym szablonie Menedżera zasobów](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)

## <a name="next-steps"></a>Następne kroki

Po utworzeniu zestawu skalowania w strefie dostępności można dowiedzieć się, jak [wdrażać aplikacje w zestawach skalowania maszyn wirtualnych](tutorial-install-apps-cli.md) lub [używać skalowania automatycznego z zestawami skalowania maszyny wirtualnej](tutorial-autoscale-cli.md).
