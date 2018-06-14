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
ms.date: 05/01/2018
ms.author: memccror
ms.openlocfilehash: 5c0726ea0da288d5306e28b101e4d3b59605b443
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894911"
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>Maszyny wirtualne niskiego priorytetu na zestawy skalowania (wersja zapoznawcza)

Za pomocą niskiego priorytetu maszyny wirtualne na zestawy skalowania umożliwia można wykorzystać możliwości unutilized na znaczne oszczędności. W dowolnym momencie w czasie, gdy platforma Azure wymaga pojemność wstecz infrastruktury platformy Azure Wyklucz niskiego priorytetu maszyn wirtualnych. W związku z tym niskiego priorytetu maszyny wirtualne są doskonałe rozwiązanie dla obciążeń, które może obsłużyć przerw w działaniu jak przetwarzania zadań, środowiska i testowania, obliczeń dużych obciążeń i wsadowego.

Ilość dostępnej pojemności unutilized mogą się różnić w zależności od rozmiaru, region, godzinę i. Podczas wdrażania maszyn wirtualnych niskiego priorytetu na skali ustawia Azure przydzielić maszyn wirtualnych, jeśli jest dostępna pojemność, ale nie istnieje żadne umowy SLA dla tych maszyn wirtualnych. Zestaw skali niskiego priorytetu jest wdrożony w domenie pojedynczej awarii i oferuje gwarancje nie wysokiej dostępności.

## <a name="eviction-policy"></a>Zasady wykluczania

Podczas tworzenia zestawy skalowania o niskim priorytecie, można ustawić zasady wykluczenia, *Deallocate* (ustawienie domyślne) lub *usunąć*. 

*Deallocate* zasad przenosi wykluczonym maszyn wirtualnych do zatrzymana alokację stanu, co umożliwia wdrożenie wykluczonym wystąpień. Jednak nie ma gwarancji, że przydział powiedzie się. Deallocated maszyn wirtualnych zostanie zaliczony limitu przydziału wystąpienia zestawu skali i zostanie naliczona dla podstawowych dysków. 

Jeśli chcesz maszyn wirtualnych w Twojej zestaw ma zostać usunięty, gdy są one wykluczaniu skalowania niskiego priorytetu, możesz ustawić zasady wykluczania *usunąć*. Usuń zasady wykluczenia można utworzyć nowych maszyn wirtualnych, zwiększając właściwość count wystąpienia zestawu skali. Wykluczone maszyn wirtualnych zostaną usunięte wraz z ich odpowiednie dyski i dlatego użytkownik nie zostanie obciążona dla magazynu. Umożliwia także funkcję skalowania automatycznego skalowania zestawów automatycznie spróbuj i kompensacji wykluczonym maszyn wirtualnych, jednak nie ma gwarancji, że przydział powiedzie się. Zaleca się, że tylko funkcja automatycznego skalowania na zestawy skalowania niskiego priorytetu podczas ustawiania zasady wykluczania Usuń, aby uniknąć kosztów dysków i naciśnięcie klawisza limity przydziału. 

