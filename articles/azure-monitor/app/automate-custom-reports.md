---
title: Automatyzowanie raporty niestandardowe z danymi usługi Azure Application Insights
description: Automatyzacja raportów niestandardowych codziennie/tydzień/miesiąc z danymi usługi Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: f22cb620bf8cf56110bec60a4dd809066393a8ff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067657"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatyzowanie raporty niestandardowe z danymi usługi Azure Application Insights

Sprawozdania okresowe pomaga w zapewnieniu zespołu, informacje o przebiegu operacji ich krytycznych usług biznesowych. Deweloperzy, zespoły DevOps/SRE i ich menedżerami może być produktywność za pomocą raportów automatycznych, niezawodne dostarczanie szczegółowych informacji bez konieczności uświadomić im i zaloguj się w portalu. Raporty takie może pomóc w identyfikacji stopniowy wzrost w opóźnienia, obciążenie lub awaria kursów, które nie mogą wyzwalać dowolne reguły alertów.

Każde przedsiębiorstwo ma unikatowe potrzeby raportowania, takie jak: 

* Percentyl określonej agregacji metryk lub metryki niestandardowe w raporcie.
* Mają różne raporty dla codziennych, cotygodniowych i comiesięcznych rzutowania danych dla różnych grup odbiorców.
* Segmentacji według atrybutów niestandardowych, takich jak regionu lub środowiska. 
* Zgrupować niektóre zasoby sztucznej Inteligencji w ramach jednego raportu, nawet jeśli mogą być w różnych subskrypcjach lub zasób grupy itd.
* Oddzielne raporty zawierające poufne metryki wysyłane do odbiorców selektywnego.
* Raporty do uczestników projektu, którzy nie mają dostępu do zasobów w portalu.

> [!NOTE] 
> Cotygodniowe wiadomości e-mail szyfrowanego usługi Application Insights nie zezwala na dostosowywanie i nie będzie już dostępna na rzecz niestandardowe opcje, które są wymienione poniżej. Ostatnie cotygodniowe wiadomości e-mail szyfrowane będą wysyłane 11 czerwca 2018 r. Skonfiguruj jeden z poniższych opcji, aby uzyskać podobne raporty niestandardowe (Użyj zapytania sugerowane poniżej).

## <a name="to-automate-custom-report-emails"></a>Aby zautomatyzować niestandardowy raport w wiadomości e-mail

