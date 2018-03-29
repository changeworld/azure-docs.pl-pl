---
title: Tworzenie zestawu Azure skali, który używa strefy dostępności (wersja zapoznawcza) | Dokumentacja firmy Microsoft
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
ms.date: 01/11/2018
ms.author: iainfou
ms.openlocfilehash: 8b497af8bc7e3060e184dd6a029b23ccb2d2bbfb
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>Tworzenie zestawu skali maszyny wirtualnej, który używa strefy dostępności (wersja zapoznawcza)
Aby chronić Twoje zestawy skalowania maszyny wirtualnej z centrum danych na poziomie awarii, można utworzyć skali, ustaw w różnych strefach dostępności. Regiony platformy Azure obsługujące stref dostępności ma co najmniej trzech oddzielnych stref, każde z nich własne niezależne od zasilania źródła, sieci i chłodzenia. Aby uzyskać więcej informacji, zobacz [Przegląd stref dostępności](../availability-zones/az-overview.md).

[!INCLUDE [availability-zones-preview-statement.md](../../includes/availability-zones-preview-statement.md)]


## <a name="single-zone-and-zone-redundant-scale-sets"></a>Zestawy skalowania pojedynczej strefy i strefowo nadmiarowy
Podczas wdrażania zestaw skali maszyny wirtualnej, można użyć jednej strefie dostępności w regionie lub stref.

Po utworzeniu skali w jednej strefie, można sterować która strefa uruchomienie wszystkich wystąpień tych maszyn wirtualnych i jest zarządzany zestaw skali i autoscales tylko w ramach tej strefy. Zestaw skali strefowo nadmiarowy umożliwia tworzenie zestaw skalowania pojedynczego obejmującej wiele stref. Podczas tworzenia wystąpień maszyny Wirtualnej, a domyślnie one są równomierne różnych strefach. W przypadku przerwania wystąpienia w jednej ze stref, zestaw skali automatycznie skalować w poziomie w celu zwiększenia pojemności. Najlepszym rozwiązaniem jest skonfigurowanie reguł skalowania automatycznego na podstawie użycia procesora CPU lub pamięci. Reguły automatycznego skalowania umożliwia zestaw odpowiedzieć na utratę wystąpień maszyn wirtualnych w tej strefie przez skalowanie w poziomie nowych wystąpień w pozostałych strefach operacyjne skalowania.

