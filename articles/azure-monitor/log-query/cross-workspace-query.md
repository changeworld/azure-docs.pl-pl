---
title: Zapytania w zasobach za pomocą usługi Azure Monitor | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak wykonać zapytanie względem zasobów z wielu obszarów roboczych i aplikacji usługi App Insights w Twojej subskrypcji.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: magoedte
ms.openlocfilehash: 51645f4f0c6dcc70d76ed1a20bc40f95db9d9717
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693363"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Wykonywać zapytania obejmujące wiele zasobów dzienników w usłudze Azure Monitor  

Wcześniej z usługą Azure Monitor, można było tylko analizować dane z bieżącego obszaru roboczego i jego ograniczoną możliwość wysyłania zapytań w wielu obszarach roboczych zdefiniowanych w subskrypcji.  Ponadto można przeszukiwać tylko elementy danych telemetrycznych zebranych z aplikacji sieci web za pomocą usługi Application Insights bezpośrednio w usłudze Application Insights lub z programu Visual Studio. To również on żądania do natywnie analizy operacyjnej i dane aplikacji ze sobą.   

Teraz można tworzyć zapytania nie tylko między wiele obszarów roboczych usługi Log Analytics, ale także dane z określonej aplikacji usługi Application Insights w tej samej grupie zasobów, innej grupy zasobów lub innej subskrypcji. Zapewnia widok całego systemu danych. Można wykonać tylko te typy zapytań w [usługi Log Analytics](portals.md).

## <a name="cross-resource-query-limits"></a>Limity zapytania obejmujące wiele zasobów 

