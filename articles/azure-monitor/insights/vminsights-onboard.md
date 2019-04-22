---
title: Wdrażanie usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak wdrożyć i skonfigurować usługi Azure Monitor dla maszyn wirtualnych, aby można było zacząć, zrozumienie, jak działa aplikacja rozproszona i problemy z kondycją, które zostały zidentyfikowane.
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
ms.date: 03/13/2019
ms.author: magoedte
ms.openlocfilehash: 1a4bfae22477e345176971bd40b0afa91c8867fb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885829"
---
# <a name="deploy-azure-monitor-for-vms-preview"></a>Wdrażanie usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym artykule opisano sposób konfigurowania usługi Azure Monitor dla maszyn wirtualnych. Monitoruje kondycję systemu operacyjnego usługi Azure virtual machines (VMs) i zestawy skalowania maszyn wirtualnych i maszyn wirtualnych w danym środowisku. Monitorowanie obejmuje odnajdywania oraz mapowania zależności aplikacji, które mogą być hostowane na nich. 

Należy włączyć usługi Azure Monitor dla maszyn wirtualnych przy użyciu jednej z następujących metod:

* Włącz pojedynczej maszyny wirtualnej platformy Azure, wybierając **Insights (wersja zapoznawcza)** bezpośrednio z poziomu maszyny Wirtualnej.
* Włącz co najmniej dwóch maszyn wirtualnych platformy Azure za pomocą usługi Azure Policy. Za pomocą tej metody wymagane zależności istniejących i nowych maszyn wirtualnych są zainstalowane i poprawnie skonfigurowane. Niezgodne maszyny wirtualne są zgłaszane, dzięki czemu możesz zdecydować, czy chcesz je włączyć, a jaki chcesz je skorygować.
* Włączyć dwie lub więcej maszyn wirtualnych platformy Azure lub na maszynie wirtualnej skalować zestawy skalowania w określonej subskrypcji lub grupy zasobów przy użyciu programu PowerShell.

Dodatkowe informacje na temat poszczególnych metod znajduje się w dalszej części tego artykułu.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że rozumiesz, informacje w poniższych sekcjach.

### <a name="log-analytics"></a>Log Analytics

Usługa Azure Monitor dla maszyn wirtualnych obsługuje obszar roboczy usługi Log Analytics w następujących regionach:

- Środkowo-zachodnie stany USA
- Wschodnie stany USA
- Canada Central<sup>1</sup>
- UK South<sup>1</sup>
- Europa Zachodnia
- Azja południowo-wschodnia<sup>1</sup>

<sup>1</sup> ten region nie obsługuje obecnie funkcję kondycji usługi Azure monitor maszyn wirtualnych.

>[!NOTE]
>Usługa Azure virtual machines można wdrażać z dowolnego regionu i nie są ograniczone do określonych regionów obsługiwanych dla obszaru roboczego usługi Log Analytics.
>

