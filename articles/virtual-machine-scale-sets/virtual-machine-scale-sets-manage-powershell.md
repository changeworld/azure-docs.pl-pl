---
title: Zarządzanie zestawami skalowania maszyn wirtualnych za pomocą programu Azure PowerShell
description: Typowe polecenia cmdlet programu Azure PowerShell do zarządzania zestawami skalowania maszyny wirtualnej, takie jak uruchamianie i zatrzymywania wystąpienia lub zmienianie pojemności zestawu skalowania.
author: cynthn
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: cc83c8b73c73b2aa70bc36bad175e5c19c1ab700
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275714"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Zarządzanie zestawem skalowania maszyny wirtualnej za pomocą programu Azure PowerShell

W całym cyklu życia zestawu skalowania maszyn wirtualnych konieczne może być uruchomienie jednego lub większej liczby zadań zarządzania. Ponadto może pojawić się potrzeba tworzenia skryptów automatyzujących różne zadania cyklu życia. W tym artykule opisano niektóre typowe polecenia cmdlet programu Azure PowerShell, które umożliwiają wykonywanie tych zadań.

Jeśli chcesz utworzyć zestaw skalowania maszyny wirtualnej, możesz [utworzyć zestaw skalowania za pomocą programu Azure PowerShell](quick-create-powershell.md).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="view-information-about-a-scale-set"></a>Wyświetlanie informacji o zestawie skalowania
Aby wyświetlić ogólne informacje o zestawie skalowania, należy użyć pliku [Get-AzVmss](/powershell/module/az.compute/get-azvmss). Poniższy przykład pobiera informacje o zestawie skalowania o nazwie *myScaleSet* w grupie zasobów *myResourceGroup.* Wprowadź swoje własne nazwy w następujący sposób:

```powershell
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Wyświetlanie maszyn wirtualnych w zestawie skalowania
Aby wyświetlić listę wystąpienia maszyny Wirtualnej w zestawie skalowania, należy użyć pliku [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). Poniższy przykład zawiera listę wszystkich wystąpień maszyn wirtualnych w zestawie skalowania o nazwie *myScaleSet* i w grupie zasobów *myResourceGroup.* Podaj własne wartości dla tych nazw:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Aby wyświetlić dodatkowe informacje o określonym `-InstanceId` wystąpieniu maszyny Wirtualnej, dodaj parametr do [pliku Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) i określ wystąpienie do wyświetlenia. W poniższym przykładzie wyświetlają informacje o wystąpieniu maszyny Wirtualnej *0* w zestawie skalowania o nazwie *myScaleSet* i grupie zasobów *myResourceGroup.* Wprowadź swoje własne nazwy w następujący sposób:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Zmienianie pojemności zestawu skalowania
Poprzednie polecenia pokazywały informacje o zestawie skalowania i wystąpieniach maszyny Wirtualnej. Aby zwiększyć lub zmniejszyć liczbę wystąpień w zestawie skalowania, można zmienić pojemność. Zestaw skalowania automatycznie tworzy lub usuwa wymaganą liczbę maszyn wirtualnych, a następnie konfiguruje maszyny wirtualne do odbierania ruchu aplikacji.

Najpierw utwórz obiekt zestawu skalowania za pomocą polecenia [Get-AzVmss](/powershell/module/az.compute/get-azvmss), a następnie podaj nową wartość parametru `sku.capacity`. Aby zastosować zmiany pojemności, użyj polecenia [Update-AzVmss](/powershell/module/az.compute/update-azvmss). Poniższy przykład aktualizuje *myScaleSet* w grupie zasobów *myResourceGroup* do pojemności *5* wystąpień. Podaj własne wartości w następujący sposób:

```powershell
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

Aktualizacja pojemności zestawu skalowania trwa kilka minut. Jeśli zmniejszysz pojemność zestawu skalowania, maszyny wirtualne o najwyższych identyfikatorach wystąpień zostaną najpierw usunięte.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Zatrzymywanie i uruchamianie maszyn wirtualnych w zestawie skalowania
Aby zatrzymać co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj polecenia [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss). Parametr `-InstanceId` umożliwia wskazanie maszyn wirtualnych, które mają zostać zatrzymane. Jeśli nie podasz identyfikatora wystąpienia, zostaną zatrzymane wszystkie maszyny wirtualne w zestawie skalowania. Aby zatrzymać wiele maszyn wirtualnych, należy oddzielić każdy identyfikator wystąpienia przecinkiem.

