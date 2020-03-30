---
title: Monitorowanie i rejestrowanie zapory aplikacji sieci Azure
description: Dowiedz się Zapora aplikacji sieci Web (WAF) z monitorowaniem i rejestrowaniem FrontDoor
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 4488fadf5db3b32049b5dce4bbee1fa76c320e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284147"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitorowanie i rejestrowanie zapory aplikacji sieci Azure 

Monitorowanie i rejestrowanie zapory aplikacji sieci Web platformy Azure (WAF) są udostępniane za pośrednictwem rejestrowania i integracji z dziennikami usługi Azure Monitor i Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

WAF z dziennikiem FrontDoor jest zintegrowany z [usługą Azure Monitor.](../../azure-monitor/overview.md) Usługa Azure Monitor umożliwia śledzenie informacji diagnostycznych, w tym alertów i dzienników WAF. Monitorowanie WAF można skonfigurować w zasobie drzwiach frontowych w portalu w obszarze **diagnostyki** kartę lub za pośrednictwem usługi Azure Monitor bezpośrednio.

W witrynie Azure portal przejdź do typu zasobu drzwiami frontowymi. Na karcie **Monitorowanie**/**metryki** po lewej stronie można dodać **WebApplicationFirewallRequestCount** do śledzenia liczby żądań, które pasują do reguł WAF. Filtry niestandardowe można tworzyć na podstawie typów akcji i nazw reguł.

![Wskaźnik WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Dzienniki i diagnostyka

WAF z drzwiami frontowymi zapewnia szczegółowe raportowanie każdego wykrytego zagrożenia. Rejestrowanie jest zintegrowane z dziennikami diagnostycznymi platformy Azure, a alerty są zapisywane w formacie json. Te dzienniki można zintegrować z [dziennikami usługi Azure Monitor.](../../azure-monitor/insights/azure-networking-analytics.md)

![WAFDiag (WAFDiag)](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog rejestruje wszystkie żądania, które są przekazywane do zaplecza klienta. FrontdoorWebApplicationFirewallLog rejestruje każde żądanie, które pasuje do reguły WAF.

Następująca przykładowa kwerenda uzyskuje dzienniki WAF w zablokowanych żądaniach:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_name_s == "Block"

```

Oto przykład zarejestrowanego żądania w dzienniku WAF:

``` WAFlogQuerySample
{
    "PreciseTimeStamp": "2020-01-25T00:11:19.3866091Z",
    "time": "2020-01-25T00:11:19.3866091Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "xx.xx.xxx.xxx",
        "socketIP": "xx.xx.xxx.xxx",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/?q=../../x",
        "ruleName": "Microsoft_DefaultRuleSet-1.1-LFI-930100",
        "policy": "WafDemoCustomPolicy",
        "action": "Block",
        "host": "wafdemofrontdoorwebapp.azurefd.net",
        "refString": "0p4crXgAAAABgMq5aIpu0T6AUfCYOroltV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode": "prevention"
    }
}

``` 

Następująca przykładowa kwerenda uzyskuje wpisy AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Oto przykład zarejestrowanego żądania w dzienniku programu Access:

``` AccessLogSample
{
    "PreciseTimeStamp": "2020-01-25T00:11:12.0160150Z",
    "time": "2020-01-25T00:11:12.0160150Z",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0n4crXgAAAACnRKbdALbyToAqNfSHssDvV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "httpMethod": "GET",
        "httpVersion": "2.0",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/",
        "requestBytes": "710",
        "responseBytes": "3116",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4017.0 Safari/537.36 Edg/81.0.389.2",
        "clientIp": "xx.xx.xxx.xxx",
        "timeTaken": "0.598",
        "securityProtocol": "TLS 1.2",
        "routingRuleName": "WAFdemoWebAppRouting",
        "backendHostname": "wafdemouksouth.azurewebsites.net:443",
        "sentToOriginShield": false,
        "httpStatusCode": "200",
        "httpStatusDetails": "200"
    }
}

```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [drzwiach frontowych](../../frontdoor/front-door-overview.md).
