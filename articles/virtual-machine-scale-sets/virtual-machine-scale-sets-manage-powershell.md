---
title: Zarządzanie zestawy skalowania maszyny wirtualnej przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Pojemność zestawu wspólnych Azure poleceń cmdlet programu PowerShell Zarządzanie zestawy skalowania maszyny wirtualnej, takie jak jak uruchamianie i zatrzymywanie wystąpienie lub zmień skali.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: c463dd26c106b3178becc977a8afd742220d7973
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Zarządzaj skalowania maszyny wirtualnej, ustawić przy użyciu programu Azure PowerShell
W całym cyklu życia zestawu skali maszyny wirtualnej może być konieczne uruchomienie jednego lub więcej zadań zarządzania. Ponadto może pojawić się potrzeba tworzenia skryptów automatyzujących różne zadania cyklu życia. Ten artykuł zawiera szczegóły dotyczące niektórych typowych poleceń cmdlet programu Azure PowerShell, które umożliwiają wykonywanie tych zadań.

Aby wykonać te zadania zarządzania, należy najnowsze modułu Azure PowerShell. Aby uzyskać informacje, zobacz [wprowadzenie do programu Azure PowerShell](/powershell/azure/get-started-azureps). Jeśli musisz utworzyć zestaw skali maszyny wirtualnej, możesz [tworzenia skali ustawiony za pomocą programu Azure PowerShell](quick-create-powershell.md).


## <a name="view-information-about-a-scale-set"></a>Wyświetl informacje o zestawie skali
Aby wyświetlić ogólne informacje o zestawie skali, użyj [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss). Poniższy przykład pobiera informacje o zestaw o nazwie skalowania *myScaleSet* w *myResourceGroup* grupy zasobów. Wprowadź własne nazwy w następujący sposób:

```powershell
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Wyświetlanie maszyn wirtualnych w zestawie skalowania
Aby wyświetlić listę wystąpienia maszyny Wirtualnej w zestawie skalowania, użyj [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). Poniższy przykład listy wszystkich wystąpień maszyn wirtualnych w zestaw o nazwie skalowania *myScaleSet* i *myResourceGroup* grupy zasobów. Należy podać własne wartości dla następujących nazw:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Aby wyświetlić dodatkowe informacje na temat określonego wystąpienia maszyny Wirtualnej, należy dodać `-InstanceId` parametr [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) i określ wystąpienie do wyświetlenia. Poniższy przykład wyświetla informacje o wystąpieniu maszyny Wirtualnej *0* w zestaw o nazwie skalowania *myScaleSet* i *myResourceGroup* grupy zasobów. Wprowadź własne nazwy w następujący sposób:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Zmień pojemność zestawu skalowania
Powyższych poleceń pokazano informacji o zestawie skali i wystąpień maszyny Wirtualnej. Aby zwiększyć lub zmniejszyć liczbę wystąpień w zestawie skalowania, można zmienić pojemność. Automatycznie zestaw skalowania tworzy lub usuwa wymaganej liczby maszyn wirtualnych, a następnie konfiguruje maszyn wirtualnych na odbieranie ruchu w ramach aplikacji.

Najpierw utwórz obiekt zestawu skali z [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss), następnie określ nową wartość dla `sku.capacity`. Aby zastosować zmiany pojemności, użyj [AzureRmVmss aktualizacji](/powershell/module/azurerm.compute/update-azurermvmss). Następujące aktualizacje przykład *myScaleSet* w *myResourceGroup* grupę zasobów do pojemności *5* wystąpień. Podać własne wartości w następujący sposób:

```powershell
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

Jeśli ustawiona przyjmuje kilka minut, aby zaktualizować pojemności na skalę. Jeśli pojemność skali zestaw, maszyn wirtualnych o najwyższym wystąpienia, które identyfikatory są najpierw usunąć.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Zatrzymywania i uruchamiania maszyn wirtualnych w zestawie skalowania
Aby zatrzymać przynajmniej jednej maszyny wirtualnej w zestawie skalowania, użyj [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss). `-InstanceId` Parametr umożliwia określenie przynajmniej jednej maszyny wirtualnej, aby zatrzymać. Jeśli nie określisz Identyfikatora wystąpienia, wszystkich maszyn wirtualnych w zestawie skalowania zostały zatrzymane. Aby zatrzymać wiele maszyn wirtualnych, każdy identyfikator wystąpienia należy oddzielić przecinkami.

