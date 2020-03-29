---
title: Uruchamianie i zatrzymywania maszyn wirtualnych platformy Azure DevTest Labs za pomocą narzędzi wiersza polecenia
description: Dowiedz się, jak używać narzędzi wiersza polecenia do uruchamiania i zatrzymywania maszyn wirtualnych w laboratoriach devtest azure.
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
ms.openlocfilehash: fd643559a09d5c75aad9be5f35c653994c8488cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169250"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Uruchamianie i zatrzymywania maszyn wirtualnych w laboratoriach DevTest Labs za pomocą narzędzi wiersza polecenia
W tym artykule pokazano, jak używać narzędzia Azure PowerShell lub interfejsu wiersza polecenia platformy Azure do uruchamiania lub zatrzymywania maszyn wirtualnych w laboratorium w laboratoriach devtest platformy Azure. Skrypty programu PowerShell/CLI można utworzyć w celu zautomatyzowania tych operacji. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie
Usługa Azure DevTest Labs to sposób na tworzenie szybkich, łatwych i oszczędnych środowisk deweloperskich/testowych. Umożliwia zarządzanie kosztami, szybkie aprowizację maszyn wirtualnych i minimalizowanie odpadów.  Istnieją wbudowane funkcje w witrynie Azure portal, które umożliwiają konfigurowanie maszyn wirtualnych w laboratorium, aby automatycznie uruchamiać i zatrzymywać w określonych godzinach. 

Jednak w niektórych scenariuszach można zautomatyzować uruchamianie i zatrzymywanie maszyn wirtualnych ze skryptów programu PowerShell/CLI. Zapewnia pewną elastyczność w uruchamianiu i zatrzymywaniu poszczególnych maszyn w dowolnym momencie, a nie w określonych godzinach. Oto niektóre z sytuacji, w których uruchamianie tych zadań przy użyciu skryptów byłoby pomocne.

- Podczas korzystania z aplikacji 3-warstwowej jako część środowiska testowego warstwy muszą być uruchamiane w sekwencji. 
- Wyłącz maszynę wirtualną, gdy spełnione są niestandardowe kryteria, aby zaoszczędzić pieniądze. 
- Użyj go jako zadanie w przepływie pracy ciągłej integracji/ciągłego wdrażania, aby rozpocząć na początku przepływu, użyj maszyn wirtualnych jako maszyn kompilacji, maszyn testowych lub infrastruktury, a następnie zatrzymać maszyny wirtualne po zakończeniu procesu. Przykładem tego może być niestandardowa fabryka obrazów w laboratoriach DevTest Azure.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> Poniższy skrypt używa modułu Az programu Azure PowerShell. 

Następujący skrypt programu PowerShell uruchamia maszynę wirtualną w laboratorium. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) jest głównym fokusem dla tego skryptu. **Parametr ResourceId** jest w pełni kwalifikowanym identyfikatorem zasobu maszyny Wirtualnej w laboratorium. **Action** Parametr jest, gdzie **start** lub **stop** opcje są ustawiane w zależności od tego, co jest potrzebne.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Narzędzie [wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) to kolejny sposób automatyzacji uruchamiania i zatrzymywania maszyn wirtualnych DevTest Labs. Narzędzie wiersza polecenia platformy Azure można [zainstalować](/cli/azure/install-azure-cli?view=azure-cli-latest) w różnych systemach operacyjnych. Poniższy skrypt zawiera polecenia uruchamiania i zatrzymywania maszyny Wirtualnej w laboratorium. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>Następne kroki
Zobacz następujący artykuł dotyczący korzystania z witryny Azure portal do wykonywania tych operacji: [Uruchom ponownie maszynę wirtualną](devtest-lab-restart-vm.md).
