---
title: Użyj usługi Azure Log Analytics, aby sprawdzić dzienniki zapory aplikacji sieci Web usługi Application Gateway
description: W tym artykule pokazano, jak można użyć usługi Azure Log Analytics można sprawdzić dzienniki zapory aplikacji sieci Web usługi Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: aa867e33ef0faa96b6a66a9075a3a5b8b0b0bca4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712178"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Użyj usługi Log Analytics, aby sprawdzić dzienniki zapory aplikacji sieci Web bramy aplikacji

Po działa brama aplikacji zapory aplikacji internetowych można włączyć dzienniki, aby sprawdzić, co się dzieje z każdym żądaniem. Dzienniki zapory zapewniają wgląd jakie zapory aplikacji sieci Web jest dokonanie oceny oprogramowania dopasowania i blokowanie. Za pomocą usługi Log Analytics, można sprawdzić dane wewnątrz dzienniki zapory, aby zapewnić jeszcze więcej szczegółowych informacji. Aby uzyskać więcej informacji na temat tworzenia obszaru roboczego usługi Log Analytics, zobacz [Utwórz obszar roboczy usługi Log Analytics w witrynie Azure portal](../azure-monitor/learn/quick-create-workspace.md). Aby uzyskać więcej informacji o zapytaniach dzienników, zobacz [Przegląd dziennika zapytań w usłudze Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>Importowanie dzienników zapory aplikacji sieci Web

Aby zaimportować swoje dzienniki zapory w usłudze Log Analytics, zobacz [kondycja zaplecza, dzienniki diagnostyczne i metryki dla usługi Application Gateway](application-gateway-diagnostics.md#diagnostic-logging). Jeśli masz dzienniki zapory w obszarze roboczym usługi Log Analytics, mogą wyświetlać dane, pisania zapytań, tworzenie wizualizacji i dodać je do pulpitu nawigacyjnego portalu.

## <a name="explore-data-with-examples"></a>Eksplorowanie danych wraz z przykładami

Aby wyświetlić nieprzetworzone dane w dzienniku zapory, można uruchomić następujące zapytanie:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Będzie to wyglądać podobnie do następującej kwerendy:

![Zapytanie analizy dzienników](media/log-analytics/log-query.png)

Możesz przejść do szczegółów na temat danych i wykreślanie wykresów lub tworzenie wizualizacji w tym miejscu. Jako punktu wyjścia, zobacz następujące zapytania:

### <a name="matchedblocked-requests-by-ip"></a>Dopasowane zablokowane żądania według adresu IP

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Dopasowane zablokowane żądania przez identyfikator URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Najważniejsze pasujących reguł

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Pierwsze pięć grup dopasowane reguły

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Dodaj do swojego pulpitu nawigacyjnego

Po utworzeniu zapytania, można dodać go do pulpitu nawigacyjnego.  Wybierz **Przypnij do pulpitu nawigacyjnego** w prawym górnym rogu obszaru roboczego usługi log analytics. Przy użyciu poprzednich zapytań cztery przypięte do pulpitu nawigacyjnego przykładu to dane, które widać w skrócie:

![Pulpit nawigacyjny](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Kolejne kroki

[Kondycja zaplecza, dzienniki diagnostyczne i metryki dla usługi Application Gateway](application-gateway-diagnostics.md)
