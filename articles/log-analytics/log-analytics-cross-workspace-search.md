---
title: Wyszukiwanie w całym zasobów przy użyciu usługi Azure Log Analytics | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak można wykonać zapytanie względem zasobów z wielu obszarów roboczych i aplikacji usługi App Insights w ramach subskrypcji.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: magoedte
ms.openlocfilehash: e46ae3af3a718703f9e1d6b847b2342469bf3a1e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="perform-cross-resource-log-searches-in-log-analytics"></a>Wyszukiwanie zasobów między dziennika w analizy dzienników  

Wcześniej z Azure Log Analytics tylko przeanalizowanie danych z poziomu bieżący obszar roboczy, a ono ograniczone możliwości zapytań przez wiele obszarów roboczych, zdefiniowane w ramach subskrypcji.  Ponadto można przeszukiwać tylko elementy dane telemetryczne zebrane z aplikacji sieci web z usługą Application Insights bezpośrednio w usłudze Application Insights lub z programu Visual Studio.  To również go wyzwanie natywnie analizowanie operacyjne i dane aplikacji jednocześnie.   

Teraz można badać nie tylko przez wiele obszarów roboczych usługi Analiza dzienników, ale także dane z określonej aplikacji usługi Application Insights w tej samej grupie zasobów, w innej grupie zasobów lub w innej subskrypcji. Udostępnia systemowe widoku danych.  Można wykonać tylko te typy zapytań w [portal zaawansowane](log-analytics-log-search-portals.md#advanced-analytics-portal), a nie w portalu Azure.  

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Wykonywanie zapytania między obszarami roboczymi analizy dzienników i z usługi Application Insights
Aby odwołać się do innego obszaru roboczego w kwerendzie użyć [ *obszaru roboczego* ](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/workspace()) identyfikator i dla aplikacji z usługi Application Insights, użyj [ *aplikacji* ](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/app())identyfikator.  

### <a name="identifying-workspace-resources"></a>Identyfikowanie zasobów obszaru roboczego
W poniższych przykładach pokazano kwerendy między obszarami roboczymi analizy dzienników zwraca podsumowania Licznik aktualizacji z aktualizacja tabeli w obszarze roboczym o nazwie *contosoretail-it*. 

Identyfikowanie obszaru roboczego można zakończonej co kilka sposobów:

* Nazwa zasobu — jest zrozumiałą nazwę obszaru roboczego, czasami określane jako *nazwa składnika*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >Identyfikowanie obszaru roboczego według nazwy zakłada unikatowości we wszystkich dostępnych subskrypcji. Jeśli masz wiele aplikacji o określonej nazwie, zapytanie kończy się niepowodzeniem z powodu niejednoznaczności. W takim przypadku należy użyć jednego z innych identyfikatorów.

* Nazwa kwalifikowana — jest "Pełna nazwa" obszaru roboczego, zawierający nazwę subskrypcji, grupy zasobów i nazwy składnika w następującym formacie: *NazwaSkładnika-Nazwa subskrypcji/resourceGroup*. 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Ponieważ nazwy subskrypcji platformy Azure nie są unikatowe, ten identyfikator może być niejednoznaczna. 
    >

* Identyfikator obszaru roboczego - identyfikator obszaru roboczego jest unikatowy, modyfikować, identyfikator przypisany do każdego obszaru roboczego reprezentowane jako unikatowy identyfikator globalny (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure Resource ID — definicja Azure unikatową tożsamość obszaru roboczego. Identyfikator zasobu jest używany, gdy nazwa zasobu jest niejednoznaczna.  Obszary robocze, jest format: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/workspaces/NazwaSkładnika*.  

    Na przykład:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

### <a name="identifying-an-application"></a>Identyfikowanie aplikacji
Poniższe przykłady zwróciło podsumowanie liczby żądań wysyłanych z aplikacji o nazwie *fabrikamapp* w usłudze Application Insights. 

Identyfikowanie aplikacji w usłudze Application Insights można wdrożyć, stosując *app(Identifier)* wyrażenia.  *Identyfikator* argument określa aplikacji przy użyciu jednej z następujących czynności:

* Nazwa zasobu — jest człowieka dla użytkownika nazwę aplikacji, czasami określane jako *nazwa składnika*.  

    `app("fabrikamapp")`

* Nazwa kwalifikowana — jest "Pełna nazwa" aplikacja składa się z nazwy subskrypcji, grupy zasobów i nazwy składnika w następującym formacie: *NazwaSkładnika-Nazwa subskrypcji/resourceGroup*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Ponieważ nazwy subskrypcji platformy Azure nie są unikatowe, ten identyfikator może być niejednoznaczna. 
    >

* ID — identyfikator GUID aplikacji aplikacji.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure identyfikator zasobu - zdefiniowane Azure unikatową tożsamość aplikacji. Identyfikator zasobu jest używany, gdy nazwa zasobu jest niejednoznaczna. Format: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. NazwaSkładnika OperationalInsights/składniki*.  

    Na przykład:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

## <a name="next-steps"></a>Kolejne kroki

Przegląd [analizy dzienników dziennika odwołanie wyszukiwania](https://docs.loganalytics.io/docs/Language-Reference) Aby wyświetlić wszystkie dostępne w analizy dzienników Opcje składni zapytania.    