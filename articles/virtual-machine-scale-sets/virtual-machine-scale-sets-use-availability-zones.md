---
title: Tworzenie zestawu skalowania na platformie Azure, który używa strefy dostępności | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć zestawy skalowania maszyn wirtualnych platformy Azure, używających stref dostępności w celu zwiększenia nadmiarowości względem awarii
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: cynthn
ms.openlocfilehash: 24cff3a2ec4d0bed7a030ca430eaa698eb4a7325
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59278772"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Tworzenie zestawu skalowania maszyn wirtualnych, który używa strefy dostępności

Aby chronić Twoje zestawy skalowania maszyn wirtualnych przed awariami na poziomie centrum danych, można utworzyć zestawu skalowania w strefach dostępności. Regiony platformy Azure, które obsługują strefy dostępności mają co najmniej trzy oddzielne strefy, z których każdy z własnych niezależne od zasilania źródła, sieć i chłodzenie. Aby uzyskać więcej informacji, zobacz [Przegląd stref dostępności](../availability-zones/az-overview.md).

## <a name="availability-considerations"></a>Zagadnienia dotyczące dostępności

Podczas wdrażania zestawu skalowania w co najmniej jedna strefa, począwszy od wersji interfejsu API *2017-12-01*, masz możliwość wdrożenia aplikacji za pomocą "Maksymalna rozmieszczenie" lub "statyczne 5 usterek domeny rozprzestrzeniania się". Dzięki rozproszeniu Maksymalny zestaw skalowania się maszyn wirtualnych między wiele domen błędów, jak to możliwe w każdej strefie. To rozmieszczenie może być między większą lub mniej niż pięć domeny dla stref błędów. Za pomocą "statyczne 5 usterek domeny rozprzestrzeniania się", zestaw skalowania się maszyn wirtualnych w domenach błędów pięć dokładnie na strefy. Zestaw skalowania nie może znaleźć pięcioma domenami błędów distinct na strefy, aby spełnić żądania alokacji, żądanie kończy się niepowodzeniem.

**Firma Microsoft zaleca wdrażanie przy użyciu maksymalna rozmieszczenie dla większości obciążeń**, jak to podejście oferuje bezpieczny najlepsze rozsyłanie się w większości przypadków. Repliki, aby można było ich rozmieszczenie jednostek izolacji różne sprzętu, należy zalecamy rozsyłanie się w różnych strefach dostępności i wykorzystywać maksymalna rozmieszczenie w każdej strefie.

Za pomocą maksymalna rozmieszczenie widoczne są tylko jedną domenę błędów w widok wystąpienia maszyny Wirtualnej zestawu skalowania i metadane wystąpienia niezależnie od tego, jak wiele domen błędów, które maszyny wirtualne są rozproszone między. Rozsyłanie się w każdej strefie jest niejawne.

Aby użyć maksymalna rozmieszczenie, ustaw *elementem platformFaultDomainCount* do *1*. Aby użyć statycznego domeny błędów w pięciu rozprzestrzeniania się, ustaw *elementem platformFaultDomainCount* do *5*. W wersji interfejsu API *2017-12-01*, *elementem platformFaultDomainCount* wartość domyślna to *1* skalowania pojedynczej strefy i międzystrefowego ustawia. Obecnie tylko statyczne pięć błędów domeny rozmieszczenie jest obsługiwane dla zestawów skalowania regionalne.

### <a name="placement-groups"></a>Grupy umieszczania

Gdy wdrażasz zestaw skalowania, istnieje również możliwość wdrożenia aplikacji za pomocą jednego [grupy umieszczania](./virtual-machine-scale-sets-placement-groups.md) za strefę dostępności lub z wieloma na strefy. Dla zestawów skalowania regionalne wybór jest mają pojedynczej grupy umieszczania w regionie lub ustawić wiele w regionie. Dla większości obciążeń zaleca się wiele grup umieszczania, co pozwala na większą skalę. W wersji interfejsu API *2017-12-01*, domyślne zestawy skalowania do wielu grup umieszczania dla zestawów skalowania pojedynczej strefy i między strefami, ale domyślnie pojedynczej grupy umieszczania dla zestawów skalowania regionalne.

