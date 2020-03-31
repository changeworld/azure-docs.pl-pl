---
title: Konfigurowanie klastrów OpenShift usługi Azure Red Hat za pomocą usługi Azure Monitor dla kontenerów | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób konfigurowania monitorowania klastra kubernetes za pomocą usługi Azure Monitor hostowanego na platformie Azure Red Hat OpenShift.
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: c2fd3568be2c51296bb1377e91031ebfb7ca6ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275518"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>Konfigurowanie klastrów openshift usługi Azure Red Hat za pomocą usługi Azure Monitor dla kontenerów

Usługa Azure Monitor dla kontenerów zapewnia zaawansowane środowisko monitorowania dla klastrów usługi Azure Kubernetes (AKS) i AKS Engine. W tym artykule opisano, jak włączyć monitorowanie klastrów kubernetes hostowanych na [platformie Azure Red Hat OpenShift,](../../openshift/intro-openshift.md) aby osiągnąć podobne środowisko monitorowania.

>[!NOTE]
>Obsługa platformy Azure Red Hat OpenShift jest funkcją w publicznej wersji zapoznawczej w tej chwili.
>

Usługa Azure Monitor dla kontenerów może być włączona dla nowych lub co najmniej jednego istniejącego wdrożenia usługi Azure Red Hat OpenShift przy użyciu następujących obsługiwanych metod:

- Dla istniejącego klastra z witryny Azure portal lub przy użyciu szablonu Usługi Azure Resource Manager.
- Dla nowego klastra przy użyciu szablonu Usługi Azure Resource Manager lub podczas tworzenia nowego klastra przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Obsługiwane i nieobsługiwały funkcje

Usługa Azure Monitor dla kontenerów obsługuje monitorowanie usługi Azure Red Hat OpenShift zgodnie z opisem w [artykule Omówienie,](container-insights-overview.md) z wyjątkiem następujących funkcji:

- Dane na żywo (wersja zapoznawcza)
- [Zbieranie metryk](container-insights-update-metrics.md) z węzłów klastra i zasobników i przechowywanie ich w bazie danych metryk usługi Azure Monitor

## <a name="prerequisites"></a>Wymagania wstępne