* Liczba zasobów usługi Application Insights i obszary robocze usługi Log Analytics, które można uwzględnić w ramach pojedynczego zapytania jest ograniczona do 100.
* Zapytania obejmujące wiele zasobów nie jest obsługiwana w Projektancie widoku. Można tworzyć zapytania w usłudze Log Analytics i przypiąć go do pulpitu nawigacyjnego platformy Azure i [wizualizacja przeszukiwania dzienników](../../azure-monitor/learn/tutorial-logs-dashboards.md#visualize-a-log-search). 
* Zapytania obejmujące wiele zasobów w alertów dzienników jest obsługiwana w nowym [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Domyślnie używa usługi Azure Monitor [interfejsu API starszych Log Analytics alertu](../platform/api-alerts.md) do tworzenia reguł alertów nowy dziennik z witryny Azure portal, jeśli nie możesz przejść z wersji [starszej wersji interfejsu API z alertów dziennika](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Po przełączniku nowy interfejs API staje się domyślnie na nowe reguły alertów w witrynie Azure portal i umożliwia tworzenie dziennika alertów reguły zapytania obejmujące wiele zasobów. Można tworzyć zapytania obejmujące wiele zasobów dziennika reguł alertów bez wprowadzania przełącznik przy użyciu [szablon ARM scheduledQueryRules interfejsu API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) — ale tę regułę alertu jest łatwe w zarządzaniu jednak [scheduledQueryRules interfejsu API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) a nie z witryny Azure portal.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Wykonywanie zapytań w obszarach roboczych usługi Log Analytics i z usługi Application Insights
Aby odwoływać się do innego obszaru roboczego w zapytaniu, należy użyć [ *obszaru roboczego* ](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) identyfikatora w przypadku aplikacji z usługi Application Insights, użyj [ *aplikacji* ](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression)identyfikatora.  

### <a name="identifying-workspace-resources"></a>Identyfikowanie zasobów obszaru roboczego
W poniższych przykładach pokazano zapytania w obszarach roboczych usługi Log Analytics, aby zwrócić podsumowania liczby dzienników z tabeli aktualizacji w obszarze roboczym o nazwie *contosoretail it*. 

Identyfikowanie obszar roboczy można wykonać jeden z kilku sposobów:

* Nazwa zasobu — jest zrozumiałą nazwę obszaru roboczego, czasami nazywane *nazwa składnika*. 

    `workspace("contosoretail-it").Update | count`
 
    >[!NOTE]
    >Identyfikowanie obszar roboczy o nazwie zakłada unikatowości we wszystkich subskrypcjach dostępne. Jeśli masz wiele aplikacji o określonej nazwie, zapytanie kończy się niepowodzeniem z powodu niejednoznaczności. W takim przypadku należy użyć jednego z innych identyfikatorów.

* Kwalifikowana nazwa - jest "Pełna nazwa" w obszarze roboczym składa się z nazwy subskrypcji, grupy zasobów i nazwy składnika w następującym formacie: *componentName-subscriptionName/resourceGroup*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Ponieważ nazwy subskrypcji platformy Azure nie są unikatowe, ten identyfikator może być niejednoznaczna. 
    >

* Identyfikator obszaru roboczego — identyfikator obszaru roboczego jest unikatowy, niemodyfikowalny identyfikator przypisany do każdego obszaru roboczego, reprezentowane jako unikatowy identyfikator globalny (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Usługa Azure Resource ID — zdefiniowane Azure unikatowa tożsamość obszaru roboczego. Za pomocą Identyfikatora zasobu Nazwa zasobu jest niejednoznaczna.  W przypadku obszarów roboczych, format to: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. Obszary robocze/OperationalInsights/componentName*.  

    Na przykład:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identyfikowanie aplikacji
Poniższe przykłady zwrócić podsumowane liczbę żądania skierowanego do aplikacji o nazwie *fabrikamapp* w usłudze Application Insights. 

Identyfikowanie aplikacji w usłudze Application Insights można osiągnąć za pomocą *app(Identifier)* wyrażenia.  *Identyfikator* argument określa aplikacji przy użyciu jednej z następujących czynności:

* Nazwa zasobu — jest ludzi czytelna Nazwa aplikacji, czasami nazywane *nazwa składnika*.  

    `app("fabrikamapp")`

* Kwalifikowana nazwa - nazywa się "pełne" aplikacja składa się z nazwy subskrypcji, grupy zasobów i nazwy składnika w następującym formacie: *componentName-subscriptionName/resourceGroup*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Ponieważ nazwy subskrypcji platformy Azure nie są unikatowe, ten identyfikator może być niejednoznaczna. 
    >

* ID — identyfikator GUID aplikacji aplikacji.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Usługa Azure identyfikator zasobu - zdefiniowane Azure unikatowa tożsamość aplikacji. Za pomocą Identyfikatora zasobu Nazwa zasobu jest niejednoznaczna. Format to: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. Składniki/OperationalInsights/componentName*.  

    Na przykład:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Wykonywanie zapytania w wielu zasobach
Można tworzyć zapytania wielu zasobów za pomocą dowolnego wystąpienia zasobów, mogą to być obszarów roboczych i aplikacji w połączeniu.
    
Przykład zapytania w dwóch obszarach roboczych:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Przy użyciu zapytania obejmujące wiele zasobów dla wielu zasobów
Korelowanie danych z wielu obszarów roboczych usługi Log Analytics i zasoby usługi Application Insights za pomocą zapytania obejmujące wiele zasobów, zapytanie może stać się złożona i trudne w utrzymaniu. Powinny wykorzystywać [zapytania dziennika usługi functions w usłudze Azure Monitor](functions.md) do oddzielania logiki zapytania od zakres zasobów kwerendy, co upraszcza strukturę zapytania. Poniższy przykład pokazuje, jak można monitorować wiele zasobów usługi Application Insights i zwizualizować liczbę żądań zakończonych niepowodzeniem według nazwy aplikacji. 

Utwórz zapytanie, podobnie do poniższego, który odwołuje się do zakresu zasobów usługi Application Insights. `withsource= SourceApp` Polecenie dodaje kolumny, która określa nazwę aplikacji, które są wysyłane dziennika. [Zapisz zapytanie jako funkcja](functions.md#create-a-function) z aliasem _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Możesz teraz [funkcja ta](../../azure-monitor/log-query/functions.md#use-a-function) zapytania obejmujące wiele zasobów, podobnie do poniższego. Alias funkcji _applicationsScoping_ zwraca złożenie tabeli żądań z określonych aplikacji. Zapytanie, a następnie filtruje dla żądań zakończonych niepowodzeniem i umożliwia wizualizowanie trendów przez aplikację. _Przeanalizować_ operator jest opcjonalny w tym przykładzie. Wyodrębnia nazwę aplikacji z _SourceApp_ właściwości.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```
![Wykres czasu](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Kolejne kroki

- Przegląd [Analizuj dane dzienników w usłudze Azure Monitor](log-query-overview.md) Przegląd dziennika zapytań i struktury danych dzienników usługi Azure Monitor.
- Przegląd [zapytań dzienników usługi Azure Monitor](query-language.md) do wyświetlania wszystkich zasobów usługi Azure Monitor dziennika zapytań.
