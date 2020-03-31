---
title: Jak zatrzymać monitorowanie klastra usługi Azure Kubernetes | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak można przerwać monitorowanie klastra usługi Azure AKS za pomocą usługi Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 08/19/2019
ms.openlocfilehash: 618a4d7e10212dd2b042724b1ea11c97920dad57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275258"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Jak zatrzymać monitorowanie usługi Azure Kubernetes Service z usługą Azure Monitor dla kontenerów

Po włączeniu monitorowania klastra AKS można zatrzymać monitorowanie klastra, jeśli zdecydujesz, że nie chcesz go już monitorować. W tym artykule pokazano, jak to zrobić przy użyciu interfejsu wiersza polecenia platformy Azure lub za pomocą szablonów usługi Azure Resource Manager.  


## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj polecenia [az aks disable-addons,](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) aby wyłączyć usługę Azure Monitor dla kontenerów. Polecenie usuwa agenta z węzłów klastra, nie usuwa rozwiązania lub danych już zebranych i przechowywanych w zasobie usługi Azure Monitor.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Aby ponownie włączyć monitorowanie klastra, zobacz [Włączanie monitorowania przy użyciu interfejsu wiersza polecenia platformy Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

Pod warunkiem, że są dwa szablony usługi Azure Resource Manager do obsługi usuwania zasobów rozwiązania konsekwentnie i wielokrotnie w grupie zasobów. Jednym z nich jest szablon JSON określający konfigurację zatrzymania monitorowania, a drugi zawiera wartości parametrów skonfigurowane w celu określenia identyfikatora zasobu klastra AKS i grupy zasobów, w której jest wdrażany klaster.

Jeśli nie znasz pojęcia wdrażania zasobów przy użyciu szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)
* [Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>Szablon musi zostać wdrożony w tej samej grupie zasobów klastra. Jeśli podczas korzystania z tego szablonu pominięto inne właściwości lub dodatki, może to spowodować ich usunięcie z klastra. Na przykład *enableRBAC* dla zasad RBAC zaimplementowanych w klastrze lub *aksResourceTagValues,* jeśli tagi są określone dla klastra AKS.  
>

Jeśli zdecydujesz się korzystać z interfejsu wiersza polecenia platformy Azure, najpierw należy zainstalować i używać interfejsu wiersza polecenia lokalnie. Musi być uruchomiony interfejsu wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować swoją `az --version`wersję, uruchom polecenie . Jeśli chcesz zainstalować lub uaktualnić platformę Azure CLI, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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
       },
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
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
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Zapisz ten plik jako **OptOutTemplate.json** w folderze lokalnym.

3. Wklej do pliku następującą składnię JSON:

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

4. Edytuj wartości **aksResourceId** i **aksResourceLocation** przy użyciu wartości klastra AKS, które można znaleźć na stronie **Właściwości** dla wybranego klastra.

    ![Strona właściwości kontenera](media/container-insights-optout/container-properties-page.png)

    Gdy znajdujesz się na stronie **Właściwości,** skopiuj również **identyfikator zasobu obszaru roboczego**. Ta wartość jest wymagana, jeśli zdecydujesz, że chcesz usunąć obszar roboczy usługi Log Analytics później. Usuwanie obszaru roboczego usługi Log Analytics nie jest wykonywane w ramach tego procesu.

    Edytuj wartości **wartości aksResourceTagValues,** aby dopasować je do istniejących wartości tagów określonych dla klastra AKS.

5. Zapisz ten plik jako **OptOutParam.json** w folderze lokalnym.

6. Wszystko jest teraz gotowe do wdrożenia tego szablonu.

### <a name="remove-the-solution-using-azure-cli"></a>Usuń rozwiązanie przy użyciu interfejsu wiersza polecenia platformy Azure

Wykonaj następujące polecenie za pomocą narzędzia Azure CLI w systemie Linux, aby usunąć rozwiązanie i wyczyścić konfigurację w klastrze AKS.

```azurecli
az login   
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Zmiana konfiguracji może potrwać kilka minut. Po jego zakończeniu zwracany jest komunikat podobny do następującego, który zawiera wynik:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Usuwanie rozwiązania za pomocą programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wykonaj następujące polecenia programu PowerShell w folderze zawierającym szablon, aby usunąć rozwiązanie i wyczyścić konfigurację z klastra AKS.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Zmiana konfiguracji może potrwać kilka minut. Po jego zakończeniu zwracany jest komunikat podobny do następującego, który zawiera wynik:

```output
ProvisioningState       : Succeeded
```


## <a name="next-steps"></a>Następne kroki

Jeśli obszar roboczy został utworzony tylko do obsługi monitorowania klastra i nie jest już potrzebny, należy ręcznie go usunąć. Jeśli nie znasz sposobu usuwania obszaru roboczego, zobacz [Usuwanie obszaru roboczego usługi Azure Log Analytics za pomocą portalu Azure.](../../log-analytics/log-analytics-manage-del-workspace.md) Nie zapomnij o **identyfikatorze zasobu obszaru roboczego** skopiowanym wcześniej w kroku 4, będziesz tego potrzebować.
