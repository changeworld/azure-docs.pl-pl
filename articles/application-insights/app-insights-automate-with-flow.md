---
title: Automatyzacja procesów usługi Azure Application Insights przy użyciu Microsoft Flow
description: Dowiedz się, jak Microsoft Flow umożliwia szybkie zautomatyzować powtarzalnych procesów za pomocą łącznika usługi Application Insights.
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
ms.openlocfilehash: 449a6274b67f3eb72ea6d8bd19f555fc59158d7e
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299104"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Zautomatyzować procesy usługi Azure Application Insights za pomocą łącznika usługi Microsoft Flow

Czy masz ochotę wielokrotnie systemem tego samego zapytania dane telemetryczne, aby sprawdzić, czy usługa działa właściwie? Czy chcesz zautomatyzować te zapytania służące do znajdowania trendów i anomalii, a następnie utworzyć własne przepływy pracy wokół nich? Łącznik usługi Azure Application Insights (wersja zapoznawcza) dla Microsoft Flow jest właściwych narzędzi do tych celów.

Dzięki tej integracji możesz teraz zautomatyzować wiele procesów bez konieczności pisania nawet jednego wiersza kodu. Po utworzeniu przepływu za pomocą akcji usługi Application Insights, przepływ automatycznie uruchamia zapytania analizy usługi Application Insights. 

Możesz dodać również dodatkowe akcje. Microsoft Flow jest udostępnienie kilkuset akcji. Na przykład można użyć Microsoft Flow automatycznie wysyłać wiadomość e-mail z powiadomieniem lub Utwórz usterkę w DevOps platformy Azure. Można także użyć jednego z wielu [szablony](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) dostępnych dla łącznika dla Microsoft Flow. Te szablony przyspieszyć proces tworzenia przepływu. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Tworzenie przepływu dla usługi Application Insights

W tym samouczku dowiesz się, jak utworzyć przepływ, który używa algorytmu klastra automatycznie Analytics do grupy atrybutów danych dla aplikacji sieci web. Przepływ automatycznie wysyła wyniki za pośrednictwem poczty e-mail, tylko jeden przykład sposobu użycia usługi Microsoft Flow i Application Insights — analiza ze sobą. 

### <a name="step-1-create-a-flow"></a>Krok 1: Tworzenie przepływu
1. Zaloguj się do [Microsoft Flow](http://flow.microsoft.com), a następnie wybierz pozycję **moje przepływy**.
1. Kliknij przycisk **Tworzenie przepływu od podstaw**.

### <a name="step-2-create-a-trigger-for-your-flow"></a>Krok 2: Tworzenie wyzwalacza przepływu
1. Wybierz **harmonogram**, a następnie wybierz pozycję **harmonogram — cyklicznie**.
1. W **częstotliwość** wybierz opcję **dzień**, a następnie w **interwał** wprowadź **1**.

    ![Okno dialogowe wyzwalacz Microsoft Flow](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-application-insights-action"></a>Krok 3: Dodaj akcję usługi Application Insights
1. Kliknij przycisk **nowy krok**, a następnie kliknij przycisk **Dodaj akcję**.
1. Wyszukaj **usługi Application Insights**.
1. Kliknij przycisk **usługi Azure Application Insights — analiza wizualizacji zapytań (wersja zapoznawcza)**.

    ![Uruchom okno zapytania usługi Analytics](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Krok 4: Łączenie z zasobem usługi Application Insights

Aby ukończyć ten krok, należy identyfikator i klucz interfejsu API zasobu bazy danych. Można je pobrać w witrynie Azure portal, jak pokazano na poniższym diagramie:

![Identyfikator aplikacji w witrynie Azure portal](./media/app-insights-automate-with-flow/appid.png) 

- Podaj nazwę połączenia, wraz z kluczem identyfikator i interfejsu API aplikacji.

    ![Okno połączenia Microsoft Flow](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Krok 5: Określ typ zapytania i wykres analizy
To zapytanie przykładowe wybiera żądań zakończonych niepowodzeniem w ciągu ostatniego dnia i jest ich skorelowana z wyjątków, które wystąpiły w ramach operacji. Analiza koreluje je na podstawie identyfikatora operation_Id. Zapytanie, a następnie segmentów wyników przy użyciu algorytmu autocluster. 

Podczas tworzenia własnych zapytań, sprawdź, czy działają prawidłowo w usłudze Analytics przed dodaniem go do przepływu.

- Dodaj następujące zapytanie usługi Analytics, a następnie wybierz typ wykresu tabeli HTML. 

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
    
    ![Okno konfiguracji zapytania usługi Analytics](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Krok 6. Konfigurowanie przepływu do wysyłania wiadomości e-mail

1. Kliknij przycisk **nowy krok**, a następnie kliknij przycisk **Dodaj akcję**.
1. Wyszukaj **usługi Office 365 Outlook**.
1. Kliknij przycisk **usługi Office 365 Outlook — Wyślij wiadomość e-mail**.

    ![Okno wyboru programu Outlook usługi Office 365](./media/app-insights-automate-with-flow/flow2b.png)

1. W **Wyślij wiadomość e-mail** okna, wykonaj następujące czynności:

   a. Wpisz adres e-mail adresata.

   b. Wpisz temat wiadomości e-mail.

   c. Kliknij w dowolnym miejscu **treści** polu, a następnie w menu zawartości dynamicznej, zostanie otwarty po prawej stronie, wybierz **treści**.

   d. Kliknij przycisk **Pokaż opcje zaawansowane**.

    ![Konfiguracja programu Outlook usługi Office 365](./media/app-insights-automate-with-flow/flow5.png)

1. W menu zawartości dynamicznej wykonaj następujące czynności:

    a. Wybierz **nazwą załącznika**.

    b. Wybierz **zawartość załącznika**.
    
    c. W **HTML jest** wybierz opcję **tak**.

    ![Okna konfiguracji poczty e-mail usługi Office 365](./media/app-insights-automate-with-flow/flow7.png)

### <a name="step-7-save-and-test-your-flow"></a>Krok 7: Zapisywanie i przetestowanie przepływu
- W **Nazwa przepływu** , Dodaj nazwę przepływowi, a następnie kliknij przycisk **Utwórz przepływ**.

    ![Tworzenie przepływu okna](./media/app-insights-automate-with-flow/flow8.png)

Możesz poczekać, aż wyzwalacz, aby uruchomić tej akcji lub uruchamianie przepływu w natychmiast przez [uruchamianie wyzwalacza na żądanie](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

Przebieg przepływu adresatów, do których określony na liście e-mail otrzymywać wiadomości e-mail, która wygląda podobnie do poniższego:

![Przykładowa wiadomość e-mail wysłana](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej na temat tworzenia [zapytań analitycznych](app-insights-analytics-using.md).
- Dowiedz się więcej o [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





