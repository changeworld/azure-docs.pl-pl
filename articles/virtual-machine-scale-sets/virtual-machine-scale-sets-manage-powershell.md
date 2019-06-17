---
title: Zarządzanie zestawami skalowania maszyn wirtualnych przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Pojemność zestawu typowych poleceń cmdlet Azure PowerShell do zarządzania programem Virtual Machine Scale Sets, takie jak uruchamianie i zatrzymywanie wystąpienia lub zmienianie skali.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: a6474320fd8b1545d61320cd43e155ab077ba310
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64683526"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Zarządzanie maszyny wirtualnej zestawu skalowania przy użyciu programu Azure PowerShell

W całym cyklu życia zestawu skalowania maszyn wirtualnych konieczne może być uruchomienie jednego lub większej liczby zadań zarządzania. Ponadto może pojawić się potrzeba tworzenia skryptów automatyzujących różne zadania cyklu życia. Ten artykuł szczegółowo opisuje niektóre typowe poleceń cmdlet programu Azure PowerShell, które umożliwiają wykonywanie tych zadań.

Jeśli musisz utworzyć zestaw skalowania maszyn wirtualnych, możesz to zrobić [Tworzenie zestawu skalowania przy użyciu programu Azure PowerShell](quick-create-powershell.md).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="view-information-about-a-scale-set"></a>Wyświetl informacje o zestawie skalowania
Aby wyświetlić ogólne informacje o zestawie skalowania, użyj [Get AzVmss](/powershell/module/az.compute/get-azvmss). Poniższy przykład pobiera informacje o zestawu skalowania o nazwie *myScaleSet* w *myResourceGroup* grupy zasobów. Wprowadź własne nazwy w następujący sposób:

```powershell
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Wyświetlanie maszyn wirtualnych w zestawie skalowania
Aby wyświetlić listę wystąpień maszyn wirtualnych w zestawie skalowania, użyj [Get AzVmssVM](/powershell/module/az.compute/get-azvmssvm). Poniższy przykład wyświetla listę wszystkich wystąpień maszyn wirtualnych w zestawie o nazwie skalowania *myScaleSet* i *myResourceGroup* grupy zasobów. Należy podać własne wartości dla tych nazw:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Aby wyświetlić dodatkowe informacje na temat określonego wystąpienia maszyny Wirtualnej, Dodaj `-InstanceId` parametr [Get AzVmssVM](/powershell/module/az.compute/get-azvmssvm) i określ wystąpienie, aby wyświetlić. Poniższy przykład wyświetla informacje o wystąpieniu maszyny Wirtualnej *0* w zestawu skalowania o nazwie *myScaleSet* i *myResourceGroup* grupy zasobów. Wprowadź własne nazwy w następujący sposób:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Zmienianie pojemności zestawu skalowania
Poprzednich poleceniach wykazało, że informacje o zestawie skalowania oraz wystąpień maszyn wirtualnych. Aby zwiększyć lub zmniejszyć liczbę wystąpień w zestawie skalowania, można zmienić pojemność. Automatyczne zestawu skalowania tworzy lub usuwa wymaganą liczbę maszyn wirtualnych, a następnie konfiguruje maszynom wirtualnym odbierać ruchu aplikacji.

Najpierw utwórz obiekt zestawu skalowania za pomocą polecenia [Get-AzVmss](/powershell/module/az.compute/get-azvmss), a następnie podaj nową wartość parametru `sku.capacity`. Aby zastosować zmiany pojemności, użyj polecenia [Update-AzVmss](/powershell/module/az.compute/update-azvmss). Następujące aktualizacje przykład *myScaleSet* w *myResourceGroup* grupę zasobów do pojemności *5* wystąpień. Podaj własne wartości w następujący sposób:

```powershell
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