> [!NOTE]
> Jeśli używasz maksymalna rozmieszczenie, należy użyć wielu grup umieszczania.

### <a name="zone-balancing"></a>Strefa równoważenia

Na koniec dla zestawów skalowania, wdrożonych w wielu strefach, również masz możliwość wyboru "najlepsze równowadze stref nakład pracy" lub "równowadze stref strict". Zestaw skalowania jest uznawany za "zrównoważone", jeśli każda strefa taką samą liczbę maszyn wirtualnych lub +\\-1 maszyna wirtualna w innych strefach dla zestawu skalowania. Na przykład:

- Zestaw skalowania z 2 maszyn wirtualnych w strefie 1, 3 maszyn wirtualnych w strefie 2 i 3 maszyn wirtualnych w strefie 3 jest uznawany za zrównoważone. Ma tylko jedną strefę z różnych liczba maszyn wirtualnych i jest tylko 1, mniejsze niż w innych strefach. 
- Zestaw skalowania z 1 maszyny Wirtualnej w strefie 1, 3 maszyn wirtualnych w strefie 2 i 3 maszyn wirtualnych w strefie 3 jest uznawany za niezrównoważone. Strefa 1 ma 2 maszyny wirtualne z mniej niż strefy 2 i 3.

Istnieje możliwość, że pomyślnie tworzone są maszyny wirtualne w zestawie skalowania, ale rozszerzenia na tych maszynach wirtualnych nie można wdrożyć. Te maszyny wirtualne z błędami rozszerzeń nadal są uwzględniane podczas określania, jeśli zestaw skalowania jest równoważone. Na przykład, zestaw skalowania z 3 maszyn wirtualnych w strefie 1, 3 maszyn wirtualnych w strefie 2 i 3 maszyn wirtualnych w strefie 3 jest uznawany za równoważenia nawet wtedy, gdy wszystkie rozszerzenia nie powiodła się w strefie 1 i wszystkie rozszerzenia zakończyło się pomyślnie w strefach 2 i 3.

Próby skalowania do wewnątrz i na zewnątrz przy zachowaniu równowagi zestawu skalowania z saldem strefy największej staranności. Jednakże jeśli z jakiegoś powodu nie jest to możliwe (na przykład, jeśli w jednej strefie ulegnie awarii, zestaw skalowania nie można utworzyć nową maszynę Wirtualną w tej strefie), zestaw skalowania umożliwia tymczasowe nierównowagi pomyślnie skalowanie w pionie lub poziomie. Na kolejnych prób skalowalnego w poziomie zestaw skalowania dodaje maszyn wirtualnych do stref, które wymagają więcej maszyn wirtualnych dla zestawu skalowania w celu zrównoważenia. Podobnie na kolejne skali w przypadku prób ataków, zestaw skalowania usuwa maszyny wirtualne w stref, które wymaga mniejszej liczby maszyn wirtualnych zestawu skalowania do zrównoważenia. Za pomocą "równowadze stref strict" zestaw skalowania nie powiedzie się każda próba ich skalowanie w pionie lub poziomie jeśli mogłoby to spowodować niezrównoważonej.

Aby użyć równowadze stref optymalnych, ustaw *zoneBalance* do *false*. To ustawienie jest ustawieniem domyślnym w wersji interfejsu API *2017-12-01*. Aby użyć równowadze stref strict, ustaw *zoneBalance* do *true*.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Zestawy skalowania pojedynczej strefy i strefowo nadmiarowy

Podczas wdrażania zestawu skalowania maszyn wirtualnych, można użyć jednej strefie dostępności w regionie lub stref.

