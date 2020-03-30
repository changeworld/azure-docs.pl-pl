---
title: Włączanie usługi Azure Monitor dla maszyn wirtualnych za pomocą programu PowerShell lub szablonów
description: W tym artykule opisano sposób włączania usługi Azure Monitor dla maszyn wirtualnych dla co najmniej jednej maszyny wirtualnej platformy Azure lub zestawów skalowania maszyn wirtualnych przy użyciu szablonów usługi Azure PowerShell lub Usługi Azure Resource Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/14/2019
ms.openlocfilehash: 75d5203e7c475a44b6a00dbf9286f43114b7b54f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480848"
---
# <a name="enable-azure-monitor-for-vms-using-azure-powershell-or-resource-manager-templates"></a>Włączanie usługi Azure Monitor dla maszyn wirtualnych przy użyciu szablonów programu Azure PowerShell lub Resource Manager

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym artykule wyjaśniono, jak włączyć usługę Azure Monitor dla maszyn wirtualnych dla maszyn wirtualnych platformy Azure lub zestawów skalowania maszyn wirtualnych przy użyciu szablonów usługi Azure PowerShell lub usługi Azure Resource Manager. Po zakończeniu tego procesu pomyślnie rozpoczniesz monitorowanie wszystkich maszyn wirtualnych i dowiesz się, czy występują problemy z wydajnością lub dostępnością.

## <a name="set-up-a-log-analytics-workspace"></a>Konfigurowanie obszaru roboczego usługi Log Analytics

