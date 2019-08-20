---
title: Jak zatrzymać monitorowanie Twojej usługi Azure Kubernetes Service klastra | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak można przerwać monitorowania klastra usługi AKS platformy Azure z usługą Azure Monitor dla kontenerów.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/19/2019
ms.author: magoedte
ms.openlocfilehash: 376259686d1668d62cc79f340e2161ef11be5e12
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624367"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Jak zatrzymać monitorowanie usługi Azure Kubernetes Service (AKS) z usługą Azure Monitor dla kontenerów

Po włączeniu monitorowania klastra usługi AKS, można zatrzymać monitorowania klastra, jeśli zdecydujesz, że już nie chcesz monitorować ją. W tym artykule pokazano, jak to zrobić przy użyciu wiersza polecenia platformy Azure lub przy użyciu dostarczonych szablonów usługi Azure Resource Manager.  


## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj [az aks disable dodatków](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) polecenie, aby wyłączyć usługi Azure Monitor dla kontenerów. Polecenie usuwa agenta z węzłów klastra, nie usuwa rozwiązania lub dane są już zbierane i przechowywane w zasobie Azure Monitor.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Aby ponownie włączyć monitorowania dla klastra, zobacz [Włącz monitorowanie przy użyciu wiersza polecenia platformy Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

Czy podana, dwa szablonu usługi Azure Resource Manager umożliwiają usunięcie spójnego i wielokrotnego zasoby rozwiązania w grupie zasobów. Jeden to szablon JSON określający konfigurację, aby zatrzymać monitorowanie, a drugi zawiera wartości parametrów, które można skonfigurować, aby określić identyfikator zasobu klastra AKS i grupę zasobów, w której jest wdrażany klaster. 

Jeśli znasz koncepcji wdrażanie zasobów za pomocą szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/resource-group-template-deploy.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

>[!NOTE]
>Szablon musi zostać wdrożony w tej samej grupie zasobów klastra. Jeśli pominięto wszystkie inne właściwości lub dodatki podczas korzystania z tego szablonu, może to spowodować usunięcie z klastra. Na przykład *enableRBAC* dla zasad RBAC wdrożonych w klastrze lub *aksResourceTagValues* , jeśli Tagi są określone dla klastra AKS.  
>

Jeśli zdecydujesz się użyć wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie. Musi być uruchomiona wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować wersję, uruchom `az --version`. Jeśli musisz zainstalować lub uaktualnić wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-template"></a>Tworzenie szablonu

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
       }
       },
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
      }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]"
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Zapisz ten plik jako **OptOutTemplate.json** do folderu lokalnego.

3. Wklej następującą składnię JSON do pliku:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterRegion>"
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

4. Edytuj wartości **aksResourceId** i **aksResourceLocation** przy użyciu wartości klastra AKS, który można znaleźć na **właściwości** strona dla wybranego klastra .

    ![Strona właściwości kontenera](media/container-insights-optout/container-properties-page.png)

    Gdy jesteś w **właściwości** strony, również skopiować **identyfikator zasobu obszaru roboczego**. Ta wartość jest wymagana, jeśli zdecydujesz, że chcesz później usunąć obszar roboczy usługi Log Analytics. Trwa usuwanie obszaru roboczego usługi Log Analytics nie jest wykonywane jako część tego procesu. 

    Edytuj wartości dla **aksResourceTagValues** , aby pasowały do istniejących wartości tagów określonych dla klastra AKS.

5. Zapisz ten plik jako **OptOutParam.json** do folderu lokalnego.

6. Wszystko jest teraz gotowe do wdrożenia tego szablonu. 

### <a name="remove-the-solution-using-azure-cli"></a>Usuń rozwiązanie za pomocą wiersza polecenia platformy Azure

W systemie Linux, aby usunąć rozwiązanie i wyczyścić konfiguracji klastra usługi AKS, wykonaj następujące polecenie z wiersza polecenia platformy Azure.

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Zmiana konfiguracji może potrwać kilka minut. Po ukończeniu, jest zwracany komunikat podobny do poniższego, który zawiera wynik:

```azurecli
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Usuń rozwiązanie przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wykonaj następujące polecenia programu PowerShell w folderu zawierającego szablon, aby usunąć rozwiązanie i wyczyścić konfiguracji z klastra usługi AKS.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Zmiana konfiguracji może potrwać kilka minut. Po ukończeniu, jest zwracany komunikat podobny do poniższego, który zawiera wynik:

```powershell
ProvisioningState       : Succeeded
```

Jeśli obszar roboczy został utworzony tylko do obsługi monitorowania klastra i nie jest już potrzebny, należy ręcznie je usunąć. Jeśli nie znasz sposób usuwania obszaru roboczego, zobacz [usunąć obszar roboczy usługi Azure Log Analytics w witrynie Azure portal](../../log-analytics/log-analytics-manage-del-workspace.md). Nie zapomnij o **identyfikator zasobu obszaru roboczego** możemy skopiowany wcześniej w kroku 4, możesz zacząć potrzebna. 

