---
title: Włączanie usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) przy użyciu szablonu programu Azure PowerShell lub usługi Resource Manager | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak włączyć usługi Azure Monitor dla maszyn wirtualnych dla maszyn wirtualnych platformy Azure lub zestawy skalowania maszyn wirtualnych przy użyciu szablonów programu Azure PowerShell lub usługi Azure Resource Manager.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: a22bc88fb066d9b845f7fdf1592e2194a03915bc
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524133"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-template"></a>Włączanie usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) przy użyciu szablonu programu Azure PowerShell lub Menedżera zasobów

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym artykule opisano sposób włączania usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) dla maszyn wirtualnych platformy Azure lub zestawy skalowania maszyn wirtualnych przy użyciu szablonów programu Azure PowerShell lub usługi Azure Resource Manager. Po zakończeniu tego procesu, który zostanie pomyślnie rozpoczęto monitorowanie wszystkich maszyn wirtualnych i Dowiedz się, jeśli występują dowolne jakości lub dostępności spowodowanych. 

## <a name="set-up-a-log-analytics-workspace"></a>Skonfiguruj obszar roboczy usługi Log Analytics 

Jeśli nie masz obszaru roboczego usługi Log Analytics, utwórz ją, przeglądając metody, które są zalecane w [wymagania wstępne](vminsights-enable-overview.md#log-analytics) sekcji przed wykonaniem czynności ją skonfigurować, aby można było ukończyć wdrożenie usługi Azure Monitor Maszyny wirtualne przy użyciu metody szablonu usługi Azure Resource Manager.

### <a name="enable-performance-counters"></a>Włącz liczniki wydajności

Jeśli obszar roboczy usługi Log Analytics, który odwołuje się do niej rozwiązanie nie jest już skonfigurowany można zebrać liczników wydajności wymaganych przez to rozwiązanie, należy je włączyć. Można to zrobić na dwa sposoby:
* Ręcznie, zgodnie z opisem w [Windows i Linux źródła danych dotyczących wydajności w usłudze Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Pobierając i uruchamiając skrypt programu PowerShell, który jest dostępny z [galerii programu PowerShell systemu Azure](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Instalowanie rozwiązania ServiceMap i InfrastructureInsights
Ta metoda obejmuje szablon JSON, który określa konfigurację włączanie składników rozwiązania w Twoim obszarze roboczym usługi Log Analytics.

Jeśli znasz wdrażanie zasobów za pomocą szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/resource-group-template-deploy.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Jeśli zdecydujesz się użyć wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie. Musi być uruchomiona wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować wersję, uruchom `az --version`. Jeśli musisz zainstalować lub uaktualnić wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Zapisz ten plik jako *installsolutionsforvminsights.json* do folderu lokalnego.

1. Przechwytywanie wartości *WorkspaceName*, *ResourceGroupName*, i *WorkspaceLocation*. Wartość *WorkspaceName* to nazwa obszaru roboczego usługi Log Analytics. Wartość *WorkspaceLocation* jest region, w obszarze roboczym jest zdefiniowany w.

1. Wszystko jest teraz gotowe do wdrożenia tego szablonu.
 
    * Użyj następujących poleceń programu PowerShell w folderze, który zawiera szablon:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        Zmiana konfiguracji może potrwać kilka minut. Po jej zakończeniu zostanie wyświetlony komunikat podobny do poniższego, która zawiera wynik:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Aby za pomocą wiersza polecenia platformy Azure, należy uruchomić następujące polecenie:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

Zmiana konfiguracji może potrwać kilka minut. Po jej zakończeniu zostanie wyświetlony komunikat podobny do poniższego, która zawiera wynik:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-azure-resource-manager-template"></a>Włącz przy użyciu szablonu usługi Azure Resource Manager
Udostępniliśmy przykładowe szablony usługi Azure Resource Manager do dołączenia do maszyn wirtualnych i zestawów skalowania maszyn wirtualnych. Te szablony obejmują scenariusze dotyczące włączania monitorowania w istniejącym zasobie oraz do tworzenia nowego zasobu, mających monitorowanie jest włączone.

>[!NOTE]
>Szablon musi zostać wdrożony w tej samej grupie zasobów co zasób ma zostać dołączona.

Jeśli znasz koncepcji wdrażanie zasobów za pomocą szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/resource-group-template-deploy.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Jeśli zdecydujesz się użyć wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie. Musi być uruchomiona wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować wersję, uruchom `az --version`. Jeśli musisz zainstalować lub uaktualnić wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="download-templates"></a>Pobierz szablony

Szablony usługi Azure Resource Manager znajdują się w pliku archiwum (.zip), które są dostępne [Pobierz](https://aka.ms/VmInsightsARMTemplates) z repozytorium GitHub. Zawartość tego pliku zawierają foldery reprezentujący poszczególnych scenariuszy wdrażania z plików szablonu oraz parametrów. Przed uruchomieniem, zmodyfikuj plik parametrów i podaj wartości wymagane. Nie należy modyfikować pliku szablonu, o ile nie trzeba dostosować go do obsługi konkretne wymagania. Po zmodyfikowaniu pliku parametrów możesz wdrożyć ją za pomocą następujących metod, które są opisane w dalszej części tego artykułu. 

Pobrany plik zawiera następujące szablony w różnych scenariuszach:

- **ExistingVmOnboarding** szablonu włącza usługi Azure Monitor dla maszyn wirtualnych, jeśli maszyny wirtualne już istnieje.
- **NewVmOnboarding** szablon umożliwia utworzenie maszyny wirtualnej i umożliwia usłudze Azure Monitor dla maszyn wirtualnych ją monitorować.
- **ExistingVmssOnboarding** szablonu umożliwia usłudze Azure Monitor dla maszyn wirtualnych, jeśli istnieje już zestaw skalowania maszyn wirtualnych.
- **NewVmssOnboarding** szablon tworzy zestawy skalowania maszyn wirtualnych i umożliwia usłudze Azure Monitor dla maszyn wirtualnych, ich monitorowania.
- **ConfigureWorksapce*** szablonu konfiguruje obszaru roboczego usługi Log Analytics w celu obsługi usługi Azure Monitor dla maszyn wirtualnych przez włączenie rozwiązania i zbieranie liczników wydajności systemu operacyjnego Windows i Linux.

>[!NOTE]
>Jeśli były obecne zestawy skalowania maszyn wirtualnych i ustawiono zasady uaktualniania **ręczne**, usługi Azure Monitor dla maszyn wirtualnych nie zostanie włączona dla wystąpień domyślnie po uruchomieniu **ExistingVmssOnboarding** Szablon usługi Azure Resource Manager. Musisz ręcznie uaktualnić wystąpienia.

### <a name="deploy-using-azure-powershell"></a>Wdrażanie przy użyciu programu Azure PowerShell

Następny krok umożliwia monitorowanie za pomocą programu Azure PowerShell.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Zmiana konfiguracji może potrwać kilka minut. Po jej zakończeniu zostanie wyświetlony komunikat podobny do poniższego, która zawiera wynik:

```powershell
provisioningState       : Succeeded
```
### <a name="deploy-using-azure-cli"></a>Wdrażanie przy użyciu wiersza polecenia platformy Azure

Następny krok umożliwia monitorowanie za pomocą wiersza polecenia platformy Azure.   

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

Dane wyjściowe będą wyglądać w następujący sposób:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Włącz przy użyciu programu PowerShell

Aby włączyć usługi Azure Monitor dla maszyn wirtualnych, wiele maszyn wirtualnych i zestawów skalowania maszyn wirtualnych, możesz użyć skryptu programu PowerShell [VMInsights.ps1 instalacji](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), która jest dostępna w galerii programu PowerShell platformy Azure. Ten skrypt wykonuje iterację przez każdy wirtualny maszyny i maszyn wirtualnych zestawu skalowania w ramach subskrypcji, w grupie zasobów o określonym zakresie, który jest określony przez *ResourceGroup*, lub do jednej maszyny Wirtualnej lub maszyny wirtualnej zestawu skalowania, który jest określony przez *Nazwa*. Dla każdej maszyny Wirtualnej lub maszyny wirtualnej zestawu skalowania skrypt sprawdza, czy rozszerzenie maszyny Wirtualnej jest już zainstalowany. Jeśli nie zainstalowano rozszerzenia maszyny Wirtualnej, skrypt próbuje zainstalować go ponownie. Jeśli zainstalowano rozszerzenia maszyny Wirtualnej, skrypt instaluje rozszerzenia maszyny Wirtualnej agenta usługi Log Analytics i zależności.

Ten skrypt wymaga programu Azure PowerShell module Az wersji 1.0.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

Aby uzyskać listę szczegółów argumentu oraz przykład użycia skryptu, uruchom `Get-Help`.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VMs and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VMs/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


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

    Install for all VMs in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to reinstall (move to a new workspace)
```

W poniższym przykładzie pokazano, aby włączyć usługi Azure Monitor dla maszyn wirtualnych i zrozumieć oczekiwanych danych wyjściowych za pomocą poleceń programu PowerShell w folderze:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or VM ScaleSets matching criteria specified

VMs or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on above 2 VMs or VM Scale Sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example to update workspace

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

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy monitorowanie jest włączone dla maszyn wirtualnych, te informacje są dostępne dla analizy dzięki usłudze Azure Monitor dla maszyn wirtualnych. Aby dowiedzieć się, jak korzystać z funkcji Health, zobacz [widok usługi Azure Monitor kondycji maszyn wirtualnych](vminsights-health.md). Aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych mapy](vminsights-maps.md). Aby zidentyfikować wąskie gardła i ogólnego użycia za pomocą wydajność maszyn wirtualnych, zobacz [wydajność maszyny Wirtualnej platformy Azure w widoku](vminsights-performance.md), lub aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych jest mapowany](vminsights-maps.md).