---
title: Sprawdzanie dzienników zapory aplikacji bramy aplikacji usługi Azure za pomocą usługi Azure Log Analytics
description: W tym artykule pokazano, jak za pomocą usługi Azure Log Analytics można sprawdzać dzienniki zapory aplikacji bramy aplikacji
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/06/2019
ms.author: victorh
ms.openlocfilehash: e1bc3b58f425b374e4cae1da6e9800579e503f0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73516593"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Używanie usługi Log Analytics do badania dzienników zapory aplikacji internetowej usługi Application Gateway

Gdy brama aplikacji WAF działa, można włączyć dzienniki, aby sprawdzić, co dzieje się z każdym żądaniem. Dzienniki zapory dają wgląd w to, co WAF ocenia, dopasowuje i blokuje. Za pomocą usługi Log Analytics można sprawdzić dane wewnątrz dzienników zapory, aby uzyskać jeszcze więcej szczegółowych informacji. Aby uzyskać więcej informacji na temat tworzenia obszaru roboczego usługi Log Analytics, zobacz [Tworzenie obszaru roboczego usługi Log Analytics w witrynie Azure portal](../../azure-monitor/learn/quick-create-workspace.md). Aby uzyskać więcej informacji na temat zapytań dziennika, zobacz [Omówienie zapytań dziennika w usłudze Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>Importowanie dzienników WAF

Aby zaimportować dzienniki zapory do usługi Log Analytics, zobacz [Kondycja zaplecza, dzienniki diagnostyczne i metryki bramy aplikacji](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging). Jeśli masz dzienniki zapory w obszarze roboczym usługi Log Analytics, możesz wyświetlać dane, zapisywać zapytania, tworzyć wizualizacje i dodawać je do pulpitu nawigacyjnego portalu.

## <a name="explore-data-with-examples"></a>Eksploruj dane za pomocą przykładów

Aby wyświetlić nieprzetworzone dane w dzienniku zapory, można uruchomić następującą kwerendę:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Będzie to wyglądać podobnie do następującej kwerendy:

![Zapytanie usługi Log Analytics](../media/log-analytics/log-query.png)

Można przejść do szczegółów danych, wykreślić wykresy lub utworzyć wizualizacje w tym miejscu. Zobacz następujące zapytania jako punkt wyjścia:

### <a name="matchedblocked-requests-by-ip"></a>Dopasowane/zablokowane żądania ip

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Dopasowane/zablokowane żądania identyfikatora URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Reguły o najwyższej dopasowywce

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Pięć najlepszych dopasowanych grup reguł

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Dodawanie do pulpitu nawigacyjnego

Po utworzeniu kwerendy można ją dodać do pulpitu nawigacyjnego.  Wybierz **przypnij do pulpitu nawigacyjnego** w prawym górnym rogu obszaru roboczego analizy dzienników. W przypadku czterech poprzednich zapytań przypiętych do przykładowego pulpitu nawigacyjnego są to dane, które można wyświetlić na pierwszy rzut oka:

![Pulpit nawigacyjny](../media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Następne kroki

[Kondycja zaplecza, dzienniki diagnostyczne i metryki dla usługi Application Gateway](../../application-gateway/application-gateway-diagnostics.md)