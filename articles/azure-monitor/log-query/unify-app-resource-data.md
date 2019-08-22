---
title: Ujednolicenie wielu zasobów Application Insights Azure Monitor | Microsoft Docs
description: Ten artykuł zawiera szczegółowe informacje dotyczące używania funkcji w dziennikach Azure Monitor do wykonywania zapytań dotyczących wielu zasobów Application Insights i wizualizacji tych danych.
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
ms.openlocfilehash: d441b72b34da6146eba523563a09c2908cdcbbf4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650136"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Ujednolicenie wielu zasobów Application Insights Azure Monitor 
W tym artykule opisano sposób wykonywania zapytań i wyświetlania wszystkich danych dziennika Application Insights w jednym miejscu, nawet w przypadku, gdy znajdują się one w różnych subskrypcjach platformy Azure, jako zamiennik dla zaniechania Application Insights Connector. Liczba zasobów Application Insights, które można uwzględnić w pojedynczym zapytaniu, jest ograniczona do 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Zalecane podejście do wykonywania zapytań dotyczących wielu zasobów Application Insights 
Wyświetlanie listy wielu zasobów Application Insights w zapytaniu może być uciążliwe i trudne do utrzymania. Zamiast tego można wykorzystać funkcję do oddzielenia logiki zapytania od zakresu aplikacji.  

W tym przykładzie pokazano, jak można monitorować wiele zasobów Application Insights i wizualizować liczbę nieudanych żądań według nazwy aplikacji. Przed rozpoczęciem Uruchom to zapytanie w obszarze roboczym, który jest połączony z zasobami Application Insights, aby uzyskać listę połączonych aplikacji: 

```
ApplicationInsights
| summarize by ApplicationName
```

Utwórz funkcję używającą operatora UNION z listą aplikacji, a następnie Zapisz zapytanie w obszarze roboczym jako funkcję z aliasem *applicationsScoping*. 

W dowolnym momencie możesz modyfikować wymienione aplikacje w portalu, przechodząc do Eksploratora zapytań w obszarze roboczym i wybierając funkcję do edycji, a następnie zapisując lub używając `SavedSearch` polecenia cmdlet programu PowerShell. 

>[!NOTE]
>Ta metoda nie może być używana z alertami dziennika, ponieważ sprawdzanie dostępu do zasobów reguły alertów, w tym obszarów roboczych i aplikacji, odbywa się w czasie tworzenia alertu. Dodawanie nowych zasobów do funkcji po utworzeniu alertu nie jest obsługiwane. Jeśli wolisz używać funkcji dla określania zakresu zasobów w alertach dziennika, musisz zmodyfikować regułę alertu w portalu lub z szablonem Menedżer zasobów, aby zaktualizować zasoby w zakresie. Alternatywnie możesz dołączyć listę zasobów do zapytania alertu dziennika.

`withsource= SourceApp` Polecenie dodaje kolumnę do wyników, które wyznaczają aplikację, która wysłała dziennik. Operator Parse jest opcjonalny w tym przykładzie i służy do wyodrębniania nazwy aplikacji z właściwości SourceApp. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Teraz można przystąpić do używania funkcji applicationsScoping w kwerendzie obejmującej wiele zasobów:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

Zapytanie używa schematu Application Insights, mimo że zapytanie jest wykonywane w obszarze roboczym, ponieważ funkcja applicationsScoping zwraca strukturę danych Application Insights. Alias funkcji zwraca Unię żądań ze wszystkich zdefiniowanych aplikacji. Zapytanie następnie filtruje żądania nieudane i wizualizowa trendy według aplikacji.

![Przykład wyników między zapytaniami](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Wykonywanie zapytań dotyczących zasobów Application Insights i danych obszaru roboczego 
Po zatrzymaniu łącznika i zalogowaniu się w przedziale czasowym, który został przycięty przez Application Insights przechowywanie danych (90 dni), należy wykonać [zapytania obejmujące wiele zasobów](../../azure-monitor/log-query/cross-workspace-query.md) w obszarze roboczym i Application Insights zasobów dla pośredniego czasu. Jest to do momentu, aż dane aplikacji będą gromadzone na nowe Application Insights przechowywanie danych wymienionych powyżej. Zapytanie wymaga pewnego manipulowania, ponieważ schematy w Application Insights i obszar roboczy są różne. Zapoznaj się z tabelą w dalszej części tej sekcji, aby wyróżnić różnice między schematami. 

>[!NOTE]
>[Zapytanie między zasobami](../log-query/cross-workspace-query.md) w ramach alertów dziennika jest obsługiwane w nowym [interfejsie API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Domyślnie Azure Monitor używa [starszego interfejsu API alertów log Analytics](../platform/api-alerts.md) na potrzeby tworzenia nowych reguł alertów dziennika z Azure Portal, chyba że zostanie przełączony w [STARSZEJ wersji interfejsu API alertów dziennika](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Po przełączeniu nowy interfejs API zostanie ustawiony jako domyślny dla nowych reguł alertów w Azure Portal i umożliwia tworzenie reguł alertów dziennika zapytań dla wielu zasobów. Można tworzyć reguły alertów dziennika [zapytań dla wielu zasobów](../log-query/cross-workspace-query.md) bez przełączenia przy użyciu [szablonu ARM dla interfejsu API scheduledQueryRules](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) , ale ta reguła alertu jest możliwa do zarządzania, chociaż [interfejs API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) , a nie z Azure Portal.

Na przykład, jeśli łącznik przestanie działać na 2018-11-01, podczas wykonywania zapytania dotyczącego dzienników w Application Insights zasobów i aplikacji w obszarze roboczym zapytanie zostanie skonstruowane jak w poniższym przykładzie:

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

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Application Insights i Log Analytics różnice w schemacie obszaru roboczego
W poniższej tabeli przedstawiono różnice schematu między Log Analytics i Application Insights.  

| Log Analytics właściwości obszaru roboczego| Właściwości zasobów Application Insights|
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
| OperationID | parametrów |
| OperationName | operation_Name | 
| OS | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | name | 
| ParentOperationID | Parametrów | 
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
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Następne kroki

Użyj [wyszukiwanie w dzienniku](../../azure-monitor/log-query/log-query-overview.md) Aby wyświetlić szczegółowe informacje o aplikacjach Application Insights.
