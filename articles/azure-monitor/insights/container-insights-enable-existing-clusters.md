---
title: Monitorowanie wdrożonego klastra usługi Azure Kubernetes (AKS) | Dokumenty firmy Microsoft
description: Dowiedz się, jak włączyć monitorowanie klastra usługi Azure Kubernetes (AKS) za pomocą usługi Azure Monitor dla kontenerów już wdrożonych w ramach subskrypcji.
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 8589ea71b5c7affadc61d5e4543f734a660ab543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275453"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Włącz monitorowanie już wdrożonego klastra usługi Azure Kubernetes (AKS)

W tym artykule opisano sposób konfigurowania usługi Azure Monitor dla kontenerów w celu monitorowania zarządzanego klastra kubernetes hostowanego w [usłudze Azure Kubernetes,](https://docs.microsoft.com/azure/aks/) które zostały już wdrożone w ramach subskrypcji.

Można włączyć monitorowanie klastra AKS, który jest już wdrożony przy użyciu jednej z obsługiwanych metod:

* Interfejs wiersza polecenia platformy Azure
* Terraform
* [Z usługi Azure Monitor](#enable-from-azure-monitor-in-the-portal) lub [bezpośrednio z klastra AKS](#enable-directly-from-aks-cluster-in-the-portal) w witrynie Azure portal
* Za [pomocą szablonu usługi Azure Resource Manager](#enable-using-an-azure-resource-manager-template) przy `New-AzResourceGroupDeployment` użyciu polecenia cmdlet programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com).

## <a name="enable-using-azure-cli"></a>Włącz korzystanie z interfejsu wiersza polecenia platformy Azure

Poniższy krok umożliwia monitorowanie klastra AKS przy użyciu interfejsu wiersza polecenia platformy Azure. W tym przykładzie nie są wymagane do tworzenia lub określania istniejącego obszaru roboczego. To polecenie upraszcza proces, tworząc domyślny obszar roboczy w domyślnej grupie zasobów subskrypcji klastra AKS, jeśli nie istnieje jeszcze w regionie.  Utworzony domyślny obszar roboczy przypomina format *DefaultWorkspace\<-\<GUID>- Region>*.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

Dane wyjściowe będą podobne do następujących:

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Integracja z istniejącym obszarem roboczym

Jeśli wolisz zintegrować z istniejącym obszarem roboczym, wykonaj następujące kroki, aby najpierw zidentyfikować `--workspace-resource-id` pełny identyfikator zasobu obszaru roboczego usługi Log Analytics wymaganego dla parametru, a następnie uruchom polecenie, aby włączyć dodatek monitorowania względem określonego obszaru roboczego.  

1. Wyświetl listę wszystkich subskrypcji, do których masz dostęp, używając następującego polecenia:

    ```azurecli
    az account list --all -o table
    ```

    Dane wyjściowe będą podobne do następujących:

    ```output
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Skopiuj wartość **identyfikatora subscriptionid**.

2. Przełącz się do subskrypcji obsługującej obszar roboczy usługi Log Analytics za pomocą następującego polecenia:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. W poniższym przykładzie jest wyświetlana lista obszarów roboczych w subskrypcjach w domyślnym formacie JSON.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    W danych wyjściowych znajdź nazwę obszaru roboczego, a następnie skopiuj pełny identyfikator zasobu tego obszaru roboczego usługi Log Analytics pod **identyfikatorem**pola .

4. Uruchom następujące polecenie, aby włączyć dodatek monitorowania, zastępując `--workspace-resource-id` wartość parametru. Wartość ciągu musi znajdować się w cudzysłowie:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    Dane wyjściowe będą podobne do następujących:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Włącz korzystanie z terraform

1. Dodawanie **oms_agent** profilu dodatku do istniejącego [zasobu azurerm_kubernetes_cluster](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Dodaj [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) wykonując kroki opisane w dokumentacji terraform.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Włącz z usługi Azure Monitor w portalu

Aby włączyć monitorowanie klastra AKS w witrynie Azure portal z usługi Azure Monitor, wykonaj następujące czynności:

1. W witrynie Azure portal wybierz pozycję **Monitor**.

2. Wybierz **pozycję Kontenery** z listy.

3. Na stronie **Monitor — kontenery** wybierz pozycję **Klastry niemonitorowane**.

4. Na liście niemonitorowanych klastrów znajdź kontener na liście i kliknij przycisk **Włącz**.   

5. Na stronie **Dołączanie do usługi Azure Monitor dla kontenerów,** jeśli masz istniejący obszar roboczy usługi Log Analytics w tej samej subskrypcji co klaster, wybierz go z listy rozwijanej.  
    Lista wstępnieselektuje domyślny obszar roboczy i lokalizację, do których kontener AKS jest wdrażany w ramach subskrypcji.

    ![Włączanie monitorowania szczegółowych informacji kontenerów AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Jeśli chcesz utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z klastra, postępuj zgodnie z instrukcjami w [obszarze roboczym Tworzenie usługi Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Pamiętaj, aby utworzyć obszar roboczy w tej samej subskrypcji, w których wdrożono kontener AKS.

Po włączeniu monitorowania może upłynąć około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra.

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Włączanie bezpośrednio z klastra AKS w portalu

Aby włączyć monitorowanie bezpośrednio z jednego z klastrów AKS w witrynie Azure portal, wykonaj następujące czynności:

1. W witrynie Azure portal wybierz pozycję **Wszystkie usługi**.

2. Na liście zasobów rozpocznij wpisywanie **kontenerów**.  Lista filtruje na podstawie danych wejściowych.

3. Wybierz **pozycję Usługi Kubernetes**.  

    ![Łącze usług Kubernetes](./media/container-insights-onboard/portal-search-containers-01.png)

4. Na liście kontenerów wybierz kontener.

5. Na stronie przegląd kontenerów wybierz pozycję **Monitoruj kontenery**.  

6. Na stronie **Dołączanie do usługi Azure Monitor dla kontenerów,** jeśli masz istniejący obszar roboczy usługi Log Analytics w tej samej subskrypcji co klaster, wybierz go na liście rozwijanej.  
    Lista wstępnieselektuje domyślny obszar roboczy i lokalizację, do których kontener AKS jest wdrażany w ramach subskrypcji.

    ![Włączanie monitorowania kondycji kontenera AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Jeśli chcesz utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z klastra, postępuj zgodnie z instrukcjami w [obszarze roboczym Tworzenie usługi Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Pamiętaj, aby utworzyć obszar roboczy w tej samej subskrypcji, w których wdrożono kontener AKS.

Po włączeniu monitorowania może upłynąć około 15 minut, zanim będzie można wyświetlić dane operacyjne dla klastra.

## <a name="enable-using-an-azure-resource-manager-template"></a>Włączanie przy użyciu szablonu usługi Azure Resource Manager

Ta metoda zawiera dwa szablony JSON. Jeden szablon określa konfigurację włączania monitorowania, a drugi zawiera wartości parametrów skonfigurowane w celu określenia następujących wartości:

* Identyfikator zasobu kontenera AKS.
* Grupa zasobów, w których jest wdrażany klaster.

>[!NOTE]
>Szablon musi zostać wdrożony w tej samej grupie zasobów co klaster.
>

Obszar roboczy usługi Log Analytics musi zostać utworzony przed włączeniem monitorowania przy użyciu programu Azure PowerShell lub interfejsu wiersza polecenia. Aby utworzyć obszar roboczy, można go skonfigurować za pośrednictwem [usługi Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), za pośrednictwem programu [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)lub w [portalu Azure](../../azure-monitor/learn/quick-create-workspace.md).

Jeśli nie znasz pojęcia wdrażania zasobów przy użyciu szablonu, zobacz:

* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)

* [Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Jeśli zdecydujesz się korzystać z interfejsu wiersza polecenia platformy Azure, najpierw należy zainstalować i używać interfejsu wiersza polecenia lokalnie. Musi być uruchomiony interfejsu wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Aby zidentyfikować swoją `az --version`wersję, uruchom polecenie . Jeśli chcesz zainstalować lub uaktualnić platformę Azure CLI, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

2. Zapisz ten plik jako **istniejącyClusterOnboarding.json** w folderze lokalnym.

3. Wklej do pliku następującą składnię JSON:

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

4. Edytuj wartości **aksResourceId** i **aksResourceLocation** przy użyciu wartości na stronie **Przegląd AKS** dla klastra AKS. Wartość **workspaceResourceId** jest pełny identyfikator zasobu obszaru roboczego usługi Log Analytics, który zawiera nazwę obszaru roboczego.

    Edytuj wartości **wartości aksResourceTagValues,** aby dopasować je do istniejących wartości tagów określonych dla klastra AKS.

5. Zapisz ten plik jako **istniejącyClusterParam.json** w folderze lokalnym.

6. Wszystko jest teraz gotowe do wdrożenia tego szablonu.

   * Aby wdrożyć za pomocą programu Azure PowerShell, należy użyć następujących poleceń w folderze, który zawiera szablon:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       Zmiana konfiguracji może potrwać kilka minut. Po jego zakończeniu zostanie wyświetlony komunikat podobny do następującego i zawiera wynik:

       ```output
       provisioningState       : Succeeded
       ```

   * Aby wdrożyć za pomocą interfejsu wiersza polecenia platformy Azure, uruchom następujące polecenia:

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       Zmiana konfiguracji może potrwać kilka minut. Po jego zakończeniu zostanie wyświetlony komunikat podobny do następującego i zawiera wynik:

       ```output
       provisioningState       : Succeeded
       ```

       Po włączeniu monitorowania może upłynąć około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra.

## <a name="verify-agent-and-solution-deployment"></a>Weryfikowanie wdrażania agenta i rozwiązania

W wersji agenta *06072018* lub nowszej można sprawdzić, czy agent i rozwiązanie zostały pomyślnie wdrożone. We wcześniejszych wersjach agenta można zweryfikować tylko wdrożenie agenta.

### <a name="agent-version-06072018-or-later"></a>Wersja agenta 06072018 lub nowsza

Uruchom następujące polecenie, aby sprawdzić, czy agent został pomyślnie wdrożony.

```
kubectl get ds omsagent --namespace=kube-system
```

Dane wyjściowe powinny przypominać następujące, co wskazuje, że został wdrożony poprawnie:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Aby zweryfikować wdrożenie rozwiązania, uruchom następujące polecenie:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Dane wyjściowe powinny przypominać następujące, co wskazuje, że został wdrożony poprawnie:

```output
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Wersja agenta wcześniejsza niż 06072018

Aby sprawdzić, czy wersja agenta usługi Log Analytics wydana przed *06072018* jest poprawnie wdrożona, uruchom następujące polecenie:  

```
kubectl get ds omsagent --namespace=kube-system
```

Dane wyjściowe powinny przypominać następujące, co wskazuje, że został wdrożony poprawnie:  

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Wyświetlanie konfiguracji za pomocą interfejsu wiersza polecenia

Użyj `aks show` polecenia, aby uzyskać szczegółowe informacje, takie jak jest włączone rozwiązanie lub nie, co to jest log analytics obszar roboczy resourceID i szczegóły podsumowania o klastrze.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Po kilku minutach polecenie kończy i zwraca informacje o rozwiązaniu w formacie JSON.  Wyniki polecenia powinny wyświetlać profil dodatku monitorowania i przypomina następujące przykładowe dane wyjściowe:

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

* Jeśli podczas próby wprowadzenia rozwiązania wystąpią problemy, zapoznaj się z [przewodnikiem rozwiązywania problemów](container-insights-troubleshoot.md)

* Dzięki możliwości monitorowania włączonemu do zbierania kondycji i wykorzystania zasobów klastra AKS i uruchomionych na nich obciążeń dowiedz się, [jak używać](container-insights-analyze.md) usługi Azure Monitor dla kontenerów.