- Aby włączyć i uzyskać dostęp do funkcji w usłudze Azure Monitor dla kontenerów, co najmniej musisz być członkiem roli *współautora* platformy Azure w subskrypcji platformy Azure i członkiem roli [*współautora analizy dzienników*](../platform/manage-access.md#manage-access-using-azure-permissions) obszaru roboczego usługi Log Analytics skonfigurowanego za pomocą usługi Azure Monitor dla kontenerów.

- Aby wyświetlić dane monitorowania, jesteś członkiem uprawnienia roli [*czytnika usługi Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) z obszaru roboczego usługi Log Analytics skonfigurowany za pomocą usługi Azure Monitor dla kontenerów.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Włączanie nowego klastra przy użyciu szablonu usługi Azure Resource Manager

Wykonaj następujące kroki, aby wdrożyć klaster OpenShift usługi Azure Red Hat z włączoną funkcją monitorowania. Przed kontynuowaniem zapoznaj się z [samouczkiem Tworzenie klastra OpenShift usługi Azure Red Hat,](../../openshift/tutorial-create-cluster.md#prerequisites) aby zrozumieć zależności, które należy skonfigurować, aby twoje środowisko zostało poprawnie skonfigurowane.

Ta metoda zawiera dwa szablony JSON. Jeden szablon określa konfigurację do wdrożenia klastra z włączonym monitorowaniem, a drugi zawiera wartości parametrów, które można skonfigurować w celu określenia następujących wartości:

- Identyfikator zasobu klastra Azure Red Hat OpenShift.

- Grupa zasobów, w skład w skład tej grupy, wcenie klastra.

- [Identyfikator dzierżawy usługi Azure Active Directory odnotowany](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) po wykonaniu kroków w celu utworzenia jednego lub jednego już utworzonego.

- [Identyfikator aplikacji klienckiej usługi Azure Active Directory odnotowany](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) po wykonaniu kroków w celu utworzenia jednego lub jednego już utworzonego.

- [Klucz tajny klienta usługi Azure Active Directory odnotowany](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) po wykonaniu kroków w celu utworzenia jednego lub jednego już utworzonego.

- [Grupa zabezpieczeń usługi Azure AD](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) zauważyć po wykonaniu kroków, aby utworzyć jeden lub jeden już utworzony.

- Identyfikator zasobu istniejącego obszaru roboczego usługi Log Analytics.

- Liczba węzłów głównych do utworzenia w klastrze.

- Liczba węzłów obliczeniowych w profilu puli agenta.

- Liczba węzłów infrastruktury w profilu puli agenta.

Jeśli nie znasz pojęcia wdrażania zasobów przy użyciu szablonu, zobacz:

- [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)

- [Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Jeśli zdecydujesz się korzystać z interfejsu wiersza polecenia platformy Azure, najpierw należy zainstalować i używać interfejsu wiersza polecenia lokalnie. Musi być uruchomiony interfejsu wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Aby zidentyfikować swoją `az --version`wersję, uruchom polecenie . Jeśli chcesz zainstalować lub uaktualnić platformę Azure CLI, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

Obszar roboczy usługi Log Analytics musi zostać utworzony przed włączeniem monitorowania przy użyciu programu Azure PowerShell lub interfejsu wiersza polecenia. Aby utworzyć obszar roboczy, można go skonfigurować za pośrednictwem [usługi Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), za pośrednictwem programu [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)lub w [portalu Azure](../../azure-monitor/learn/quick-create-workspace.md).

1. Pobierz i zapisz w folderze lokalnym, szablonie i pliku parametrów usługi Azure Resource Manager, aby utworzyć klaster z dodatkiem monitorowania przy użyciu następujących poleceń:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Logowanie do platformy Azure

    ```azurecli
    az login    
    ```

    Jeśli masz dostęp do wielu `az account set -s {subscription ID}` subskrypcji, uruchom zastąpienie `{subscription ID}` subskrypcją, której chcesz użyć.

3. Utwórz grupę zasobów dla klastra, jeśli jeszcze jej nie masz. Aby uzyskać listę regionów platformy Azure obsługujących usługę OpenShift na platformie Azure, zobacz [Obsługiwane regiony](../../openshift/supported-resources.md#azure-regions).

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Edytuj plik parametru JSON **newClusterWithMonitoringParam.json** i zaktualizuj następujące wartości:

    - *Lokalizacji*
    - *nazwa klastra*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *obszar roboczyZdanie zasobów*
    - *masterNodeCount*
    - *computeNodeCount (Liczba obliczeń)*
    - *infraNodeCount (infraNodeCount)*

5. Poniższy krok wdraża klastra z monitorowaniem włączone przy użyciu interfejsu wiersza polecenia platformy Azure.

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    Dane wyjściowe są podobne do następujących:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Włączanie dla istniejącego klastra

Wykonaj następujące kroki, aby włączyć monitorowanie klastra Azure Red Hat OpenShift wdrożonego na platformie Azure. Można to zrobić z witryny Azure portal lub przy użyciu dostarczonych szablonów.

### <a name="from-the-azure-portal"></a>Z witryny Azure Portal

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

2. W menu portalu Azure lub na stronie głównej wybierz pozycję **Azure Monitor**. W sekcji **Insights** wybierz pozycję **Kontenery**.

3. Na stronie **Monitor — kontenery** wybierz pozycję **Klastry niemonitorowane**.

4. Na liście niemonitorowanych klastrów znajdź klaster na liście i kliknij przycisk **Włącz**. Wyniki można zidentyfikować na liście, wyszukując wartość **ARO** w kolumnie **TYP KLASTRA**.

5. Na stronie **Dołączanie do usługi Azure Monitor dla kontenerów,** jeśli masz istniejący obszar roboczy usługi Log Analytics w tej samej subskrypcji co klaster, wybierz go z listy rozwijanej.  
    Lista wstępnieselektuje domyślny obszar roboczy i lokalizację, w których klaster jest wdrażany w ramach subskrypcji.

    ![Włącz monitorowanie niemonitorowanych klastrów](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Jeśli chcesz utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z klastra, postępuj zgodnie z instrukcjami w [obszarze roboczym Tworzenie usługi Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Pamiętaj, aby utworzyć obszar roboczy w tej samej subskrypcji, w których wdrożono klaster RedHat OpenShift.

Po włączeniu monitorowania może upłynąć około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra.

### <a name="enable-using-an-azure-resource-manager-template"></a>Włączanie przy użyciu szablonu usługi Azure Resource Manager

Ta metoda zawiera dwa szablony JSON. Jeden szablon określa konfigurację włączania monitorowania, a drugi zawiera wartości parametrów skonfigurowane w celu określenia następujących wartości:

- Identyfikator zasobu klastra Azure RedHat OpenShift.

- Grupa zasobów, w skład w skład tej grupy, wcenie klastra.

- Obszar roboczy usługi Log Analytics.

Jeśli nie znasz pojęcia wdrażania zasobów przy użyciu szablonu, zobacz:

- [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)

- [Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Jeśli zdecydujesz się korzystać z interfejsu wiersza polecenia platformy Azure, najpierw należy zainstalować i używać interfejsu wiersza polecenia lokalnie. Musi być uruchomiony interfejsu wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Aby zidentyfikować swoją `az --version`wersję, uruchom polecenie . Jeśli chcesz zainstalować lub uaktualnić platformę Azure CLI, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

Obszar roboczy usługi Log Analytics musi zostać utworzony przed włączeniem monitorowania przy użyciu programu Azure PowerShell lub interfejsu wiersza polecenia. Aby utworzyć obszar roboczy, można go skonfigurować za pośrednictwem [usługi Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), za pośrednictwem programu [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)lub w [portalu Azure](../../azure-monitor/learn/quick-create-workspace.md).

1. Pobierz plik szablonu i parametrów, aby zaktualizować klaster za pomocą dodatku do monitorowania za pomocą następujących poleceń:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Logowanie do platformy Azure

    ```azurecli
    az login    
    ```

    Jeśli masz dostęp do wielu `az account set -s {subscription ID}` subskrypcji, uruchom zastąpienie `{subscription ID}` subskrypcją, której chcesz użyć.

3. Określ subskrypcję klastra OpenShift usługi Azure RedHat.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Uruchom następujące polecenie, aby zidentyfikować lokalizację klastra i identyfikator zasobu:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Edytuj plik parametru JSON **existingClusterParam.json** i zaktualizuj wartości *araResourceId* i *araResoruceLocation*. Wartość **workspaceResourceId** jest pełny identyfikator zasobu obszaru roboczego usługi Log Analytics, który zawiera nazwę obszaru roboczego.

6. Aby wdrożyć za pomocą interfejsu wiersza polecenia platformy Azure, uruchom następujące polecenia:

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    Dane wyjściowe są podobne do następujących:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Następne kroki

- Dzięki możliwości monitorowania włączonemu do zbierania kondycji i wykorzystania zasobów klastra RedHat OpenShift i uruchomionych na nich obciążeń dowiedz się, [jak używać](container-insights-analyze.md) usługi Azure Monitor dla kontenerów.

- Aby dowiedzieć się, jak zatrzymać monitorowanie klastra za pomocą usługi Azure Monitor dla kontenerów, zobacz [Jak zatrzymać monitorowanie klastra OpenShift red hat azure](container-insights-optout-openshift.md).