Podczas tworzenia zestawu skalowania w jednej strefie, Ty określasz, która strefa te wystąpienia maszyn wirtualnych, uruchom w i odbywa się zestawu skalowania i automatycznego skalowania, tylko w ramach tej strefy. Zestaw skalowania strefowo nadmiarowe umożliwia utworzenie pojedynczego zestawu skalowania obejmującej wiele stref. Podczas tworzenia wystąpień maszyn wirtualnych, domyślnie one są równomiernie równoważone w strefach. Czy przerwę w przypadku wystąpienia w jednej ze stref, zestaw skalowania automatycznie skalować w poziomie w celu zwiększenia pojemności. Najlepszym rozwiązaniem byłoby skonfigurować reguły skalowania automatycznego na podstawie użycia procesora CPU lub pamięci. Reguły automatycznego skalowania umożliwia zestawie skalowania, aby reagować na utratę wystąpień maszyn wirtualnych w tej strefie, skalując w poziomie nowe wystąpienia w pozostałych strefach operacyjnej.

Do korzystania ze stref dostępności, należy utworzyć zestaw skalowania w [obsługiwanym regionie platformy Azure](../availability-zones/az-overview.md#regions-that-support-availability-zones). Można utworzyć zestaw skalowania, który używa strefach dostępności przy użyciu jednego z następujących metod:

- [Azure Portal](#use-the-azure-portal)
- Interfejs wiersza polecenia platformy Azure
- [Azure PowerShell](#use-azure-powershell)
- [Szablony usługi Azure Resource Manager](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Proces, aby utworzyć zestaw skalowania, który używa strefy dostępności jest taki sam, zgodnie z opisem w [wprowadzenie artykułu](quick-create-portal.md). Po wybraniu obsługiwany region platformy Azure, można utworzyć zestawu skalowania w jedną lub więcej stref dostępności, jak pokazano w poniższym przykładzie:

![Tworzenie zestawu skalowania w jednej strefie dostępności](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

Zestaw skalowania i obsługi zasobów, takich jak usługa Azure load balancer i publicznego adresu IP są tworzone w jednej strefie, które określisz.

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

Proces, aby utworzyć zestaw skalowania, który używa strefy dostępności jest taki sam, zgodnie z opisem w [wprowadzenie artykułu](quick-create-cli.md). Aby korzystać ze stref dostępności, musisz utworzyć skalowania jest ustawiana w obsługiwanym regionie platformy Azure.

Dodaj `--zones` parametr [tworzenie az vmss](/cli/azure/vmss) polecenia i określić które strefę do użycia (takie jak strefy *1*, *2*, lub *3*). Poniższy przykład tworzy jednostrefowego zestawu skalowania o nazwie *myScaleSet* w strefie *1*:

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

Pełny przykład Skaluj jednostrefowego zestawu i zasobów sieciowych, zobacz [ten przykładowy skrypt interfejsu wiersza polecenia](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh)

### <a name="zone-redundant-scale-set"></a>Zestaw skalowania strefowo nadmiarowy

Aby utworzyć skalę strefowo nadmiarowy zestaw, należy użyć *standardowa* jednostki SKU publicznego adresu IP adres i obciążenia równoważenia. Rozszerzone nadmiarowość *standardowa* jednostki SKU tworzy zasobów sieciowych strefowo nadmiarowe. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Load Balancer w warstwie standardowa](../load-balancer/load-balancer-standard-overview.md) i [Balancer w warstwie standardowa i strefy dostępności](../load-balancer/load-balancer-standard-availability-zones.md).

Aby utworzyć zestaw skalowania strefowo nadmiarowe, należy określić wieloma strefami z `--zones` parametru. Poniższy przykład tworzy strefowo nadmiarowego zestawu skalowania o nazwie *myScaleSet* w strefach *1,2,3*:

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

Trwa kilka minut, aby utworzyć i skonfigurować wszystko, co zestaw skalowania maszyn wirtualnych i zasobów w określonej strefy. Pełny przykład skalowania strefowo nadmiarowego zestawu i zasobów sieciowych, zobacz [ten przykładowy skrypt interfejsu wiersza polecenia](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Aby korzystać ze stref dostępności, musisz utworzyć skalowania jest ustawiana w obsługiwanym regionie platformy Azure. Dodaj `-Zone` parametr [New AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) polecenia i określić które strefę do użycia (takie jak strefy *1*, *2*, lub *3*).

Poniższy przykład tworzy jednostrefowego zestawu skalowania o nazwie *myScaleSet* w *wschodnie stany USA 2* strefy *1*. Zasoby sieciowe platformy Azure dla sieci wirtualnej, publiczny adres IP i moduł równoważenia obciążenia są tworzone automatycznie. Po wyświetleniu monitu podaj własne odpowiednie poświadczenia administracyjne dla wystąpień maszyn wirtualnych w zestawie skalowania:

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

### <a name="zone-redundant-scale-set"></a>Zestaw skalowania strefowo nadmiarowy

Aby utworzyć zestaw skalowania strefowo nadmiarowe, należy określić wieloma strefami z `-Zone` parametru. Poniższy przykład tworzy strefowo nadmiarowego zestawu skalowania o nazwie *myScaleSet* między *wschodnie stany USA 2* stref *1, 2, 3*. Zasoby strefowo nadmiarowa sieć platformy Azure dla sieci wirtualnej, publiczny adres IP i równoważenia obciążenia są tworzone automatycznie. Po wyświetleniu monitu podaj własne odpowiednie poświadczenia administracyjne dla wystąpień maszyn wirtualnych w zestawie skalowania:

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

Proces, aby utworzyć zestaw skalowania, który używa strefy dostępności jest taki sam, zgodnie z opisem w artykule wprowadzającym dla [Linux](quick-create-template-linux.md) lub [Windows](quick-create-template-windows.md). Aby korzystać ze stref dostępności, musisz utworzyć skalowania jest ustawiana w obsługiwanym regionie platformy Azure. Dodaj `zones` właściwości *Microsoft.Compute/virtualMachineScaleSets* zasobów, wpisz w szablonie i określ strefę, które ma być używana (takie jak strefy *1*, *2*, lub *3*).

Poniższy przykład tworzy Linux jednostrefowego zestawu skalowania o nazwie *myScaleSet* w *wschodnie stany USA 2* strefy *1*:

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

Pełny przykład Skaluj jednostrefowego zestawu i zasobów sieciowych, zobacz [ten przykładowy szablon usługi Resource Manager](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Zestaw skalowania strefowo nadmiarowy

Aby utworzyć zestaw skalowania strefowo nadmiarowe, należy określić wiele wartości w `zones` właściwość *Microsoft.Compute/virtualMachineScaleSets* typu zasobu. Poniższy przykład tworzy strefowo nadmiarowego zestawu skalowania o nazwie *myScaleSet* między *wschodnie stany USA 2* stref *1,2,3*:

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

W przypadku tworzenia publicznego adresu IP lub równoważenia obciążenia, określić *"sku": {"name": "Standardowy"} "* właściwości, aby utworzyć zasoby sieciowe strefowo nadmiarowe. Należy również utworzyć sieciową grupę zabezpieczeń i reguł, aby zezwolić na ruch. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Load Balancer w warstwie standardowa](../load-balancer/load-balancer-standard-overview.md) i [Balancer w warstwie standardowa i strefy dostępności](../load-balancer/load-balancer-standard-availability-zones.md).

Pełny przykład skalowania strefowo nadmiarowego zestawu i zasobów sieciowych, zobacz [ten przykładowy szablon usługi Resource Manager](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)

## <a name="next-steps"></a>Kolejne kroki

Teraz, po utworzeniu zestawu skalowania w strefie dostępności, możesz dowiedzieć się jak [wdrażanie aplikacji na maszynie wirtualnej zestawach skalowania](tutorial-install-apps-cli.md) lub [korzystanie ze skalowania automatycznego za pomocą zestawów skalowania maszyn wirtualnych](tutorial-autoscale-cli.md).
