---
title: Otrzymuj powiadomienia o konserwacji maszyn wirtualnych platformy Azure przy użyciu programu PowerShell
description: Wyświetl powiadomienia o konserwacji dla maszyn wirtualnych uruchomionych na platformie Azure i rozpocznij samoobsługową konserwację przy użyciu programu PowerShell.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: b23c210d7c8a9f1d42e6e1b46e0f7f81bda857b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916086"
---
# <a name="handling-planned-maintenance-using-powershell"></a>Obsługa planowanej konserwacji za pomocą programu PowerShell

**Ten artykuł dotyczy maszyn wirtualnych z systemem Linux i Windows.**

Za pomocą programu Azure PowerShell można sprawdzić, kiedy maszyny wirtualne są zaplanowane do [konserwacji.](maintenance-notifications.md) Informacje o planowanej konserwacji są dostępne z polecenia cmdlet `-status` [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) podczas korzystania z parametru.
  
Informacje o konserwacji są zwracane tylko wtedy, gdy planowana jest konserwacja. Jeśli nie jest zaplanowana żadna konserwacja, która wpływa na maszynę wirtualną, polecenie cmdlet nie zwraca żadnych informacji o konserwacji. 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

Następujące właściwości są zwracane w obszarze MaintenanceRedeployStatus: 

| Wartość | Opis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed (Dozwolone) | Wskazuje, czy w tej chwili można rozpocząć konserwację maszyny Wirtualnej |
| Czas rozpoczęcia pracy przedwidokadowy         | Początek okna samoobsługowego konserwacji, kiedy można zainicjować konserwację maszyny Wirtualnej |
| Czas wstępnej opiekiDostępniewaczas           | Koniec okna samoobsługowego konserwacji, kiedy można zainicjować konserwację maszyny Wirtualnej |
| Czas rozpoczęcia konserwacji            | Początek zaplanowanej konserwacji, w której platforma Azure inicjuje konserwację na maszynie Wirtualnej |
| MaintenanceWindowEndTime (Czas konserwacji systemu Windows              | Koniec zaplanowanego okna konserwacji, w którym platforma Azure inicjuje konserwację na maszynie Wirtualnej |
| Kod LastOperationResultCode               | Wynik ostatniej próby zainicjowania konserwacji na maszynie wirtualnej |



Można również uzyskać stan konserwacji dla wszystkich maszyn wirtualnych w grupie zasobów przy użyciu [get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) i nie określania maszyny Wirtualnej.
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

Poniższy przykład programu PowerShell przyjmuje identyfikator subskrypcji i zwraca listę maszyn wirtualnych, które są zaplanowane do konserwacji.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Rozpoczynanie konserwacji maszyny Wirtualnej przy użyciu programu PowerShell

Korzystając z informacji z funkcji w poprzedniej sekcji, następujące rozpoczyna konserwację na maszynie Wirtualnej, jeśli **IsCustomerInitiatedMaintenanceAllowed** jest ustawiona na true.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Klasyczne wdrożenia

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Jeśli nadal masz starsze maszyny wirtualne, które zostały wdrożone przy użyciu klasycznego modelu wdrażania, można użyć programu PowerShell do wykonywania zapytań dotyczących maszyn wirtualnych i inicjowania konserwacji.

Aby uzyskać stan konserwacji maszyny Wirtualnej, wpisz:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Aby rozpocząć konserwację klasycznej maszyny Wirtualnej, należy wpisać:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>Następne kroki

Można również obsługiwać planowaną konserwację przy użyciu [interfejsu wiersza polecenia](maintenance-notifications-cli.md) lub portalu [azure.](maintenance-notifications-portal.md)
