---
title: Uruchomienie maszyn za pomocą elementów runbook usługi Automation w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można uruchomić maszyny wirtualne w laboratorium Azure DevTest Labs przy użyciu elementów runbook usługi Azure Automation.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.openlocfilehash: 8d3885ba25e479316f97ecbb0681a1680650fc09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61083622"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Uruchom maszyny wirtualne w laboratorium w kolejności przy użyciu elementów runbook usługi Azure Automation
[Autostart](devtest-lab-set-lab-policy.md#set-autostart) funkcja DevTest Labs umożliwia konfigurowanie maszyn wirtualnych do automatycznego uruchamiania o określonej godzinie. Jednak ta funkcja nie obsługuje maszyn do uruchamiania w określonej kolejności. Istnieje kilka scenariuszy, w którym ten typ automatyzacji byłoby.  Jeden scenariusz polega na tym, gdzie serwer Przesiadkowy maszyny Wirtualnej w ramach laboratorium musi być uruchomiona najpierw przed innymi maszynami wirtualnymi, ponieważ serwer Przesiadkowy jest używany jako punkt dostępu do innych maszyn wirtualnych.  W tym artykule pokazano, jak skonfigurować konto usługi Azure Automation za pomocą elementu runbook programu PowerShell, który jest wykonywany skrypt. Skrypt używa tagów na maszynach wirtualnych w środowisku laboratoryjnym pozwala kontrolować kolejność uruchamiania bez konieczności zmiany skryptu.

## <a name="setup"></a>Konfigurowanie
W tym przykładzie, musisz mieć tag maszyny wirtualne w laboratorium **StartupOrder** dodane z odpowiednią wartością (0,1,2, itp.). Wyznaczanie dowolnym komputerze, który nie musi zostać uruchomiona jako wartość -1.

## <a name="create-an-azure-automation-account"></a>Tworzenie konta usługi Azure Automation
Tworzenie konta usługi Azure Automation, postępując zgodnie z instrukcjami opisanymi w [w tym artykule](../automation/automation-create-standalone-account.md). Wybierz **konta Uruchom jako** opcji podczas tworzenia konta. Po utworzeniu konta usługi automation, otwórz **modułów** strony i wybierz **aktualizowania modułów platformy Azure** na pasku menu. Domyślne moduły są różne wersje starych i bez skryptu aktualizacji mogą nie działać.

## <a name="add-a-runbook"></a>Dodaj element runbook
Teraz, aby dodać element runbook do konta usługi automation, należy wybrać **elementów Runbook** w menu po lewej stronie. Wybierz **Dodaj element runbook** na pasku menu, a następnie postępuj zgodnie z instrukcjami, aby [Tworzenie elementu runbook programu PowerShell](../automation/automation-first-runbook-textual-powershell.md).

## <a name="powershell-script"></a>Skrypt programu PowerShell
Poniższy skrypt ma nazwę subskrypcji, nazwę laboratorium jako parametry. Przepływ skryptu jest rozpoczynanie wszystkich maszyn wirtualnych w laboratorium, a następnie Przeanalizuj informacje tag, aby utworzyć listę nazw maszyn wirtualnych i ich kolejność uruchamiania. Skrypt przeprowadzi maszyn wirtualnych w kolejności i uruchamia maszyny wirtualne. W przypadku wielu maszyn wirtualnych w liczbie określonej kolejności, zostaną uruchomione asynchronicznie przy użyciu zadań programu PowerShell. Dla tych maszyn wirtualnych, które nie mają tag, ustaw wartość uruchamiania za ostatnie 10, ich zostanie uruchomiony ostatniego, domyślnie.  Jeśli laboratorium nie chcesz, aby maszyna wirtualna może być uruchamiana automatycznie, ustaw wartość tagu 11 i zostaną zignorowane.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>Utwórz harmonogram
Aby ten skrypt, codziennie, wykonaj [Utwórz harmonogram](../automation/shared-resources/schedules.md#creating-a-schedule) na koncie usługi automation. Po utworzeniu harmonogramu [połączyć elementu runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook). 

W dużej skali sytuacji, w których istnieje wiele subskrypcji za pomocą wielu labs przechowują informacje o parametrach w pliku do innego laboratoriów i przekazać plik do skryptu zamiast poszczególne parametry. Skrypt musi być modyfikowane, ale wykonywanie podstawowych będą takie same. W tym przykładzie jest używana usługa Azure Automation do uruchomienia skryptu programu PowerShell, istnieją inne opcje, takie jak przy użyciu zadań w potoku kompilacji/wydania.

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujący artykuł, aby dowiedzieć się więcej o usłudze Azure Automation: [Wprowadzenie do usługi Azure Automation](../automation/automation-intro.md).
