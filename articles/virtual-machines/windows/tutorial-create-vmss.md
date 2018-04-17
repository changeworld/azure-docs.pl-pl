---
title: Utwórz zestawy skalowania maszyny wirtualnej dla systemu Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Tworzenie i wdrażanie aplikacji wysokiej dostępności na maszynach wirtualnych z systemem Windows przy użyciu zestawu skali maszyny wirtualnej
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: article
ms.date: 03/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a4a2d8d616a503a2b200627a52103b78e573f767
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows"></a>Tworzenie zestawu skalowania maszyny wirtualnej i wdrażanie wysokiej dostępności aplikacji w systemie Windows
Zestaw skalowania maszyn wirtualnych umożliwia wdrożenie zestawu identycznych, automatycznie skalowanych maszyn wirtualnych, oraz zarządzanie nimi. Maszyny wirtualne w zestawie skalowania można skalować ręcznie lub można zdefiniować reguły skalowania automatycznego na podstawie użycia zasobów, takich jak procesor CPU, zapotrzebowanie na pamięć lub ruch sieciowy. W tym samouczku wdrażany jest zestaw skalowania maszyn wirtualnych na platformie Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Umożliwia zdefiniowanie witryny usług IIS można skalować niestandardowe rozszerzenie skryptu
> * Tworzenie modułu równoważenia obciążenia dla zestawu skalowania
> * Tworzenie zestawu skalowania maszyn wirtualnych
> * Zwiększanie lub zmniejszanie liczby wystąpień w zestawie skalowania
> * Tworzenie reguł skalowania automatycznego

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli wybierzesz do zainstalowania i używania programu PowerShell lokalnie, ten samouczek wymaga programu Azure PowerShell w wersji modułu 5.6 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.


## <a name="scale-set-overview"></a>Omówienie zestawów skalowania
Zestaw skalowania maszyn wirtualnych umożliwia wdrożenie zestawu identycznych, automatycznie skalowanych maszyn wirtualnych, oraz zarządzanie nimi. Maszyny wirtualne w zestawie skalowania są dystrybuowane w ramach domen aktualizacji i usterek logiki w co najmniej jednej *grupie umieszczania*. Są to grupy podobnie skonfigurowanych maszyn wirtualnych — podobne do [zestawów dostępności](tutorial-availability-sets.md).

Maszyny wirtualne są tworzone zgodnie z potrzebami w zestawie skalowania. W celu kontrolowania tego, jak i kiedy maszyny wirtualne są dodawane lub usuwane z zestawu skalowania, definiuje się reguły skalowania automatycznego. Te reguły może wyzwolić na podstawie metryk, takich jak obciążenie procesora CPU, pamięć lub ruchu sieciowego.

Zestawy skalowania obsługują maksymalnie 1000 maszyn wirtualnych, gdy jest używany obraz platformy Azure. W przypadku obciążeń ze znaczącymi wymaganiami dotyczącymi dostosowywania instalacji lub maszyn wirtualnych warto rozważyć [utworzenie niestandardowego obrazu maszyny wirtualnej](tutorial-custom-images.md). Przy użyciu obrazu niestandardowego można utworzyć maksymalnie 300 maszyn wirtualnych w zestawie skalowania.


## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania
Utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). W poniższym przykładzie pokazano tworzenie zestawu skalowania o nazwie *myScaleSet*, używającego obrazu platformy systemu *Windows Server 2016 Datacenter*. Zasoby sieciowe platformy Azure dla sieci wirtualnej, publiczny adres IP i moduł równoważenia obciążenia są tworzone automatycznie. Po wyświetleniu monitu podaj własne odpowiednie poświadczenia administracyjne dla wystąpień maszyn wirtualnych w zestawie skalowania:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic"
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.


## <a name="deploy-sample-application"></a>Wdrażanie przykładowej aplikacji
Aby przetestować zestaw skalowania, należy zainstalować podstawową aplikację internetową. Niestandardowe rozszerzenie skryptu platformy Azure służy do pobierania i uruchamiania skryptu, który instaluje usługi IIS na wystąpieniach maszyn wirtualnych. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalowania oprogramowania lub każdego innego zadania związanego z konfiguracją lub zarządzaniem. Aby uzyskać więcej informacji, zobacz [Omówienie niestandardowego rozszerzenia skryptu](extensions-customscript.md).

Użyj niestandardowego rozszerzenia skryptu, aby zainstalować podstawowy serwer internetowy usług IIS. Zastosuj niestandardowe rozszerzenie skryptu, które instaluje usługi IIS, w następujący sposób:

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroupScaleSet" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```