Aktualizacja pojemności zestawu skalowania trwa kilka minut. Jeśli zmniejszyć pojemność skalowania ustawiony, maszyny wirtualne o najwyższym wystąpienie, które identyfikatory najpierw są usuwane.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Zatrzymywanie i uruchamianie maszyn wirtualnych w zestawie skalowania
Aby zatrzymać co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj polecenia [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss). Parametr `-InstanceId` umożliwia wskazanie maszyn wirtualnych, które mają zostać zatrzymane. Jeśli nie podasz identyfikatora wystąpienia, zostaną zatrzymane wszystkie maszyny wirtualne w zestawie skalowania. Aby zatrzymać wiele maszyn wirtualnych, każdy identyfikator wystąpienia należy oddzielić przecinkami.

W poniższym przykładzie zostaje zatrzymane wystąpienie *0* w zestawu skalowania o nazwie *myScaleSet* i *myResourceGroup* grupy zasobów. Podaj własne wartości w następujący sposób:

```powershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Domyślnie następuje cofnięcie przydziału zatrzymanych maszyn wirtualnych, co sprawia, że opłaty za operacje obliczeniowe nie są naliczane. Jeśli zatrzymana maszyna wirtualna ma zachować stan Aprowizowano, dodaj parametr `-StayProvisioned` do poprzedniego polecenia. W przypadku zatrzymanych maszyn wirtualnych ze stanem Aprowizowano są naliczane opłaty za operacje obliczeniowe.


### <a name="start-vms-in-a-scale-set"></a>Uruchom maszyny wirtualne w zestawie skalowania
Aby uruchomić co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj polecenia [Start-AzVmss](/powershell/module/az.compute/start-azvmss). Parametr `-InstanceId` umożliwia wskazanie maszyn wirtualnych, które mają zostać uruchomione. Jeśli nie podasz identyfikatora wystąpienia, zostaną uruchomione wszystkie maszyny wirtualne w zestawie skalowania. Można uruchomić wiele maszyn wirtualnych, każdy identyfikator wystąpienia należy oddzielić przecinkami.

Poniższy przykład uruchamia wystąpienie *0* w zestawu skalowania o nazwie *myScaleSet* i *myResourceGroup* grupy zasobów. Podaj własne wartości w następujący sposób:

```powershell
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Ponowne uruchamianie maszyn wirtualnych w zestawie skalowania
Aby ponownie uruchomić co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj [AzVmss ponowne uruchomienie](/powershell/module/az.compute/restart-azvmss). Parametr `-InstanceId` umożliwia wskazanie maszyn wirtualnych, które mają zostać uruchomione ponownie. Jeśli nie podasz identyfikatora wystąpienia, wszystkie maszyny wirtualne w zestawie skalowania zostaną uruchomione ponownie. Aby ponownie uruchomić wiele maszyn wirtualnych, każdy identyfikator wystąpienia należy oddzielić przecinkami.

Poniższy przykład powoduje ponowne uruchomienie wystąpienia *0* w zestawu skalowania o nazwie *myScaleSet* i *myResourceGroup* grupy zasobów. Podaj własne wartości w następujący sposób:

```powershell
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Usuwanie maszyn wirtualnych w zestawie skalowania
Aby usunąć co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj [AzVmss Usuń](/powershell/module/az.compute/remove-azvmss). `-InstanceId` Parametr umożliwia określenie co najmniej jedną maszynę do usunięcia. Jeśli nie podasz Identyfikatora wystąpienia, zostaną usunięte wszystkie maszyny wirtualne w zestawie skalowania. Aby usunąć wiele maszyn wirtualnych, każdy identyfikator wystąpienia należy oddzielić przecinkami.

Poniższy przykład usuwa wystąpienie *0* w zestawu skalowania o nazwie *myScaleSet* i *myResourceGroup* grupy zasobów. Podaj własne wartości w następujący sposób:

```powershell
Remove-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Kolejne kroki
Inne typowe zadania dotyczące zestawów skalowania obejmują jak [wdrażania aplikacji](virtual-machine-scale-sets-deploy-app.md), i [uaktualnić wystąpienia maszyny Wirtualnej](virtual-machine-scale-sets-upgrade-scale-set.md). Można również użyć programu Azure PowerShell [Konfigurowanie reguł automatycznego skalowania](virtual-machine-scale-sets-autoscale-overview.md).
