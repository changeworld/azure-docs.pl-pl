---
title: Zapora aplikacji sieci web platformy Azure, monitorowanie i rejestrowanie
description: Dowiedz się, zapory aplikacji sieci web (WAF) przy użyciu FrontDoor, monitorowanie i rejestrowanie
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 5368ed8d1e60a646366065e2cf617fb2f3735b53
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59782891"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Zapora aplikacji sieci web platformy Azure, monitorowanie i rejestrowanie 

Rejestrowanie i monitorowanie umożliwiającymi zainstalowanie zapory aplikacji sieci web platformy Azure są dostarczane Dzięki rejestrowaniu i integracji z usługą Azure Monitor i Azure Monitor dzienniki.

> [!IMPORTANT]
> Zapora aplikacji sieci Web, które są monitorowanie, rejestrowanie funkcji Azure drzwiami frontowymi jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="azure-monitor"></a>Azure Monitor

Zapora aplikacji sieci Web z dziennikiem FrontDoor jest zintegrowana z usługą [usługi Azure Monitor](../azure-monitor/overview.md). Usługa Azure Monitor pozwala śledzić informacje diagnostyczne, w tym zapory aplikacji sieci Web, alerty i dzienniki. Możesz skonfigurować Monitorowanie zapory aplikacji sieci Web, w ramach zasobu drzwiami frontowymi w portalu w obszarze **diagnostyki** tab lub za pośrednictwem usługi Azure Monitor service bezpośrednio.

Z witryny Azure portal przejdź do drzwiami frontowymi typu zasobu. Z **monitorowanie**/**metryki** karcie po lewej stronie, można dodać **WebApplicationFirewallRequestCount** do śledzenia liczby żądań, które odpowiadają reguł zapory aplikacji sieci Web. Filtry niestandardowe można tworzyć w oparciu o typy akcji i nazwy reguł.

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Dzienniki i Diagnostyka

Zapora aplikacji sieci Web za pomocą drzwiami frontowymi udostępnia szczegółowe raporty w zakresie każdego wykrytego zagrożenia. Rejestrowanie jest zintegrowane z dziennikami diagnostycznymi platformy Azure, a alerty są zapisywane w formacie json. Te dzienniki można zintegrować z [dzienniki usługi Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog rejestruje wszystkie żądania, które są przekazywane do zaplecza klienta. FrontdoorWebApplicationFirewallLog rejestruje każde żądanie, które jest zgodny z regułą zapory aplikacji sieci Web.

Poniższe przykładowe zapytanie pobiera dzienniki zapory aplikacji sieci Web na zablokowane żądania:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Poniższe przykładowe zapytanie uzyskuje wpisy AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [drzwiami frontowymi](front-door-overview.md).

