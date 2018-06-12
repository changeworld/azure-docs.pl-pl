---
title: Automatyzowanie procesów Azure Application Insights z Flow firmy Microsoft
description: Dowiedz się, jak można użyć Microsoft Flow można szybko zautomatyzować powtarzalnych procesów za pomocą łącznika usługi Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2017
ms.author: mbullwin
ms.openlocfilehash: c92b50ee78c100a531a2973181251444b3810967
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293777"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Zautomatyzować procesy Azure Application Insights z łącznikiem Flow firmy Microsoft

Czy zaczniesz wielokrotnego uruchamiania tej samej zapytań na podstawie danych telemetrii, aby sprawdzić, czy usługa działa prawidłowo? Szukasz zautomatyzować te zapytania do znajdowania trendów i anomalii i późniejszego kompilowania własne przepływy pracy wokół nich? Łącznik usługi Azure Application Insights (wersja zapoznawcza) dla Flow firmy Microsoft jest właściwych narzędzi dla tych celów.

Dzięki tej integracji teraz można zautomatyzować wiele procesów bez pisania pojedynczy wiersz kodu. Po utworzeniu przepływu za pomocą akcji usługi Application Insights przepływ automatycznie uruchamia kwerendy analizy wgląd w aplikacji. 

Można dodać również dodatkowe akcje. Microsoft Flow udostępnia setki akcje. Na przykład można użyć Microsoft Flow można automatycznie wysłać wiadomość e-mail z powiadomieniem lub tworzenie usterki w programie Visual Studio Team Services. Można także użyć jednego z wielu [szablony](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) dostępnych dla łącznika Microsoft Flow. Te szablony przyspieszyć proces tworzenia przepływu. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Utwórz strumień dla usługi Application Insights

Z tego samouczka dowiesz się, jak utworzyć przepływ, który używa algorytmu klastra automatycznie Analytics grupy atrybutów w danych dla aplikacji sieci web. Przepływ automatycznie wysyła wyniki za pośrednictwem poczty e-mail, tylko jeden przykład sposobu użycia Flow firmy Microsoft i analizy aplikacji Insights ze sobą. 

### <a name="step-1-create-a-flow"></a>Krok 1: Tworzenie przepływu
1. Zaloguj się do [Microsoft Flow](http://flow.microsoft.com), a następnie wybierz **Moje przepływa**.
2. Kliknij przycisk **Utwórz przepływem na podstawie puste**.

### <a name="step-2-create-a-trigger-for-your-flow"></a>Krok 2: Tworzenie wyzwalacza dla Twojego przepływu
1. Wybierz **harmonogram**, a następnie wybierz **harmonogram - cyklu**.
2. W **częstotliwość** wybierz opcję **dzień**, a następnie w **interwał** wprowadź **1**.

    ![Okno dialogowe Microsoft Flow wyzwalacza](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-application-insights-action"></a>Krok 3: Dodaj akcję usługi Application Insights
1. Kliknij przycisk **nowy krok**, a następnie kliknij przycisk **Dodaj akcję**.
2. Wyszukaj **Azure Application Insights**.
3. Kliknij przycisk **Azure Application Insights — wizualizacji analizy zapytania Podgląd**.

    ![Uruchom okno kwerendy analityka](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Krok 4: Łączenie z zasobu usługi Application Insights

Aby ukończyć ten krok, należy identyfikator i klucz interfejsu API dla zasobu. Można je pobrać z portalu Azure, jak pokazano na poniższym diagramie:

![Identyfikator aplikacji w portalu Azure](./media/app-insights-automate-with-flow/appid.png) 

- Podaj nazwę dla połączenia, wraz z kluczem interfejsów API i identyfikator aplikacji.

    ![Okno połączenia Flow firmy Microsoft](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Krok 5: Określ typ zapytania i wykres analizy
To przykładowe zapytanie wybiera żądań zakończonych niepowodzeniem w ciągu ostatniego dnia i są powiązane z nimi wyjątków, które wystąpiły w ramach operacji. Analiza są powiązane z nimi na podstawie identyfikatora operation_Id. Zapytanie następnie segmentów wyników za pomocą algorytmu autocluster. 

Podczas tworzenia własnych zapytań, sprawdź, czy działają prawidłowo w module analiz przed dodaniem go do Twojego przepływu.

- Dodaj następujące zapytanie Analytics, a następnie wybierz typ wykresu tabeli HTML. 

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```
    
    ![Okno konfiguracji kwerendy analityka](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Krok 6: Konfigurowanie przepływu do wysyłania wiadomości e-mail

1. Kliknij przycisk **nowy krok**, a następnie kliknij przycisk **Dodaj akcję**.
2. Wyszukaj **Outlook pakietu Office 365**.
3. Kliknij przycisk **Office 365 Outlook — Wyślij usługi poczty e-mail**.

    ![Okno wyboru Outlook pakietu Office 365](./media/app-insights-automate-with-flow/flow2b.png)

4. W **wysłać wiadomość e-mail** okna, wykonaj następujące czynności:

   a. Wpisz adres e-mail odbiorcy.

   b. Wpisz temat wiadomości e-mail.

   c. Kliknij w dowolnym miejscu **treści** polu, a następnie na dynamicznej zawartości wyświetlonym menu z prawej strony, wybierz **treści**.

   d. Kliknij przycisk **Pokaż zaawansowane opcje**.

    ![Konfiguracja programu Outlook pakietu Office 365](./media/app-insights-automate-with-flow/flow5.png)

5. W menu zawartości dynamicznej wykonaj następujące czynności:

    a. Wybierz **Nazwa załącznika**.

    b. Wybierz **zawartości załącznika**.
    
    c. W **HTML jest** wybierz opcję **tak**.

    ![Okno Konfiguracja poczty e-mail usługi Office 365](./media/app-insights-automate-with-flow/flow7.png)

### <a name="step-7-save-and-test-your-flow"></a>Krok 7: Zapisz i testowania przepływu użytkownika
- W **nazwy przepływu** , Dodaj nazwę użytkownika przepływ, a następnie kliknij przycisk **utworzyć przepływ**.

    ![Tworzenie przepływu okna](./media/app-insights-automate-with-flow/flow8.png)

Możesz poczekać wyzwalacz do uruchomienia tej akcji, lub uruchomić przepływ natychmiast przez [systemem wyzwalacz na żądanie](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

Po uruchomieniu przepływu określone na liście e-mail adresatów otrzymywać wiadomości e-mail, która wygląda następująco:

![Przykładowej wiadomości e-mail](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o tworzeniu [zapytania analityczne](app-insights-analytics-using.md).
- Dowiedz się więcej o [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





