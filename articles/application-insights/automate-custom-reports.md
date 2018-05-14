---
title: Automatyzowanie niestandardowych raportów z danymi Azure Application Insights
description: Automatyzowanie niestandardowych raportów codziennie/tygodniowo/miesięcznie z danymi Azure Application Insights
services: application-insights
documentationcenter: ''
author: sdash
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: sdash
ms.openlocfilehash: 804e8c7a43d1ab16d11b6075be44599b33b46a3e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatyzowanie niestandardowych raportów z danymi Azure Application Insights

Raporty okresowe zapewnić zespołu świadomość, jak robią ich krytycznych usług biznesowych. Deweloperów, metodyki DevOps/SRE zespołów i ich menedżerów mogą być wydajni z automatycznych raportów niezawodnie dostarczania insights bez konieczności wszyscy zalogować się do portalu. Raporty takie może pomóc w identyfikacji stopniowego zwiększenia w opóźnienia, obciążenia lub niepowodzenie stawki, które nie mogą wyzwalać dowolnego alertu reguły.

Każdego przedsiębiorstwa ma unikalne potrzeby raportowania, takie jak: 

* Agregacje percentyl określonych metryk lub metryki niestandardowe w raporcie.
* Mają różne raporty dla codziennych, cotygodniowych i comiesięcznych rzutowania danych do różnych grup odbiorców.
* Segmentacja przez atrybutów niestandardowych, takich jak region lub środowiska. 
* Zgrupować niektórych zasobów AI jeden raport, nawet jeśli mogą być w różnych subskrypcji lub zasób grupy itp.
* Oddzielne raporty zawierające poufne metryki wysłany do odbiorców selektywnego.
* Raporty do uczestników, którzy nie mają dostępu do zasobów portalu.

> [!NOTE] 
> Co tydzień e-mail szyfrowanego usługi Application Insights nie zezwala na wszystkie dostosowania, a nie będzie już dostępna na rzecz niestandardowych opcji wymienionych poniżej. Ostatnią wiadomość e-mail co tydzień skrót zostanie wysłana na 11 czerwca 2018. Skonfiguruj jeden z poniższych opcji, aby uzyskać podobne raporty niestandardowe (Użyj zapytania sugerowane poniżej).

## <a name="to-automate-custom-report-emails"></a>Aby zautomatyzować raportu niestandardowego w wiadomości e-mail

Możesz [programowo zapytania usługi Application Insights](https://dev.applicationinsights.io/) danych, aby generować raporty niestandardowe, zgodnie z harmonogramem. Następujące opcje ułatwia szybkie rozpoczęcie pracy:

* [Raporty w usłudze Microsoft Flow automatyzacji](app-insights-automate-with-flow.md)
* [Automatyzowanie raporty w usłudze Logic Apps](automate-with-logic-apps.md)
* Użyj "szyfrowanego zaplanowane usługi Application Insights" [funkcji Azure](https://azure.microsoft.com/services/functions/) szablonu w scenariuszu monitorowanie. Funkcja ta używa SendGrid do dostarczania wiadomości e-mail. 

    ![Szablon funkcji platformy Azure](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Przykładowe zapytanie dla tygodniowy wiadomość e-mail z podsumowaniem
Następujące zapytanie zawiera dołączenie przez wiele zestawów danych, co tydzień e-mail szyfrowanego jak raport. Dostosuj go zgodnie z potrzebami i używać go z dowolnymi spośród opcji wymienionych powyżej można zautomatyzować Raport tygodniowy.   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

  
## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o tworzeniu [zapytania analityczne](app-insights-analytics-using.md).
- Dowiedz się więcej o [programowane wykonywanie zapytania na danych z usługi Application Insights](https://dev.applicationinsights.io/)
- Dowiedz się więcej na temat usługi [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
- Dowiedz się więcej o [Microsoft Flow](https://ms.flow.microsoft.com).


