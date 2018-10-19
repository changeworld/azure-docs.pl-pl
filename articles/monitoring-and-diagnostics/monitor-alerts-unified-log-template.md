---
title: Tworzenie alertu dziennika za pomocą szablonu usługi Resource Manager
description: Dowiedz się, jak można utworzyć alertu dziennika za pomocą szablonu usługi Azure Resource Manager i interfejsu API.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 6ec07d02e61d50aa588d75ba7337eb9237e11207
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49402901"
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>Tworzenie alertu dziennika za pomocą szablonu usługi Resource Manager
W tym artykule pokazano, jak zarządzać [alerty dzienników](monitor-alerts-unified-log.md) programowo w dużej skali, na platformie Azure przy użyciu [szablonu usługi Azure Resource Manager](..//azure-resource-manager/resource-group-authoring-templates.md) za pośrednictwem [programu Azure Powershell](../azure-resource-manager/resource-group-template-deploy.md) i [Wiersza polecenia platformy azure](../azure-resource-manager/resource-group-template-deploy-cli.md). Obecnie usługi Azure Alerts obsługuje alerty dzienników dla zapytań z [usługi Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) i [usługi Azure Application Insights](../application-insights/app-insights-analytics-tour.md).

## <a name="managing-log-alert-on-log-analytics"></a>Zarządzanie alertu dziennika w usłudze Log Analytics
Alert dziennika dla [usługi Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) jest zintegrowana z [nowych alertów platformy Azure środowiska](monitoring-overview-unified-alerts.md); nadal wychodzi poza interfejsami API analizy dzienników i pozostaje compatibile ze schematem wcześniej używane.

> [!NOTE]
> Począwszy od 14 maja 2018 r. wszystkie alerty w obszarze roboczym już automatycznie rozszerzone na platformę Azure. Aby uzyskać więcej informacji, zobacz [rozszerzyć alerty na platformę Azure](monitoring-alerts-extend.md). 

### <a name="using-azure-resource-manager-template"></a>Przy użyciu szablonu usługi Resource Manager platformy Azure
Alerty dzienników usługi Log Analytics są tworzone przez reguły alertów, uruchamianych zapisanej operacji wyszukiwania w regularnych odstępach czasu. Jeśli wyniki zapytania dopasowania określone kryteria, zostaje utworzony rekord alertu i są uruchamiane co najmniej jednej akcji. 

Zasób szablonu dla dziennika zapisanego wyszukiwania analizy i Log analytics alertsare dostępnych w sekcji dokumentacji usługi Log Analytics. Aby dowiedzieć się więcej, zobacz [Dodawanie usługi Log Analytics zapisane wyszukiwania i alerty](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md); w tym przykłady ilustrujące, jak również szczegóły schematu.

### <a name="using-resource-template-via-apipowershell"></a>Za pomocą szablonu zasobów za pomocą interfejsu API/programu Powershell
Log Analytics alertu interfejsu API REST jest zgodne ze specyfikacją REST i możliwy za pośrednictwem interfejsu REST API usługi Azure Resource Manager. Dlatego są dostępne z wiersza polecenia środowiska PowerShell i interfejsu API, w którym zostanie zwróci wyniki wyszukiwania dla użytkownika w formacie JSON, co pozwala na wykorzystanie wyniki na wiele różnych sposobów programowo.

Dowiedz się więcej o [tworzenie i zarządzanie nimi reguły alertów w usłudze Log Analytics przy użyciu interfejsu API REST](../log-analytics/log-analytics-api-alerts.md), w tym przykłady uzyskiwania dostępu do interfejsu API z poziomu programu Powershell.

## <a name="managing-log-alert-on-application-insights"></a>Zarządzanie alertu dziennika w usłudze Application Insights
Alerty dzienników usługi Azure Application Insights zostały wprowadzone w ramach nowych alertów platformy Azure w ramach usługi Azure Monitor. Dlatego jest uruchamiana przy użyciu interfejsu API usługi Azure Monitor jako [zaplanowane reguł zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) grupy operacji REST.

### <a name="using-azure-resource-manager-template"></a>Przy użyciu szablonu usługi Resource Manager platformy Azure
Alert dziennika dla zasobów usługi Application Insights ma typ `Microsoft.Insights/scheduledQueryRules/`. Aby uzyskać więcej informacji na temat tego typu zasobu, zobacz [usługi Azure Monitor — zaplanowane reguły dokumentację interfejsu API zapytań](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

Poniżej przedstawiono strukturę dla [tworzenia reguł zapytań zaplanowane](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) na podstawie szablonu zasobów z zestawem danych przykładowych jako zmienne.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0", 
    "parameters": {      
    },   
    "variables": {
    "alertLocation": "southcentralus",
    "alertName": "samplelogalert",
    "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
    "alertDesription": "Sample log search alert",
    "alertStatus": "true",
    "alertSource":{
        "Query":"requests",
        "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
        "Type":"ResultCount"
         },
     "alertSchedule":{
         "Frequency": 15,
         "Time": 60
         },
     "alertActions":{
         "SeverityLevel": "4"
         },
      "alertTrigger":{
        "Operator":"GreaterThan",
        "Threshold":"1"
         },
       "actionGrp":{
        "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
        "Subject": "Customized Email Header",
        "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"           
         }
  },
  "resources":[ {
    "name":"[variables('alertName')]",
    "type":"Microsoft.Insights/scheduledQueryRules",
    "apiVersion": "2018-04-16",
    "location": "[variables('alertLocation')]",
    "tags":{"[variables('alertTag')]": "Resource"},
    "properties":{
       "description": "[variables('alertDesription')]",
       "enabled": "[variables('alertStatus')]",
       "source": {
           "query": "[variables('alertSource').Query]",
           "dataSourceId": "[variables('alertSource').SourceId]",
           "queryType":"[variables('alertSource').Type]"
       },
      "schedule":{
           "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
           "timeWindowInMinutes": "[variables('alertSchedule').Time]"    
       },
      "action":{
           "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
           "severity":"[variables('alertActions').SeverityLevel]",
           "aznsAction":{
               "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
               "emailSubject":"[variables('actionGrp').Subject]",
               "customWebhookPayload":"[variables('actionGrp').Webhook]"
           },
       "trigger":{
               "thresholdOperator":"[variables('alertTrigger').Operator]",
               "threshold":"[variables('alertTrigger').Threshold]"
           }
       }
     }
   }
 ]
}
```
> [!IMPORTANT]
> Pole znacznika ukryte w Link do zasobu docelowego jest wymagane użycie [zaplanowane reguł zapytań ](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) szablonu połączenia lub zasobu interfejsu API. 

Przykładowy kod json powyżej, można zapisać jako (np.) sampleScheduledQueryRule.json na potrzeby tego przewodnika i można wdrożyć przy użyciu [usługi Azure Resource Manager w witrynie Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

### <a name="using-resource-template-via-clipowershell"></a>Za pomocą szablonu zasobów za pomocą interfejsu wiersza polecenia/programu Powershell
Usługa Azure Monitor — interfejs API reguły zaplanowanego zapytania jest w pełni zgodny z interfejsu REST API usługi Azure Resource Manager i interfejsu API REST. Dlatego można używać za pośrednictwem programu Powershell przy użyciu polecenia cmdlet usługi Resource Manager, a także wiersza polecenia platformy Azure.

Przedstawione poniżej użycia za pomocą polecenia cmdlet programu PowerShell usługi Resource Manager platformy Azure, w przykładzie przedstawionym wcześniej szablon zasobu (sampleScheduledQueryRule.json):
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
Przedstawione poniżej użycia za pomocą polecenia usługi Azure Resource Manager w interfejsie wiersza polecenia platformy Azure w przykładzie przedstawionym wcześniej szablon zasobu (sampleScheduledQueryRule.json):

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
Na powodzenie operacji 201 zostanie przywrócony do stanu nowych Tworzenie reguły alertu lub 200 zostanie zwrócona, jeśli istniejącą regułę alertu został zmodyfikowany.


## <a name="next-steps"></a>Kolejne kroki
* Zrozumienie [Akcje elementu Webhook dla alertów dzienników](monitor-alerts-unified-log-webhook.md)
* Dowiedz się więcej o nowym [Azure Alerts](monitoring-overview-unified-alerts.md)
* Dowiedz się więcej o [usługi Application Insights](../application-insights/app-insights-analytics.md)
* Dowiedz się więcej o [usługi Log Analytics](../log-analytics/log-analytics-overview.md).   