## <a name="test-your-scale-set"></a>Testowanie zestawu skalowania
Aby wyświetlić na skalę w akcji, Uzyskaj publicznego adresu IP z usługi równoważenia obciążenia z [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Poniższy przykład uzyskuje adres IP dla *myPublicIP* utworzona w ramach zestawu skali:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myPublicIPAddress" | select IpAddress
```

Wprowadź publiczny adres IP w przeglądarce internetowej. Wyświetlana jest aplikacja sieci web, w tym nazwa hosta maszyny wirtualnej, ruch do dystrybucji modułu równoważenia obciążenia:

![Uruchamianie witryny usług IIS](./media/tutorial-create-vmss/running-iis-site.png)

Aby zobaczyć zestaw skalowania w działaniu, możesz wymusić odświeżenie przeglądarki internetowej i przyjrzeć się temu, jak moduł równoważenia obciążenia rozdziela ruch między wszystkie maszyny wirtualne, na których działa aplikacja.


## <a name="management-tasks"></a>Zadania zarządzania
W całym cyklu życia zestawu skalowania konieczne może być uruchomienie jednego lub większej liczby zadań zarządzania. Ponadto może pojawić się potrzeba tworzenia skryptów automatyzujących różne zadania cyklu życia. Program Azure PowerShell umożliwia szybkie do wykonywania tych zadań. Poniżej przedstawiono kilka typowych zadań.

### <a name="view-vms-in-a-scale-set"></a>Wyświetlanie maszyn wirtualnych w zestawie skalowania
Aby wyświetlić listę wystąpień maszyn wirtualnych w zestawie skalowania, użyj polecenia [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) w następujący sposób:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet"
```

Następujące przykładowe dane wyjściowe zawierają dwa wystąpienia maszyn wirtualnych w zestawie skalowania:

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Aby wyświetlić dodatkowe informacje na temat określonego wystąpienia maszyny wirtualnej, dodaj parametr `-InstanceId` do polecenia [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). W poniższym przykładzie są widoczne informacje o wystąpieniu maszyny wirtualnej *1*:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>Zwiększanie lub zmniejszanie liczby wystąpień maszyn wirtualnych
Aby wyświetlić liczbę wystąpień aktualnie zainstalowana w zestawie skalowania, użyj [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) i wykonywać zapytania na *sku.capacity*:

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -VMScaleSetName "myScaleSet" | `
    Select -ExpandProperty Sku
```

Możesz ręcznie zwiększyć lub zmniejszyć liczbę maszyn wirtualnych w skali ustawiony za pomocą [AzureRmVmss aktualizacji](/powershell/module/azurerm.compute/update-azurermvmss). W poniższym przykładzie liczba maszyn wirtualnych w zestawie skalowania jest ustawiana na *3*:

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

Jeśli ustawiona przyjmuje kilka minut, aby zaktualizować określoną liczbę wystąpień w skali sieci.


### <a name="configure-autoscale-rules"></a>Konfigurowanie skalowania automatycznego
Zamiast ręcznie skalowanie liczby wystąpień w skali sieci, należy zdefiniować regułę automatycznego skalowania. Te reguły monitorują wystąpienia w zestawie skalowania i odpowiednio reagują na podstawie zdefiniowanych metryk i progów. W poniższym przykładzie skala liczby wystąpień jest zwiększana o jedno, gdy średnie obciążenie procesora przekracza 60% w okresie 5 minut. Jeśli w okresie 5-minutowy średnie obciążenie procesora CPU spadnie poniżej 30%, wystąpienia są skalowane w przez jedno wystąpienie:

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzureRmVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5-minute period
$myRuleScaleDown = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfiles $myScaleProfile
```

Aby uzyskać więcej informacji dotyczących projektowania na temat korzystania ze skalowania automatycznego, zobacz [najlepsze rozwiązania w zakresie skalowania automatycznego](/azure/architecture/best-practices/auto-scaling).


## <a name="next-steps"></a>Kolejne kroki
W tym samouczku tworzony jest zestaw skalowania maszyn wirtualnych. W tym samouczku omówiono:

> [!div class="checklist"]
> * Umożliwia zdefiniowanie witryny usług IIS można skalować niestandardowe rozszerzenie skryptu
> * Tworzenie modułu równoważenia obciążenia dla zestawu skalowania
> * Tworzenie zestawu skalowania maszyn wirtualnych
> * Zwiększanie lub zmniejszanie liczby wystąpień w zestawie skalowania
> * Tworzenie reguł skalowania automatycznego

Przejdź do kolejnego samouczka, aby uzyskać więcej informacji o pojęciach dotyczących równoważenia obciążenia dla maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Równoważenie obciążenia maszyn wirtualnych](tutorial-load-balancer.md)
