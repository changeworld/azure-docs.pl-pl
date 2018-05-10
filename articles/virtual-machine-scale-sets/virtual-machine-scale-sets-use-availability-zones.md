---
title: Tworzenie zestawu Azure skali, który używa strefy dostępności | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć zestawy skalowania maszyny wirtualnej platformy Azure, korzystających z dostępności strefy w celu zwiększenia nadmiarowości na awarie
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: iainfou
ms.openlocfilehash: cc91366bda4dd443392c78cd61e5cc0ddb261fe2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Tworzenie zestawu skali maszyny wirtualnej, który używa strefy dostępności

Aby chronić Twoje zestawy skalowania maszyny wirtualnej z centrum danych na poziomie awarii, można utworzyć skali, ustaw w różnych strefach dostępności. Regiony platformy Azure obsługujące stref dostępności ma co najmniej trzech oddzielnych stref, każde z nich własne niezależne od zasilania źródła, sieci i chłodzenia. Aby uzyskać więcej informacji, zobacz [Przegląd stref dostępności](../availability-zones/az-overview.md).

## <a name="availability-considerations"></a>Zagadnienia dotyczące dostępności

Podczas wdrażania skalę zestawu do co najmniej jedna strefa począwszy od wersji interfejsu API *2017-12-01*, masz możliwość wdrożenia aplikacji z "max rozpraszania" lub "statyczne 5 błędów domeny rozpraszania". Z rozpraszania Maksymalny zestaw skalowania rozprzestrzenia maszyn wirtualnych między dowolną liczbę domen usterki, jak to możliwe w ramach każdej strefy. To rozłożenie może być między większą lub mniej niż pięć fault domen na strefy. Z "statyczne 5 błędów domeny rozpraszania", zestaw skalowania rozprzestrzenia maszyn wirtualnych w domenach awarii dokładnie pięć na strefy. Zestaw skalowania nie może znaleźć pięć domen błędów distinct na strefy, aby można było zrealizować żądanie alokacji, żądanie kończy się niepowodzeniem.

**Zaleca się wdrożenie z maksymalną rozłożenie większości obciążeń**, ponieważ metoda ta umożliwia rozłożenie najlepsze w większości przypadków. Repliki, aby było ich rozmieszczenie jednostek izolacji różne sprzętu należy zaleca rozsyłanie się w różnych strefach dostępności i wykorzystywać max rozsyłanie się w każdej strefie.

Z maksymalną rozpraszania widoczne są tylko jedną domenę błędów w widok wystąpienia maszyny Wirtualnej zestawu skalowania i metadanych wystąpienia niezależnie od tego, jak wiele domen błędów maszyn wirtualnych są rozkładane między. Rozsyłanie się w każdej strefie jest niejawnie.

Aby używać rozpraszania max, ustaw *platformFaultDomainCount* do *1*. Aby użyć statycznego rozpraszania domeny błędów pięć, ustaw *platformFaultDomainCount* do *5*. W wersji interfejsu API *2017-12-01*, *platformFaultDomainCount* domyślnie *1* Skala jednego strefy i cross ustawia. Obecnie tylko statyczne pięć usterek domeny rozpraszania jest obsługiwana dla zestawów skalowania regionalne.

### <a name="placement-groups"></a>Grupy umieszczania

Podczas wdrażania zestaw skali, masz również możliwość wdrożenia aplikacji za pomocą jednej [umieszczania grupy](./virtual-machine-scale-sets-placement-groups.md) na dostępność strefy lub w wielu na strefy. Dla zestawów skalowania regionalne wybór jest w celu zapewnienia grupy umieszczania pojedynczy region lub do wielu w regionie. Dla większości obciążeń zaleca się wiele grup umieszczania, co pozwala na większą skalę. W wersji interfejsu API *2017-12-01*, skala ustawia domyślne do wielu grup umieszczania dla zestawów skalowania pojedynczej strefy i cross, ale domyślnie umieszczania jednej grupy dla zestawów skalowania regionalne.

> [!NOTE]
> Użycie rozpraszania max, należy użyć wielu grup umieszczania.

### <a name="zone-balancing"></a>Strefy równoważenia

Na koniec dla zestawów skalowania wdrożyć w wielu strefach, masz również wybrać "najlepsze nakładu strefy saldo" lub "strict strefy balance". Zestaw skalowania jest uznawany za "zrównoważonym" w przypadku liczby maszyn wirtualnych w każdej strefie w ramach jednej liczby maszyn wirtualnych we wszystkich strefach dla zestawu skalowania. Na przykład:

- Skali ustawiana 2 maszyn wirtualnych w strefie 1, 3 maszyn wirtualnych w strefie maszyn wirtualnych 2 i 3 w strefie 3 jest uznawany za zrównoważone.
- Zestaw skalowania o 1 maszyna wirtualna w strefie 1, 3 maszyn wirtualnych w strefie maszyn wirtualnych 2 i 3 w strefie 3 jest uznawany za niezrównoważone.

