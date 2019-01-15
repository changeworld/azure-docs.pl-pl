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
ms.date: 01/10/2019
ms.author: magoedte
ms.openlocfilehash: e3b118306b5a139ba31029bc6191368690b36666
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265213"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Ujednolicenie wiele zasobów usługi Azure Monitor Application Insights 
W tym artykule opisano, jak wykonywać zapytania i wyświetlić wszystkie usługi Application Insights dziennika danych aplikacji w jednym miejscu, nawet wtedy, gdy są one w różnych subskrypcjach platformy Azure, jako zamiennika amortyzacja łącznik usługi Application Insights.  

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Zalecane podejście do wykonywania zapytań w wielu zasobów usługi Application Insights 
Wyświetlanie wielu zasobów usługi Application Insights w zapytaniu tego typu może być skomplikowane i trudne w utrzymaniu. Zamiast tego mogą korzystać z funkcji rozdzielają logikę zapytania z aplikacji, określania zakresu.  

W tym przykładzie pokazano, jak można monitorować wiele zasobów usługi Application Insights i zwizualizować liczbę żądań zakończonych niepowodzeniem według nazwy aplikacji. Przed rozpoczęciem, wykonania tego zapytania, w obszarze roboczym, który jest podłączony do zasobów usługi Application Insights, aby uzyskać listę połączonych aplikacji: 

```
ApplicationInsights
| summarize by ApplicationName
```

Tworzenie funkcji przy użyciu operatora union, z listy aplikacji, a następnie Zapisz zapytanie jako funkcję z aliasu *applicationsScoping*.  

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
>Uwzględnione na liście aplikacji można zmodyfikować w dowolnym momencie, przechodząc do obszaru Eksploratora zapytań w dziennikach portalu i edytowanie funkcji lub przy użyciu `SavedSearch` polecenia cmdlet programu PowerShell. `withsource= SourceApp` Polecenie dodaje kolumnę do wyników, opisująca aplikacji wysyłane dziennika. 
>
>Zapytanie używa schematu usługi Application Insights, mimo że zapytanie jest wykonywane w obszarze roboczym, ponieważ funkcja applicationsScoping zwraca strukturę danych usługi Application Insights. 
>
>Operator analiza jest opcjonalny w tym przykładzie, wyodrębnia nazwę aplikacji z SourceApp właściwości. 

Teraz można przystąpić do użycia funkcja applicationsScoping zapytania obejmujące wiele zasobów. Alias funkcji zwraca złożenie żądania z określonych aplikacji. Zapytanie, a następnie filtruje dla żądań zakończonych niepowodzeniem i umożliwia wizualizowanie trendów przez aplikację. ![Przykład zapytania dla wielu wyników](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Wykonywanie zapytań dotyczących zasobów usługi Application Insights i obszar roboczy danych 
Po zatrzymaniu łącznika i potrzeb dotyczących wykonywania zapytań w zakresie czasu, który został przycięty za przechowywanie danych usługi Application Insights (90 dni), należy wykonać [zapytania obejmujące wiele zasobów](../../azure-monitor/log-query/cross-workspace-query.md) na obszar roboczy i usługi Application Insights zasoby dla pośredniego. Jest to, aż danych aplikacji gromadzi na nowe przechowywanie danych usługi Application Insights wymienionych powyżej. Zapytanie wymaga niektóre operacje, ponieważ różnią się schematów w usłudze Application Insights i obszaru roboczego. Zobacz tabelę w dalszej części tej sekcji szczególnym uwzględnieniem różnic schematu. 

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
| AvailabilityCount | : ItemCount |
| AvailabilityDuration | czas trwania |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | sygnatura czasowa |
| Przeglądarka | client_browser |
| Miasto | client_city |
| ClientIP | client_IP |
| Computer (Komputer) | cloud_RoleInstance | 
| Kraj | client_CountryOrRegion | 
| CustomEventCount | : ItemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| Typ urządzenia | client_Type | 
| ExceptionCount | : ItemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| Typ | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| System operacyjny | client_OS | 
| PageViewCount | : ItemCount |
| PageViewDuration | czas trwania | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | : ItemCount | 
| RequestDuration | czas trwania | 
| Identyfikator żądania | id | 
| RequestName | name | 
| RequestSuccess | powodzenie | 
| ResponseCode | resultCode | 
| Rola | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| Identyfikator sesji | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| Adres URL | _url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Kolejne kroki

Użyj [wyszukiwanie w dzienniku](../../azure-monitor/log-query/log-query-overview.md) Aby wyświetlić szczegółowe informacje o aplikacjach Application Insights.