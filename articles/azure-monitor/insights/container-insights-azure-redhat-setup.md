---
title: Konfigurowanie klastrów usługi Azure Red Hat OpenShift za pomocą Azure Monitor dla kontenerów | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania Azure Monitor kontenerów do monitorowania klastrów Kubernetes hostowanych na platformie Azure Red Hat OpenShift.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/18/2019
ms.openlocfilehash: 6fc81fa84d4d40d5318d6bf3690aa4b90c4e3c9b
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196815"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>Konfigurowanie klastrów usługi Azure Red Hat OpenShift za pomocą Azure Monitor dla kontenerów

Azure Monitor dla kontenerów zapewnia rozbudowane środowisko monitorowania dla klastrów usługi Azure Kubernetes Service (AKS) i AKS Engine. W tym artykule opisano sposób włączania monitorowania klastrów Kubernetes hostowanych na [platformie Azure Red Hat OpenShift](../../openshift/intro-openshift.md) w celu osiągnięcia podobnego środowiska monitorowania.

Azure Monitor dla kontenerów można włączyć dla nowych lub jednego lub kilku istniejących wdrożeń usługi Azure Red Hat OpenShift przy użyciu następujących obsługiwanych metod:

- W przypadku istniejącego klastra z Azure Portal lub przy użyciu szablonu Azure Resource Manager
- Nowy klaster przy użyciu szablonu Azure Resource Manager 

## <a name="supported-and-unsupported-features"></a>Obsługiwane i nieobsługiwane funkcje

Azure Monitor for Containers obsługuje monitorowanie usługi Azure Red Hat OpenShift zgodnie z opisem w artykule [Omówienie](container-insights-overview.md) , z wyjątkiem następujących funkcji:

- Dane dynamiczne
- OdPrometheusość metryk
- Zbieranie metryk
- Funkcja kondycji

## <a name="prerequisites"></a>Wymagania wstępne

- Aby włączyć i uzyskać dostęp do funkcji w Azure Monitor dla kontenerów, musisz być członkiem roli *współautor* platformy Azure w ramach subskrypcji platformy Azure i członkiem roli [*współautor Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) obszaru roboczego log Analytics skonfigurowanym przy użyciu Azure monitor dla kontenerów.

- Aby wyświetlić dane monitorowania, należy być członkiem uprawnienia roli [*czytelnik log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) z obszarem roboczym log Analytics skonfigurowanym za pomocą Azure monitor dla kontenerów.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Włącz dla nowego klastra przy użyciu szablonu Azure Resource Manager