Istnieje możliwość, że maszyny wirtualne w zestawie skalowania zostały utworzone pomyślnie, ale rozszerzeń na tych maszynach wirtualnych nie można wdrożyć. Podczas ustalania, czy zestaw skalowania jest rozmieszczana nadal zliczane są te maszyny wirtualne z błędami rozszerzenia. Na przykład skali ustawić 3 maszyn wirtualnych w strefie 1, 3 maszyn wirtualnych w strefie 2 i 3 maszyn wirtualnych w strefie 3 jest uznawany za zrównoważonym nawet wtedy, gdy wszystkie rozszerzenia nie powiodło się w strefie 1 i wszystkie rozszerzenia powiodło się w strefach 2 i 3.

Saldo strefy optymalnych skali ustawienie prób można skalować i wylogowanie, przy zachowaniu równowagi. Jednak jeśli z jakiegoś powodu nie jest to możliwe (na przykład, jeśli jedną strefę ulegnie awarii, zestaw skalowania nie można utworzyć nowej maszyny Wirtualnej w tej strefie), zestaw skalowania umożliwia tymczasowe nierównowaga pomyślnie skalowania przychodzący lub wychodzący. W kolejnych prób skalowalnego w poziomie zestaw skalowania dodaje maszyn wirtualnych dla stref, które wymagają więcej maszyn wirtualnych skali ustawioną rozmieszczane równomiernie. Podobnie na skali kolejnych prób zalogowania, zestaw skalowania usuwa maszyn wirtualnych ze stref wymagające mniejszej liczby maszyn wirtualnych skali ustawioną rozmieszczane równomiernie. Z "balance strefy strict" zestaw skalowania nie powiedzie się wszystkie próby skalować lub, jeśli mogłoby to spowodować niezrównoważonej.

Aby używać saldo optymalnych strefy, ustaw *zoneBalance* do *false*. To ustawienie jest ustawieniem domyślnym w wersji interfejsu API *2017-12-01*. Aby używać saldo strict strefy, ustaw *zoneBalance* do *true*.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Zestawy skalowania pojedynczej strefy i strefowo nadmiarowy

Podczas wdrażania zestaw skali maszyny wirtualnej, można użyć jednej strefie dostępności w regionie lub stref.

Po utworzeniu skali w jednej strefie, można sterować która strefa uruchomienie wszystkich wystąpień tych maszyn wirtualnych i jest zarządzany zestaw skali i autoscales tylko w ramach tej strefy. Zestaw skali strefowo nadmiarowy umożliwia tworzenie zestaw skalowania pojedynczego obejmującej wiele stref. Podczas tworzenia wystąpień maszyny Wirtualnej, a domyślnie one są równomierne różnych strefach. W przypadku przerwania wystąpienia w jednej ze stref, zestaw skali automatycznie skalować w poziomie w celu zwiększenia pojemności. Najlepszym rozwiązaniem jest skonfigurowanie reguł skalowania automatycznego na podstawie użycia procesora CPU lub pamięci. Reguły automatycznego skalowania umożliwia zestaw odpowiedzieć na utratę wystąpień maszyn wirtualnych w tej strefie przez skalowanie w poziomie nowych wystąpień w pozostałych strefach operacyjne skalowania.