Jeśli nie masz obszaru roboczego usługi Log Analytics, musisz go utworzyć. Przejrzyj metody, które są sugerowane w sekcji [Wymagania wstępne](vminsights-enable-overview.md#log-analytics) przed kontynuowaniem kroków, aby go skonfigurować. Następnie można zakończyć wdrażanie usługi Azure Monitor dla maszyn wirtualnych przy użyciu metody szablonu usługi Azure Resource Manager.

### <a name="install-the-vminsights-solution"></a>Instalowanie rozwiązania VMInsights

Ta metoda zawiera szablon JSON, który określa konfigurację włączania składników rozwiązania w obszarze roboczym usługi Log Analytics.

Jeśli nie wiesz, jak wdrożyć zasoby przy użyciu szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)
* [Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Aby korzystać z interfejsu wiersza polecenia platformy Azure, należy najpierw zainstalować i używać interfejsu wiersza polecenia lokalnie. Musi być uruchomiony interfejsu wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować swoją `az --version`wersję, uruchom polecenie . Aby zainstalować lub uaktualnić platformę Azure CLI, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Zapisz ten plik jako *installsolutionsforvminsights.json* w folderze lokalnym.

1. Przechwytywanie wartości *nazw workspace,* *resourcegroupname*i *miejsca pracy*. Wartość *WorkspaceName* jest nazwą obszaru roboczego usługi Log Analytics. Wartość *obszaru roboczegoLokalizacja* jest regionem zdefiniowanym w obszarze roboczym.

1. Wszystko jest teraz gotowe do wdrożenia tego szablonu.

    * Użyj następujących poleceń programu PowerShell w folderze zawierającym szablon:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        Zmiana konfiguracji może potrwać kilka minut, aby zakończyć. Po zakończeniu zostanie wyświetlony komunikat podobny do następującego i zawiera wynik:

        ```output
        provisioningState       : Succeeded
        ```

    * Aby uruchomić następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure:

        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        Zmiana konfiguracji może potrwać kilka minut, aby zakończyć. Po zakończeniu wyświetlany jest komunikat podobny do następującego i zawiera wynik:

        ```output
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Włącz za pomocą szablonów usługi Azure Resource Manager

Utworzyliśmy przykład szablony usługi Azure Resource Manager do dołączania maszyn wirtualnych i zestawów skalowania maszyny wirtualnej. Szablony te obejmują scenariusze, których można użyć, aby włączyć monitorowanie istniejącego zasobu i utworzyć nowy zasób, który ma włączone monitorowanie.

>[!NOTE]
>Szablon musi zostać wdrożony w tej samej grupie zasobów, co zasób, który ma zostać wprowadzony na pokład.

Jeśli nie wiesz, jak wdrożyć zasoby przy użyciu szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)
* [Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Aby korzystać z interfejsu wiersza polecenia platformy Azure, należy najpierw zainstalować i używać interfejsu wiersza polecenia lokalnie. Musi być uruchomiony interfejsu wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować swoją `az --version`wersję, uruchom polecenie . Aby zainstalować lub uaktualnić platformę Azure CLI, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="download-templates"></a>Pobierz szablony

Szablony usługi Azure Resource Manager znajdują się w pliku archiwum (.zip), który można [pobrać](https://aka.ms/VmInsightsARMTemplates) z naszego repozytorium GitHub. Zawartość pliku zawiera foldery reprezentujące każdy scenariusz wdrażania z plikiem szablonu i parametru. Przed ich uruchomieniem należy zmodyfikować plik parametrów i określić wymagane wartości. Nie modyfikuj pliku szablonu, chyba że trzeba go dostosować do obsługi określonych wymagań. Po zmodyfikowaniu pliku parametrów można go wdrożyć przy użyciu następujących metod opisanych w dalszej części tego artykułu.

Plik pobierania zawiera następujące szablony dla różnych scenariuszy:

- **Istniejący szablon VmOnboarding** umożliwia platformę Azure Monitor dla maszyn wirtualnych, jeśli maszyna wirtualna już istnieje.
- **Szablon NewVmOnboarding** tworzy maszynę wirtualną i umożliwia platformie Azure Monitor dla maszyn wirtualnych, aby ją monitorować.
- **Istniejący szablon VmssOnboarding** umożliwia platformę Azure Monitor dla maszyn wirtualnych, jeśli zestaw skalowania maszyny wirtualnej już istnieje.
- **Szablon NewVmssOnboarding** tworzy zestawy skalowania maszyny wirtualnej i umożliwia platformie Azure Monitor dla maszyn wirtualnych ich monitorowanie.
- **ConfigureWorkspace** szablon konfiguruje obszar roboczy usługi Log Analytics do obsługi usługi Azure Monitor dla maszyn wirtualnych, włączając rozwiązania i kolekcję liczników wydajności systemu operacyjnego Linux i Windows.

>[!NOTE]
>Jeśli zestawy skalowania maszyny wirtualnej były już obecne, a zasady uaktualniania są ustawione na **Ręczne,** usługa Azure Monitor dla maszyn wirtualnych nie będzie domyślnie włączona dla wystąpień po uruchomieniu szablonu **ExistingVmssOnboarding** Azure Resource Manager. Musisz ręcznie uaktualnić wystąpienia.

### <a name="deploy-by-using-azure-powershell"></a>Wdrażanie przy użyciu programu Azure PowerShell

Poniższy krok umożliwia monitorowanie przy użyciu programu Azure PowerShell.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Zmiana konfiguracji może potrwać kilka minut, aby zakończyć. Po zakończeniu zostanie wyświetlony komunikat podobny do następującego i zawiera wynik:

```output
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Poniższy krok umożliwia monitorowanie przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

Dane wyjściowe są podobne do następujących:

```output
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Włącz za pomocą programu PowerShell

Aby włączyć usługę Azure Monitor dla maszyn wirtualnych dla wielu maszyn wirtualnych lub zestawów skalowania maszyn wirtualnych, użyj skryptu programu PowerShell [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0). Jest on dostępny w galerii programu Azure PowerShell. Ten skrypt iteruje przez:

- Każda maszyna wirtualna i skala maszyny wirtualnej ustawiona w ramach subskrypcji.
- Grupa zasobów o określonym w zakresie przez *Grupę zasobów*.
- Pojedyncza maszyna wirtualna lub zestaw skalowania maszyny wirtualnej określony przez *Name*.

Dla każdego zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej skrypt sprawdza, czy rozszerzenie maszyny wirtualnej jest już zainstalowane. Jeśli jest zainstalowane rozszerzenie maszyny Wirtualnej, skrypt próbuje go ponownie zainstalować. Jeśli rozszerzenie maszyny Wirtualnej nie jest zainstalowane, skrypt instaluje rozszerzenia maszyny wirtualnej agenta usługi Log Analytics i zależności.

Sprawdź, czy używasz modułu Programu Azure PowerShell Az w `Enable-AzureRM` wersji 1.0.0 lub nowszej z włączonymi aliasami zgodności. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

Aby uzyskać listę szczegółów argumentu skryptu i `Get-Help`przykład użycia, uruchom program .

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or reconfigures the following on VMs and virtual machine scale sets:
    - Log Analytics VM extension configured to supplied Log Analytics workspace
    - Dependency agent VM extension

    Can be applied to:
    - Subscription
    - Resource group in a subscription
    - Specific VM or virtual machine scale set
    - Compliance results of a policy for a VM or VM extension

    Script will show you a list of VMs or virtual machine scale sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed, they will not install again.
    Use -ReInstall switch if you need to, for example, update the workspace.

    Use -WhatIf if you want to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a resource group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example, to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source and to reinstall (move to a new workspace)
```

Poniższy przykład pokazuje przy użyciu poleceń programu PowerShell w folderze, aby włączyć usługę Azure Monitor dla maszyn wirtualnych i zrozumieć oczekiwane dane wyjściowe:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or virtual machine scale sets matching criteria specified

VMs or virtual machine scale sets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on the previous two VMs or virtual machine scale sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example, to update workspace.

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy monitorowanie jest włączone dla maszyn wirtualnych, te informacje są dostępne do analizy za pomocą usługi Azure Monitor dla maszyn wirtualnych.

- Aby wyświetlić odnalezione zależności aplikacji, zobacz [Wyświetlanie usługi Azure Monitor dla map maszyn wirtualnych.](vminsights-maps.md)

- Aby zidentyfikować wąskie gardła i ogólne wykorzystanie wydajności maszyny wirtualnej, zobacz [Wyświetlanie wydajności maszyny Wirtualnej platformy Azure.](vminsights-performance.md)
