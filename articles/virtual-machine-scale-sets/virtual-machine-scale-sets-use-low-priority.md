---
title: Tworzenie zestawu Azure skali, który używa niskiego priorytetu maszyny wirtualne (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć zestawy skalowania maszyny wirtualnej platformy Azure, które umożliwia kosztów maszyn wirtualnych o niskim priorytecie
services: virtual-machine-scale-sets
documentationcenter: ''
author: mmccrory
manager: rajraj
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: memccror
ms.openlocfilehash: f25e4d1e3906a610e7c60e348f872a78d7db8fd3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>Maszyny wirtualne niskiego priorytetu na zestawy skalowania (wersja zapoznawcza)

Za pomocą niskiego priorytetu maszyny wirtualne na zestawy skalowania umożliwia można wykorzystać możliwości unutilized na znaczne oszczędności. W dowolnym momencie w czasie, gdy platforma Azure wymaga pojemność wstecz infrastruktury platformy Azure Wyklucz niskiego priorytetu maszyn wirtualnych. W związku z tym niskiego priorytetu maszyny wirtualne są doskonałe rozwiązanie dla obciążeń, które może obsłużyć przerw w działaniu jak przetwarzania zadań, środowiska i testowania, obliczeń dużych obciążeń i wsadowego.

Ilość dostępnej pojemności unutilized mogą się różnić w zależności od rozmiaru, region, godzinę i. Podczas wdrażania maszyn wirtualnych niskiego priorytetu na skali ustawia Azure przydzielić maszyn wirtualnych, jeśli jest dostępna pojemność, ale nie istnieje żadne umowy SLA dla tych maszyn wirtualnych. Zestaw skali niskiego priorytetu jest wdrożony w domenie pojedynczej awarii i oferuje gwarancje nie wysokiej dostępności.

> [!NOTE]
> Zestawy skalowania niskiego priorytetu są w wersji zapoznawczej i gotowe do tworzenia i testowania scenariuszy. 

## <a name="eviction-policy"></a>Zasady wykluczania

Po ustawieniu na skalę niskiego priorytetu maszyny wirtualne są wykluczony, zostaną przeniesione do stanu zatrzymania (cofnięciu przydziału) domyślnie. Z tymi zasadami wykluczenia można wdrożyć ponownie wykluczonym wystąpienia, ale nie ma gwarancji, że przydział powiedzie się. Zatrzymano maszyn wirtualnych zostanie zaliczony limitu przydziału wystąpienia zestawu skali i zostanie naliczona dla podstawowych dysków. 

Jeśli chcesz maszyn wirtualnych w Twojej zestaw ma zostać usunięty, gdy są one wykluczaniu skalowania niskiego priorytetu, możesz ustawić zasady wykluczania do usunięcia w Twojej [szablonu usługi Azure Resource Manager](#use-azure-resource-manager-templates). Usuń zasady wykluczenia można utworzyć nowych maszyn wirtualnych, zwiększając właściwość count wystąpienia zestawu skali. Wykluczone maszyn wirtualnych zostaną usunięte wraz z ich odpowiednie dyski i dlatego użytkownik nie zostanie obciążona dla magazynu. Umożliwia także funkcję skalowania automatycznego skalowania zestawów automatycznie spróbuj i kompensacji wykluczonym maszyn wirtualnych, jednak nie ma gwarancji, że przydział powiedzie się. Zaleca się, że tylko funkcja automatycznego skalowania na zestawy skalowania niskiego priorytetu podczas ustawiania zasady wykluczania Usuń, aby uniknąć kosztów dysków i naciśnięcie klawisza limity przydziału. 

> [!NOTE]
> Podczas udostępniania wersji zapoznawczej, można ustawić zasady wykluczania przy użyciu [szablonów usługi Azure Resource Manager](#use-azure-resource-manager-templates). 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>Wdrażanie maszyn wirtualnych niskiego priorytetu na skali ustawia

Aby wdrożyć niskiego priorytetu maszyny wirtualne na zestawy skalowania, można ustawić nowy *priorytet* flaga *małej*. Wszystkie maszyny wirtualne w zestawie skali zostanie ustawiona do niskiego priorytetu. Aby utworzyć skali Ustawianie niskiego priorytetu maszyn wirtualnych, użyj jednej z następujących metod:
- [Interfejs wiersza polecenia platformy Azure 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Szablony usługi Azure Resource Manager](#use-azure-resource-manager-templates)

## <a name="use-the-azure-cli-20"></a>Użyj Azure CLI 2.0

Proces tworzenia skali Ustawianie niskiego priorytetu maszyn wirtualnych jest taka sama, jak określono w [wprowadzenie artykułu](quick-create-cli.md). Po prostu Dodaj "--priorytet" Parametr interfejsu wiersza polecenia wywołania i ustaw ją na *małej* jak pokazano w poniższym przykładzie:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Low
```

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Proces tworzenia skali Ustawianie niskiego priorytetu maszyn wirtualnych jest taka sama, jak określono w [wprowadzenie artykułu](quick-create-powershell.md).
Po prostu Dodaj "-priorytet" parametr [AzureRmVmssConfig nowy](/powershell/module/azurerm.compute/new-azurermvmssconfig) i ustaw ją na *małej* jak pokazano w poniższym przykładzie:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Użyj szablonów usługi Azure Resource Manager

Proces tworzenia zestawu skali, który używa niskiego priorytetu maszyn wirtualnych jest taka sama, zgodnie z opisem w artykule Rozpoczęto pobieranie dla [Linux](quick-create-template-linux.md) lub [Windows](quick-create-template-windows.md). Dodaj właściwość 'priority' do *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* zasobów, wpisz w szablonie i określ *małej* jako wartość. Należy użyć *2017-10-30-preview* wersja interfejsu API lub nowszej. 

Aby skonfigurować zasady wykluczania do usunięcia, Dodaj parametr "evictionPolicy" i ustaw ją na *usunąć*.

Poniższy przykład tworzy zestaw o nazwie skalowania niskiego priorytetu Linux *myScaleSet* w *zachodnie środkowe stany*, który będzie *usunąć* maszyn wirtualnych w skali ustawiać wykluczenia:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Low",
       "evictionPolicy": "delete",
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
## <a name="next-steps"></a>Kolejne kroki
Teraz, po utworzeniu skali Ustawianie niskiego priorytetu maszyn wirtualnych, spróbuj wdrożyć naszych [automatycznego skalowania szablonu przy użyciu niskiego priorytetu](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Zapoznaj się z [zestaw skali maszyny wirtualnej cennikiem](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) Aby uzyskać szczegółowe informacje o cenach.