Do korzystania ze stref dostępności, należy utworzyć w zestawie skali [obsługiwany region platformy Azure](../availability-zones/az-overview.md#regions-that-support-availability-zones). Można utworzyć zestaw skalowania, który używa strefy dostępności z jednego z następujących metod:

- [Azure Portal](#use-the-azure-portal)
- [Interfejs wiersza polecenia platformy Azure 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Szablony usługi Azure Resource Manager](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Proces tworzenia zestawu skali, który używa strefy dostępności jest taka sama, jak określono w [wprowadzenie artykułu](quick-create-portal.md). Po wybraniu obsługiwanych region platformy Azure można utworzyć skali w co najmniej jedna strefa dostępne, jak pokazano w poniższym przykładzie:

![Utwórz skali w jednej strefie dostępności](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

Zestaw skali i pomocnicze zasoby, takie jak usługi równoważenia obciążenia Azure i publiczny adres IP, są tworzone w jednej strefie, który określisz.

## <a name="use-the-azure-cli-20"></a>Użyj Azure CLI 2.0

Proces tworzenia zestawu skali, który używa strefy dostępności jest taka sama, jak określono w [wprowadzenie artykułu](quick-create-cli.md). Do korzystania ze stref dostępność, należy utworzyć na skalę w obsługiwanym regionie Azure.

Dodaj `--zones` parametr [utworzyć az vmss](/cli/azure/vmss#az_vmss_create) polecenia i określić, która strefa do użycia (takich jak strefy *1*, *2*, lub *3*). Poniższy przykład tworzy zestaw o nazwie skalowania pojedynczej strefy *myScaleSet* w strefie *1*:

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

Pełny przykład skali strefy jednego zestawu i zasobów sieciowych, zobacz [tego przykładowego skryptu interfejsu wiersza polecenia](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh.)

### <a name="zone-redundant-scale-set"></a>Zestaw skali strefowo nadmiarowy

Aby utworzyć strefowo nadmiarowy skali zestaw, możesz użyć *standardowe* SKU publicznego adresu IP adres i obciążenia modułu równoważenia. W celu zapewnienia nadmiarowości rozszerzone *standardowe* SKU tworzy zasobów sieciowych strefowo nadmiarowy. Aby uzyskać więcej informacji, zobacz [standardowe usługi równoważenia obciążenia Azure omówienie](../load-balancer/load-balancer-standard-overview.md).

Aby utworzyć zestaw skali strefowo nadmiarowy, należy określić wiele stref z `--zones` parametru. Poniższy przykład tworzy zestaw o nazwie strefowo nadmiarowy skalowania *myScaleSet* różnych strefach *1,2,3*:

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

Trwa kilka minut, aby utworzyć i skonfigurować wszystkie zestawu skalowania zasobów i maszyn wirtualnych w strefy, który określisz. Pełny przykład strefowo nadmiarowy skali zestawu i zasobów sieciowych, zobacz [tego przykładowego skryptu interfejsu wiersza polecenia](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Do korzystania ze stref dostępność, należy utworzyć na skalę w obsługiwanym regionie Azure. Dodaj `-Zone` parametr [AzureRmVmssConfig nowy](/powershell/module/azurerm.compute/new-azurermvmssconfig) polecenia i określić, która strefa do użycia (takich jak strefy *1*, *2*, lub *3*).

Poniższy przykład tworzy zestaw o nazwie skalowania pojedynczej strefy *myScaleSet* w *wschodnie stany USA 2* strefy *1*. Zasoby sieciowe platformy Azure dla sieci wirtualnej, publiczny adres IP i moduł równoważenia obciążenia są tworzone automatycznie. Po wyświetleniu monitu podaj własne odpowiednie poświadczenia administracyjne dla wystąpień maszyn wirtualnych w zestawie skalowania:

```powershell
New-AzureRmVmss `
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

### <a name="zone-redundant-scale-set"></a>Zestaw skali strefowo nadmiarowy

Aby utworzyć zestaw skali strefowo nadmiarowy, należy określić wiele stref z `-Zone` parametru. Poniższy przykład tworzy zestaw o nazwie strefowo nadmiarowy skalowania *myScaleSet* między *wschodnie stany USA 2* strefy *1, 2, 3*. Zasoby strefowo nadmiarowy sieć platformy Azure dla sieci wirtualnej, publiczny adres IP i modułu równoważenia obciążenia są tworzone automatycznie. Po wyświetleniu monitu podaj własne odpowiednie poświadczenia administracyjne dla wystąpień maszyn wirtualnych w zestawie skalowania:

```powershell
New-AzureRmVmss `
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

Proces tworzenia zestawu skali, który używa strefy dostępności jest taka sama, zgodnie z opisem w artykule Rozpoczęto pobieranie dla [Linux](quick-create-template-linux.md) lub [Windows](quick-create-template-windows.md). Do korzystania ze stref dostępność, należy utworzyć na skalę w obsługiwanym regionie Azure. Dodaj `zones` właściwości *Microsoft.Compute/virtualMachineScaleSets* zasobów typu w szablonie i określić, która strefa do użycia (takich jak strefy *1*, *2*, lub *3*).

Poniższy przykład tworzy zestaw o nazwie skalowania pojedynczej strefy Linux *myScaleSet* w *wschodnie stany USA 2* strefy *1*:

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

Pełny przykład skali strefy jednego zestawu i zasobów sieciowych, zobacz [ten przykładowy szablon usługi Resource Manager](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Zestaw skali strefowo nadmiarowy

Aby utworzyć zestaw skali strefowo nadmiarowy, określić wiele wartości w `zones` właściwość *Microsoft.Compute/virtualMachineScaleSets* typu zasobu. Poniższy przykład tworzy zestaw o nazwie strefowo nadmiarowy skalowania *myScaleSet* między *wschodnie stany USA 2* strefy *1,2,3*:

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

Jeśli tworzysz publicznego adresu IP lub usługę równoważenia obciążenia, określ *"sku": {"name": "Standardowy"} "* właściwość, aby utworzyć zasobów sieciowych strefowo nadmiarowy. Należy także utworzyć grupy zabezpieczeń sieci i reguł, aby zezwolić na cały ruch. Aby uzyskać więcej informacji, zobacz [standardowe usługi równoważenia obciążenia Azure omówienie](../load-balancer/load-balancer-standard-overview.md).

Pełny przykład strefowo nadmiarowy skali zestawu i zasobów sieciowych, zobacz [ten przykładowy szablon usługi Resource Manager](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)

## <a name="next-steps"></a>Kolejne kroki

Teraz, po utworzeniu skali w strefie dostępności, możesz dowiedzieć się jak [Wdrażaj aplikacje na maszynie wirtualnej skalowanie zestawów](tutorial-install-apps-cli.md) lub [automatycznego skalowania za pomocą zestawów skali maszyny wirtualnej](tutorial-autoscale-cli.md).