Możesz [programowo wykonać zapytanie dotyczące usługi Application Insights](https://dev.applicationinsights.io/) danych, aby generować raporty niestandardowe, zgodnie z harmonogramem. Może to pomóc szybko rozpoczniesz pracę następujące opcje:

* [Automatyzowanie raporty w usłudze Microsoft Flow](automate-with-flow.md)
* [Automatyzowanie raporty w usłudze Logic Apps](automate-with-logic-apps.md)
* Użyj "Podsumowanie zaplanowanych usługi Application Insights" [funkcji platformy Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) szablonu w tym scenariuszu monitorowanie. Ta funkcja korzysta z usługi SendGrid do dostarczania wiadomości e-mail. 

    ![Szablon funkcji platformy Azure](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Przykładowe zapytanie dla tygodniowy wiadomość e-mail z podsumowaniem
Następujące zapytanie pokazuje, łączenie między wiele zestawów danych dla cotygodniowe wiadomości e-mail szyfrowanego jak raport. Dostosowuj go zgodnie z potrzebami i używać go z jedną z opcji wymienionych powyżej w celu zautomatyzowania Raport tygodniowy.   

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

## <a name="application-insights-scheduled-digest-report"></a>Raport Podsumowanie zaplanowanych szczegółowych informacji w aplikacji

1. W witrynie Azure portal, wybierz **Utwórz zasób** > **obliczenia** > **aplikacji funkcji**.

   ![Utwórz zrzut ekranu aplikacji funkcji zasobów platformy Azure](./media/automate-custom-reports/function-app-01.png)

2. Wprowadź odpowiednie informacje o aplikacji i wybierz _Utwórz_. (Usługa application Insights _na_ jest wymagany tylko wtedy, gdy chcesz monitorować nową aplikację funkcji przy użyciu usługi Application Insights)

   ![Utwórz zrzut ekranu ustawień aplikacji funkcji zasobów platformy Azure](./media/automate-custom-reports/function-app-02.png)

3. Po ukończeniu wdrażania nowej aplikacji funkcji wybierz **przejdź do zasobu**.

4. Wybierz **nową funkcję**.

   ![Tworzenie nowego zrzutu ekranu — funkcja](./media/automate-custom-reports/function-app-03.png)

5. Wybierz  **_usługi Application Insights zaplanowane podsumowanie szablonu_** .

     > [!NOTE]
     > Domyślnie aplikacje funkcji są tworzone za pomocą środowiska uruchomieniowego w wersji 2.x. Należy najpierw [docelowa wersja środowiska uruchomieniowego usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/set-runtime-version) 1.x do użycia w usłudze Application Insights zaplanowane podsumowanie szablonu.

   ![Nowy szablon usługi Insights aplikacji funkcji zrzut ekranu](./media/automate-custom-reports/function-app-04.png)

6. Wprowadź adres e-mail adresata odpowiedni raport i wybierz **Utwórz**.

   ![Zrzut ekranu ustawień — funkcja](./media/automate-custom-reports/function-app-05.png)

7. Wybierz swoje **aplikacji funkcji** > **funkcje platformy** > **ustawienia aplikacji**.

    ![Usługa Azure zrzut ekranu ustawień aplikacji funkcji](./media/automate-custom-reports/function-app-07.png)

8. Utwórz trzy nowe ustawienia aplikacji odpowiednimi wartościami odpowiednich ``AI_APP_ID``, ``AI_APP_KEY``, i ``SendGridAPI``. Wybierz pozycję **Zapisz**.

     ![Zrzut ekranu: interfejs integracji — funkcja](./media/automate-custom-reports/function-app-08.png)
    
    (Wartości AI_ znajdują się w ramach dostępu do interfejsu API dla zasobu usługi Application Insights chcesz sporządzić raport na temat. Jeśli nie masz klucza interfejsu API usługi Application Insights jest możliwość **Utwórz klucz interfejsu API**.)
    
   * AI_APP_ID = identyfikator aplikacji
   * AI_APP_KEY = klucz interfejsu API
   * SendGridAPI = klucz interfejsu API usługi SendGrid

     > [!NOTE]
     > Jeśli nie masz konta usługi SendGrid można go utworzyć. Dokumentacja usługi SendGrid dla usługi Azure Functions jest [tutaj](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid). Jeśli jest to po prostu chcesz minimalny wyjaśnienie sposobu instalacji usługi SendGrid i wygeneruj klucz interfejsu API, który został dostarczony na końcu tego artykułu. 

9. Wybierz **integracja** i w obszarze danych wyjściowych kliknij **SendGrid ($return)** .

     ![Zrzut ekranu danych wyjściowych](./media/automate-custom-reports/function-app-09.png)

10. W obszarze **ustawienia aplikacji klucz SendGridAPI**, wybierz nowo utworzony ustawienie aplikacji w taki sposób, aby uzyskać **SendGridAPI**.

     ![Uruchom zrzut ekranu aplikacji funkcji](./media/automate-custom-reports/function-app-010.png)

11. Uruchom i przetestuj swoją aplikację funkcji.

     ![Zrzut ekranu testu](./media/automate-custom-reports/function-app-11.png)

12. Sprawdź, aby upewnić się, że komunikat pomyślnie wysłać/odebrać poczty e-mail.

     ![Zrzut ekranu wiersza tematu wiadomości e-mail](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>Usługa SendGrid z platformą Azure

Te kroki mają zastosowanie, jeśli nie masz jeszcze konta SendGrid, skonfigurowane.

1. W usłudze Azure portal wybierz pozycję **Utwórz zasób** Wyszukaj **dostarczanie wiadomości E-mail usługi SendGrid** > kliknij **Utwórz** > i wypełnić tworzyć instrukcje dotyczące usługi SendGrid. 

     ![Tworzenie zasobu usługi SendGrid zrzut ekranu](./media/automate-custom-reports/function-app-13.png)

2. Po utworzeniu w ramach kont usługi SendGrid wybierz **Zarządzaj**.

     ![Zrzut ekranu klucz interfejsu API ustawienia](./media/automate-custom-reports/function-app-14.png)

3. Spowoduje to uruchomienie witryny SendGrid. Wybierz **ustawienia** > **klucze interfejsu API**.

     ![Można tworzyć i wyświetlać zrzut ekranu aplikacji klucz interfejsu API](./media/automate-custom-reports/function-app-15.png)

4. Utwórz klucz interfejsu API > Wybierz **widoku & Utwórz** (można znaleźć w dokumentacji usługi SendGrid na ograniczony dostęp do ustalenia, jaki poziom uprawnień jest odpowiedni klucz interfejsu API. Pełny dostęp jest wybrane w tym miejscu tylko w celach demonstracyjnych.)

   ![Zrzut ekranu z pełnym dostępem](./media/automate-custom-reports/function-app-16.png)

5. Skopiuj cały klucz, wartość ta jest potrzebne w ustawieniach aplikacji funkcji z wartością dla SendGridAPI

   ![Kopiuj zrzut ekranu klucza interfejsu API](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej na temat tworzenia [zapytań analitycznych](../../azure-monitor/log-query/get-started-queries.md).
* Dowiedz się więcej o [programowe wykonywanie zapytań o dane usługi Application Insights](https://dev.applicationinsights.io/)
* Dowiedz się więcej na temat usługi [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Dowiedz się więcej o [Microsoft Flow](https://ms.flow.microsoft.com).
