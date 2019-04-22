---
title: Dodawanie artefaktu do maszyny Wirtualnej w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodawanie artefaktu do maszyny wirtualnej w laboratorium Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 19a7d6052091f8889a88c61793186b7bf7d9d869
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59047028"
---
# <a name="add-an-artifact-to-a-vm"></a>Dodawanie artefaktu do maszyny Wirtualnej
Podczas tworzenia maszyny Wirtualnej, możesz dodać do niego artefaktami. Te artefakty mogą być albo [publicznego repozytorium Git laboratoria DevTest](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) lub z własnym repozytorium Git. W tym artykule przedstawiono sposób dodawania artefaktów w witrynie Azure portal i za pomocą programu Azure PowerShell. 

Usługa Azure DevTest Labs *artefaktów* umożliwiają określenie *akcje* które są wykonywane, gdy maszyna wirtualna jest aprowizowana, takie jak uruchamianie skryptów środowiska Windows PowerShell, uruchamianie poleceń powłoki Bash i instalowania oprogramowania. Artefakt *parametry* można dostosować artefaktu dla konkretnego scenariusza.

Aby dowiedzieć się więcej o tym, jak utworzyć niestandardowe artefakty, zapoznaj się z artykułem: [Tworzenie niestandardowych artefaktów](devtest-lab-artifact-author.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal 
1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
1. Zaznacz na liście laboratoriów laboratorium zawierającej maszynę Wirtualną, z którym chcesz pracować.  
1. Wybierz **Moje maszyny wirtualne**.
1. Wybierz odpowiednią maszynę Wirtualną.
1. Wybierz **Zarządzanie artefaktami**. 
1. Wybierz **artefakty stosowanie**.
1. Na **artefakty stosowanie** okienku zaznacz artefaktów, które chcesz dodać do maszyny Wirtualnej.
1. Na **Dodawanie artefaktu** okienku, wprowadź wymagane wartości parametrów i wszystkie parametry opcjonalne, które są potrzebne.  
1. Wybierz **Dodaj** Dodawanie artefaktu, a następnie wróć do **artefakty stosowanie** okienka.
1. Kontynuuj, dodawania artefaktów, zgodnie z potrzebami dla maszyny Wirtualnej.
1. Po dodaniu artefaktów można [zmienić kolejność, w którym są uruchamiane artefakty](#change-the-order-in-which-artifacts-are-run). Możesz również przejść z powrotem do [wyświetlić lub zmodyfikować artefakt](#view-or-modify-an-artifact).
1. Po zakończeniu dodawania artefaktów wybierz **Zastosuj**

### <a name="change-the-order-in-which-artifacts-are-run"></a>Zmień kolejność uruchamiania artefaktów
Domyślnie akcje artefakty są wykonywane w kolejności, w której są dodawane do maszyny Wirtualnej. Następujące kroki pokazują jak zmienić kolejność, w którym są uruchamiane artefaktów.

1. W górnej części **artefakty stosowanie** okienku zaznacz łącze określającą liczbę artefaktów, które zostały dodane do maszyny Wirtualnej.
   
    ![Liczbę artefaktów, które dodano do maszyny Wirtualnej](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Na **wybrane artefaktów** okienku przeciągnij i upuść artefaktów w odpowiedni sposób. Jeśli masz problemy z przeciąganiem artefaktu, upewnij się, przeciągasz z lewej strony artefaktu. 
1. Po zakończeniu wybierz przycisk **OK**.  

### <a name="view-or-modify-an-artifact"></a>Wyświetlanie lub modyfikowanie artefakt
Poniższe kroki ilustrują wyświetlanie lub modyfikowanie parametrów artefaktu:

1. W górnej części **artefakty stosowanie** okienku zaznacz łącze określającą liczbę artefaktów, które zostały dodane do maszyny Wirtualnej.
   
    ![Liczbę artefaktów, które dodano do maszyny Wirtualnej](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Na **wybrane artefaktów** okienku wybierz artefakt, który chcesz wyświetlić lub edytować.  
1. Na **Dodawanie artefaktu** okienka, upewnij dowolne wymagane zmiany, a następnie wybierz **OK** zamknąć **Dodawanie artefaktu** okienka.
1. Wybierz **OK** zamknąć **wybrane artefaktów** okienka.

## <a name="use-powershell"></a>Korzystanie z programu PowerShell
Poniższy skrypt dotyczy określonego artefaktu określoną maszynę Wirtualną. [Invoke AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) polecenia jest tą, która wykonuje operację.  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>Kolejne kroki
Na artefaktów, zobacz następujące artykuły:

- [Określ obowiązkowe artefaktów dla swojego laboratorium](devtest-lab-mandatory-artifacts.md)
- [Tworzenie niestandardowych artefaktów](devtest-lab-artifact-author.md)
- [Dodawanie repozytorium artefaktów do laboratorium](devtest-lab-artifact-author.md)
- [Diagnozowanie błędów artefaktów](devtest-lab-troubleshoot-artifact-failure.md)
