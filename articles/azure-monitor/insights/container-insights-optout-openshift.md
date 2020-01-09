---
title: Jak zatrzymać monitorowanie klastra Red Hat OpenShift platformy Azure | Microsoft Docs
description: W tym artykule opisano, jak można zatrzymać monitorowanie klastra Red Hat OpenShift platformy Azure za pomocą Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: e726d2d8254598869f1c6305421c674c870e3d31
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75404294"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-cluster-with-azure-monitor-for-containers"></a>Jak zatrzymać monitorowanie klastra Red Hat OpenShift na platformie Azure za pomocą Azure Monitor dla kontenerów

Po włączeniu monitorowania klastra Red Hat OpenShift na platformie Azure Możesz zatrzymać monitorowanie klastra, jeśli zdecydujesz, że nie chcesz już go monitorować. W tym artykule pokazano, jak to zrobić przy użyciu podanych szablonów Azure Resource Manager.  

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

Czy podana, dwa szablonu usługi Azure Resource Manager umożliwiają usunięcie spójnego i wielokrotnego zasoby rozwiązania w grupie zasobów. Jeden to szablon JSON określający konfigurację, aby zatrzymać monitorowanie, a drugi zawiera wartości parametrów, które można skonfigurować w celu określenia identyfikatora zasobu klastra OpenShift i regionu świadczenia usługi Azure, w którym wdrożono klaster. 

Jeśli znasz koncepcji wdrażanie zasobów za pomocą szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/resource-group-template-deploy.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Jeśli zdecydujesz się użyć wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie. Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Aby zidentyfikować wersję, uruchom `az --version`. Jeśli musisz zainstalować lub uaktualnić wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-template"></a>Tworzenie szablonu

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
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
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. Edytuj wartości dla **aroResourceId** i **aroResourceLocation** przy użyciu wartości klastra OpenShift, który można znaleźć na stronie **Właściwości** wybranego klastra.

    ![Strona właściwości kontenera](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Zapisz ten plik jako **OptOutParam.json** do folderu lokalnego.

6. Wszystko jest teraz gotowe do wdrożenia tego szablonu. 

### <a name="remove-the-solution-using-azure-cli"></a>Usuń rozwiązanie za pomocą wiersza polecenia platformy Azure

Wykonaj następujące polecenie w interfejsie wiersza polecenia platformy Azure w systemie Linux, aby usunąć rozwiązanie i wyczyścić konfigurację w klastrze.

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

Wykonaj następujące polecenia programu PowerShell w folderze zawierającym szablon, aby usunąć rozwiązanie i wyczyścić konfigurację z klastra.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Zmiana konfiguracji może potrwać kilka minut. Po ukończeniu, jest zwracany komunikat podobny do poniższego, który zawiera wynik:

```powershell
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>Następne kroki

Jeśli obszar roboczy został utworzony tylko do obsługi monitorowania klastra i nie jest już potrzebny, należy ręcznie je usunąć. Jeśli nie wiesz, jak usunąć obszar roboczy, zobacz temat [Usuwanie obszaru roboczego usługi Azure log Analytics](../../log-analytics/log-analytics-manage-del-workspace.md). 
