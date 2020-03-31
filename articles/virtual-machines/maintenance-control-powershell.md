---
title: Kontrola konserwacji maszyn wirtualnych platformy Azure przy użyciu programu PowerShell
description: Dowiedz się, jak kontrolować, gdy konserwacja jest stosowana do maszyn wirtualnych platformy Azure przy użyciu kontroli konserwacji i programu PowerShell.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: dc47afe9cb6eca1b10f8caca7b85087023c5eadf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060134"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>W wersji zapoznawczej: kontroluj aktualizacje za pomocą kontroli konserwacji i programu Azure PowerShell

Zarządzanie aktualizacjami platformy, które nie wymagają ponownego uruchomienia, przy użyciu kontroli konserwacji. Platforma Azure często aktualizuje swoją infrastrukturę, aby zwiększyć niezawodność, wydajność, zabezpieczenia lub uruchomić nowe funkcje. Większość aktualizacji jest nieprzejrzysta dla użytkowników. Niektóre poufne obciążenia, takie jak gry, przesyłanie strumieniowe multimediów i transakcje finansowe, nie tolerują nawet kilku sekund zamrożenia lub odłączenia maszyny Wirtualnej w celu konserwacji. Kontrola konserwacji umożliwia oczekiwanie na aktualizacje platformy i zastosowanie ich w 35-dniowym oknie kroczącym. 

Kontrola konserwacji pozwala zdecydować, kiedy należy zastosować aktualizacje do izolowanych maszyn wirtualnych.

Dzięki kontroli konserwacji można:
- Aktualizacje wsadowe do jednego pakietu aktualizacji.
- Poczekaj do 35 dni, aby zastosować aktualizacje. 
- Zautomatyzuj aktualizacje platformy dla okna konserwacji przy użyciu usługi Azure Functions.
- Konfiguracje konserwacji działają między subskrypcjami i grupami zasobów. 

> [!IMPORTANT]
> Kontrola konserwacji jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 

## <a name="limitations"></a>Ograniczenia

- Maszyny wirtualne muszą znajdować się na [dedykowanym hoście](./linux/dedicated-hosts.md)lub być tworzone przy użyciu [izolowanego rozmiaru maszyny Wirtualnej.](./linux/isolation.md)
- Po 35 dniach aktualizacja zostanie automatycznie zastosowana.
- Użytkownik musi mieć dostęp **współautora zasobów.**


## <a name="enable-the-powershell-module"></a>Włączanie modułu programu PowerShell

Upewnij `PowerShellGet` się, że jest aktualna.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Polecenia cmdlet programu Az.Maintenance programu PowerShell są w wersji `AllowPrerelease` zapoznawczej, więc należy zainstalować moduł z parametrem w aplikacji Cloud Shell lub lokalnej instalacji programu PowerShell.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

Jeśli instalujesz lokalnie, upewnij się, że monit programu PowerShell został otwarty jako administrator.

Może zostać również poproszony o potwierdzenie, że chcesz zainstalować z *niezaufanego repozytorium*. Wpisz `Y` lub wybierz **tak na wszystkie,** aby zainstalować moduł.



## <a name="create-a-maintenance-configuration"></a>Tworzenie konfiguracji konserwacji

Utwórz grupę zasobów jako kontener dla twojej konfiguracji. W tym przykładzie grupa zasobów o nazwie *myMaintenanceRG* jest tworzony w *eastus*. Jeśli masz już grupę zasobów, której chcesz użyć, możesz pominąć tę część i zastąpić nazwę grupy zasobów własną w pozostałych przykładach.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Użyj [New-AzMaintenanceConfiguration,](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) aby utworzyć konfigurację konserwacji. W tym przykładzie tworzy konfigurację konserwacji o nazwie *myConfig* o zakresie do hosta. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

Korzystanie `-MaintenanceScope host` zapewnia, że konfiguracja konserwacji jest używana do kontrolowania aktualizacji do hosta.

Jeśli spróbujesz utworzyć konfigurację o tej samej nazwie, ale w innej lokalizacji, pojawi się błąd. Nazwy konfiguracji muszą być unikatowe dla subskrypcji.

Za pomocą funkcji [Get-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration)można wyszukiwać dostępne konfiguracje konserwacji.

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>Przypisywanie konfiguracji

Użyj [new-AzConfigurationAssignment przypisać](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) konfigurację do izolowanej maszyny Wirtualnej lub hosta dedykowanego platformy Azure.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Zastosuj konfigurację do maszyny Wirtualnej przy użyciu identyfikatora konfiguracji. Określ `-ResourceType VirtualMachines` i podaj nazwę `-ResourceName`maszyny Wirtualnej dla i `-ResourceGroupName`grupy zasobów maszyny Wirtualnej dla . 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>Dedykowany host

Aby zastosować konfigurację do dedykowanego hosta, należy również uwzględnić `-ResourceType hosts`, `-ResourceParentName` z nazwą grupy hosta i `-ResourceParentType hostGroups`. 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>Sprawdzanie oczekujących aktualizacji

Użyj [Get-AzMaintenanceUpdate,](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) aby sprawdzić, czy są oczekujące aktualizacje. Służy `-subscription` do określania subskrypcji platformy Azure maszyny Wirtualnej, jeśli różni się od tej, do której jesteś zalogowany.

Jeśli nie ma żadnych aktualizacji, aby pokazać, to polecenie zwróci nic. W przeciwnym razie zwróci obiekt PSApplyUpdate:

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Sprawdź, czy oczekujące aktualizacje dla izolowanej maszyny Wirtualnej. W tym przykładzie dane wyjściowe są formatowane jako tabela dla czytelności.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Dedykowany host

Aby sprawdzić oczekujące aktualizacje dla dedykowanego hosta. W tym przykładzie dane wyjściowe są formatowane jako tabela dla czytelności. Zastąp wartości zasobów własnymi.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>Stosowanie aktualizacji

Użyj [New-AzApplyUpdate,](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) aby zastosować oczekujące aktualizacje.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Utwórz żądanie, aby zastosować aktualizacje do izolowanej maszyny Wirtualnej.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

Po sukcesie to polecenie `PSApplyUpdate` zwróci obiekt. Aby sprawdzić stan aktualizacji, `Get-AzApplyUpdate` można użyć atrybutu Nazwa w poleceniu. Zobacz [Sprawdzanie stanu aktualizacji](#check-update-status).

### <a name="dedicated-host"></a>Dedykowany host

Zastosuj aktualizacje do dedykowanego hosta.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>Sprawdź stan aktualizacji
Użyj [Get-AzApplyUpdate,](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate) aby sprawdzić stan aktualizacji. Polecenia pokazane poniżej pokazują stan najnowszej `default` aktualizacji `-ApplyUpdateName` przy użyciu parametru. Można zastąpić nazwę aktualizacji (zwróconą przez [polecenie New-AzApplyUpdate),](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) aby uzyskać stan określonej aktualizacji.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime będzie czas, kiedy aktualizacja została zakończona, zainicjowane przez Ciebie lub przez platformę w przypadku, gdy okno samoobsługi nie był używany. Jeśli nigdy nie było aktualizacji stosowane za pomocą kontroli konserwacji pokaże wartość domyślną.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Sprawdź, czy nie ma aktualizacji do określonej maszyny wirtualnej.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>Dedykowany host

Sprawdź dostępność aktualizacji dedykowanego hosta.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>Usuwanie konfiguracji konserwacji

Użyj [Remove-AzMaintenanceConfiguration,](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) aby usunąć konfigurację konserwacji.

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej, zobacz [Konserwacja i aktualizacje](maintenance-and-updates.md).
