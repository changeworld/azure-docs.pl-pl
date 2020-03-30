---
title: Wykonywanie zapytań między zasobami za pomocą usługi Azure Monitor | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak można kwerendy względem zasobów z wielu obszarów roboczych i aplikacji Usługi App Insights w ramach subskrypcji.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/05/2019
ms.openlocfilehash: 4740034bd970f42833125fa43bfdf72f710ac147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249609"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Wykonywanie kwerend dziennika między zasobami w usłudze Azure Monitor  

Wcześniej za pomocą usługi Azure Monitor można było analizować tylko dane z bieżącego obszaru roboczego i ograniczało możliwość wykonywania zapytań w wielu obszarach roboczych zdefiniowanych w ramach subskrypcji.  Ponadto można przeszukiwać tylko elementy telemetryczne zebrane z aplikacji sieci web za pomocą usługi Application Insights bezpośrednio w usłudze Application Insights lub z programu Visual Studio. To również wyzwanie dla natywnie analizy danych operacyjnych i aplikacji razem.

Teraz można wyszukiwać nie tylko w wielu obszarach roboczych usługi Log Analytics, ale także dane z określonej aplikacji usługi Application Insights w tej samej grupie zasobów, innej grupie zasobów lub innej subskrypcji. Zapewnia to ogólnosystemowy widok danych. Tego typu kwerendy można wykonywać tylko w [usłudze Log Analytics.](portals.md)

## <a name="cross-resource-query-limits"></a>Limity zapytań między zasobami 

