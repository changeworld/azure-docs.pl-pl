---
title: Jak zatrzymać monitorowanie klastra OpenShift Red Hat w usłudze Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak można zatrzymać monitorowanie klastra Azure Red Hat OpenShift za pomocą usługi Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: eff5203aeedd3c7ad283b55ba12f0e45a556460d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250731"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-cluster-with-azure-monitor-for-containers"></a>Jak zatrzymać monitorowanie klastra Usługi Azure Red Hat OpenShift za pomocą usługi Azure Monitor dla kontenerów

Po włączeniu monitorowania klastra OpenShift usługi Azure Red Hat można zatrzymać monitorowanie klastra, jeśli zdecydujesz, że nie chcesz już go monitorować. W tym artykule pokazano, jak to zrobić przy użyciu szablonów usługi Azure Resource Manager.  

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

Pod warunkiem, że są dwa szablony usługi Azure Resource Manager do obsługi usuwania zasobów rozwiązania konsekwentnie i wielokrotnie w grupie zasobów. Jednym z nich jest szablon JSON określający konfigurację, aby zatrzymać monitorowanie, a drugi zawiera wartości parametrów, które można skonfigurować w celu określenia identyfikatora zasobu klastra OpenShift i regionu platformy Azure, w którym jest wdrażany klaster.

Jeśli nie znasz pojęcia wdrażania zasobów przy użyciu szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)
* [Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Jeśli zdecydujesz się korzystać z interfejsu wiersza polecenia platformy Azure, najpierw należy zainstalować i używać interfejsu wiersza polecenia lokalnie. Musi być uruchomiony interfejsu wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Aby zidentyfikować swoją `az --version`wersję, uruchom polecenie . Jeśli chcesz zainstalować lub uaktualnić platformę Azure CLI, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

2. Zapisz ten plik jako **OptOutTemplate.json** w folderze lokalnym.

3. Wklej do pliku następującą składnię JSON:

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

4. Edytuj wartości **aroResourceId** i **aroResourceLocation** przy użyciu wartości klastra OpenShift, które można znaleźć na stronie **Właściwości** dla wybranego klastra.

    ![Strona właściwości kontenera](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Zapisz ten plik jako **OptOutParam.json** w folderze lokalnym.

6. Wszystko jest teraz gotowe do wdrożenia tego szablonu.

### <a name="remove-the-solution-using-azure-cli"></a>Usuń rozwiązanie przy użyciu interfejsu wiersza polecenia platformy Azure

Wykonaj następujące polecenie za pomocą narzędzia Azure CLI w systemie Linux, aby usunąć rozwiązanie i wyczyścić konfigurację w klastrze.

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

Wykonaj następujące polecenia programu PowerShell w folderze zawierającym szablon, aby usunąć rozwiązanie i wyczyścić konfigurację z klastra.    

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

Jeśli obszar roboczy został utworzony tylko do obsługi monitorowania klastra i nie jest już potrzebny, należy ręcznie go usunąć. Jeśli nie znasz sposobu usuwania obszaru roboczego, zobacz [Usuwanie obszaru roboczego usługi Azure Log Analytics](../../log-analytics/log-analytics-manage-del-workspace.md).