> [!NOTE]
> Podczas udostępniania wersji zapoznawczej, można ustawić zasady wykluczania przy użyciu [portalu Azure](#use-the-azure-portal) i [szablonów usługi Azure Resource Manager](#use-azure-resource-manager-templates). 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>Wdrażanie maszyn wirtualnych niskiego priorytetu na skali ustawia

Aby wdrożyć niskiego priorytetu maszyny wirtualne na zestawy skalowania, można ustawić nowy *priorytet* flaga *małej*. Wszystkie maszyny wirtualne w zestawie skali zostanie ustawiona do niskiego priorytetu. Aby utworzyć skali Ustawianie niskiego priorytetu maszyn wirtualnych, użyj jednej z następujących metod:
- [Azure Portal](#use-the-azure-portal)
- [Interfejs wiersza polecenia platformy Azure 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Szablony usługi Azure Resource Manager](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Proces tworzenia zestawu skali, który używa niskiego priorytetu maszyn wirtualnych jest taka sama, jak określono w [wprowadzenie artykułu](quick-create-portal.md). Wdrażając zestaw skali, można ustawić flagi niskiego priorytetu i zasady wykluczania: ![tworzenia skali Ustawianie niskiego priorytetu maszyn wirtualnych](media/virtual-machine-scale-sets-use-low-priority/vmss-low-priority-portal.png)

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

Proces tworzenia zestawu skali, który używa niskiego priorytetu maszyn wirtualnych jest taka sama, zgodnie z opisem w artykule Rozpoczęto pobieranie dla [Linux](quick-create-template-linux.md) lub [Windows](quick-create-template-windows.md). Dodaj właściwość 'priority' do *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* zasobów, wpisz w szablonie i określ *małej* jako wartość. Należy użyć *2018-03-01* wersja interfejsu API lub nowszej. 

Aby skonfigurować zasady wykluczania do usunięcia, Dodaj parametr "evictionPolicy" i ustaw ją na *usunąć*.

Poniższy przykład tworzy zestaw o nazwie skalowania niskiego priorytetu Linux *myScaleSet* w *zachodnie środkowe stany*, który będzie *usunąć* maszyn wirtualnych w skali ustawiać wykluczenia:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2018-03-01",
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
## <a name="faq"></a>Często zadawane pytania

### <a name="can-i-convert-existing-scale-sets-to-low-priority-scale-sets"></a>Czy mogę przekonwertować istniejące zestawy skalowania do zestawów skalowania o niskim priorytecie
Nie, ustawienie flagi niskiego priorytetu jest obsługiwana tylko w czasie tworzenia.

### <a name="can-i-create-a-scale-set-with-both-regular-vms-and-low-priority-vms"></a>Czy mogę tworzyć skali ustawić z regularnych maszyn wirtualnych i maszyn wirtualnych o niskim priorytecie
Nie, zestaw skalowania nie może obsługiwać więcej niż jeden typ priorytet.

### <a name="how-is-quota-managed-for-low-priority-vms"></a>Sposób zarządzania limitu przydziału dla maszyn wirtualnych niskiego priorytetu?
Niskiego priorytetu maszyny wirtualne i regularne maszyn wirtualnych korzystają z tej samej puli przydziału. 

### <a name="can-i-use-autoscale-with-low-priority-scale-sets"></a>Czy można użyć automatycznego skalowania zestawów skalowania o niskim priorytecie?
Tak, można ustawić Skalowanie automatyczne reguły w zestawie skalowania o niskim priorytecie. Jeśli maszyny wirtualne są usunięty, skalowania automatycznego spróbować utworzyć nowe niskiego priorytetu maszyny wirtualne. Należy pamiętać, że nie ma gwarancji tej pojemności jednak. 

### <a name="does-autoscale-work-with-both-eviction-policies-deallocate-and-delete"></a>Funkcja automatycznego skalowania działa z obie zasady wykluczania (deallocate i Usuń)?
Zalecane jest, aby ustawić zasady wykluczenia, aby usunąć przy użyciu automatycznego skalowania. Jest to spowodowane deallocated wystąpienia są uwzględniane w zestawie skali liczba pojemności. Podczas korzystania z automatycznego skalowania, prawdopodobnie nastąpi trafienie Twojej liczba wystąpień docelowych szybko z powodu wystąpienia deallocated, wykluczone. 

## <a name="next-steps"></a>Kolejne kroki
Teraz, po utworzeniu skali Ustawianie niskiego priorytetu maszyn wirtualnych, spróbuj wdrożyć naszych [automatycznego skalowania szablonu przy użyciu niskiego priorytetu](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Zapoznaj się z [zestaw skali maszyny wirtualnej cennikiem](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) Aby uzyskać szczegółowe informacje o cenach.