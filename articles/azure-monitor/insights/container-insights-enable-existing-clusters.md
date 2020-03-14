---
title: Monitoruj wdrożony klaster usługi Azure Kubernetes Service (AKS) | Microsoft Docs
description: Dowiedz się, jak włączyć monitorowanie klastra usługi Azure Kubernetes Service (AKS) za pomocą Azure Monitor dla kontenerów już wdrożonych w ramach subskrypcji.
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 8589ea71b5c7affadc61d5e4543f734a660ab543
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275453"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Włącz monitorowanie już wdrożonego klastra usługi Azure Kubernetes Service (AKS)

W tym artykule opisano sposób konfigurowania Azure Monitor kontenerów do monitorowania zarządzanego klastra Kubernetes hostowanego w [usłudze Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) , która została już wdrożona w ramach subskrypcji.

Można włączyć monitorowanie klastra AKS, który jest już wdrożony przy użyciu jednej z obsługiwanych metod:

* Interfejs wiersza polecenia platformy Azure
* Terraform
* [Z Azure monitor](#enable-from-azure-monitor-in-the-portal) lub [bezpośrednio z klastra AKS](#enable-directly-from-aks-cluster-in-the-portal) w Azure Portal
* Za pomocą [podanego szablonu Azure Resource Manager](#enable-using-an-azure-resource-manager-template) przy użyciu polecenia cmdlet Azure PowerShell `New-AzResourceGroupDeployment` lub z interfejsem wiersza polecenia platformy Azure.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Azure portal](https://portal.azure.com).

## <a name="enable-using-azure-cli"></a>Włącz przy użyciu wiersza polecenia platformy Azure

Następny krok umożliwia monitorowanie klastra usługi AKS przy użyciu wiersza polecenia platformy Azure. W tym przykładzie nie należy utworzyć na lub określić istniejący obszar roboczy. To polecenie upraszcza ten proces dla Ciebie przez utworzenie domyślnego obszaru roboczego w domyślnej grupie zasobów subskrypcji klastra AKS, jeśli jeden nie istnieje w tym regionie.  Utworzony domyślny obszar roboczy przypomina format *DefaultWorkspace\<identyfikator GUID >-\<regionu >* .  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

Dane wyjściowe będą wyglądać w następujący sposób:

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Integracja z istniejącym obszarem roboczym

Jeśli chcesz zintegrować z istniejącym obszarem roboczym, wykonaj następujące kroki, aby najpierw zidentyfikować pełny identyfikator zasobu Log Analytics obszaru roboczego, który jest wymagany dla parametru `--workspace-resource-id`, a następnie uruchom polecenie, aby włączyć dodatek monitorowania względem określonego obszaru roboczego.  

1. Wyświetl listę wszystkich subskrypcji, do których masz dostęp, za pomocą następującego polecenia:

    ```azurecli
    az account list --all -o table
    ```

    Dane wyjściowe będą wyglądać w następujący sposób:

    ```output
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Skopiuj wartość identyfikatora **subskrypcji**.

2. Przejdź do subskrypcji, w której znajduje się obszar roboczy Log Analytics, przy użyciu następującego polecenia:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Poniższy przykład wyświetla listę obszarów roboczych w Twoich subskrypcjach w domyślnym formacie JSON.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    W danych wyjściowych Znajdź nazwę obszaru roboczego, a następnie skopiuj pełny identyfikator zasobu tego Log Analytics obszaru roboczego pod **identyfikatorem**pola.

4. Uruchom następujące polecenie, aby włączyć dodatek monitorowania, zastępując wartość parametru `--workspace-resource-id`. Wartość ciągu musi znajdować się w podwójnych cudzysłowach:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    Dane wyjściowe będą wyglądać w następujący sposób:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Włącz przy użyciu programu Terraform

1. Dodawanie profilu dodatku **oms_agent** do istniejącego [zasobu azurerm_kubernetes_cluster](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Dodaj [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) , wykonując czynności opisane w dokumentacji Terraform.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Włączanie z Azure Monitor w portalu

Aby włączyć monitorowanie klastra usługi AKS w witrynie Azure portal z usługi Azure Monitor, wykonaj następujące czynności:

1. W Azure Portal wybierz pozycję **Monitoruj**.

2. Z listy wybierz **kontenery** .

3. Na stronie **monitorowanie kontenerów** wybierz pozycję **Niemonitorowane klastry**.

4. Na liście niemonitorowanych klastrów Znajdź kontener na liście i kliknij pozycję **Włącz**.   

5. Na stronie Dołączanie **do Azure monitor dla kontenerów** Jeśli masz istniejący obszar roboczy log Analytics w tej samej subskrypcji co klaster, wybierz go z listy rozwijanej.  
    Lista preselects domyślnego obszaru roboczego i lokalizacji, który kontenerów AKS jest wdrażany w ramach subskrypcji.

    ![Włączanie monitorowania szczegółowych danych dotyczących kontenera usługi AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Jeśli chcesz utworzyć nowy obszar roboczy Log Analytics do przechowywania danych monitorowania z klastra, postępuj zgodnie z instrukcjami w temacie [tworzenie log Analytics obszaru roboczego](../../azure-monitor/learn/quick-create-workspace.md). Pamiętaj utworzyć obszar roboczy w tej samej subskrypcji, który kontenerów AKS jest wdrażany.

Po włączeniu monitorowania może potrwać około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra.

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Włącz bezpośrednio z klastra AKS w portalu

Aby włączyć monitorowanie bezpośrednio z jednego z klastrów AKS w Azure Portal, wykonaj następujące czynności:

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**.

2. Na liście zasobów Rozpocznij wpisywanie **kontenerów**.  Listy jest filtrowana w oparciu o wpisywane dane.

3. Wybierz pozycję **Kubernetes Services**.  

    ![Link usługi Kubernetes](./media/container-insights-onboard/portal-search-containers-01.png)

4. Na liście kontenerów Wybierz kontener.

5. Na stronie Przegląd kontenera wybierz pozycję **Monitoruj kontenery**.  

6. Na stronie Dołączanie **do Azure monitor dla kontenerów** Jeśli masz istniejący obszar roboczy log Analytics w tej samej subskrypcji co klaster, wybierz go na liście rozwijanej.  
    Lista preselects domyślnego obszaru roboczego i lokalizacji, który kontenerów AKS jest wdrażany w ramach subskrypcji.

    ![Włącz monitorowanie kondycji kontenera w usłudze AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Jeśli chcesz utworzyć nowy obszar roboczy Log Analytics do przechowywania danych monitorowania z klastra, postępuj zgodnie z instrukcjami w temacie [tworzenie log Analytics obszaru roboczego](../../azure-monitor/learn/quick-create-workspace.md). Pamiętaj utworzyć obszar roboczy w tej samej subskrypcji, który kontenerów AKS jest wdrażany.

Po włączeniu monitorowania może potrwać około 15 minut, zanim będzie można wyświetlić danych operacyjnych dla klastra.

## <a name="enable-using-an-azure-resource-manager-template"></a>Włączanie przy użyciu szablonu Azure Resource Manager

Ta metoda obejmuje dwa szablony JSON. Jeden szablon Określa konfigurację, aby włączyć monitorowanie, a drugi zawiera wartości parametrów, które można skonfigurować w celu określ następujące ustawienia:

* Identyfikator zasobu kontenera usługi AKS.
* Grupa zasobów, który jest wdrażany klaster w.

>[!NOTE]
>Szablon musi zostać wdrożony w tej samej grupie zasobów co klaster.
>

Aby można było włączyć monitorowanie za pomocą Azure PowerShell lub interfejsu wiersza polecenia, należy utworzyć obszar roboczy Log Analytics. Aby utworzyć obszar roboczy, można go skonfigurować za pomocą [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), za pomocą [programu PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)lub [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).

Jeśli znasz koncepcji wdrażanie zasobów za pomocą szablonu, zobacz:

* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)

* [Wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Jeśli zdecydujesz się użyć wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie. Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Aby zidentyfikować swoją wersję, uruchom `az --version`. Jeśli konieczne jest zainstalowanie lub uaktualnienie interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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
        "aksResourceTagValues": {
          "type": "object",
          "metadata": {
            "description": "Existing all tags on AKS Cluster Resource"
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
          "tags": "[parameters('aksResourceTagValues')]",
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

2. Zapisz ten plik jako **existingClusterOnboarding. JSON** w folderze lokalnym.

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
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Edytuj wartości dla **aksResourceId** i **aksResourceLocation** przy użyciu wartości na stronie **Przegląd AKS** dla klastra AKS. Wartość **workspaceResourceId** to pełny identyfikator zasobu obszaru roboczego log Analytics, który obejmuje nazwę obszaru roboczego.

    Edytuj wartości dla **aksResourceTagValues** , aby pasowały do istniejących wartości tagów określonych dla klastra AKS.

5. Zapisz ten plik jako **existingClusterParam. JSON** w folderze lokalnym.

6. Wszystko jest teraz gotowe do wdrożenia tego szablonu.

   * Aby wdrożyć program przy użyciu Azure PowerShell, użyj następujących poleceń w folderze zawierającym szablon:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       Zmiana konfiguracji może potrwać kilka minut. Po jej zakończeniu zostanie wyświetlony komunikat podobny do poniższego, która zawiera wynik:

       ```output
       provisioningState       : Succeeded
       ```

   * Aby przeprowadzić wdrożenie przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom następujące polecenia:

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       Zmiana konfiguracji może potrwać kilka minut. Po jej zakończeniu zostanie wyświetlony komunikat podobny do poniższego, która zawiera wynik:

       ```output
       provisioningState       : Succeeded
       ```

       Po włączeniu monitorowania może potrwać około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra.

## <a name="verify-agent-and-solution-deployment"></a>Sprawdź wdrożenie agenta i rozwiązania

W przypadku agenta w wersji *06072018* lub nowszej można sprawdzić, czy zarówno Agent, jak i rozwiązanie zostały pomyślnie wdrożone. W starszych wersjach programu agent można sprawdzić tylko na wdrożenie agenta.

### <a name="agent-version-06072018-or-later"></a>Wersja agenta 06072018 lub nowszy

Uruchom następujące polecenie, aby sprawdzić, czy agent zostanie wdrożony pomyślnie.

```
kubectl get ds omsagent --namespace=kube-system
```

Dane wyjściowe powinien przypominać poniższe polecenie, co oznacza, że została prawidłowo wdrożona:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Aby zweryfikować wdrożenie rozwiązania, uruchom następujące polecenie:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Dane wyjściowe powinien przypominać poniższe polecenie, co oznacza, że została prawidłowo wdrożona:

```output
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Agent w wersji wcześniejszej niż 06072018

Aby sprawdzić, czy wersja agenta Log Analytics wydana przed *06072018* zostanie wdrożona prawidłowo, uruchom następujące polecenie:  

```
kubectl get ds omsagent --namespace=kube-system
```

Dane wyjściowe powinien przypominać poniższe polecenie, co oznacza, że została prawidłowo wdrożona:  

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Wyświetl konfigurację przy użyciu interfejsu wiersza polecenia

Użyj `aks show` polecenia, aby uzyskać szczegółowe informacje, takie jak rozwiązanie włączone lub nie, co to jest identyfikator zasobu Log Analytics obszaru roboczego oraz szczegóły podsumowania klastra.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Po kilku minutach polecenie kończy i zwraca sformatowanego JSON informacji na temat rozwiązania.  Wyniki polecenia powinien być wyświetlony profilu monitorowania dodatku i podobne do następujących przykładowych danych wyjściowych:

```output
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Następne kroki

* Jeśli wystąpią problemy podczas próby dołączenia rozwiązania, zapoznaj się z [przewodnikiem rozwiązywania problemów](container-insights-troubleshoot.md)

* Po włączeniu monitorowania w celu zbierania danych o kondycji i użyciu zasobów klastra AKS oraz obciążeń na nich uruchomionych należy dowiedzieć się, [jak używać](container-insights-analyze.md) Azure monitor do kontenerów.