W następującym przykładzie zatrzymano wystąpienia *0* w zestaw o nazwie skalowania *myScaleSet* i *myResourceGroup* grupy zasobów. Podaj wartości w następujący sposób:

```powershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Domyślnie zatrzymania maszyny wirtualne są alokację, a nie naliczenie opłat za obliczenia. Jeśli chcesz, aby maszyna wirtualna może pozostawać w stanie udostępnione po zatrzymaniu, Dodaj `-StayProvisioned` do poprzedniego polecenia. Zatrzymania maszyn wirtualnych, które pozostają elastycznie naliczenie opłat za obliczenia regularne.


### <a name="start-vms-in-a-scale-set"></a>Uruchom w zestawie skalowania maszyn wirtualnych
Aby uruchomić co najmniej jednej maszyny wirtualnej w zestawie skalowania, użyj [Start AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss). `-InstanceId` Parametr umożliwia określenie przynajmniej jednej maszyny wirtualnej, aby rozpocząć. Jeśli nie określisz Identyfikatora wystąpienia, są uruchamiane wszystkie maszyny wirtualne w zestawie skalowania. Aby uruchomić wiele maszyn wirtualnych, każdy identyfikator wystąpienia należy oddzielić przecinkami.

Poniższy przykład uruchamia wystąpienie *0* w zestaw o nazwie skalowania *myScaleSet* i *myResourceGroup* grupy zasobów. Podaj wartości w następujący sposób:

```powershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Ponowne uruchomienie maszyn wirtualnych w zestawie skalowania
Aby ponownie uruchomić co najmniej jednej maszyny wirtualnej w zestawie skalowania, użyj [Retart AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss). `-InstanceId` Parametr umożliwia określenie przynajmniej jednej maszyny wirtualnej, aby ponownie uruchomić. Jeśli nie określisz Identyfikatora wystąpienia, ponownego uruchomienia wszystkich maszyn wirtualnych w zestawie skalowania. Aby ponownie uruchomić wiele maszyn wirtualnych, każdy identyfikator wystąpienia należy oddzielić przecinkami.

Poniższy przykład ponowne uruchomienie wystąpienia *0* w zestaw o nazwie skalowania *myScaleSet* i *myResourceGroup* grupy zasobów. Podaj wartości w następujący sposób:

```powershell
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Usuń z zestawu skalowania maszyn wirtualnych
Aby usunąć przynajmniej jednej maszyny wirtualnej w zestawie skalowania, użyj [AzureRmVmss Usuń](/powershell/module/azurerm.compute/remove-azurermvmss). `-InstanceId` Parametr umożliwia określenie przynajmniej jednej maszyny wirtualnej do usunięcia. Jeśli nie określisz Identyfikatora wystąpienia, wszystkich maszyn wirtualnych w zestawie skalowania zostaną usunięte. Aby usunąć wiele maszyn wirtualnych, każdy identyfikator wystąpienia należy oddzielić przecinkami.

Poniższy przykład umożliwia usunięcie wystąpienia *0* w zestaw o nazwie skalowania *myScaleSet* i *myResourceGroup* grupy zasobów. Podaj wartości w następujący sposób:

```powershell
Remove-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Kolejne kroki
Obejmują innych typowych zadań dla zestawów skalowania jak [wdrażania aplikacji](virtual-machine-scale-sets-deploy-app.md), i [uaktualnienia wystąpień maszyn wirtualnych](virtual-machine-scale-sets-upgrade-scale-set.md). Można również użyć programu Azure PowerShell do [Konfigurowanie reguł automatycznego skalowania](virtual-machine-scale-sets-autoscale-overview.md).
