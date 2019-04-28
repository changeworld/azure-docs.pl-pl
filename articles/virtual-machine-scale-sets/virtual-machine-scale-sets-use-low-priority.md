---
title: Tworzenie zestawu skalowania na platformie Azure, który używa maszyn wirtualnych o niskim priorytecie (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć zestawy skalowania maszyn wirtualnych platformy Azure, które umożliwia zaoszczędzić na kosztach maszyny wirtualne o niskim priorytecie
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
ms.openlocfilehash: 861c68ae8163e0ba8c2af2a3d96153ac3e84855f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803224"
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>Maszyny wirtualne o niskim priorytecie, na zestawach skalowania (wersja zapoznawcza)

Przy użyciu maszyn wirtualnych o niskim priorytecie na zestawach skalowania pozwala wykorzystać możliwości unutilized na znaczne oszczędności. W dowolnym momencie w czasie, gdy platforma Azure wymaga pojemności ponownie infrastruktury platformy Azure Wyklucz maszyny wirtualne o niskim priorytecie. W związku z tym maszyny wirtualne o niskim priorytecie są doskonałe dla obciążeń, które może obsłużyć przerwy, takich jak wsadowo zadania, środowiska deweloperskie i testowe, obciążeń dużych obliczeń i.

Ilość dostępnej pojemności unutilized może się różnić w zależności od rozmiaru, region, godzinę i. Podczas wdrażania maszyn wirtualnych o niskim priorytecie w skali ustawia Azure przydzielić maszyn wirtualnych, jeśli jest dostępna pojemność, ale nie ma umowy SLA dla tych maszyn wirtualnych. Zestaw skalowania o niskim priorytecie jest wdrożony w domenie pojedynczej awarii i oferuje gwarancje nie wysokiej dostępności.

## <a name="eviction-policy"></a>Zasady eksmisji

Podczas tworzenia zestawów skalowania o niskim priorytecie, można ustawić zasady eksmisji, *Deallocate* (ustawienie domyślne) lub *Usuń*. 

*Deallocate* zasad przenosi wykluczonym maszyn wirtualnych do stanu zatrzymana bez alokacji, umożliwiając ponowne wdrażanie wystąpień wykluczone. Jednak nie ma żadnej gwarancji, że alokacji zostanie wykonana pomyślnie. Cofnięto alokację maszyny wirtualne będą uwzględniane w limicie przydziału wystąpienia zestawu skalowania, a opłata wyniesie dla podstawowych dysków. 

Jeśli chcesz maszyn wirtualnych w zestawie do usunięcia, gdy są one wykluczona skalowania o niskim priorytecie, można ustawić zasady eksmisji *Usuń*. Usuń zasady eksmisji można tworzyć nowe maszyny wirtualne, zwiększając właściwości liczba wystąpień zestawu skalowania. Wykluczone maszyny wirtualne zostaną usunięte wraz z ich odpowiednie dyski i w związku z tym użytkownik nie zostanie obciążona do przechowywania. Umożliwia także funkcja automatycznego skalowania, zestawów skalowania i automatycznie spróbuj kompensuje wykluczonym maszyn wirtualnych, jednak nie ma żadnej gwarancji, że alokacji zostanie wykonana pomyślnie. Zalecane jest, że używasz tylko z funkcji automatycznego skalowania na zestawach skalowania o niskim priorytecie po ustawieniu zasady eksmisji, można usunąć, aby uniknąć kosztów na dyskach i osiągnięcia limitów przydziału. 

> [!NOTE]
> W trakcie okresu zapoznawczego, można skonfigurować zasady eksmisji przy użyciu [witryny Azure portal](#use-the-azure-portal) i [szablonów usługi Azure Resource Manager](#use-azure-resource-manager-templates). 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>Ustawia wdrażania maszyn wirtualnych o niskim priorytecie w skali

Aby wdrożyć o niskim priorytecie maszyn wirtualnych w zestawach skalowania, można ustawić nowy *priorytet* flaga *niski*. Wszystkie maszyny wirtualne w zestawie skalowania ustawi o niskim priorytecie. Aby utworzyć zestaw skalowania z maszyn wirtualnych o niskim priorytecie, użyj jednej z następujących metod:
- [Azure Portal](#use-the-azure-portal)
- Interfejs wiersza polecenia platformy Azure
- [Azure PowerShell](#use-azure-powershell)
- [Szablony usługi Azure Resource Manager](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Proces tworzenia zestawu skalowania, który używa maszyn wirtualnych o niskim priorytecie jest taka sama, zgodnie z opisem w [wprowadzenie artykułu](quick-create-portal.md). Gdy wdrażasz zestaw skalowania, można ustawić flagi o niskim priorytecie i zasady eksmisji: ![Tworzenie zestawu skalowania przy użyciu maszyn wirtualnych o niskim priorytecie](media/virtual-machine-scale-sets-use-low-priority/vmss-low-priority-portal.png)

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

Proces, aby utworzyć zestaw skalowania z maszyn wirtualnych o niskim priorytecie jest taki sam, zgodnie z opisem w [wprowadzenie artykułu](quick-create-cli.md). Wystarczy dodać atrybut "--priorytet" parametr dla interfejsu wiersza polecenia wywołania i ustaw ją na *niski* jak pokazano w poniższym przykładzie:

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

Proces, aby utworzyć zestaw skalowania z maszyn wirtualnych o niskim priorytecie jest taki sam, zgodnie z opisem w [wprowadzenie artykułu](quick-create-powershell.md).
Wystarczy dodać atrybut "— priorytet" parametr [New AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) i ustaw ją na *niski* jak pokazano w poniższym przykładzie:

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Użyj szablonów usługi Azure Resource Manager

Proces tworzenia zestawu skalowania, który używa maszyn wirtualnych o niskim priorytecie jest taka sama, zgodnie z opisem w artykule wprowadzającym dla [Linux](quick-create-template-linux.md) lub [Windows](quick-create-template-windows.md). Dodaj właściwość "priority" do *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* zasobów, wpisz w szablonie i określ *niski* jako wartość. Należy użyć *2018-03-01* wersji interfejsu API lub nowszej. 

Aby ustawić zasady eksmisji do usunięcia, Dodaj parametr "evictionPolicy" i ustaw ją na *Usuń*.

Poniższy przykład tworzy systemu Linux o niskim priorytecie zestawu skalowania o nazwie *myScaleSet* w *zachodnio-środkowe stany USA*, który będzie *Usuń* maszyn wirtualnych w zestawie na eksmisji skalowania:

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

### <a name="can-i-convert-existing-scale-sets-to-low-priority-scale-sets"></a>Czy mogę przekonwertować istniejące zestawy skalowania do zestawów skalowania o niskim priorytecie?
Nie, ustawienie flagi o niskim priorytecie jest obsługiwana tylko w czasie jego tworzenia.

### <a name="can-i-create-a-scale-set-with-both-regular-vms-and-low-priority-vms"></a>Można utworzyć zestaw skalowania z zarówno zwykłymi maszynami wirtualnymi, jak i maszyny wirtualne o niskim priorytecie?
Nie, zestaw skalowania nie może obsługiwać więcej niż jeden typ priorytetu.

### <a name="how-is-quota-managed-for-low-priority-vms"></a>Jak odbywa się limitu przydziału dla maszyn wirtualnych o niskim priorytecie?
Maszyny wirtualne o niskim priorytecie i zwykłymi maszynami wirtualnymi używają tej samej puli limitu przydziału. 

### <a name="can-i-use-autoscale-with-low-priority-scale-sets"></a>Czy można używać automatycznego skalowania, za pomocą zestawów skalowania o niskim priorytecie?
Tak, można ustawiać reguły skalowania automatycznego zestawu skalowania o niskim priorytecie. Jeśli Twoje maszyny wirtualne są usunięty, automatycznego skalowania spróbować tworzyć nowe maszyny wirtualne o niskim priorytecie. Należy pamiętać, że w tej pojemności nie są gwarantowane jednak. 

### <a name="does-autoscale-work-with-both-eviction-policies-deallocate-and-delete"></a>Działa automatyczne skalowanie w usłudze obie zasady eksmisji (Cofnij przydział i Usuń)?
Zaleca się, że są ustawione zasady eksmisji, aby usunąć podczas korzystania ze skalowania automatycznego. Jest to spowodowane cofnięto alokację wystąpień są przeliczane względem liczba Twoich pojemności w zestawie skalowania. Podczas korzystania ze skalowania automatycznego, prawdopodobnie nastąpi trafienie usługi docelowej liczby wystąpień szybko z powodu wystąpienia przydział zostanie cofnięty, wykluczone. 

## <a name="next-steps"></a>Kolejne kroki
Teraz, po utworzeniu zestawu skalowania przy użyciu maszyn wirtualnych o niskim priorytecie, spróbuj wdrożyć nasze [szablonu skalowania automatycznego za pomocą o niskim priorytecie](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Zapoznaj się z [zestawu skalowania maszyn wirtualnych stronę z cennikiem](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) szczegółowy cennik.
