---
title: Przykłady usługi Log Analytics dla zapory usługi Azure
description: Przykłady usługi Log Analytics dla zapory usługi Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 10/24/2018
ms.author: victorh
ms.openlocfilehash: 554ecd51eec3a4ade9a04c7280c0feff4b5bec27
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49989225"
---
# <a name="azure-firewall-log-analytics-samples"></a>Przykłady usługi Log Analytics dla zapory usługi Azure

Poniższe przykłady zapytań usługi Log Analytics może służyć do analizowania dzienników zapory usługi Azure.

## <a name="application-rules"></a>Reguły aplikacji

```
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
| where msg_s !contains "SNI TLS extension was missing" 
| parse msg_s with Protocol "request from" SourceIP ":" SourcePort:int " to" TargetURL ":" TargetPort:int ". Action:" Action "." *
| parse msg_s with * ". Rule Collection:" UserRuleCollection ". Rule:" UserRule
| parse msg_s with * "No rule matched. Proceeding with" DefaultRule
| extend Rule = case(DefaultRule == "", UserRule, DefaultRule), RuleCollection = case(UserRuleCollection == "", DefaultRule, UserRuleCollection)
| project TimeGenerated,msg_s, Protocol, Action, SourceIP, tostring(SourcePort), TargetURL, tostring(TargetPort), RuleCollection, Rule
```

## <a name="network-rules"></a>Reguły sieciowych

```
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
| parse msg_s with Protocol "request from" SourceIP ":" SourcePort:int " to" TargetIP ":" TargetPort:int *
| parse msg_s with * ". Action:" fw_Action
| parse msg_s with * "was" nat_Action
| extend Action = case(fw_Action == "", nat_Action, fw_Action)
| project TimeGenerated, msg_s, SourceIP,tostring(SourcePort),TargetIP,tostring(TargetPort),Action
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się o zapory usługi Azure, monitorowania i diagnostyki, zobacz [samouczek: dzienniki zapory usługi Azure Monitor i metryki](tutorial-diagnostics.md).