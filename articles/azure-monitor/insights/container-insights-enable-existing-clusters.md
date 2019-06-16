---
title: Monitorowanie klastra usługi Azure Kubernetes Service (AKS) wdrożone | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć monitorowanie klastra usługi Azure Kubernetes Service (AKS) z usługą Azure Monitor dla kontenerów już wdrożonych w ramach subskrypcji.
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
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: ae340fb11d422b7516cc315e78be974d22239503
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074683"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Aby włączyć monitorowanie klastra Azure Kubernetes Service (AKS) już wdrożony

W tym artykule opisano sposób konfigurowania usługi Azure Monitor dla kontenerów do monitorowania zarządzanych klastrów Kubernetes w serwisie [usługi Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) które już zostały wdrożone w ramach subskrypcji.

Aby umożliwić monitorowanie klastra usługi AKS, który jest już wdrożony przy użyciu jednej z obsługiwanych metod:

* Interfejs wiersza polecenia platformy Azure
* Terraform
* [Z usługi Azure Monitor](#enable-from-azure-monitor-in-the-portal) lub [bezpośrednio z klastrem AKS](#enable-directly-from-aks-cluster-in-the-portal) w witrynie Azure portal 
* Za pomocą [podany szablon usługi Azure Resource Manager](#enable-using-an-azure-resource-manager-template) za pomocą polecenia cmdlet programu Azure PowerShell `New-AzResourceGroupDeployment` lub z wiersza polecenia platformy Azure. 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

## <a name="enable-using-azure-cli"></a>Włącz przy użyciu wiersza polecenia platformy Azure

Następny krok umożliwia monitorowanie klastra usługi AKS przy użyciu wiersza polecenia platformy Azure. W tym przykładzie nie należy utworzyć na lub określić istniejący obszar roboczy. To polecenie upraszcza ten proces dla Ciebie przez utworzenie domyślnego obszaru roboczego w domyślnej grupie zasobów subskrypcji klastra AKS, jeśli jeden nie istnieje w tym regionie.  Domyślny obszar roboczy utworzony podobny format *DefaultWorkspace -\<GUID >-\<Region >* .  

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

## <a name="enable-using-terraform"></a>Włącz przy użyciu programu Terraform

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

## <a name="enable-from-azure-monitor-in-the-portal"></a>Włącz z usługi Azure Monitor w portalu 

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

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Włącz bezpośrednio z poziomu klastra AKS w portalu

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

## <a name="enable-using-an-azure-resource-manager-template"></a>Włącz przy użyciu szablonu usługi Azure Resource Manager

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

### <a name="create-and-execute-a-template"></a>Tworzenie i wykonywanie szablonu

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
