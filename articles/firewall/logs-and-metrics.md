---
title: Przegląd dzienników zapory usługi Azure
description: W tym artykule przedstawiono omówienie dzienników diagnostycznych zapory usługi Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 0698f1dbc491781089ef94eec32f2a427fd3cca4
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422392"
---
# <a name="azure-firewall-logs"></a>Dzienniki zapory platformy Azure

Usługę Azure Firewall możesz monitorować przy użyciu dzienników zapory. Ponadto dzienniki aktywności umożliwiają inspekcję operacji wykonywanych względem zasobów usługi Azure Firewall.

Niektóre z tych dzienników są dostępne za pośrednictwem portalu. Dzienniki mogą być wysyłane do usługi [Log Analytics](../azure-monitor/insights/azure-networking-analytics.md), Storage i Event Hubs oraz analizowane za pomocą usługi Log Analytics lub innych narzędzi, takich jak program Excel i usługa Power BI.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

 Dla usługi Azure Firewall są dostępne następujące dzienniki diagnostyczne:

* **Dziennik reguł aplikacji**

   Dziennik reguł aplikacji jest zapisywany na koncie magazynu, przesyłany strumieniowo do usługi Event Hubs i/lub wysyłany do usługi Log Analytics tylko wtedy, gdy włączono go dla każdej zapory Azure Firewall. W wyniku każdego nowego połączenia, które jest zgodne z jedną ze skonfigurowanych reguł aplikacji, jest tworzony dziennik dla zaakceptowanego/odrzuconego połączenia. Dane są rejestrowane w formacie JSON, jak pokazano w poniższym przykładzie:

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Dziennik reguł sieci**

   Dziennik reguł sieci jest zapisywany na koncie magazynu, przesyłany strumieniowo do usługi Event Hubs i/lub wysyłany do usługi Log Analytics tylko wtedy, gdy włączono go dla każdej zapory Azure Firewall. W wyniku każdego nowego połączenia, które jest zgodne z jedną ze skonfigurowanych reguł sieci, jest tworzony dziennik dla zaakceptowanego/odrzuconego połączenia. Dane są rejestrowane w formacie JSON, jak pokazano w poniższym przykładzie:

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Masz trzy opcje przechowywania dzienników:

* **Konto magazynu**: konta magazynu najlepiej sprawdzają się w przypadku dzienników przechowywanych przez dłuższy czas i przeglądanych w razie potrzeby.
* **Event Hubs**: usługa Event Hubs to doskonałe rozwiązanie umożliwiające integrację z innymi narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM, Security Information and Event Management), aby otrzymywać alerty dotyczące zasobów.
* **Log Analytics**: usługa Log Analytics najlepiej nadaje się do ogólnego monitorowania aplikacji w czasie rzeczywistym lub przyglądania się trendom.

## <a name="activity-logs"></a>Dzienniki aktywności

   Wpisy dziennika aktywności są zbierane domyślnie i można je wyświetlać w witrynie Azure Portal.

   Korzystając z [dzienników aktywności platformy Azure](../azure-resource-manager/resource-group-audit.md) (znanych wcześniej jako dzienniki operacyjne i dzienniki inspekcji), możesz wyświetlać wszystkie operacje przesyłane do Twojej subskrypcji platformy Azure.


## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak monitorować dzienniki zapory platformy Azure i metryk, zobacz [samouczek: dzienniki zapory usługi Azure Monitor](tutorial-diagnostics.md).