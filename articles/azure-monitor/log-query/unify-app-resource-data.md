---
title: Ujednolicenie wiele zasobów usługi Azure Monitor Application Insights | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera szczegółowe informacje dotyczące sposobu używania funkcji w usłudze Azure Monitor dziennikach wizualizowanie danych i wykonywanie zapytań względem wielu zasobów usługi Application Insights.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: magoedte
ms.openlocfilehash: 3f3de81197b05d4f025a3fd8638cffe4b07cecad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61424661"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Ujednolicenie wiele zasobów usługi Azure Monitor Application Insights 
W tym artykule opisano, jak wykonywać zapytania i wyświetlić wszystkie usługi Application Insights dziennika danych aplikacji w jednym miejscu, nawet wtedy, gdy są one w różnych subskrypcjach platformy Azure, jako zamiennika amortyzacja łącznik usługi Application Insights. Liczba zasobów zasoby usługi Application Insights, uwzględnionych w ramach pojedynczego zapytania jest ograniczona do 100.  

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Zalecane podejście do wykonywania zapytań w wielu zasobów usługi Application Insights 
Wyświetlanie wielu zasobów usługi Application Insights w zapytaniu tego typu może być skomplikowane i trudne w utrzymaniu. Zamiast tego mogą korzystać z funkcji rozdzielają logikę zapytania z aplikacji, określania zakresu.  

W tym przykładzie pokazano, jak można monitorować wiele zasobów usługi Application Insights i zwizualizować liczbę żądań zakończonych niepowodzeniem według nazwy aplikacji. Przed rozpoczęciem, wykonania tego zapytania, w obszarze roboczym, który jest podłączony do zasobów usługi Application Insights, aby uzyskać listę połączonych aplikacji: 

```
ApplicationInsights
| summarize by ApplicationName
```

Tworzenie funkcji przy użyciu operatora union, z listy aplikacji, a następnie Zapisz zapytanie w obszarze roboczym jako funkcję z aliasu *applicationsScoping*.  

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

>[!NOTE]
>Uwzględnione na liście aplikacji można modyfikować w dowolnym momencie w portalu, przejdź do Eksploratora zapytań w obszarze roboczym i wybierając funkcja edytowania i zapisanie lub korzystania z `SavedSearch` polecenia cmdlet programu PowerShell. `withsource= SourceApp` Polecenie dodaje kolumnę do wyników, opisująca aplikacji wysyłane dziennika. 
>
>Zapytanie używa schematu usługi Application Insights, mimo że zapytanie jest wykonywane w obszarze roboczym, ponieważ funkcja applicationsScoping zwraca strukturę danych usługi Application Insights. 
>
>Operator analiza jest opcjonalny w tym przykładzie, wyodrębnia nazwę aplikacji z SourceApp właściwości. 

Teraz można przystąpić do użycia funkcja applicationsScoping zapytania obejmujące wiele zasobów:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

Alias funkcji zwraca złożenie żądania z określonych aplikacji. Zapytanie, a następnie filtruje dla żądań zakończonych niepowodzeniem i umożliwia wizualizowanie trendów przez aplikację.

![Przykład zapytania dla wielu wyników](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Wykonywanie zapytań dotyczących zasobów usługi Application Insights i obszar roboczy danych 
Po zatrzymaniu łącznika i potrzeb dotyczących wykonywania zapytań w zakresie czasu, który został przycięty za przechowywanie danych usługi Application Insights (90 dni), należy wykonać [zapytania obejmujące wiele zasobów](../../azure-monitor/log-query/cross-workspace-query.md) na obszar roboczy i usługi Application Insights zasoby dla pośredniego. Jest to, aż danych aplikacji gromadzi na nowe przechowywanie danych usługi Application Insights wymienionych powyżej. Zapytanie wymaga niektóre operacje, ponieważ różnią się schematów w usłudze Application Insights i obszaru roboczego. Zobacz tabelę w dalszej części tej sekcji szczególnym uwzględnieniem różnic schematu. 

>[!NOTE]
>[Zapytania obejmujące wiele zasobów](../log-query/cross-workspace-query.md) w dzienniku alertów jest obsługiwana w nowym [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Domyślnie używa usługi Azure Monitor [interfejsu API starszych Log Analytics alertu](../platform/api-alerts.md) do tworzenia reguł alertów nowy dziennik z witryny Azure portal, jeśli nie możesz przejść z wersji [starszej wersji interfejsu API z alertów dziennika](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Po przełączniku nowy interfejs API staje się domyślnie na nowe reguły alertów w witrynie Azure portal i umożliwia tworzenie dziennika alertów reguły zapytania obejmujące wiele zasobów. Można utworzyć [zapytania obejmujące wiele zasobów](../log-query/cross-workspace-query.md) rejestrowanie reguł alertów bez wprowadzania przełącznik przy użyciu [szablon ARM scheduledQueryRules interfejsu API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) — ale tę regułę alertu jest łatwe w zarządzaniu jednak [ Interfejs API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) , a nie z witryny Azure portal.

Na przykład zatrzymanie łącznik działa na 2018-11-01, podczas wysyłania zapytania dzienniki danych zasobów i aplikacji usługi Application Insights w obszarze roboczym, zapytanie będzie zbudowane tak jak w poniższym przykładzie:

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics workspace 
    | where TimeGenerated < (datetime("2018-12-01") 
    | where RequestSuccess == 'False' 
    | where RequestDuration > 1000 
    | extend duration = RequestDuration //align to Application Insights schema 
    | extend timestamp = TimeGenerated //align to Application Insights schema 
    | extend name = RequestName //align to Application Insights schema 
    | extend resultCode = ResponseCode //align to Application Insights schema 
    | project-away RequestDuration , RequestName , ResponseCode , TimeGenerated 
) 
| project timestamp , duration , name , resultCode 
```

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Różnice schematu obszaru roboczego usługi Application Insights i Log Analytics
W poniższej tabeli przedstawiono różnice w schemacie między usługi Log Analytics i usługi Application Insights.  

| Właściwości obszaru roboczego analizy dziennika| Właściwości zasobów szczegółowych informacji w aplikacji|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | timestamp |
| Browser | client_browser |
| City | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| Country | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| OS | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | name | 
| RequestSuccess | success | 
| ResponseCode | resultCode | 
| Role | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL | _url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Kolejne kroki

Użyj [wyszukiwanie w dzienniku](../../azure-monitor/log-query/log-query-overview.md) Aby wyświetlić szczegółowe informacje o aplikacjach Application Insights.