Poniższy przykład zatrzymuje wystąpienie *0* w zestawie skalowania o nazwie *myScaleSet* i grupie zasobów *myResourceGroup.* Podaj własne wartości w następujący sposób:

```powershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Domyślnie następuje cofnięcie przydziału zatrzymanych maszyn wirtualnych, co sprawia, że opłaty za operacje obliczeniowe nie są naliczane. Jeśli zatrzymana maszyna wirtualna ma zachować stan Aprowizowano, dodaj parametr `-StayProvisioned` do poprzedniego polecenia. W przypadku zatrzymanych maszyn wirtualnych ze stanem Aprowizowano są naliczane opłaty za operacje obliczeniowe.


### <a name="start-vms-in-a-scale-set"></a>Uruchamianie maszyn wirtualnych w zestawie skalowania
Aby uruchomić co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj polecenia [Start-AzVmss](/powershell/module/az.compute/start-azvmss). Parametr `-InstanceId` umożliwia wskazanie maszyn wirtualnych, które mają zostać uruchomione. Jeśli nie podasz identyfikatora wystąpienia, zostaną uruchomione wszystkie maszyny wirtualne w zestawie skalowania. Aby uruchomić wiele maszyn wirtualnych, należy oddzielić każdy identyfikator wystąpienia przecinkiem.

W poniższym przykładzie rozpoczyna się wystąpienie *0* w zestawie skalowania o nazwie *myScaleSet* i grupie zasobów *myResourceGroup.* Podaj własne wartości w następujący sposób:

```powershell
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Ponowne uruchamianie maszyn wirtualnych w zestawie skalowania
Aby ponownie uruchomić jedną lub więcej maszyn wirtualnych w zestawie skalowania, użyj [funkcji Uruchom ponownie AzVmss](/powershell/module/az.compute/restart-azvmss). Parametr `-InstanceId` umożliwia wskazanie maszyn wirtualnych, które mają zostać uruchomione ponownie. Jeśli nie podasz identyfikatora wystąpienia, wszystkie maszyny wirtualne w zestawie skalowania zostaną uruchomione ponownie. Aby ponownie uruchomić wiele maszyn wirtualnych, należy oddzielić każdy identyfikator wystąpienia przecinkiem.

W poniższym przykładzie wstępuje ponowne uruchomienie wystąpienia *0* w zestawie skalowania o nazwie *myScaleSet* i grupie zasobów *myResourceGroup.* Podaj własne wartości w następujący sposób:

```powershell
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Usuwanie maszyn wirtualnych z zestawu skalowania
Aby usunąć jedną lub więcej maszyn wirtualnych w zestawie skalowania, użyj [usuń azvmss](/powershell/module/az.compute/remove-azvmss). Parametr `-InstanceId` umożliwia określenie jednej lub więcej maszyn wirtualnych do usunięcia. Jeśli nie określisz identyfikatora wystąpienia, wszystkie maszyny wirtualne w zestawie skalowania zostaną usunięte. Aby usunąć wiele maszyn wirtualnych, należy oddzielić każdy identyfikator wystąpienia przecinkiem.

Poniższy przykład usuwa wystąpienie *0* w zestawie skalowania o nazwie *myScaleSet* i grupie zasobów *myResourceGroup.* Podaj własne wartości w następujący sposób:

```powershell
Remove-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Następne kroki
Inne typowe zadania dotyczące zestawów skalowania obejmują sposób [wdrażania aplikacji](virtual-machine-scale-sets-deploy-app.md)i [uaktualnianie wystąpień maszyn wirtualnych.](virtual-machine-scale-sets-upgrade-scale-set.md) Za pomocą programu Azure PowerShell można również [skonfigurować reguły automatycznego skalowania.](virtual-machine-scale-sets-autoscale-overview.md)
