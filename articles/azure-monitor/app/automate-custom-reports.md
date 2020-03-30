---
title: Automatyzacja raportów niestandardowych za pomocą danych usługi Azure Application Insights
description: Automatyzacja niestandardowych raportów dziennych/tygodniowych/miesięcznych za pomocą danych usługi Azure Application Insights
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: d91595a863901fcc420611ac644c7856e74320dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655127"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatyzacja raportów niestandardowych za pomocą danych usługi Azure Application Insights

Raporty okresowe pomagają zespołowi informować o tym, jak radzą sobie ich usługi o znaczeniu krytycznym. Deweloperzy, zespoły DevOps/SRE i ich menedżerowie mogą być produktywni dzięki zautomatyzowanym raportom niezawodnie dostarczającym szczegółowe informacje bez konieczności logowania się do portalu przez wszystkich. Takie raporty mogą również pomóc w określeniu stopniowego wzrostu opóźnień, obciążenia lub szybkości awarii, które mogą nie powodować żadnych reguł alertów.

Każde przedsiębiorstwo ma swoje unikalne potrzeby w zakresie raportowania, takie jak: 

* Określone agregacje percentylu metryk lub metryki niestandardowe w raporcie.
* Mieć różne raporty dla dziennych, tygodniowych i miesięcznych zestawień danych dla różnych odbiorców.
* Segmentacja według atrybutów niestandardowych, takich jak region lub środowisko. 
* Pogrupuj niektóre zasoby SI razem w jednym raporcie, nawet jeśli mogą one znajdować się w różnych subskrypcjach lub grupach zasobów itp.
* Oddzielne raporty zawierające poufne dane wysyłane do wybranych odbiorców.
* Raporty dla zainteresowanych stron, które mogą nie mieć dostępu do zasobów portalu.

> [!NOTE] 
> Cotygodniowy biuletyn poczty e-mail skrótu usługi Application Insights nie zezwalał na żadne dostosowywanie i zostanie wycofany na rzecz opcji niestandardowych wymienionych poniżej. Ostatni cotygodniowy e-mail zostanie wysłany 11 czerwca 2018 roku. Skonfiguruj jedną z następujących opcji, aby uzyskać podobne raporty niestandardowe (użyj zapytania sugerowanego poniżej).

## <a name="to-automate-custom-report-emails"></a>Aby zautomatyzować niestandardowe wiadomości e-mail z raportami

