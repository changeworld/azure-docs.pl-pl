---
title: Uruchamianie maszyn przy użyciu uruchomieniu aplikacji automatyzacji w laboratoriach devtest azure
description: Dowiedz się, jak uruchomić maszyny wirtualne w laboratorium w laboratoriach usługi Azure DevTest przy użyciu śreżystw usługi Azure Automation.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9bb97a73b7ca570ca122323e8e9c5a70c9348b15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166311"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Uruchamianie maszyn wirtualnych w laboratorium przy użyciu uruchomieniu usług azure automation
Funkcja [autostartu](devtest-lab-set-lab-policy.md#set-autostart) DevTest Labs umożliwia skonfigurowanie maszyn wirtualnych do automatycznego uruchamiania w określonym czasie. Jednak ta funkcja nie obsługuje maszyn do uruchamiania w określonej kolejności. Istnieje kilka scenariuszy, w których tego typu automatyzacji byłoby przydatne.  Jednym ze scenariuszy jest, gdy Jumpbox VM w laboratorium musi zostać uruchomiony najpierw, przed innymi maszynami wirtualnymi, jak Jumpbox jest używany jako punkt dostępu do innych maszyn wirtualnych.  W tym artykule pokazano, jak skonfigurować konto usługi Azure Automation z elementem runbook programu PowerShell, który wykonuje skrypt. Skrypt używa tagów na maszynach wirtualnych w laboratorium, aby umożliwić kontrolowanie kolejności uruchamiania bez konieczności zmiany skryptu.

## <a name="setup"></a>Konfiguracja
W tym przykładzie maszyny wirtualne w laboratorium muszą mieć tag **StartupOrder** dodany z odpowiednią wartością (0,1,2 itp.). Wyznacz dowolną maszynę, która nie musi być uruchamiana jako -1.

## <a name="create-an-azure-automation-account"></a>Tworzenie konta usługi Azure Automation
Utwórz konto usługi Azure Automation, postępując zgodnie z instrukcjami w [tym artykule](../automation/automation-create-standalone-account.md). Wybierz opcję **Uruchom jako konta** podczas tworzenia konta. Po utworzeniu konta automatyzacji otwórz stronę **Moduły** i wybierz **pozycję Aktualizuj moduły platformy Azure** na pasku menu. Domyślne moduły są kilka wersji starych i bez aktualizacji skrypt może nie działać.

## <a name="add-a-runbook"></a>Dodawanie umiecie
Teraz, aby dodać element runbook do konta automatyzacji, wybierz **runbooks** w menu po lewej stronie. Wybierz **pozycję Dodaj element runbook** w menu i postępuj zgodnie z instrukcjami, aby utworzyć element [runbook programu PowerShell](../automation/automation-first-runbook-textual-powershell.md).

## <a name="powershell-script"></a>Skrypt programu PowerShell
Poniższy skrypt przyjmuje nazwę subskrypcji, nazwę laboratorium jako parametry. Przepływ skryptu jest uzyskanie wszystkich maszyn wirtualnych w laboratorium, a następnie przeanalizować informacje tagu, aby utworzyć listę nazw maszyn wirtualnych i ich kolejności uruchamiania. Skrypt przechodzi przez maszyny wirtualne w kolejności i uruchamia maszyny wirtualne. Jeśli istnieje wiele maszyn wirtualnych w określonym numerze zamówienia, są one uruchamiane asynchronicznie przy użyciu zadań programu PowerShell. Dla tych maszyn wirtualnych, które nie mają tagu, ustaw wartość uruchamiania jako ostatnią (10), zostaną one uruchomione jako ostatnie, domyślnie.  Jeśli laboratorium nie chce, aby maszyna wirtualna została automatycznie rozpoczęta, ustaw wartość znacznika na 11 i zostanie ona zignorowana.

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

## <a name="create-a-schedule"></a>Tworzenie harmonogramu
Aby ten skrypt był wykonywany codziennie, [należy utworzyć harmonogram](../automation/shared-resources/schedules.md#creating-a-schedule) na koncie automatyzacji. Po utworzeniu harmonogramu [połącz go z programem runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook). 

W sytuacji na dużą skalę, gdzie istnieje wiele subskrypcji z wieloma laboratoriami, należy przechowywać informacje o parametrach w pliku dla różnych laboratoriów i przekazać plik do skryptu zamiast poszczególnych parametrów. Skrypt musiałby zostać zmodyfikowany, ale wykonanie rdzenia będzie takie samo. W tym przykładzie użyto usługi Azure Automation do wykonania skryptu programu PowerShell, istnieją inne opcje, takie jak przy użyciu zadania w potoku kompilacji/wydania.

## <a name="next-steps"></a>Następne kroki
Zobacz następujący artykuł, aby dowiedzieć się więcej o usłudze Azure Automation: [Wprowadzenie do usługi Azure Automation.](../automation/automation-intro.md)