Wykonaj następujące kroki, aby wdrożyć klaster Red Hat OpenShift platformy Azure z włączonym monitorowaniem. Przed kontynuowaniem zapoznaj się z samouczkiem [Tworzenie klastra usługi Azure Red Hat OpenShift](../../openshift/tutorial-create-cluster.md#prerequisites) , aby poznać zależności, które należy skonfigurować, aby środowisko zostało prawidłowo skonfigurowane.

Ta metoda obejmuje dwa szablony JSON. Jeden szablon określa konfigurację wdrożenia klastra z włączonym monitorowaniem, a druga zawiera wartości parametrów, które można skonfigurować, aby określić następujące elementy:

- Identyfikator zasobu klastra Red Hat OpenShift platformy Azure. 

- Grupa zasobów, w której jest wdrażany klaster.

- Zanotowano [Azure Active Directory identyfikator dzierżawy](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) po wykonaniu kroków w celu utworzenia jednego lub już utworzonego.

- [Azure Active Directory identyfikator aplikacji klienta](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) zanotowany po wykonaniu kroków w celu utworzenia jednego lub już utworzonego.

- [Azure Active Directory klucz tajny klienta](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) zanotowany po wykonaniu kroków tworzenia jednego lub już utworzonego.

- Zanotowano [grupę zabezpieczeń usługi Azure AD](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) po wykonaniu kroków w celu utworzenia jednej lub już utworzonej.

- Identyfikator zasobu istniejącego obszaru roboczego Log Analytics.

- Liczba węzłów głównych do utworzenia w klastrze.

- Liczba węzłów obliczeniowych w profilu puli agentów.

- Liczba węzłów infrastruktury w profilu puli agentów. 

Jeśli znasz koncepcji wdrażanie zasobów za pomocą szablonu, zobacz:

- [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/resource-group-template-deploy.md)

- [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Jeśli zdecydujesz się użyć wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie. Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Aby zidentyfikować wersję, uruchom `az --version`. Jeśli musisz zainstalować lub uaktualnić wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

Aby można było włączyć monitorowanie za pomocą Azure PowerShell lub interfejsu wiersza polecenia, należy utworzyć obszar roboczy Log Analytics. Aby utworzyć obszar roboczy, możesz skonfigurować go za pośrednictwem [usługi Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)za pośrednictwem [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), lub [witryny Azure portal](../../azure-monitor/learn/quick-create-workspace.md).

1. Pobierz i Zapisz w folderze lokalnym, Azure Resource Manager szablonu i pliku parametrów, aby utworzyć klaster z dodatkiem monitorowania przy użyciu następujących poleceń:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json` 

2. Logowanie do platformy Azure 

    ```azurecli
    az login    
    ```
    
    Jeśli masz dostęp do wielu subskrypcji, uruchom `az account set -s {subscription ID}` zastępowanie `{subscription ID}` subskrypcją, której chcesz użyć.
 
3. Utwórz grupę zasobów dla klastra, jeśli jeszcze jej nie masz. Aby zapoznać się z listą regionów świadczenia usługi Azure, które obsługują OpenShift na platformie Azure, zobacz [Obsługiwane regiony](../../openshift/supported-resources.md#azure-regions). 

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location> 
    ```

4. Edytuj plik parametrów JSON **newClusterWithMonitoringParam. JSON** i zaktualizuj następujące wartości:

    - *location*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret* 
    - *aadCustomerAdminGroupId* 
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. W poniższym kroku wdrożono klaster z włączonym monitorowaniem za pomocą interfejsu wiersza polecenia platformy Azure. 

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json 
    ```
 
    Dane wyjściowe są podobne do następujących:

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Włącz dla istniejącego klastra

Wykonaj następujące kroki, aby włączyć monitorowanie klastra Red Hat OpenShift platformy Azure wdrożonego na platformie Azure. Można to zrobić z poziomu Azure Portal lub przy użyciu podanych szablonów.

### <a name="from-the-azure-portal"></a>Z Azure Portal
 
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W menu Azure Portal lub na stronie głównej wybierz pozycję **Azure monitor**. W obszarze **Insights** zaznacz **kontenery**. 

3. Na **Monitor — kontenery** wybierz opcję **monitorowany bez klastrów**.

4. Na liście niemonitorowanych klastrów Znajdź klaster na liście i kliknij pozycję **Włącz**. Wyniki można zidentyfikować na liście, wyszukując wartość **ARO** w kolumnie **Typ klastra**.

5. Na **dołączania do usługi Azure Monitor dla kontenerów** strony, jeśli masz istniejące usługi Log Analytics obszaru roboczego w tej samej subskrypcji co klaster, wybierz ją z listy rozwijanej.  
    Na tej liście jest wybierany domyślny obszar roboczy i lokalizacja, w ramach której wdrożono klaster. 

    ![Włącz monitorowanie dla niemonitorowanych klastrów](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Jeśli chcesz utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z klastra, postępuj zgodnie z instrukcjami [Utwórz obszar roboczy usługi Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Upewnij się, że obszar roboczy jest tworzony w tej samej subskrypcji, w której wdrożono klaster RedHat OpenShift. 
 
Po włączeniu monitorowania może potrwać około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra. 

### <a name="enable-using-an-azure-resource-manager-template"></a>Włączanie przy użyciu szablonu Azure Resource Manager

Ta metoda obejmuje dwa szablony JSON. Jeden szablon Określa konfigurację, aby włączyć monitorowanie, a drugi zawiera wartości parametrów, które można skonfigurować w celu określ następujące ustawienia:

- Identyfikator zasobu klastra usługi Azure RedHat OpenShift. 

- Grupa zasobów, w której jest wdrażany klaster.

- Obszar roboczy usługi Log Analytics.

Jeśli znasz koncepcji wdrażanie zasobów za pomocą szablonu, zobacz:

- [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/resource-group-template-deploy.md)

- [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Jeśli zdecydujesz się użyć wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie. Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Aby zidentyfikować wersję, uruchom `az --version`. Jeśli musisz zainstalować lub uaktualnić wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

Aby można było włączyć monitorowanie za pomocą Azure PowerShell lub interfejsu wiersza polecenia, należy utworzyć obszar roboczy Log Analytics. Aby utworzyć obszar roboczy, możesz skonfigurować go za pośrednictwem [usługi Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)za pośrednictwem [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), lub [witryny Azure portal](../../azure-monitor/learn/quick-create-workspace.md).

1. Pobierz szablon i plik parametrów, aby zaktualizować klaster przy użyciu dodatku do monitorowania za pomocą następujących poleceń:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json` 

2. Logowanie do platformy Azure 

    ```azurecli
    az login    
    ```

    Jeśli masz dostęp do wielu subskrypcji, uruchom `az account set -s {subscription ID}` zastępowanie `{subscription ID}` subskrypcją, której chcesz użyć.

3. Określ subskrypcję klastra usługi Azure RedHat OpenShift.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Uruchom następujące polecenie, aby zidentyfikować lokalizację klastra i identyfikator zasobu:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName> 
    ```

5. Edytuj plik parametrów JSON **existingClusterParam. JSON** i zaktualizuj wartości *araResourceId* i *araResoruceLocation*. Wartość **workspaceResourceId** jest pełny identyfikator zasobu obszaru roboczego usługi Log Analytics, która zawiera nazwę obszaru roboczego. 

6. Aby przeprowadzić wdrożenie przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom następujące polecenia: 

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json 
    ```

    Dane wyjściowe są podobne do następujących:

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Kolejne kroki

Po włączeniu monitorowania w celu zbierania informacji o kondycji i użyciu zasobów klastra RedHat OpenShift oraz obciążeń na nich działających należy dowiedzieć się, [jak używać](container-insights-analyze.md) Azure monitor do kontenerów.