Można [programowo wysyłać zapytania](https://dev.applicationinsights.io/) do danych usługi Application Insights, aby generować raporty niestandardowe zgodnie z harmonogramem. Następujące opcje mogą pomóc w szybkim rozpoczęciu pracy:

* [Automatyzuj raporty za pomocą usługi Microsoft Flow](automate-with-flow.md)
* [Automatyzuj raporty za pomocą aplikacji logiki](automate-with-logic-apps.md)
* Użyj szablonu funkcji platformy [Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) "Usługa application insights scheduled digest" w scenariuszu monitorowania. Ta funkcja używa SendGrid do dostarczania wiadomości e-mail. 

    ![Szablon funkcji platformy Azure](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Przykładowa kwerenda dla cotygodniowej wiadomości e-mail z podsumowaniem
Poniższa kwerenda pokazuje łączenie między wieloma zestawami danych dla cotygodniowego raportu popisowego, takiego jak raport. Dostosuj go zgodnie z wymaganiami i użyj dowolnej z opcji wymienionych powyżej, aby zautomatyzować cotygodniowy raport.   

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

## <a name="application-insights-scheduled-digest-report"></a>Raport zaplanowanego skrótu usługi Application Insights

1. W witrynie Azure portal wybierz pozycję Utwórz**aplikację funkcji obliczeniowej****Compute** >  **zasobów** > .

   ![Zrzut ekranu przedstawiający tworzenie aplikacji funkcji zasobów platformy Azure](./media/automate-custom-reports/function-app-01.png)

2. Wprowadź odpowiednie informacje dla aplikacji i wybierz pozycję _Utwórz_. (Usługa Application Insights _On_ jest wymagana tylko wtedy, gdy chcesz monitorować nową aplikację funkcji za pomocą usługi Application Insights)

   ![Zrzut ekranu przedstawiający tworzenie ustawień aplikacji funkcji zasobów platformy Azure](./media/automate-custom-reports/function-app-02.png)

3. Po zakończeniu wdrażania nowej aplikacji funkcji wybierz pozycję **Przejdź do zasobu**.

4. Wybierz **pozycję Nowa funkcja**.

   ![Tworzenie nowego zrzutu ekranu funkcji](./media/automate-custom-reports/function-app-03.png)

5. Wybierz **_szablon zaplanowanego skrótu usługi Application Insights_**.

     > [!NOTE]
     > Domyślnie aplikacje funkcji są tworzone w wersji 2.x środowiska wykonawczego. Aby użyć szablonu zaplanowanego skrótu usługi Application Insights, należy [kierować](https://docs.microsoft.com/azure/azure-functions/set-runtime-version) witrynę Azure Functions w wersji **1.x.**  ![zrzut ekranu ze środowiska uruchomieniowego](./../../../includes/media/functions-view-update-version-portal/function-app-view-version.png)



   ![Zrzut ekranu przedstawiający szablon nowego szablonu aplikacji funkcji](./media/automate-custom-reports/function-app-04.png)

6. Wprowadź odpowiedni adres e-mail adresata raportu i wybierz pozycję **Utwórz**.

   ![Zrzut ekranu przedstawiający ustawienia funkcji](./media/automate-custom-reports/function-app-05.png)

7. Wybierz pozycję**platformy** >  **aplikacji** > funkcji**ustawienia aplikacji**.

    ![Zrzut ekranu przedstawiający ustawienia aplikacji funkcji platformy Azure](./media/automate-custom-reports/function-app-07.png)

8. Utwórz trzy nowe ustawienia ``AI_APP_ID``aplikacji ``AI_APP_KEY``z ``SendGridAPI``odpowiednimi odpowiednimi wartościami , i . Wybierz **pozycję Zapisz**.

     ![Zrzut ekranu przedstawiający interfejs integracji funkcji](./media/automate-custom-reports/function-app-08.png)
    
    (Wartości AI_ można znaleźć w obszarze Dostęp interfejsu API dla zasobu usługi Application Insights, który chcesz zgłosić. Jeśli nie masz klucza interfejsu API usługi Application Insights, istnieje opcja **tworzenia klucza interfejsu API.)**
    
   * AI_APP_ID = Identyfikator aplikacji
   * AI_APP_KEY = Klucz API
   * SendGridAPI =Klucz interfejsu API SendGrid

     > [!NOTE]
     > Jeśli nie masz konta SendGrid, możesz je utworzyć. Dokumentacja SendGrid dla usługi Azure Functions jest [tutaj](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid). Jeśli chcesz tylko minimalne wyjaśnienie, jak skonfigurować SendGrid i wygenerować klucz interfejsu API jeden jest pod koniec tego artykułu. 

9. Wybierz **pozycję Zintegruj** i w obszarze Wyjścia kliknij **pozycję SendGrid ($return).**

     ![Zrzut ekranu przedstawiający dane wyjściowe](./media/automate-custom-reports/function-app-09.png)

10. W obszarze **Ustawienie aplikacji klucza SendGridAPI**wybierz nowo utworzone ustawienie aplikacji dla **sendgridAPI**.

     ![Uruchom aplikację funkcji zrzut ekranu](./media/automate-custom-reports/function-app-010.png)

11. Uruchom i przetestuj aplikację funkcji.

     ![Zrzut ekranu przedstawiający test](./media/automate-custom-reports/function-app-11.png)

12. Sprawdź swój adres e-mail, aby potwierdzić, że wiadomość została wysłana/odebrana pomyślnie.

     ![Zrzut ekranu wiersza tematu wiadomości e-mail](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid z platformą Azure

Te kroki mają zastosowanie tylko wtedy, gdy nie masz jeszcze skonfigurowanego konta SendGrid.

1. Z witryny Azure portal wybierz **Utwórz** wyszukiwanie zasobów **sendgrid dostarczania wiadomości e-mail** > kliknij przycisk **Utwórz** > i wypełnić SendGrid określonych instrukcji tworzenia. 

     ![Utwórz zasób SendGrid Zrzut z ekranu](./media/automate-custom-reports/function-app-13.png)

2. Po utworzeniu w obszarze Konta SendGrid wybierz pozycję **Zarządzaj**.

     ![Zrzut ekranu przedstawiający klucz interfejsu API ustawień](./media/automate-custom-reports/function-app-14.png)

3. Spowoduje to uruchomienie witryny SendGrid. Wybierz pozycję **Ustawienia** > **klawiszy INTERFEJSU API**.

     ![Zrzut ekranu przedstawiający aplikację do tworzenia i wyświetlania klucza interfejsu API](./media/automate-custom-reports/function-app-15.png)

4. Utwórz klucz interfejsu API > wybierz pozycję **Utwórz widok &** (zapoznaj się z dokumentacją SendGrid dotyczącą ograniczonego dostępu, aby ustalić, jaki poziom uprawnień jest odpowiedni dla klucza interfejsu API. Pełny dostęp jest zaznaczony tutaj tylko na przykładowe cele.)

   ![Pełny zrzut ekranu dostępu](./media/automate-custom-reports/function-app-16.png)

5. Skopiuj cały klucz, ta wartość jest tym, czego potrzebujesz w ustawieniach aplikacji funkcji jako wartość sendgridAPI

   ![Kopiowanie klucza interfejsu API — zrzut ekranu](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o tworzeniu [zapytań Analytics](../../azure-monitor/log-query/get-started-queries.md).
* Dowiedz się więcej o [programowym wyszukiwaniu danych usługi Application Insights](https://dev.applicationinsights.io/)
* Dowiedz się więcej na temat usługi [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Dowiedz się więcej o [usłudze Microsoft Flow](https://ms.flow.microsoft.com).