Jeśli nie masz obszaru roboczego, można utworzyć jeden z jedną z następujących metod:
* [Interfejs wiersza polecenia platformy Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [Program PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Witryna Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Jeśli w przypadku włączenia monitorowania dla pojedynczej maszyny Wirtualnej platformy Azure w witrynie Azure portal, można utworzyć obszar roboczy, w trakcie tego procesu.

Aby włączyć rozwiązanie dla scenariusza na dużą skalę, należy najpierw skonfigurować następujące w obszarze roboczym usługi Log Analytics:

* Zainstaluj ServiceMap i InfrastructureInsights rozwiązań. Tylko przy użyciu szablonu usługi Azure Resource Manager, który znajduje się w tym artykule można wykonać tej instalacji.
* Konfiguruj obszar roboczy usługi Log Analytics, można zebrać liczników wydajności.

Aby skonfigurować obszar roboczy dla scenariusza na dużą skalę, zobacz zestaw się obszaru roboczego analizy dzienników dla wdrożenia usługi na dużą skalę.

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

W poniższej tabeli wymieniono systemy operacyjne Windows i Linux, które są obsługiwane z usługą Azure Monitor dla maszyn wirtualnych. Pełną listę, która szczegóły wersji systemu operacyjnego Linux głównych i pomocniczych i obsługiwane wersje jądra później znajduje się w tej sekcji.

|Wersja systemu operacyjnego |Wydajność |Maps |Health |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | |
|System Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14.04, 16.04, 18.04 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 11, 12 | X | X | X |
|Debian 8, 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> funkcja wydajności usługi Azure Monitor dla maszyn wirtualnych jest dostępna tylko z usługi Azure Monitor. Nie jest dostępna, gdy zostanie do niego dostęp bezpośrednio z poziomu okienka po lewej stronie maszyny Wirtualnej platformy Azure.

>[!NOTE]
>Poniższe informacje dotyczą do obsługi systemów operacyjnych Linux:
> - Obsługiwane są tylko wersje domyślne i wersje SMP jądra systemu Linux.
> - Zwalnia jądra niestandardowe, takie jak rozszerzenia adresu fizycznego (PAE) i Xen, nie są obsługiwane w przypadku dowolnej dystrybucji systemu Linux. Na przykład system z ciągu wersji *2.6.16.21-0.8-xen* nie jest obsługiwane.
> - Niestandardowe jądra, tym rekompilacji standardowa jądra nie są obsługiwane.
> - CentOSPlus jądra jest obsługiwana.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

### <a name="centosplus"></a>CentOSPlus
| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Wersja systemu operacyjnego | Wersja jądra |
|:--|:--|
| Ubuntu 18.04 | jądra 4.15. * |
| Systemu Ubuntu 16.04.3 | jądra 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Wersja systemu operacyjnego | Wersja jądra
|:--|:--|
|11 SP4 | 3.0.* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Wersja systemu operacyjnego | Wersja jądra
|:--|:--|
|12 Z DODATKIEM SP2 | 4.4. * |
|12 Z DODATKIEM SP3 | 4.4. * |

### <a name="the-microsoft-dependency-agent"></a>Program Microsoft Dependency agent
Usługi Azure Monitor dla funkcji mapy maszyny wirtualne, dane są pobierane z Microsoft Dependency agent. Agent zależności zależy od agenta usługi Log Analytics do połączenia z usługi Log Analytics. W związku z tym system musi mieć agenta usługi Log Analytics, zainstalować i skonfigurować za pomocą agenta zależności.

Czy włączyć usługi Azure Monitor dla maszyn wirtualnych z jednej maszyny wirtualnej platformy Azure lub użyć metody wdrożenia na dużą skalę, należy zainstalować agenta w ramach środowiska za pomocą rozszerzenia agenta zależności maszyn wirtualnych platformy Azure.

W środowisku hybrydowym można pobrać i zainstalować agenta zależności na dwa sposoby: Ręcznie lub za pomocą metody automatycznego wdrażania dla maszyn wirtualnych, znajdują się poza systemem Azure.

W poniższej tabeli opisano połączone źródła obsługiwanych przez funkcję mapy w środowisku hybrydowym.

| Połączone źródło | Obsługiwane | Opis |
|:--|:--|:--|
| Agenci dla systemu Windows | Yes | Oprócz [agenta usługi Log Analytics dla Windows](../../azure-monitor/platform/log-analytics-agent.md), agenci Windows wymagają Microsoft Dependency agent. Aby uzyskać pełną listę wersji systemów operacyjnych, zobacz [obsługiwane systemy operacyjne](#supported-operating-systems). |
| Agenci dla systemu Linux | Yes | Oprócz [agenta usługi Log Analytics dla systemu Linux](../../azure-monitor/platform/log-analytics-agent.md), agenci dla systemu Linux wymaga program Microsoft Dependency agent. Aby uzyskać pełną listę wersji systemów operacyjnych, zobacz [obsługiwane systemy operacyjne](#supported-operating-systems). |
| Grupa zarządzania programu System Center Operations Manager | Nie | |

Agent zależności można pobrać z następujących lokalizacji:

| Plik | System operacyjny | Wersja | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach
Aby włączyć i funkcje w usłudze Azure Monitor są dostępne dla maszyn wirtualnych, musisz mieć przypisane następujące role dostępu:

- Aby włączyć rozwiązanie, konieczne jest posiadanie *Współautor usługi Log Analytics* roli.

- Aby wyświetlić kondycję, wydajność i mapowanie danych, konieczne jest posiadanie *Czytelnik monitorowania* roli maszyny wirtualnej platformy Azure. Obszar roboczy usługi Log Analytics, musi być skonfigurowany dla usługi Azure Monitor dla maszyn wirtualnych.

Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego usługi Log Analytics, zobacz [możesz zarządzać obszarami roboczymi](../../azure-monitor/platform/manage-access.md).

## <a name="enable-monitoring-in-the-azure-portal"></a>Aby włączyć monitorowanie w witrynie Azure portal
Aby włączyć monitorowanie maszyny wirtualnej platformy Azure w witrynie Azure portal, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **maszyn wirtualnych**.

1. Z listy wybierz maszynę wirtualną.

1. Na stronie maszyny Wirtualnej w **monitorowanie** zaznacz **Insights (wersja zapoznawcza)**.

1. Na **Insights (wersja zapoznawcza)** wybierz opcję **Wypróbuj teraz usługę**.

    ![Włączanie usługi Azure Monitor dla maszyn wirtualnych dla maszyny Wirtualnej](./media/vminsights-onboard/enable-vminsights-vm-portal-01.png)
1. Na **Azure Monitor Insights dołączania** strony, jeśli masz istniejące usługi Log Analytics obszaru roboczego w tej samej subskrypcji, wybierz go na liście rozwijanej.  
    Lista preselects domyślnego obszaru roboczego i lokalizacji, która została wdrożona maszyna wirtualna, aby w ramach subskrypcji. 

    >[!NOTE]
    >Jeśli chcesz utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z maszyny Wirtualnej, postępuj zgodnie z instrukcjami [Utwórz obszar roboczy usługi Log Analytics](../../azure-monitor/learn/quick-create-workspace.md) w jednym z obsługiwanych regionów wymienionych wcześniej.

Po włączeniu monitorowania może zająć około 10 minut, zanim będzie można wyświetlić metryki kondycji dla maszyny wirtualnej.

![Włączanie usługi Azure Monitor monitorowania przetwarzanie wdrożenia maszyn wirtualnych](./media/vminsights-onboard/onboard-vminsights-vm-portal-status.png)


## <a name="deploy-at-scale"></a>Wdrażanie w skali
W tej sekcji wdrażania usługi Azure Monitor dla maszyn wirtualnych na dużą skalę za pomocą usługi Azure Policy lub programu Azure PowerShell.

Przed przystąpieniem do wdrażania maszyn wirtualnych, należy wstępnie skonfigurować obszar roboczy usługi Log Analytics, wykonując następujące czynności:

1. Jeśli nie masz jeszcze obszaru roboczego, utwórz taki, który może obsługiwać monitorowanie platformy Azure dla maszyn wirtualnych.  
    Przed kontynuowaniem, zobacz [możesz zarządzać obszarami roboczymi](../../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json) Aby zrozumieć zagadnienia dotyczące kosztów, zarządzania i zgodności.

1. Utwórz nowy obszar roboczy, jeśli nie już istnieje, może służyć do obsługi usługi Azure Monitor dla maszyn wirtualnych. Przegląd [możesz zarządzać obszarami roboczymi](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json) przed utworzeniem nowego obszaru roboczego, aby poznać zagadnienia dotyczące kosztów, zarządzania i zgodności przed kontynuowaniem.

1. Włącz liczniki wydajności w obszarze roboczym dla kolekcji w systemie Linux i Windows maszyn wirtualnych.

1. Instalowanie i włączanie ServiceMap i InfrastructureInsights rozwiązania w Twoim obszarze roboczym.

### <a name="set-up-a-log-analytics-workspace"></a>Skonfiguruj obszar roboczy usługi Log Analytics
Jeśli nie masz obszaru roboczego usługi Log Analytics, utwórz ją, przeglądając metody, które są zalecane w ["Wymagania wstępne"](#log-analytics) sekcji.

#### <a name="enable-performance-counters"></a>Włącz liczniki wydajności
Jeśli obszar roboczy usługi Log Analytics, który odwołuje się do niej rozwiązanie nie jest już skonfigurowany można zebrać liczników wydajności wymaganych przez to rozwiązanie, należy je włączyć. Można to zrobić na dwa sposoby:
* Ręcznie, zgodnie z opisem w [Windows i Linux źródła danych dotyczących wydajności w usłudze Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Pobierając i uruchamiając skrypt programu PowerShell, który jest dostępny z [galerii programu PowerShell systemu Azure](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

#### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Instalowanie rozwiązania ServiceMap i InfrastructureInsights
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

### Enable by using Azure Policy
To enable Azure Monitor for VMs at scale in a way that helps ensure consistent compliance and the automatic enabling of the newly provisioned VMs, we recommend [Azure Policy](../../governance/policy/overview.md). These policies:

* Deploy the Log Analytics agent and the Dependency agent.
* Report on compliance results.
* Remediate for non-compliant VMs.

To enable Azure Monitor for VMs by using Azure Policy in your tenant:

- Assign the initiative to a scope: management group, subscription, or resource group
- Review and remediate compliance results

For more information about assigning Azure Policy, see [Azure Policy overview](../../governance/policy/overview.md#policy-assignment) and review the [overview of management groups](../../governance/management-groups/index.md) before you continue.

The policy definitions are listed in the following table:

|Name |Description |Type |
|-----|------------|-----|
|[Preview]: Enable Azure Monitor for VMs |Enable Azure Monitor for the Virtual Machines (VMs) in the specified scope (management group, subscription, or resource group). Takes Log Analytics workspace as a parameter. |Initiative |
|[Preview]: Audit Dependency Agent Deployment – VM Image (OS) unlisted |Reports VMs as non-compliant if the VM Image (OS) isn't defined in the list and the agent isn't installed. |Policy |
|[Preview]: Audit Log Analytics Agent Deployment – VM Image (OS) unlisted |Reports VMs as non-compliant if the VM Image (OS) isn't defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Dependency Agent for Linux VMs |Deploy Dependency Agent for Linux VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Dependency Agent for Windows VMs |Deploy Dependency Agent for Windows VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Log Analytics Agent for Linux VMs |Deploy Log Analytics Agent for Linux VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Log Analytics Agent for Windows VMs |Deploy Log Analytics Agent for Windows VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |

Standalone policy (not included with the initiative) is described here:

|Name |Description |Type |
|-----|------------|-----|
|[Preview]: Audit Log Analytics Workspace for VM - Report Mismatch |Report VMs as non-compliant if they aren't logging to the Log Analytics workspace specified in the policy/initiative assignment. |Policy |

#### Assign the Azure Monitor initiative
With this initial release, you can create the policy assignment only in the Azure portal. To understand how to complete these steps, see [Create a policy assignment from the Azure portal](../../governance/policy/assign-policy-portal.md).

1. To launch the Azure Policy service in the Azure portal, select **All services**, and then search for and select **Policy**.

1. In the left pane of the Azure Policy page, select **Assignments**.  
    An assignment is a policy that has been assigned to take place within a specific scope.
    
1. At the top of the **Policy - Assignments** page, select **Assign Initiative**.

1. On the **Assign Initiative** page, select the **Scope** by clicking the ellipsis (...), and select a management group or subscription.  
    In our example, a scope limits the policy assignment to a grouping of virtual machines for enforcement.
    
1. At the bottom of the **Scope** page, save your changes by selecting **Select**.

1. (Optional) To remove one or more resources from the scope, select **Exclusions**.

1. Select the **Initiative definition** ellipsis (...) to display the list of available definitions, select **[Preview] Enable Azure Monitor for VMs**, and then select **Select**.  
    The **Assignment name** box is automatically populated with the initiative name you selected, but you can change it. You can also add an optional description. The **Assigned by** box is automatically populated based on who is logged in, and this value is optional.
    
1. In the **Log Analytics workspace** drop-down list for the supported region, select a workspace.

   > [!NOTE]
   > If the workspace is beyond the scope of the assignment, grant *Log Analytics Contributor* permissions to the policy assignment's Principal ID. If you don't do this, you might see a deployment failure such as: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`
   > To grant access, review [how to manually configure the managed identity](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  The **Managed Identity** check box is selected, because the initiative being assigned includes a policy with the *deployIfNotExists* effect.
    
1. In the **Manage Identity location** drop-down list, select the appropriate region.

1. Select **Assign**.

#### Review and remediate the compliance results

You can learn how to review compliance results by reading [identify non-compliance results](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). In the left pane, select **Compliance**, and then locate the **[Preview] Enable Azure Monitor for VMs** initiative for VMs that aren't compliant according to the assignment you created.

![Policy compliance for Azure VMs](./media/vminsights-onboard/policy-view-compliance-01.png)

Based on the results of the policies included with the initiative, VMs are reported as non-compliant in the following scenarios:

* Log Analytics or the Dependency agent isn't deployed.  
    This scenario is typical for a scope with existing VMs. To mitigate it, deploy the required agents by [creating remediation tasks](../../governance/policy/how-to/remediate-resources.md) on a non-compliant policy.  
    - [Preview]: Deploy Dependency Agent for Linux VMs
    - [Preview]: Deploy Dependency Agent for Windows VMs
    - [Preview]: Deploy Log Analytics Agent for Linux VMs
    - [Preview]: Deploy Log Analytics Agent for Windows VMs

* VM Image (OS) isn't identified in the policy definition.  
    The criteria of the deployment policy include only VMs that are deployed from well-known Azure VM images. Check the documentation to see whether the VM OS is supported. If it isn't supported, duplicate the deployment policy and update or modify it to make the image compliant.  
    - [Preview]: Audit Dependency Agent Deployment – VM Image (OS) unlisted
    - [Preview]: Audit Log Analytics Agent Deployment – VM Image (OS) unlisted

* VMs aren't logging in to the specified Log Analytics workspace.  
    It's possible that some VMs in the initiative scope are logging in to a Log Analytics workspace other than the one that's specified in the policy assignment. This policy is a tool to identify which VMs are reporting to a non-compliant workspace.  
    - [Preview]: Audit Log Analytics Workspace for VM - Report Mismatch

### Enable with PowerShell
To enable Azure Monitor for VMs for multiple VMs or virtual machine scale sets, you can use the PowerShell script [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), available from the Azure PowerShell Gallery. This script iterates through every virtual machine and virtual machine scale set in your subscription, in the scoped resource group that's specified by *ResourceGroup*, or to a single VM or virtual machine scale set that's specified by *Name*. For each VM or virtual machine scale set, the script verifies whether the VM extension is already installed. If the VM extension is not installed, the script tries to reinstall it. If the VM extension is installed, the script installs the Log Analytics and Dependency agent VM extensions.

This script requires Azure PowerShell module Az version 1.0.0 or later. Run `Get-Module -ListAvailable Az` to find the version. If you need to upgrade, see [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps). If you're running PowerShell locally, you also need to run `Connect-AzAccount` to create a connection with Azure.

To get a list of the script's argument details and example usage, run `Get-Help`.

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

## <a name="enable-for-a-hybrid-environment"></a>Jest włączone w środowisku hybrydowym
W tej sekcji opisano sposób wdrażania maszyn wirtualnych lub fizycznych komputerów, które są hostowane w centrum danych lub innych środowisk w chmurze do monitorowania usługi Azure Monitor dla maszyn wirtualnych.

Usługi Azure Monitor dla agenta zależności mapy maszyn wirtualnych nie przesyłać żadnych danych, a nie wymaga wprowadzania zmian w zapory i porty. Dane mapy są zawsze przesyłane przez agenta usługi Log Analytics w usłudze Azure Monitor, bezpośrednio lub za pośrednictwem [bramy pakietu OMS](../../azure-monitor/platform/gateway.md) Jeśli Twoje informatyczne zasady zabezpieczeń nie pozwalają komputerom w sieci, aby nawiązać połączenie z Internetem.

Przegląd wymagań i metod wdrażania [agenta Log Analytics w systemie Linux i Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Kroki są podsumować w następujący sposób:

1. Zainstaluj agenta programu Log Analytics dla Windows lub Linux.

1. Pobierz i zainstaluj usługi Azure Monitor dla agenta zależności mapy maszyn wirtualnych dla [Windows](https://aka.ms/dependencyagentwindows) lub [Linux](https://aka.ms/dependencyagentlinux).

1. Należy włączyć zbieranie liczników wydajności.

1. Wdrażanie usługi Azure Monitor dla maszyn wirtualnych.

### <a name="install-the-dependency-agent-on-windows"></a>Instalowanie agenta zależności na Windows
Można zainstalować agenta zależności ręcznie na komputerach Windows przez uruchomienie `InstallDependencyAgent-Windows.exe`. Po uruchomieniu tego pliku wykonywalnego bez żadnych opcji, uruchamia Kreatora instalacji, które można wykonać, aby zainstalować agenta interaktywnie.

>[!NOTE]
>*Administrator* uprawnienia są wymagane do zainstalowania lub odinstalowania agenta.

W poniższej tabeli wymieniono parametry, które są obsługiwane przez Instalatora agenta z wiersza polecenia.

| Parametr | Opis |
|:--|:--|
| /? | Zwraca listę opcji wiersza polecenia. |
| /S | Wykonanie dyskretnej instalacji bez interakcji użytkownika. |

Na przykład, aby uruchomić program instalacyjny z `/?` parametr, typ **InstallDependencyAgent Windows.exe /?**.

Pliki agenta zależności Windows są zainstalowane w *C:\Program Files\Microsoft Dependency Agent* domyślnie. Jeśli agenta zależności nie powiedzie się po zakończeniu instalacji, sprawdź dzienniki Aby uzyskać szczegółowe informacje o błędzie. Katalog dziennika jest *%Programfiles%\Microsoft Agent\logs zależności*.

### <a name="install-the-dependency-agent-on-linux"></a>Instalowanie agenta zależności w systemie Linux
Agent zależności jest zainstalowany na serwery z systemem Linux z *InstallDependencyAgent Linux64.bin*, skrypt powłoki, za pomocą samorozpakowujący się plik binarny. Można uruchomić go za pomocą `sh` lub Dodaj uprawnienia do wykonywania do pliku sam.

>[!NOTE]
> Aby zainstalować lub skonfigurować agenta, wymagany jest dostęp na poziomie administratora.
>

| Parametr | Opis |
|:--|:--|
| -help | Pobierz listę opcji wiersza polecenia. |
| -s | Przeprowadź instalację cichą bez monitowania użytkownika. |
| — Sprawdź | Sprawdzanie uprawnień i systemu operacyjnego, ale nie należy instalować agenta. |

Na przykład, aby uruchomić program instalacyjny z `-help` parametr, typ **InstallDependencyAgent Linux64.bin — Pomoc**.

Instalowanie agenta zależności systemu Linux jako użytkownik główny, uruchamiając następujące polecenie `sh InstallDependencyAgent-Linux64.bin`.

Jeśli agenta zależności nie powiedzie się, sprawdź dzienniki Aby uzyskać szczegółowe informacje o błędzie. Na agentach systemu Linux jest katalog dziennika */var/opt/microsoft/dependency-agent/log*.

Pliki agenta zależności są umieszczone w następujących katalogach:

| Pliki | Lokalizacja |
|:--|:--|
| Pliki jądra | /opt/microsoft/dependency-agent |
| Pliki dziennika | /var/opt/microsoft/dependency-agent/log |
| Plik konfiguracji | /etc/opt/microsoft/dependency-agent/config |
| Pliki wykonywalne usługi | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Pliki binarne magazynu | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Włącz liczniki wydajności
Jeśli obszar roboczy usługi Log Analytics, który odwołuje się do niej rozwiązanie nie jest już skonfigurowany można zebrać liczników wydajności wymaganych przez to rozwiązanie, należy je włączyć. Można to zrobić na dwa sposoby:
* Ręcznie, zgodnie z opisem w [Windows i Linux źródła danych dotyczących wydajności w usłudze Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Pobierając i uruchamiając skrypt programu PowerShell, który jest dostępny z [galerii programu PowerShell systemu Azure](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="deploy-azure-monitor-for-vms"></a>Wdrażanie usługi Azure Monitor dla maszyn wirtualnych
Ta metoda obejmuje szablon JSON, który określa konfigurację włączanie składników rozwiązania w Twoim obszarze roboczym usługi Log Analytics.

Jeśli znasz wdrażanie zasobów za pomocą szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/resource-group-template-deploy.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Jeśli zdecydujesz się użyć wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie. Musi być uruchomiona wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować wersję, uruchom `az --version`. Jeśli musisz zainstalować lub uaktualnić wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

#### <a name="create-and-execute-a-template"></a>Tworzenie i wykonywanie szablonu

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

1. Edytuj wartości *WorkspaceName*, *ResourceGroupName*, i *WorkspaceLocation*. Wartość *WorkspaceName* jest pełny identyfikator zasobu obszaru roboczego usługi Log Analytics, która zawiera nazwę obszaru roboczego. Wartość *WorkspaceLocation* jest region, w obszarze roboczym jest zdefiniowany w.

1. Możesz wdrożyć ten szablon przy użyciu następującego polecenia programu PowerShell:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Zmiana konfiguracji może potrwać kilka minut. Po jej zakończeniu zostanie wyświetlony komunikat podobny do poniższego, która zawiera wynik:

    ```powershell
    provisioningState       : Succeeded
    ```
   Po włączeniu monitorowania może zająć około 10 minut, zanim będzie można wyświetlić stan kondycji i metryki dla komputera hybrydowych.

## <a name="performance-counters-enabled"></a>Liczniki wydajności włączone
Usługa Azure Monitor dla maszyn wirtualnych służy do konfigurowania obszaru roboczego usługi Log Analytics można zebrać liczników wydajności, które są używane przez rozwiązania. Poniższa tabela zawiera listę obiektów i liczników skonfigurowane przez to rozwiązanie, które są zbierane co 60 sekund.

### <a name="windows-performance-counters"></a>Liczniki wydajności systemu Windows

|Nazwa obiektu |Nazwa licznika |
|------------|-------------|
|Dysk logiczny |% Wolnego miejsca |
|Dysk logiczny |Średni Czas dysku w s/Odczyt |
|Dysk logiczny |Średni Dysku w s/Transfer |
|Dysk logiczny |Średni Dysku w s/Zapis |
|Dysk logiczny |Bajty dysku/s |
|Dysk logiczny |Bajty odczytu z dysku/s |
|Dysk logiczny |Odczyty dysku/s |
|Dysk logiczny |Transfery dyskowe/s |
|Dysk logiczny |Bajty zapisu na dysku/s |
|Dysk logiczny |Zapisy dysku/s |
|Dysk logiczny |Wolne megabajty |
|Memory (Pamięć) |Dostępna pamięć (MB) |
|Karta sieciowa |Bajty odebrane/s |
|Karta sieciowa |Bajty wysłane/s |
|Procesor |Czas procesora (%) |

### <a name="linux-performance-counters"></a>Liczniki wydajności systemu Linux

|Nazwa obiektu |Nazwa licznika |
|------------|-------------|
|Dysk logiczny |Procent wykorzystania miejsca |
|Dysk logiczny |Bajty odczytu z dysku/s |
|Dysk logiczny |Odczyty dysku/s |
|Dysk logiczny |Transfery dyskowe/s |
|Dysk logiczny |Bajty zapisu na dysku/s |
|Dysk logiczny |Zapisy dysku/s |
|Dysk logiczny |Wolne megabajty |
|Dysk logiczny |Bajty dysku logicznego/s |
|Memory (Pamięć) |Dostępna pamięć (MB) |
|Sieć |Całkowita liczba odebranych bajtów |
|Sieć |Całkowita liczba przesłanych bajtów |
|Procesor |Czas procesora (%) |

## <a name="diagnostic-and-usage-data"></a>Dane diagnostyczne i dane dotyczące użycia
Firma Microsoft automatycznie zbiera dane dotyczące użycia i wydajności przez korzystanie z usługi Azure Monitor. Firma Microsoft używa tych danych do zapewniania i poprawiania jakości, bezpieczeństwa i integralności usługi. Aby zapewnić dokładne i wydajne funkcje do rozwiązywania problemów, dane z funkcji mapy zawiera informacje o konfiguracji oprogramowania, takie jak system operacyjny i wersję, adres IP, nazwę DNS i nazwę stacji roboczej. Firma Microsoft nie zbiera nazwisk, adresów ani innych informacji kontaktowych.

Aby uzyskać więcej informacji na temat zbierania i wykorzystywania danych, zobacz [Microsoft Online Services Privacy Statement](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]
## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy dla maszyny wirtualnej jest włączone monitorowanie, te informacje są dostępne dla analizy dzięki usłudze Azure Monitor dla maszyn wirtualnych. Aby dowiedzieć się, jak korzystać z funkcji Health, zobacz [widok usługi Azure Monitor kondycji maszyn wirtualnych](vminsights-health.md). Aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych mapy](vminsights-maps.md).
