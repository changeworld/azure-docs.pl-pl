---
title: Utwórz alert dziennika przy użyciu szablonu usługi Resource Manager | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć wpis dziennika przy użyciu szablonu Azure Resource Manager i interfejsu API.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: vinagara
ms.openlocfilehash: 4148c6d3f9d4407681159870b858c629c0b5d878
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>Utwórz alert dziennika przy użyciu szablonu usługi Resource Manager
W tym artykule opisano, jak można zarządzać [rejestrowania alertów](monitor-alerts-unified-log.md) programowo na dużą skalę, za pomocą usługi Azure [szablonu usługi Azure Resource Manager](..//azure-resource-manager/resource-group-authoring-templates.md) za pośrednictwem [programu Azure Powershell](../azure-resource-manager/resource-group-template-deploy.md) i [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md). Obecnie alerty Azure obsługuje rejestrowania alertów dla zapytań z [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) i [Azure Application Insights](../application-insights/app-insights-analytics-tour.md).

## <a name="managing-log-alert-on-log-analytics"></a>Zarządzanie alertu dziennika analizy dzienników
Alert dziennika dla [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) jest zintegrowany z [występują nowe alerty Azure](monitoring-overview-unified-alerts.md); podczas nadal wychodzi poza interfejsów API usługi Analiza dzienników i pozostaje zgodność ze schematem wcześniej używane do zarządzania [alerty w portalu OMS](..//log-analytics/log-analytics-alerts-creating.md).

> [!NOTE]
> Począwszy od 14 maja 2018 wszystkie alerty w obszarze roboczym rozpocznie się automatycznie rozszerzenie na platformie Azure. Przed 14 maja 2018 użytkownika dobrowolnie zainicjować rozszerzanie alerty na platformie Azure. Aby uzyskać więcej informacji, zobacz [rozszerzyć alerty na platformie Azure z usługą OMS](monitoring-alerts-extend.md). 

### <a name="using-azure-resource-manager-template"></a>Przy użyciu szablonu Azure Resource Manager
Alerty dziennika dla analizy dzienników są tworzone przez reguły alertów, które uruchomić zapisane wyszukiwanie w regularnych odstępach czasu. Jeśli wyniki zapytania dopasowania określone kryteria, tworzony jest rekord alertów i są uruchamiane co najmniej jednej akcji. 

Szablon zasobów dla [dziennika analizy zapisane wyszukiwania](../log-analytics/log-analytics-log-searches.md) i [rejestrowania alertów analytics](../log-analytics/log-analytics-alerts.md) są dostępne w sekcji dokumentacji analizy dzienników. Dowiedz się więcej o, [Dodawanie analizy dzienników zapisane wyszukiwania i alerty](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md); w tym przykłady opisowy, jak również szczegóły schematu.

### <a name="using-resource-template-via-apipowershell"></a>Przy użyciu szablonu zasobów za pomocą interfejsu API Powershell
Dziennik analizy alertu interfejsu API REST jest RESTful i jest dostępny za pośrednictwem interfejsu REST API usługi Azure Resource Manager. Interfejs API w związku z tym są dostępne z wiersza polecenia programu PowerShell i dane wyjściowe obejmują wyniki wyszukiwania do użytkownika w formacie JSON, co umożliwia wykorzystanie wyników na wiele różnych sposobów programowo.

Dowiedz się więcej o [tworzenia i zarządzania regułami alertów w analizy dzienników z interfejsu API REST](../log-analytics/log-analytics-api-alerts.md); w tym przykłady uzyskiwania dostępu do interfejsu API z programu Powershell.

## <a name="managing-log-alert-on-application-insights"></a>Zarządzanie alert dziennika dla usługi Application Insights
Alerty dziennika dla usługi Azure Application Insights zostały wprowadzone w ramach nowych alertów Azure w obszarze Azure Monitor. Dlatego zostanie uruchomiony w interfejsu API Monitor Azure jako [zaplanowane reguły zapytania](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) grupy operacji REST.

### <a name="using-azure-resource-manager-template"></a>Przy użyciu szablonu Azure Resource Manager
Alert dziennika dla zasobów usługi Application Insights ma typ `Microsoft.Insights/scheduledQueryRules/`. Aby uzyskać więcej informacji na ten typ zasobu, zobacz [Monitor Azure — odwołanie zaplanowane API reguły zapytania](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/).

Poniżej przedstawiono struktury [reguły zapytania zaplanowane tworzenie](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/createorupdate) na podstawie szablonu zasobów, z zestawem danych przykładowych jako zmienne.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",    
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
        "SeverityLevel": "4",
        },
     "alertTrigger":{
       "Operator":"GreaterThan",
       "Threshold":"1"
        },
      "actionGrp":{
       "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
      "Subject": "Customized Email Header",
      "Webhook": "{}"           
        }
  },
  "resources":[ {
     "name":"[variables('alertName')]",
     "type":"Microsoft.Insights/scheduledQueryRules",
     "apiVersion": "2018-04-16",
     "location": "[variables('alertLocation')]",
     "tags":{"hidden-link:[variables('alertTargetResource')]": "Resource"},
     "properties":{
        "description": "[variables('alertDescription')]",
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
> Tag pola ukryte w łącze do zasobu docelowego jest wymagane użycie [zaplanowane reguły zapytania ](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) interfejsu API wywołania lub zasobu szablon. 

Powyżej json próbki mogą zostać zapisane jako (Powiedz) sampleScheduledQueryRule.json na potrzeby tego przewodnika i wdrażane za pomocą [usługi Azure Resource Manager w portalu Azure](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

### <a name="using-resource-template-via-clipowershell"></a>Przy użyciu szablonu zasobów za pomocą interfejsu wiersza polecenia/programu Powershell
Monitor Azure — jest zaplanowane API reguły zapytania interfejsu API REST i pełni zgodny z interfejsu REST API Menedżera zasobów Azure. Dlatego może służyć za pomocą programu Powershell przy użyciu polecenia cmdlet usługi Resource Manager, a także wiersza polecenia platformy Azure.

Przedstawione poniżej użycia za pomocą polecenia cmdlet programu PowerShell usługi Azure Resource Manager dla przykładowych pokazano wcześniej szablon zasobu (sampleScheduledQueryRule.json):
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
Przedstawione poniżej użycia za pomocą polecenia usługi Azure Resource Manager w wiersza polecenia platformy Azure dla przykładowych pokazano wcześniej szablon zasobu (sampleScheduledQueryRule.json):

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
Powodzenie operacji 201 powróci do stanu nowych Tworzenie reguły alertu lub 200 zostanie zwrócona w przypadku modyfikacji istniejącego reguły alertu.


## <a name="next-steps"></a>Kolejne kroki
* Zrozumienie [Akcje elementu Webhook dla dziennika alertów](monitor-alerts-unified-log-webhook.md)
* Dowiedz się więcej o nowe [alerty Azure](monitoring-overview-unified-alerts.md)
* Dowiedz się więcej o [usługi Application Insights](../application-insights/app-insights-analytics.md)
* Dowiedz się więcej o [analizy dzienników](../log-analytics/log-analytics-overview.md).   