---
title: Automatyzowanie niestandardowych raportów z danymi Azure Application Insights
description: Automatyzowanie niestandardowych raportów codziennie/tygodniowo/miesięcznie z danymi Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: c8cff54c67ab2c9c3d09f9261617b6312cc4434a
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025818"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatyzowanie niestandardowych raportów z danymi Azure Application Insights

Raporty okresowe zapewnić zespołu świadomość, jak robią ich krytycznych usług biznesowych. Deweloperów, metodyki DevOps/SRE zespołów i ich menedżerów mogą być wydajni z automatycznych raportów niezawodnie dostarczania insights bez konieczności Wszyscy do logowania się w portalu. Raporty takie może pomóc w identyfikacji stopniowego zwiększenia w opóźnienia, obciążenia lub niepowodzenie stawki, które nie mogą wyzwalać dowolnego alertu reguły.

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
* Użyj "szyfrowanego zaplanowane usługi Application Insights" [funkcji Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) szablonu w scenariuszu monitorowanie. Funkcja ta używa SendGrid do dostarczania wiadomości e-mail. 

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

## <a name="application-insights-scheduled-digest-report"></a>Raport zaplanowane szyfrowanego Insights aplikacji

1. W portalu Azure wybierz **Utwórz zasób** > **obliczeniowe** > **aplikacji funkcji**.

   ![Utwórz zrzut ekranu aplikacji funkcji zasobów platformy Azure](./media/automate-custom-reports/function-app-01.png)

2. Wprowadź odpowiednie informacje o aplikacji i wybierz _Utwórz_. (Usługi application Insights _na_ jest wymagany tylko wtedy, gdy chcesz monitorować nowej aplikacji funkcji za pomocą usługi Application Insights)

   ![Utwórz zrzut ekranu ustawienia aplikacji funkcji zasobów platformy Azure](./media/automate-custom-reports/function-app-02.png)

3. Po zakończeniu wdrażania nowej aplikacji funkcji wybierz **przejdź do zasobu**.

4. Wybierz **nową funkcję**.

   ![Tworzenie nowego zrzutu ekranu — funkcja](./media/automate-custom-reports/function-app-03.png)

5. Wybierz  **_szablonu zaplanowane szyfrowanego usługi Application Insights_**.

   ![Nowy szablon Insights aplikacji funkcja zrzut ekranu](./media/automate-custom-reports/function-app-04.png)

6. Wprowadź adres e-mail odbiorcy właściwe dla raportu i wybierz **Utwórz**.

   ![Zrzut ekranu ustawienia funkcji](./media/automate-custom-reports/function-app-05.png)

7. Wybierz użytkownika **aplikacji funkcji** > **funkcji platformy** > **ustawienia aplikacji**.

    ![Azure zrzut ekranu ustawienia aplikacji — funkcja](./media/automate-custom-reports/function-app-07.png)

8. Utwórz trzy nowe ustawienia aplikacji z odpowiedniej wartości dla poszczególnych ``AI_APP_ID``, ``AI_APP_KEY``, i ``SendGridAPI``. Wybierz pozycję **Zapisz**.

     ![Zrzut ekranu: interfejs integracji — funkcja](./media/automate-custom-reports/function-app-08.png)
    
    (Wartości AI_ znajdują się w obszarze dostępu do interfejsu API dla zasobu usługi Application Insights uwzględnienia w raporcie. Jeśli nie masz klucz interfejsu API Application Insights, istnieje możliwość **Utwórz klucz interfejsu API**.)
    
    * AI_APP_ID = identyfikator aplikacji
    * AI_APP_KEY = klucz interfejsu API
    * SendGridAPI = SendGrid klucz interfejsu API

    > [!NOTE]
    > Jeśli nie masz konta SendGrid można go utworzyć. Jest SendGrid w dokumentacji usługi Azure Functions [tutaj](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-sendgrid). Jeśli jest to po prostu ma minimalne informacje dotyczące instalacji SendGrid i wygeneruj klucz interfejsu API, który został dostarczony na końcu tego artykułu. 

9. Wybierz **integracji** i w obszarze danych wyjściowych kliknij **SendGrid ($return)**.

     ![Zrzut ekranu danych wyjściowych](./media/automate-custom-reports/function-app-09.png)

10. W obszarze **ustawienie aplikacji klucza SendGridAPI**, wybierz nowo utworzony ustawienia aplikacji dla **SendGridAPI**.

     ![Uruchom aplikację funkcji zrzut ekranu](./media/automate-custom-reports/function-app-010.png)

11. Uruchamianie i testowanie aplikacji funkcji.

     ![Zrzut ekranu testu](./media/automate-custom-reports/function-app-11.png)

12. Sprawdź wiadomości e-mail, aby upewnić się, że komunikat został wysłany/odebrany.

     ![Zrzut ekranu wiersz tematu wiadomości e-mail](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid z platformy Azure

Ta procedura dotyczy tylko jeśli nie masz już konto SendGrid skonfigurowany.

1. Z portalu Azure wybierz opcję **Utwórz zasób** wyszukiwania **dostarczania poczty E-mail SendGrid** > kliknij **Utwórz** > i wypełnij konkretnym SendGrid tworzenia instrukcji. 

     ![Utwórz zasób SendGrid zrzut ekranu](./media/automate-custom-reports/function-app-13.png)

2. Po utworzeniu konta SendGrid wybierz **Zarządzaj**.

     ![Zrzut ekranu klucz interfejsu API ustawienia](./media/automate-custom-reports/function-app-14.png)

3. Spowoduje to uruchomienie SendGrid w lokacji. Wybierz **ustawienia** > **klucze interfejsu API**.

     ![Można tworzyć i wyświetlać zrzut ekranu aplikacji klucz interfejsu API](./media/automate-custom-reports/function-app-15.png)

4. Utwórz klucz interfejsu API > Wybierz **widoku & Utwórz** (zapoznaj się z tematem SendGrid w dokumentacji na dostęp ograniczony poziom uprawnień jest odpowiednia dla klucza interfejsu API. Pełny dostęp wybrano tutaj przykład tylko do celów.)

   ![Zrzut ekranu pełny dostęp](./media/automate-custom-reports/function-app-16.png)

5. Skopiuj cały klucz, ta wartość jest, co jest potrzebne w ustawieniach aplikacji funkcji jako wartość dla SendGridAPI

   ![Kopiowanie klucza zrzut ekranu interfejsu API](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o tworzeniu [zapytania analityczne](app-insights-analytics-using.md).
* Dowiedz się więcej o [programowane wykonywanie zapytania na danych z usługi Application Insights](https://dev.applicationinsights.io/)
* Dowiedz się więcej na temat usługi [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Dowiedz się więcej o [Microsoft Flow](https://ms.flow.microsoft.com).