Do korzystania ze stref dostępności, należy utworzyć w zestawie skali [obsługiwany region platformy Azure](../availability-zones/az-overview.md#regions-that-support-availability-zones). Należy również [rejestrowania programu Podgląd stref dostępności](http://aka.ms/azenroll). Można utworzyć zestaw skalowania, który używa strefy dostępności z jednego z następujących metod:

- [Azure Portal](#use-the-azure-portal)
- [Interfejs wiersza polecenia platformy Azure 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Szablony usługi Azure Resource Manager](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Proces tworzenia zestawu skali, który używa strefy dostępności jest taka sama, jak określono w [wprowadzenie artykułu](quick-create-portal.md). Upewnij się, że masz [zarejestrowany dla stref dostępności Podgląd](http://aka.ms/azenroll). Po wybraniu obsługiwanych region platformy Azure można utworzyć skali w jednej ze stref dostępne, jak pokazano w poniższym przykładzie:

![Utwórz skali w jednej strefie dostępności](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

Zestaw skali i pomocnicze zasoby, takie jak usługi równoważenia obciążenia Azure i publiczny adres IP, są tworzone w jednej strefie, który określisz.


## <a name="use-the-azure-cli-20"></a>Użyj Azure CLI 2.0
Proces tworzenia zestawu skali, który używa strefy dostępności jest taka sama, jak określono w [wprowadzenie artykułu](quick-create-cli.md). Do korzystania ze stref dostępności, musisz utworzyć zestaw skali w obsługiwanym regionie Azure i mieć [zarejestrowany dla stref dostępności Podgląd](http://aka.ms/azenroll).

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
Aby utworzyć strefowo nadmiarowy skali zestaw, możesz użyć *standardowe* SKU publicznego adresu IP adres i obciążenia modułu równoważenia. W celu zapewnienia nadmiarowości rozszerzone *standardowe* SKU tworzy zasobów sieciowych strefowo nadmiarowy. Aby uzyskać więcej informacji, zobacz [standardowe usługi równoważenia obciążenia Azure omówienie](../load-balancer/load-balancer-standard-overview.md). Podczas pierwszego tworzenia strefowo nadmiarowy skali ustawić lub usługę równoważenia obciążenia, należy wykonać następujące kroki, aby zarejestrować konta dla tych funkcji podglądu.

1. Konto dla zestawu skalowania strefowo nadmiarowy i sieć funkcji za pomocą rejestru [rejestru funkcji az](/cli/azure/feature#az_feature_register) w następujący sposób:

    ```azurecli
    az feature register --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```
    
2. Może upłynąć kilka minut, aby zarejestrować dla funkcji. Możesz sprawdzić stan operacji z [az funkcji Pokaż](/cli/azure/feature#az_feature_show):

    ```azurecli
    az feature show --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature show --name AllowLBPreview --namespace Microsoft.Network
    ```

    W poniższym przykładzie przedstawiono żądany stan funkcji *zarejestrowanej*:
    
    ```json
    "properties": {
          "state": "Registered"
       },
    ```

3. Po ustawieniu skali strefowo nadmiarowy i sieci zasoby raportować jako *zarejestrowanej*, należy ponownie zarejestrować *obliczeniowe* i *sieci* dostawców o [az Zarejestruj dostawcę](/cli/azure/provider#az_provider_register) w następujący sposób:

    ```azurecli
    az provider register --namespace Microsoft.Compute
    az provider register --namespace Microsoft.Network
    ```

Aby utworzyć zestaw skali strefowo nadmiarowy, należy określić wiele stref z `--zones` parametru. Poniższy przykład tworzy zestaw o nazwie strefowo nadmiarowy skalowania *myScaleSet* różnych strefach *1,2,3*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones {1,2,3}
```

Trwa kilka minut, aby utworzyć i skonfigurować wszystkie zestawu skalowania zasobów i maszyn wirtualnych w strefy, który określisz. Pełny przykład strefowo nadmiarowy skali zestawu i zasobów sieciowych, zobacz [tego przykładowego skryptu interfejsu wiersza polecenia](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)


## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Proces tworzenia zestawu skali, który używa strefy dostępności jest taka sama, jak określono w [wprowadzenie artykułu](quick-create-powershell.md). Do korzystania ze stref dostępności, musisz utworzyć zestaw skali w obsługiwanym regionie Azure i mieć [zarejestrowany dla stref dostępności Podgląd](http://aka.ms/azenroll). Dodaj `-Zone` parametr [AzureRmVmssConfig nowy](/powershell/module/azurerm.compute/new-azurermvmssconfig) polecenia i określić, która strefa do użycia (takich jak strefy *1*, *2*, lub *3*). 

Poniższy przykład tworzy config zestaw skalowania pojedynczej strefy o nazwie *vmssConfig* w *wschodnie stany USA 2* strefy *1*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

Pełny przykład skali strefy jednego zestawu i zasobów sieciowych, zobacz [ten przykładowy skrypt programu PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1)

### <a name="zone-redundant-scale-set"></a>Zestaw skali strefowo nadmiarowy
Aby utworzyć strefowo nadmiarowy skali zestaw, możesz użyć *standardowe* SKU publicznego adresu IP adres i obciążenia modułu równoważenia. W celu zapewnienia nadmiarowości rozszerzone *standardowe* SKU tworzy zasobów sieciowych strefowo nadmiarowy. Aby uzyskać więcej informacji, zobacz [standardowe usługi równoważenia obciążenia Azure omówienie](../load-balancer/load-balancer-standard-overview.md). Podczas pierwszego tworzenia strefowo nadmiarowy skali ustawić lub usługę równoważenia obciążenia, należy wykonać następujące kroki, aby zarejestrować konta dla tych funkcji podglądu.

1. Konto dla zestawu skalowania strefowo nadmiarowy i sieć funkcji za pomocą rejestru [AzureRmProviderFeature rejestru](/powershell/module/azurerm.resources/register-azurermproviderfeature) w następujący sposób:

    ```powershell
    Register-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```
    
2. Może upłynąć kilka minut, aby zarejestrować dla funkcji. Możesz sprawdzić stan operacji z [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature):

    ```powershell
    Get-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute 
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

    W poniższym przykładzie przedstawiono żądany stan funkcji *zarejestrowanej*:
    
    ```powershell
    RegistrationState
    -----------------
    Registered
    ```

3. Po ustawieniu skali strefowo nadmiarowy i sieci zasoby raportować jako *zarejestrowanej*, należy ponownie zarejestrować *obliczeniowe* i *sieci* dostawców o [ Rejestr AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) w następujący sposób:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```

Aby utworzyć zestaw skali strefowo nadmiarowy, należy określić wiele stref z `-Zone` parametru. Poniższy przykład tworzy config zestaw skali strefowo nadmiarowy, o nazwie *myScaleSet* między *wschodnie stany USA 2* strefy *1, 2, 3*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1", "2", "3"
```

Jeśli utworzysz publiczny adres IP z [AzureRmPublicIpAddress nowy](/powershell/module/azurerm.network/new-azurermpublicipaddress) lub usługę równoważenia obciążenia z [AzureRmLoadBalancer nowy](/powershell/module/AzureRM.Network/New-AzureRmLoadBalancer), określ *- SKU "Standard"* do utworzenia zasoby sieciowe strefowo nadmiarowy. Należy także utworzyć grupy zabezpieczeń sieci i reguł, aby zezwolić na cały ruch. Aby uzyskać więcej informacji, zobacz [standardowe usługi równoważenia obciążenia Azure omówienie](../load-balancer/load-balancer-standard-overview.md).

Pełny przykład strefowo nadmiarowy skali zestawu i zasobów sieciowych, zobacz [ten przykładowy skrypt programu PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1)


## <a name="use-azure-resource-manager-templates"></a>Korzystanie z szablonów usługi Azure Resource Manager
Proces tworzenia zestawu skali, który używa strefy dostępności jest taka sama, zgodnie z opisem w artykule Rozpoczęto pobieranie dla [Linux](quick-create-template-linux.md) lub [Windows](quick-create-template-windows.md). Do korzystania ze stref dostępności, musisz utworzyć zestaw skali w obsługiwanym regionie Azure i mieć [zarejestrowany dla stref dostępności Podgląd](http://aka.ms/azenroll). Dodaj `zones` właściwości *Microsoft.Compute/virtualMachineScaleSets* zasobów typu w szablonie i określić, która strefa do użycia (takich jak strefy *1*, *2*, lub *3*).

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
Teraz, po utworzeniu skali w strefie dostępności, możesz dowiedzieć się jak [Wdrażaj aplikacje na maszynie wirtualnej skalowanie zestawów](virtual-machine-scale-sets-deploy-app.md) lub [automatycznego skalowania za pomocą zestawów skali maszyny wirtualnej](virtual-machine-scale-sets-autoscale-overview.md).
