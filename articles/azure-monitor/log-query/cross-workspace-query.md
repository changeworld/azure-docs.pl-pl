---
title: Wyszukiwanie w zasobach za pomocą usługi Azure Log Analytics | Dokumentacja firmy Microsoft
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
ms.date: 11/15/2018
ms.author: magoedte
ms.openlocfilehash: 1140e9d0a9df717b8e249d8dad895baec955b754
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53192978"
---
# <a name="perform-cross-resource-log-searches-in-log-analytics"></a>Wykonaj wyszukiwanie w dzienniku między zasobami w usłudze Log Analytics  

Wcześniej przy użyciu usługi Azure Log Analytics można było tylko analizować dane z bieżącego obszaru roboczego i jego ograniczoną możliwość wysyłania zapytań w wielu obszarach roboczych zdefiniowanych w subskrypcji.  Ponadto można przeszukiwać tylko elementy danych telemetrycznych zebranych z aplikacji sieci web za pomocą usługi Application Insights bezpośrednio w usłudze Application Insights lub z programu Visual Studio.  To również on żądania do natywnie analizy operacyjnej i dane aplikacji ze sobą.   

Teraz można tworzyć zapytania nie tylko między wiele obszarów roboczych usługi Log Analytics, ale także dane z określonej aplikacji usługi Application Insights w tej samej grupie zasobów, innej grupy zasobów lub innej subskrypcji. Zapewnia widok całego systemu danych.  Można wykonać tylko te typy zapytań w [usługi Log Analytics](portals.md#log-analytics-page). Liczba zasobów (obszary robocze usługi Log Analytics i aplikacji usługi Application Insights), które można uwzględnić w ramach pojedynczego zapytania jest ograniczona do 100. 

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

    `workspace('contoso/contosoretail/contosoretail-it').Update | count `

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
Korelowanie danych z wielu usługi Log Analytics i zasoby usługi Application Insights za pomocą zapytania obejmujące wiele zasobów, zapytanie może stać się złożona i trudne w utrzymaniu. Powinny wykorzystywać [funkcji w usłudze Log Analytics](../../azure-monitor/log-query/functions.md) do oddzielania logiki zapytania od zakres zasobów kwerendy, co upraszcza strukturę zapytania. Poniższy przykład pokazuje, jak można monitorować wiele zasobów usługi Application Insights i zwizualizować liczbę żądań zakończonych niepowodzeniem według nazwy aplikacji. 

Utwórz zapytanie, podobnie do poniższego, który odwołuje się do zakresu zasobów usługi Application Insights. `withsource= SourceApp` Polecenie dodaje kolumny, która określa nazwę aplikacji, które są wysyłane dziennika. [Zapisz zapytanie jako funkcja](../../azure-monitor/log-query/functions.md#create-a-function) z aliasem _applicationsScoping_.

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
| sort by count_ desc 
| render timechart
```
![Wykres czasu](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Kolejne kroki

Przegląd [Zaloguj się odwołanie do wyszukiwania usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/kusto) Aby wyświetlić wszystkie opcje składni zapytań dostępnych w usłudze Log Analytics.    
