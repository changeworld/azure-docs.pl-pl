---
title: Użyj narzędzia wiersza polecenia do uruchamiania i zatrzymywania maszyn wirtualnych usługi Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać narzędzi wiersza polecenia do uruchamiania i zatrzymywania maszyn wirtualnych w usłudze Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: a8132735d1af08055e9341608dcac0564ed4b927
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60236682"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Użyj narzędzia wiersza polecenia do uruchamiania i zatrzymywania maszyn wirtualnych w usłudze Azure DevTest Labs
W tym artykule pokazano, jak za pomocą programu Azure PowerShell lub wiersza polecenia platformy Azure uruchamianiem lub zatrzymywaniem maszyn wirtualnych w laboratorium Azure DevTest Labs. Można utworzyć skryptów programu PowerShell/interfejsu wiersza polecenia w celu zautomatyzowania tych operacji. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie
Usługa Azure DevTest Labs jest sposobem tworzenia środowiska deweloperskie i testowe szybkie, łatwe i zwarte. Umożliwia zarządzanie kosztami, szybko aprowizuj maszyny wirtualne i zminimalizować ilość odpadów.  Ma wbudowanych funkcji w witrynie Azure portal, które można konfigurować maszyny wirtualne w laboratorium, aby automatycznie uruchomić i zatrzymać w określonym czasie. 

Jednak w niektórych scenariuszach można zautomatyzować, uruchamianie i zatrzymywanie maszyn wirtualnych ze skryptów programu PowerShell/interfejsu wiersza polecenia. Oferuje on pewną elastyczność dzięki uruchamianie i zatrzymywanie poszczególnych maszyn w dowolnym momencie, a nie w określonym czasie. Poniżej przedstawiono niektóre sytuacje, w której działa pomocny byłby te zadania za pomocą skryptów.

- Korzystając z 3-warstwowej jako część środowiska testowego, warstwy należy uruchomić w sekwencji. 
- Wyłącz Maszynę wirtualną po spełnieniu kryteriów niestandardowych w celu zaoszczędzenia pieniędzy. 
- Używać go jako zadanie w ramach przepływu pracy ciągłej integracji/ciągłego Dostarczania rozpoczynają się od początku przepływu, korzystanie z maszyn wirtualnych, jak tworzyć maszyny, przetestuj maszyny lub infrastruktury, a następnie Zatrzymaj maszyny wirtualne, gdy proces zostanie zakończony. Na przykład będą fabrycznie obrazu niestandardowego za pomocą usługi Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell
Poniższy skrypt programu PowerShell uruchamia Maszynę wirtualną w laboratorium. [Wywoływanie AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) jest podstawowym fokus dla tego skryptu. **ResourceId** parametr jest w pełni kwalifikowanego Identyfikatora zasobu dla maszyny Wirtualnej w środowisku laboratoryjnym. **Akcji** parametr ma miejsce **Start** lub **zatrzymać** opcji ustawia się w zależności od tego, co jest potrzebne.

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
if ($(Get-Module -Name AzureRM).Version.Major -eq 6) {
    $devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -Name $devTestLabName
} else {
    $devTestLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $devTestLabName
}

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
[Wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) automatyzacji, uruchamianie i zatrzymywanie maszyn wirtualnych z usługi DevTest Labs w inny sposób. Wiersza polecenia platformy Azure może być [zainstalowane](/cli/azure/install-azure-cli?view=azure-cli-latest) w różnych systemach operacyjnych. Poniższy skrypt zapewnia polecenia do uruchamiania i zatrzymywania maszyny Wirtualnej w laboratorium. 

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


## <a name="next-steps"></a>Kolejne kroki
Zobacz następujący artykuł w witrynie Azure portal można wykonywać następujące operacje: [Uruchom ponownie Maszynę wirtualną](devtest-lab-restart-vm.md).
