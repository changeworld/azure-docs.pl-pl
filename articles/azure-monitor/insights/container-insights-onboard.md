---
title: Jak dodać usługi Azure Monitor dla kontenerów | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób możesz dołączyć i konfigurowanie usługi Azure Monitor dla kontenerów, dzięki czemu można zrozumieć, jaka jest wydajność kontenera oraz zostały zidentyfikowane problemów związanych z wydajnością.
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
ms.date: 03/27/2019
ms.author: magoedte
ms.openlocfilehash: b5ba9a0abe8ec0f72cfaf42c747616e733fb3f32
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101158"
---
# <a name="how-to-onboard-azure-monitor-for-containers"></a>Jak dodać usługi Azure Monitor dla kontenerów  

W tym artykule opisano sposób konfigurowania usługi Azure Monitor dla kontenerów w celu monitorowania wydajności obciążeń, które są wdrażane do środowisk Kubernetes i w serwisie [usługi Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/).

Usługa Azure Monitor dla kontenerów można włączyć dla nowych lub obsługiwane przez jedną lub więcej istniejących wdrożeń usługi AKS przy użyciu następujących metod:

* W witrynie Azure portal, programu Azure PowerShell lub za pomocą wiersza polecenia platformy Azure
* Za pomocą [narzędzia Terraform i AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne 
Przed rozpoczęciem upewnij się, że dysponujesz następującymi elementami:

- **Obszar roboczy usługi Log Analytics.** Włącz monitorowanie dla nowego klastra AKS lub pozwolić proces dołączania, Utwórz domyślny obszar roboczy w domyślnej grupie zasobów subskrypcji klastra AKS możesz go utworzyć. Jeśli chcesz utworzyć samodzielnie, możesz je utworzyć za pomocą [usługi Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)za pośrednictwem [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), lub [witryny Azure portal](../../azure-monitor/learn/quick-create-workspace.md).
- Jesteś członkiem **roli Współautor usługi Log Analytics** umożliwiające monitorowanie kontenerów. Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego usługi Log Analytics, zobacz [możesz zarządzać obszarami roboczymi](../../azure-monitor/platform/manage-access.md).
- Jesteś członkiem **[właściciela](../../role-based-access-control/built-in-roles.md#owner)** roli dla zasobu klastra usługi AKS. 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Składniki 

Możliwość monitorowania wydajności zależy od konteneryzowanych agenta usługi Log Analytics dla opracowane specjalnie dla usługi Azure Monitor, dla kontenerów systemu Linux. To wyspecjalizowane agenta służy do zbierania danych dotyczących zdarzeń i wydajności ze wszystkich węzłów w klastrze, a agent automatycznie wdrożeniu i zarejestrowaniu z określonym obszarem roboczym usługi Log Analytics podczas wdrażania. Wersja agenta jest microsoft / oms:ciprod04202018 lub nowszej, a jest reprezentowane przez datę w następującym formacie: *mmddyyyy*. 

Po wydaniu nowej wersji agenta jest on automatycznie uaktualniany na zarządzanych klastrów Kubernetes hostowanych na platformie Azure Kubernetes Service (AKS). Aby skorzystać z wersji wydanych, zobacz [anonse wersji agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). 

>[!NOTE] 
>Jeśli masz już wdrożone w klastrze AKS, Włącz monitorowanie przy użyciu wiersza polecenia platformy Azure lub podanego szablonu Azure Resource Manager, jak pokazano w dalszej części tego artykułu. Nie można użyć `kubectl` do uaktualnienia, Usuń, Wdróż ponownie lub wdrożyć agenta. Szablon musi zostać wdrożony w tej samej grupie zasobów co klaster.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal
Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

## <a name="enable-monitoring-for-a-new-cluster"></a>Włącz monitorowanie dla nowego klastra
Podczas wdrażania możesz włączyć monitorowanie nowy klaster AKS w witrynie Azure portal przy użyciu wiersza polecenia platformy Azure lub przy użyciu programu Terraform.  Aby włączyć z poziomu portalu, wykonaj kroki opisane w artykule przewodnika Szybki Start [wdrażanie klastra usługi Azure Kubernetes Service (AKS)](../../aks/kubernetes-walkthrough-portal.md) i postępuj zgodnie z instrukcjami w sekcji **monitorowanie kondycji i dzienniki**.  

>[!NOTE]
>Gdy czynności opisane w artykule przewodnika Szybki Start, aby włączyć monitorowanie klastra usługi AKS z poziomu portalu, monit wybierz istniejący obszar roboczy usługi Log Analytics lub Utwórz nową. 

### <a name="enable-using-azure-cli"></a>Włącz przy użyciu wiersza polecenia platformy Azure
Aby włączyć monitorowanie nowy klaster AKS utworzone przy użyciu wiersza polecenia platformy Azure, przejdź do kroku w artykule przewodnika Szybki Start w sekcji [klastra AKS tworzenie](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Jeśli zdecydujesz się użyć wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie. Wiersza polecenia platformy Azure w wersji 2.0.59 musi być uruchomiona lub nowszej. Aby zidentyfikować wersję, uruchom `az --version`. Jeśli musisz zainstalować lub uaktualnić wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 
>

### <a name="enable-using-terraform"></a>Włącz przy użyciu programu Terraform
Jeśli jesteś [Wdrażanie nowego klastra AKS przy użyciu programu Terraform](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), zostaną określone argumenty, wymagane do profilu [utworzyć obszar roboczy usługi Log Analytics](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) Jeśli nie chcesz określić istniejącą grupę. 

>[!NOTE]
>Jeśli zdecydujesz się za pomocą narzędzia Terraform, musisz używać dostawcy Menedżera zasobów Azure Terraform wersji 1.17.0 lub nowszej.

Aby dodać usługi Azure Monitor dla kontenerów do obszaru roboczego, zobacz [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) i wypełnij profil umieszczając [ **addon_profile** ](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) i określ **oms_agent**. 

Po włączeniu monitorowania i wszystkie zadania konfiguracji zostaną ukończone pomyślnie, można monitorować wydajność klastra w jeden z dwóch sposobów:

* Bezpośrednio w klastrze AKS, wybierając **kondycji** w okienku po lewej stronie.
* Wybierając **szczegółowe informacje o kontenerze Monitor** kafelka na stronie klastra AKS dla wybranego klastra. W usłudze Azure Monitor w okienku po lewej stronie wybierz **kondycji**. 

  ![Opcje dotyczące wybierania usługi Azure Monitor dla kontenerów w usłudze AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Po włączeniu monitorowania może potrwać około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra. 

## <a name="enable-monitoring-for-existing-managed-clusters"></a>Włącz monitorowanie dla istniejących zarządzane klastry
Aby umożliwić monitorowanie klastra usługi AKS, który jest już wdrożony przy użyciu jednej z obsługiwanych metod:

* Interfejs wiersza polecenia platformy Azure
* Terraform
* [Z usługi Azure Monitor](#enable-from-azure-monitor-in-the-portal) lub [bezpośrednio z klastrem AKS](#enable-directly-from-aks-cluster-in-the-portal) w witrynie Azure portal 
* Za pomocą [podany szablon usługi Azure Resource Manager](#enable-using-an-azure-resource-manager-template) za pomocą polecenia cmdlet programu Azure PowerShell `New-AzResourceGroupDeployment` lub z wiersza polecenia platformy Azure. 

### <a name="enable-using-azure-cli"></a>Włącz przy użyciu wiersza polecenia platformy Azure
Następny krok umożliwia monitorowanie klastra usługi AKS przy użyciu wiersza polecenia platformy Azure. W tym przykładzie nie należy utworzyć na lub określić istniejący obszar roboczy. To polecenie upraszcza ten proces dla Ciebie przez utworzenie domyślnego obszaru roboczego w domyślnej grupie zasobów subskrypcji klastra AKS, jeśli jeden nie istnieje w tym regionie.  Domyślny obszar roboczy utworzony podobny format *DefaultWorkspace -\<GUID >-\<Region >*.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

Dane wyjściowe będą wyglądać w następujący sposób:

```azurecli
provisioningState       : Succeeded
```

Jeśli czy raczej integrują się z istniejącym obszarem roboczym, użyj następującego polecenia, aby określić obszaru roboczego.

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG --workspace-resource-id <ExistingWorkspaceResourceID> 
```

Dane wyjściowe będą wyglądać w następujący sposób:

```azurecli
provisioningState       : Succeeded
```

### <a name="enable-using-terraform"></a>Włącz przy użyciu programu Terraform
1. Dodaj **oms_agent** dodatek profilu do istniejących [azurerm_kubernetes_cluster zasobów](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Dodaj [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) zgodnie z krokami w dokumentacji programu Terraform.

### <a name="enable-from-azure-monitor-in-the-portal"></a>Włącz z usługi Azure Monitor w portalu 
Aby włączyć monitorowanie klastra usługi AKS w witrynie Azure portal z usługi Azure Monitor, wykonaj następujące czynności:

1. W witrynie Azure portal wybierz **Monitor**. 
2. Wybierz **kontenery** z listy.
3. Na **Monitor — kontenery** wybierz opcję **monitorowany bez klastrów**.
4. Z listy niemonitorowanych klastrów, Znajdź na liście kontenera, a następnie kliknij przycisk **Włącz**.   
5. Na **dołączania do usługi Azure Monitor dla kontenerów** strony, jeśli masz istniejące usługi Log Analytics obszaru roboczego w tej samej subskrypcji co klaster, wybierz ją z listy rozwijanej.  
    Lista preselects domyślnego obszaru roboczego i lokalizacji, który kontenerów AKS jest wdrażany w ramach subskrypcji. 

    ![Włączanie monitorowania szczegółowych danych dotyczących kontenera usługi AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Jeśli chcesz utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z klastra, postępuj zgodnie z instrukcjami [Utwórz obszar roboczy usługi Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Pamiętaj utworzyć obszar roboczy w tej samej subskrypcji, który kontenerów AKS jest wdrażany. 
 
Po włączeniu monitorowania może potrwać około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra. 

### <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Włącz bezpośrednio z poziomu klastra AKS w portalu
Aby włączyć monitorowanie bezpośrednio z jednego klastra usługi AKS w witrynie Azure portal, wykonaj następujące czynności:

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**. 
2. Na liście zasobów wpisz **kontenery**.  
    Listy jest filtrowana w oparciu o wpisywane dane. 
3. Wybierz **usługi Kubernetes**.  

    ![Link usługi Kubernetes](./media/container-insights-onboard/portal-search-containers-01.png)

4. Na liście kontenerów Wybierz kontener.
5. Na stronie Przegląd kontenera wybierz **monitorowanie kontenerów**.  
6. Na **dołączania do usługi Azure Monitor dla kontenerów** strony, jeśli masz istniejące usługi Log Analytics obszaru roboczego w tej samej subskrypcji co klaster, wybierz go na liście rozwijanej.  
    Lista preselects domyślnego obszaru roboczego i lokalizacji, który kontenerów AKS jest wdrażany w ramach subskrypcji. 

    ![Włącz monitorowanie kondycji kontenera w usłudze AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Jeśli chcesz utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z klastra, postępuj zgodnie z instrukcjami [Utwórz obszar roboczy usługi Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Pamiętaj utworzyć obszar roboczy w tej samej subskrypcji, który kontenerów AKS jest wdrażany. 
 
Po włączeniu monitorowania może potrwać około 15 minut, zanim będzie można wyświetlić danych operacyjnych dla klastra. 

### <a name="enable-using-an-azure-resource-manager-template"></a>Włącz przy użyciu szablonu usługi Azure Resource Manager
Ta metoda obejmuje dwa szablony JSON. Jeden szablon Określa konfigurację, aby włączyć monitorowanie, a drugi zawiera wartości parametrów, które można skonfigurować w celu określ następujące ustawienia:

* Identyfikator zasobu kontenera usługi AKS. 
* Grupa zasobów, który jest wdrażany klaster w.

>[!NOTE]
>Szablon musi zostać wdrożony w tej samej grupie zasobów co klaster.
>

Obszaru roboczego usługi Log Analytics ma zostać utworzona przed zostanie włączone monitorowanie za pomocą programu Azure PowerShell lub interfejsu wiersza polecenia. Aby utworzyć obszar roboczy, możesz skonfigurować go za pośrednictwem [usługi Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)za pośrednictwem [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), lub [witryny Azure portal](../../azure-monitor/learn/quick-create-workspace.md).

Jeśli znasz koncepcji wdrażanie zasobów za pomocą szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/resource-group-template-deploy.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Jeśli zdecydujesz się użyć wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie. Wiersza polecenia platformy Azure w wersji 2.0.59 musi być uruchomiona lub nowszej. Aby zidentyfikować wersję, uruchom `az --version`. Jeśli musisz zainstalować lub uaktualnić wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Tworzenie i wykonywanie szablonu

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
       }
    }
    },
    "resources": [
      {
    "name": "[split(parameters('aksResourceId'),'/')[8]]",
    "type": "Microsoft.ContainerService/managedClusters",
    "location": "[parameters('aksResourceLocation')]",
    "apiVersion": "2018-03-31",
    "properties": {
      "mode": "Incremental",
      "id": "[parameters('aksResourceId')]",
      "addonProfiles": {
        "omsagent": {
          "enabled": true,
          "config": {
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     }
     ]
    }
    ```

2. Zapisz ten plik jako **existingClusterOnboarding.json** do folderu lokalnego.
3. Wklej następującą składnię JSON do pliku:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "<aksClusterLocation>"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       }  
     }
    }
    ```

4. Edytuj wartości **aksResourceId** i **aksResourceLocation** przy użyciu wartości na **Omówienie usługi AKS** strony dla klastra usługi AKS. Wartość **workspaceResourceId** jest pełny identyfikator zasobu obszaru roboczego usługi Log Analytics, która zawiera nazwę obszaru roboczego. 
5. Zapisz ten plik jako **existingClusterParam.json** do folderu lokalnego.
6. Wszystko jest teraz gotowe do wdrożenia tego szablonu. 

   * Aby wdrożyć przy użyciu programu Azure PowerShell, użyj następujących poleceń w folderze, który zawiera szablon:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```
       Zmiana konfiguracji może potrwać kilka minut. Po jej zakończeniu zostanie wyświetlony komunikat podobny do poniższego, która zawiera wynik:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Aby wdrożyć przy użyciu wiersza polecenia platformy Azure, uruchom następujące polecenia:
    
       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       Zmiana konfiguracji może potrwać kilka minut. Po jej zakończeniu zostanie wyświetlony komunikat podobny do poniższego, która zawiera wynik:

       ```azurecli
       provisioningState       : Succeeded
       ```
     Po włączeniu monitorowania może potrwać około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra. 

## <a name="verify-agent-and-solution-deployment"></a>Sprawdź wdrożenie agenta i rozwiązania
Za pomocą wersji agenta *06072018* lub później, sprawdź, czy agent i rozwiązania zostały pomyślnie wdrożone. W starszych wersjach programu agent można sprawdzić tylko na wdrożenie agenta.

### <a name="agent-version-06072018-or-later"></a>Wersja agenta 06072018 lub nowszy
Uruchom następujące polecenie, aby sprawdzić, czy agent zostanie wdrożony pomyślnie. 

```
kubectl get ds omsagent --namespace=kube-system
```

Dane wyjściowe powinien przypominać poniższe polecenie, co oznacza, że została prawidłowo wdrożona:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Aby zweryfikować wdrożenie rozwiązania, uruchom następujące polecenie:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Dane wyjściowe powinien przypominać poniższe polecenie, co oznacza, że została prawidłowo wdrożona:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Agent w wersji wcześniejszej niż 06072018

Aby sprawdzić, czy wersja agenta usługi Log Analytics wydanych przed *06072018* jest wdrażany prawidłowo, uruchom następujące polecenie:  

```
kubectl get ds omsagent --namespace=kube-system
```

Dane wyjściowe powinien przypominać poniższe polecenie, co oznacza, że została prawidłowo wdrożona:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Wyświetl konfigurację przy użyciu interfejsu wiersza polecenia
Użyj `aks show` polecenia w celu uzyskania szczegółowych informacji takich, w jakim są rozwiązania włączony, czy nie, co to jest identyfikator zasobu obszaru roboczego usługi Log Analytics i podsumowanie szczegółowych informacji o klastrze.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Po kilku minutach polecenie kończy i zwraca sformatowanego JSON informacji na temat rozwiązania.  Wyniki polecenia powinien być wyświetlony profilu monitorowania dodatku i podobne do następujących przykładowych danych wyjściowych:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```
## <a name="next-steps"></a>Kolejne kroki

* Jeśli występują problemy podczas próby dołączyć rozwiązanie, zapoznaj się z [przewodnik rozwiązywania problemów](container-insights-troubleshoot.md)

* Przy użyciu funkcji monitorowania, włączyć przechwytywania metryk kondycji dla węzłów klastra usługi AKS i zasobników, te metryki kondycji są dostępne w witrynie Azure portal. Aby dowiedzieć się, jak używać usługi Azure Monitor dla kontenerów, zobacz [widok usługi Azure Kubernetes Service health](container-insights-analyze.md).
