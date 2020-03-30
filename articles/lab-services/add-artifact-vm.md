---
title: Dodawanie artefaktu do maszyny Wirtualnej w laboratoriach devtest platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak dodać artefakt do maszyny wirtualnej w laboratorium w laboratorium usługi Azure DevTest Labs
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
ms.openlocfilehash: 27fec279582d845972b87ac635c87c16c239924e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73901325"
---
# <a name="add-an-artifact-to-a-vm"></a>Dodawanie artefaktu do maszyny wirtualnej
Podczas tworzenia maszyny Wirtualnej, można dodać istniejące artefakty do niego. Artefakty te mogą pochodzić z [publicznego repozytorium DevTest Labs Git](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) lub z własnego repozytorium Git. W tym artykule pokazano, jak dodać artefakty w witrynie Azure portal i przy użyciu programu Azure PowerShell. 

Artefakty programu Azure DevTest *Labs* umożliwiają określenie *akcji* wykonywanych podczas inicjowania obsługi administracyjnej maszyny Wirtualnej, takich jak uruchamianie skryptów programu Windows PowerShell, uruchamianie poleceń Bash i instalowanie oprogramowania. *Parametry* artefaktu umożliwiają dostosowanie artefaktu dla danego scenariusza.

Aby dowiedzieć się, jak tworzyć niestandardowe artefakty, zobacz artykuł: [Tworzenie artefaktów niestandardowych](devtest-lab-artifact-author.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal 
1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**
1. Z listy laboratoriów wybierz laboratorium zawierające maszynę wirtualną, z którą chcesz pracować.  
1. Wybierz **pozycję Moje maszyny wirtualne**.
1. Wybierz żądaną maszynę wirtualną.
1. Wybierz pozycję **Zarządzaj artefaktami**. 
1. Wybierz **opcję Zastosuj artefakty**.
1. W okienku **Zastosuj artefakty** wybierz artefakt, który chcesz dodać do maszyny Wirtualnej.
1. W okienku **Dodaj artefakt** wprowadź wymagane wartości parametrów i wszystkie potrzebne parametry opcjonalne.  
1. Wybierz **dodaj,** aby dodać artefakt i powrócić do okienka **Zastosuj artefakty.**
1. Kontynuuj dodawanie artefaktów zgodnie z potrzebami maszyny Wirtualnej.
1. Po dodaniu artefaktów można [zmienić kolejność uruchamiania artefaktów.](#change-the-order-in-which-artifacts-are-run) Można również wrócić do [wyświetlania lub modyfikowania artefaktu](#view-or-modify-an-artifact).
1. Po zakończeniu dodawania artefaktów wybierz pozycję **Zastosuj**

### <a name="change-the-order-in-which-artifacts-are-run"></a>Zmienianie kolejności uruchamiania artefaktów
Domyślnie akcje artefaktów są wykonywane w kolejności, w jakiej są dodawane do maszyny Wirtualnej. Poniższe kroki ilustrują, jak zmienić kolejność uruchamiania artefaktów.

1. W górnej części okienka **Zastosuj artefakty** wybierz łącze wskazujące liczbę artefaktów, które zostały dodane do maszyny Wirtualnej.
   
    ![Liczba artefaktów dodanych do maszyny Wirtualnej](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. W okienku **Wybrane artefakty** przeciągnij i upuść artefakty do żądanej kolejności. Jeśli masz problemy z przeciągnięciem artefaktu, upewnij się, że przeciągasz od lewej strony artefaktu. 
1. Po zakończeniu wybierz przycisk **OK**.  

### <a name="view-or-modify-an-artifact"></a>Wyświetlanie lub modyfikowanie artefaktu
Poniższe kroki ilustrują sposób wyświetlania lub modyfikowania parametrów artefaktu:

1. W górnej części okienka **Zastosuj artefakty** wybierz łącze wskazujące liczbę artefaktów, które zostały dodane do maszyny Wirtualnej.
   
    ![Liczba artefaktów dodanych do maszyny Wirtualnej](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. W okienku **Wybrane artefakty** zaznacz artefakt, który chcesz wyświetlić lub edytować.  
1. W okienku **Dodawanie artefaktów** wcielij wszelkie potrzebne zmiany i wybierz przycisk **OK,** aby zamknąć okienko **Dodaj artefakt.**
1. Wybierz **przycisk OK,** aby zamknąć okienko **Wybrane artefakty.**

## <a name="use-powershell"></a>Korzystanie z programu PowerShell
Poniższy skrypt stosuje określony artefakt do określonej maszyny Wirtualnej. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) polecenie jest tym, który wykonuje operację.  

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
$resourceGroupName = (Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
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

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły na temat artefaktów:

- [Określ obowiązkowe artefakty dla swojego laboratorium](devtest-lab-mandatory-artifacts.md)
- [Tworzenie niestandardowych artefaktów](devtest-lab-artifact-author.md)
- [Dodawanie repozytorium artefaktów do laboratorium](devtest-lab-artifact-author.md)
- [Diagnozowanie błędów artefaktów](devtest-lab-troubleshoot-artifact-failure.md)
