---
title: Kontrola konserwacji maszyn wirtualnych platformy Azure przy użyciu programu PowerShell
description: Dowiedz się, jak kontrolować, kiedy konserwacja jest stosowana do maszyn wirtualnych platformy Azure przy użyciu funkcji kontroli konserwacji i programu PowerShell.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: e7a5f9ba865ab555bde3125f40ee8675709bef40
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932712"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>Wersja zapoznawcza: kontrolowanie aktualizacji z kontrolą konserwacji i Azure PowerShell

Zarządzaj aktualizacjami platformy, które nie wymagają ponownego uruchomienia, przy użyciu funkcji kontroli konserwacji. Platforma Azure często aktualizuje swoją infrastrukturę w celu poprawy niezawodności, wydajności, zabezpieczeń lub uruchamiania nowych funkcji. Większość aktualizacji jest niewidoczna dla użytkowników. Niektóre wrażliwe obciążenia, takie jak gry, przesyłanie strumieniowe multimediów i transakcje finansowe, nie mogą tolerować nawet kilku sekund zamarzania maszyny wirtualnej lub odłączenia jej do konserwacji. Sterowanie konserwacją umożliwia zaczekanie na aktualizacje platformy i stosowanie ich w 35-dniowym oknie kroczącym. 

Kontrola konserwacji pozwala określić, kiedy mają być stosowane aktualizacje odizolowanych maszyn wirtualnych.

Za pomocą kontrolki konserwacji można:
- Aktualizacje wsadowe w jednym pakiecie aktualizacji.
- Zaczekaj do 35 dni, aby zastosować aktualizacje. 
- Automatyzuj aktualizacje platformy dla Twojego okna obsługi przy użyciu Azure Functions.
- Konfiguracje konserwacji działają w ramach subskrypcji i grup zasobów. 

> [!IMPORTANT]
> Kontrola konserwacji jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 

## <a name="limitations"></a>Ograniczenia

- Maszyny wirtualne muszą znajdować się na [dedykowanym hoście](./linux/dedicated-hosts.md)lub być tworzone przy użyciu [IZOLOWANEGO rozmiaru maszyny wirtualnej](./linux/isolation.md).
- Po upływie 35 dni zostanie automatycznie zastosowana aktualizacja, a ograniczenia dostępności nie będą przestrzegane.
- Użytkownik musi mieć dostęp **właściciela zasobu** .


## <a name="enable-the-powershell-module"></a>Włączanie modułu programu PowerShell

Upewnij się, że `PowerShellGet` jest aktualne.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Polecenia cmdlet programu PowerShell AZ. Maintenance są w wersji zapoznawczej, dlatego należy zainstalować moduł z parametrem `AllowPrerelease` w Cloud Shell lub lokalnej instalacji programu PowerShell.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

Jeśli instalujesz lokalnie, upewnij się, że otwarto wiersz polecenia programu PowerShell jako administrator.

Może również pojawić się monit o potwierdzenie, że chcesz zainstalować z *niezaufanego repozytorium*. Wpisz `Y` lub wybierz opcję **tak, aby** zainstalować moduł.



## <a name="create-a-maintenance-configuration"></a>Utwórz konfigurację konserwacji

Utwórz grupę zasobów jako kontener dla konfiguracji. W tym przykładzie grupa zasobów o nazwie *myMaintenanceRG* jest tworzona w *wschodnim regionie*. Jeśli masz już grupę zasobów, której chcesz użyć, możesz pominąć tę część i zastąpić nazwę grupy zasobów własnymi w pozostałej części przykładów.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Użyj [New-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) , aby utworzyć konfigurację konserwacji. W tym przykładzie zostanie utworzona konfiguracja konserwacji o nazwie Moja *config* objęta zakresem hosta. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

Użycie `-MaintenanceScope host` zapewnia, że konfiguracja konserwacji jest używana do kontrolowania aktualizacji hosta.

Jeśli spróbujesz utworzyć konfigurację o tej samej nazwie, ale w innej lokalizacji, zostanie wyświetlony komunikat o błędzie. Nazwy konfiguracji muszą być unikatowe dla Twojej subskrypcji.

Można wykonać zapytanie o dostępne konfiguracje konserwacji za pomocą polecenia [Get-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>Przypisz konfigurację

Użyj [New-AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) , aby przypisać konfigurację do izolowanej maszyny wirtualnej lub dedykowanego hosta platformy Azure.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Zastosuj konfigurację do maszyny wirtualnej przy użyciu identyfikatora konfiguracji. Określ `-ResourceType VirtualMachines` i podaj nazwę maszyny wirtualnej dla `-ResourceName`i grupę zasobów maszyny wirtualnej dla `-ResourceGroupName`. 

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

### <a name="dedicate-host"></a>Dedykowany Host

Aby zastosować konfigurację do dedykowanego hosta, należy również uwzględnić `-ResourceType hosts`, `-ResourceParentName` z nazwą grupy hostów i `-ResourceParentType hostGroups`. 


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

## <a name="check-for-pending-updates"></a>Wyszukaj oczekujące aktualizacje

Użyj [Get-AzMaintenanceUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) , aby sprawdzić, czy istnieją oczekujące aktualizacje. Użyj `-subscription`, aby określić subskrypcję platformy Azure dla maszyny wirtualnej, jeśli jest inna niż ta, która jest zarejestrowana. 

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Sprawdź, czy istnieją oczekujące aktualizacje dla izolowanej maszyny wirtualnej. W tym przykładzie dane wyjściowe są sformatowane jako tabela na potrzeby czytelności.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```

### <a name="dedicated-host"></a>Dedykowany host

Aby sprawdzić oczekujące aktualizacje dla dedykowanego hosta. W tym przykładzie dane wyjściowe są sformatowane jako tabela na potrzeby czytelności. Zamień wartości dla zasobów własnymi.

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

Użyj opcji [New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) , aby zastosować oczekujące aktualizacje.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Utwórz żądanie zastosowania aktualizacji dla izolowanej maszyny wirtualnej.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

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

## <a name="remove-a-maintenance-configuration"></a>Usuń konfigurację konserwacji

Aby usunąć konfigurację konserwacji, należy użyć [Remove-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) .

```azurecli-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej, zobacz temat [konserwacja i aktualizacje](maintenance-and-updates.md).
