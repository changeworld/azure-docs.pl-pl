---
title: Monitorowanie i rejestrowanie zapory aplikacji sieci Web platformy Azure
description: Informacje o zaporze aplikacji sieci Web (WAF) z usługa frontdoor monitorowaniem i rejestrowaniem
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 0c705139c082f13f40362e598f0fda9ba0a128a5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512485"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitorowanie i rejestrowanie zapory aplikacji sieci Web platformy Azure 

Monitorowanie i rejestrowanie w usłudze Azure Web Application Firewall (WAF) odbywa się za pomocą rejestrowania i integracji z dziennikami Azure Monitor i Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

WAF z dziennikiem usługa frontdoor jest zintegrowany z [Azure monitor](../../azure-monitor/overview.md). Azure Monitor umożliwia śledzenie informacji diagnostycznych, w tym alertów i dzienników WAF. Monitorowanie WAF można skonfigurować w ramach zasobu front-drzwi w portalu na karcie **Diagnostyka** lub bezpośrednio za pomocą usługi Azure monitor.

W obszarze Azure Portal przejdź do pozycji Typ zasobu front-drzwi. Na karcie monitorowanie **metryk**/po lewej stronie można dodać **WebApplicationFirewallRequestCount** do śledzenia liczby żądań zgodnych z regułami WAF. Filtry niestandardowe można tworzyć w oparciu o typy akcji i nazwy reguł.

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Dzienniki i Diagnostyka

WAF z przednimi drzwiami udostępnia szczegółowe raporty dotyczące każdego wykrytego zagrożenia. Rejestrowanie jest zintegrowane z dziennikami diagnostycznymi platformy Azure, a alerty są zapisywane w formacie json. Te dzienniki można zintegrować z [dziennikami Azure monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog rejestruje wszystkie żądania, które są przekazywane do zaplecza klienta. FrontdoorWebApplicationFirewallLog rejestruje wszystkie żądania zgodne z regułą WAF.

Następujące przykładowe zapytanie pobiera WAF dzienników na zablokowane żądania:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Następujące przykładowe zapytanie pobiera wpisy AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [drzwiach zewnętrznych](../../frontdoor/front-door-overview.md).

