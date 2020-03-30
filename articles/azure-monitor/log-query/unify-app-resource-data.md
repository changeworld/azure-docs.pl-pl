---
title: Ujednolicenie wielu zasobów usługi Azure Monitor Application Insights | Dokumenty firmy Microsoft
description: Ten artykuł zawiera szczegółowe informacje na temat używania funkcji w dziennikach usługi Azure Monitor do wykonywania zapytań dotyczących wielu zasobów usługi Application Insights i wizualizacji tych danych.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: ce58aae3b1db1f0f338d353025d4f277aeb6944f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137496"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Ujednolicenie wielu zasobów usługi Azure Monitor Application Insights 
W tym artykule opisano sposób wykonywania zapytań i wyświetlania wszystkich danych dziennika usługi Application Insights w jednym miejscu, nawet jeśli znajdują się one w różnych subskrypcjach platformy Azure, jako zamiennik dla wycofania łącznika usługi Application Insights. Liczba zasobów usługi Application Insights, które można uwzględnić w jednej kwerendzie, jest ograniczona do 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Zalecane podejście do wykonywania zapytań o wiele zasobów usługi Application Insights 
Wyświetlanie wielu zasobów usługi Application Insights w kwerendzie może być uciążliwe i trudne do utrzymania. Zamiast tego można wykorzystać funkcję, aby oddzielić logikę kwerendy od zakresu aplikacji.  

W tym przykładzie pokazano, jak można monitorować wiele zasobów usługi Application Insights i wizualizować liczbę żądań nie powiodło się według nazwy aplikacji.

Utwórz funkcję przy użyciu operatora unii z listą aplikacji, a następnie zapisz kwerendę w obszarze roboczym jako funkcję za pomocą *aliasu applicationsScoping*. 

Wymienione aplikacje można zmodyfikować w dowolnym momencie w portalu, przechodząc do Eksploratora zapytań w obszarze `SavedSearch` roboczym i wybierając funkcję do edycji, a następnie zapisywania lub korzystania z polecenia cmdlet programu PowerShell. 

>[!NOTE]
>Tej metody nie można używać z alertami dziennika, ponieważ sprawdzanie poprawności dostępu zasobów reguły alertu, w tym obszarów roboczych i aplikacji, jest wykonywane w czasie tworzenia alertu. Dodawanie nowych zasobów do funkcji po utworzeniu alertu nie jest obsługiwane. Jeśli wolisz używać funkcji do określania zakresu zasobów w alertach dziennika, musisz edytować regułę alertów w portalu lub z szablonem Menedżera zasobów, aby zaktualizować zasoby o określonym zakresie. Alternatywnie można dołączyć listę zasobów w kwerendzie alertu dziennika.

Polecenie `withsource= SourceApp` dodaje kolumnę do wyników, która wyznacza aplikację, która wysłała dziennik. Operator analizy jest opcjonalny w tym przykładzie i używa do wyodrębniania nazwy aplikacji z właściwości SourceApp. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Teraz można przystąpić do użycia funkcji applicationsScoping w kwerendzie międzysóbowej:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

Kwerenda używa schematu usługi Application Insights, chociaż kwerenda jest wykonywana w obszarze roboczym, ponieważ funkcja applicationsScoping zwraca strukturę danych usługi Application Insights. Alias funkcji zwraca unię żądań ze wszystkich zdefiniowanych aplikacji. Następnie kwerenda filtruje żądania, które nie powiodły się, i wizualizuje trendy według aplikacji.

![Przykład wyników kwerend krzyżowych](media/unify-app-resource-data/app-insights-query-results.png)

>[!NOTE]
>[Kwerenda między zasobami](../log-query/cross-workspace-query.md) w alertach dziennika jest obsługiwana w nowym [interfejsie API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Domyślnie usługa Azure Monitor używa [starszego interfejsu API alertów usługi Log Analytics](../platform/api-alerts.md) do tworzenia nowych reguł alertów dziennika z witryny Azure Portal, chyba że przełączysz się ze [starszego interfejsu API alertów dziennikowych.](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) Po przełączeniu nowy interfejs API staje się domyślny dla nowych reguł alertów w witrynie Azure Portal i umożliwia tworzenie reguł alertów dziennika zapytań między zasobami. Można utworzyć reguły alertów dziennika [zapytań między zasobami](../log-query/cross-workspace-query.md) bez wprowadzania przełącznika przy użyciu [szablonu ARM dla scheduledQueryRules API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) — ale ta reguła alertu jest zarządzana, choć [scheduledQueryRules API,](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) a nie z witryny Azure portal.

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Różnice schematów usługi Application Insights i Log Analytics
W poniższej tabeli przedstawiono różnice schematu między usługą Log Analytics i aplikacją.  

| Właściwości obszaru roboczego usługi Log Analytics| Właściwości zasobów usługi Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | Appname|
| ApplicationTypeVersion (Wersja typu aplikacji) | application_Version |
| DostępnośćCount | Itemcount |
| DostępnośćDuration | czas trwania |
| DostępnośćMessage | message |
| DostępnośćRunLocation | location |
| DostępnośćTestIdId | id |
| Nazwa testu dostępności | name |
| DostępnośćStyg | sygnatura czasowa |
| Przeglądarka | client_browser |
| Miasto | client_city |
| KlientIP | client_IP |
| Computer (Komputer) | cloud_RoleInstance | 
| Kraj | client_CountryOrRegion | 
| CustomEventCount (Konto niestandardowe) | Itemcount | 
| NiestandardoweWentdymensions | niestandardoweDymensions |
| Niestandardowa nazwaventname | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| Liczba wyjątków | Itemcount | 
| ExceptionHandledAt | obsługiwane Wat |
| WyjątekMessage | message | 
| Typ wyjątku | type |
| OperationID (OperationID) | operation_id |
| OperationName | operation_Name | 
| System operacyjny | client_OS | 
| Liczba wyświetlek pageview | Itemcount |
| PageViewDuration (Liczba wyświetlek strony) | czas trwania | 
| Nazwa widoku strony | name | 
| Element nadrzędny | operation_Id | 
| Liczba żądań | Itemcount | 
| RequestDuration (Wniosekduration) | czas trwania | 
| RequestID | id | 
| Nazwa żądania | name | 
| PoprośSukces | powodzenie | 
| Kod odpowiedzi | kod wynikowy | 
| Rola | cloud_RoleName |
| RoleInstance (Nieumieja roli) | cloud_RoleInstance |
| SessionId | Session_id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| Adres URL | url |
| Identyfikator konta użytkownika | user_AccountId |

## <a name="next-steps"></a>Następne kroki

Użyj [wyszukiwania dziennika,](../../azure-monitor/log-query/log-query-overview.md) aby wyświetlić szczegółowe informacje dotyczące aplikacji usługi Application Insights.