* Liczba zasobów usługi Application Insights i obszarów roboczych usługi Log Analytics, które można uwzględnić w jednej kwerendzie, jest ograniczona do 100.
* Kwerenda między zasobami nie jest obsługiwana w projektancie widoku. Możesz zasądować kwerendę w usłudze Log Analytics i przypiąć ją do pulpitu nawigacyjnego platformy Azure, aby [zwizualizować kwerendę dziennika](../learn/tutorial-logs-dashboards.md). 
* Kwerenda między zasobami w alertach dziennika jest obsługiwana w nowym [interfejsie API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Domyślnie usługa Azure Monitor używa [starszego interfejsu API alertów usługi Log Analytics](../platform/api-alerts.md) do tworzenia nowych reguł alertów dziennika z witryny Azure Portal, chyba że przełączysz się ze [starszego interfejsu API alertów dziennikowych.](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) Po przełączeniu nowy interfejs API staje się domyślny dla nowych reguł alertów w witrynie Azure Portal i umożliwia tworzenie reguł alertów dziennika zapytań między zasobami. Można utworzyć reguły alertów dziennika kwerend między zasobami bez wprowadzania przełącznika przy użyciu [szablonu usługi Azure Resource Manager dla interfejsu API scheduledQueryRules](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) — ale ta reguła alertu jest zarządzana, chociaż [scheduledQueryRules API,](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) a nie z witryny Azure portal.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Wykonywanie zapytań w różnych obszarach roboczych usługi Log Analytics i w usłudze Application Insights
Aby odwołać się do innego obszaru roboczego w kwerendzie, użyj [*identyfikatora obszaru roboczego,*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) a dla aplikacji z usługi Application Insights użyj identyfikatora [*aplikacji.*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression)  

### <a name="identifying-workspace-resources"></a>Identyfikowanie zasobów obszaru roboczego
Poniższe przykłady pokazują kwerendy w obszarach roboczych usługi Log Analytics, aby zwrócić podsumowane liczby dzienników z tabeli Aktualizuj w obszarze roboczym o nazwie *contosoretail-it*. 

Identyfikowanie obszaru roboczego można wykonać na jeden z kilku sposobów:

* Nazwa zasobu - jest czytelną dla człowieka nazwą obszaru roboczego, czasami nazywaną *nazwą składnika*. 

    `workspace("contosoretail-it").Update | count`

* Nazwa kwalifikowana - to "pełna nazwa" obszaru roboczego, składająca się z nazwy subskrypcji, grupy zasobów i nazwy składnika w tym formacie: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Ponieważ nazwy subskrypcji platformy Azure nie są unikatowe, ten identyfikator może być niejednoznaczny. 
    >

* Identyfikator obszaru roboczego — identyfikator obszaru roboczego to unikatowy, niezmienny identyfikator przypisany do każdego obszaru roboczego reprezentowany jako unikatowy identyfikator (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Identyfikator zasobu platformy Azure — unikatowa tożsamość obszaru roboczego zdefiniowana przez platformę Azure. Identyfikator zasobu jest używany, gdy nazwa zasobu jest niejednoznaczna.  W przypadku obszarów roboczych format to: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/workspaces/componentName*.  

    Przykład:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identyfikowanie aplikacji
Poniższe przykłady zwracają podsumowaną liczbę żądań złożonych za aplikację o nazwie *fabrikamapp* w usłudze Application Insights. 

Identyfikowanie aplikacji w usłudze Application Insights można wykonać za pomocą wyrażenia *identyfikator aplikacji.*  *Argument Identyfikator* określa aplikację przy użyciu jednej z następujących czynności:

* Nazwa zasobu - jest czytelną dla człowieka nazwą aplikacji, czasami nazywaną *nazwą składnika*.  

    `app("fabrikamapp")`

    >[!NOTE]
    >Identyfikowanie aplikacji według nazwy zakłada unikatowość we wszystkich dostępnych subskrypcji. Jeśli masz wiele aplikacji o określonej nazwie, kwerenda kończy się niepowodzeniem z powodu niejednoznaczności. W takim przypadku należy użyć jednego z innych identyfikatorów.

* Nazwa kwalifikowana - to "pełna nazwa" aplikacji, składająca się z nazwy subskrypcji, grupy zasobów i nazwy składnika w tym formacie: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Ponieważ nazwy subskrypcji platformy Azure nie są unikatowe, ten identyfikator może być niejednoznaczny. 
    >

* ID - identyfikator GUID aplikacji.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Identyfikator zasobu platformy Azure — unikatowa tożsamość aplikacji zdefiniowana przez platformę Azure. Identyfikator zasobu jest używany, gdy nazwa zasobu jest niejednoznaczna. Format to: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/components/componentName*.  

    Przykład:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Wykonywanie kwerendy w wielu zasobach
Można zbadać wiele zasobów z dowolnego wystąpienia zasobów, mogą to być obszary robocze i aplikacje połączone.
    
Przykład kwerendy w dwóch obszarach roboczych:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Korzystanie z kwerendy międzysóbowej dla wielu zasobów
W przypadku używania kwerend między zasobami do skorelowania danych z wielu obszarów roboczych usługi Log Analytics i zasobów usługi Application Insights kwerenda może stać się złożona i trudna do utrzymania. Należy wykorzystać [funkcje w kwerendach dziennika usługi Azure Monitor,](functions.md) aby oddzielić logikę kwerendy od zakresu zasobów kwerendy, co upraszcza strukturę kwerend. W poniższym przykładzie pokazano, jak można monitorować wiele zasobów usługi Application Insights i wizualizować liczbę żądań, które nie powiodło się według nazwy aplikacji. 

Utwórz kwerendę podobną do poniższej, która odwołuje się do zakresu zasobów usługi Application Insights. Polecenie `withsource= SourceApp` dodaje kolumnę, która wyznacza nazwę aplikacji, która wysłała dziennik. [Zapisz kwerendę jako funkcję](functions.md#create-a-function) za pomocą aliasu _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Teraz można [użyć tej funkcji](../../azure-monitor/log-query/functions.md#use-a-function) w kwerendzie między zasobami, jak poniżej. Funkcja alias _applicationsScoping_ zwraca unię tabeli żądań ze wszystkich zdefiniowanych aplikacji. Następnie kwerenda filtruje żądania, które nie powiodły się, i wizualizuje trendy według aplikacji. Operator analizy jest _opcjonalny_ w tym przykładzie. Wyodrębnia nazwę aplikacji z _SourceApp_ właściwości.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
>Tej metody nie można używać z alertami dziennika, ponieważ sprawdzanie poprawności dostępu zasobów reguły alertu, w tym obszarów roboczych i aplikacji, jest wykonywane w czasie tworzenia alertu. Dodawanie nowych zasobów do funkcji po utworzeniu alertu nie jest obsługiwane. Jeśli wolisz używać funkcji do określania zakresu zasobów w alertach dziennika, musisz edytować regułę alertów w portalu lub z szablonem Menedżera zasobów, aby zaktualizować zasoby o określonym zakresie. Alternatywnie można dołączyć listę zasobów w kwerendzie alertu dziennika.


![Wykres czasowy](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [analizowanie danych dziennika w usłudze Azure Monitor, aby](log-query-overview.md) uzyskać omówienie zapytań dziennika i struktury danych dziennika usługi Azure Monitor.
- Przejrzyj [zapytania dziennika usługi Azure Monitor,](query-language.md) aby wyświetlić wszystkie zasoby dla zapytań dziennika usługi Azure Monitor.
