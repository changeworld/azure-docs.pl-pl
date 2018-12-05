---
title: Szybko zautomatyzuj procesy usługi Azure Application Insights za pomocą aplikacji logiki.
description: Dowiedz się, jak szybko można zautomatyzować powtarzalnych procesów, dodanie łącznika usługi Application Insights do aplikacji logiki.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: mbullwin
ms.openlocfilehash: df7d709b51c864c38b9603d2e8ed02d41217bcfc
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879717"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatyzacja procesów usługi Application Insights za pomocą usługi Logic Apps

Czy masz ochotę wielokrotnie systemem tego samego zapytań dotyczących danych telemetrii, aby sprawdzić, czy usługa działa prawidłowo? Czy chcesz zautomatyzować te zapytania służące do znajdowania trendów i anomalii, a następnie utworzyć własne przepływy pracy wokół nich? Łącznik usługi Azure Application Insights (wersja zapoznawcza) dla usługi Logic Apps jest właściwych narzędzi do tego celu.

Dzięki tej integracji można zautomatyzować wiele procesów, bez konieczności pisania nawet jednego wiersza kodu. Można utworzyć aplikację logiki, za pomocą łącznika usługi Application Insights, szybko zautomatyzować dowolny proces usługi Application Insights. 

Możesz dodać również dodatkowe akcje. Funkcja Logic Apps w usłudze Azure App Service udostępnia setki akcji. Na przykład za pomocą aplikacji logiki, możesz automatycznie wysyłać wiadomość e-mail z powiadomieniem lub Utwórz usterkę w DevOps platformy Azure. Można także użyć jednego z wielu dostępnych [szablony](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) można przyspieszyć proces tworzenia aplikacji logiki. 

## <a name="create-a-logic-app-for-application-insights"></a>Tworzenie aplikacji logiki dla usługi Application Insights

W tym samouczku dowiesz się, jak utworzyć aplikację logiki, która używa algorytmu autocluster Analytics do grupy atrybutów danych dla aplikacji sieci web. Przepływ automatycznie wysyła wyniki za pośrednictwem poczty e-mail, tylko jeden przykład sposobu korzystania z usługi Application Insights Analytics i Logic Apps ze sobą. 

### <a name="step-1-create-a-logic-app"></a>Krok 1: Tworzenie aplikacji logiki
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Kliknij przycisk **Utwórz zasób**, wybierz opcję **sieci Web i mobilność**, a następnie wybierz pozycję **aplikacji logiki**.

    ![Nowe okno aplikacji logiki](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Krok 2: Tworzenie wyzwalacza aplikacji logiki
1. W **Projektant aplikacji logiki** okna, w obszarze **Rozpocznij od typowego wyzwalacza**, wybierz opcję **cyklu**.

    ![Okno projektanta aplikacji logiki](./media/automate-with-logic-apps/logicapp2.png)

1. W **częstotliwość** wybierz opcję **dzień** a następnie w **interwał** wpisz **1**.

    ![Projektant aplikacji logiki "Cykl" okna](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-application-insights-action"></a>Krok 3: Dodaj akcję usługi Application Insights
1. Kliknij przycisk **nowy krok**, a następnie kliknij przycisk **Dodaj akcję**.

1. W **wybierz akcję** pola wyszukiwania, typ **usługi Azure Application Insights**.

1. W obszarze **akcje**, kliknij przycisk **Azure Application Insights — analiza wizualizacji zapytań (wersja zapoznawcza)**.

    ![Okna "Wybierz akcję" Projektant aplikacji logiki](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Krok 4: Łączenie z zasobem usługi Application Insights

Aby ukończyć ten krok, należy identyfikator i klucz interfejsu API zasobu bazy danych. Można je pobrać w witrynie Azure portal, jak pokazano na poniższym diagramie:

![Identyfikator aplikacji w witrynie Azure portal](./media/automate-with-logic-apps/appid.png) 

Podaj nazwę połączenia, identyfikator i klucz interfejsu API.

![Okno połączenia przepływu Projektant aplikacji logiki](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Krok 5: Określ typ zapytania i wykres analizy
W poniższym przykładzie zapytanie wybiera żądań zakończonych niepowodzeniem w ciągu ostatniego dnia i jest ich skorelowana z wyjątków, które wystąpiły w ramach operacji. Analiza jest skorelowane żądań zakończonych niepowodzeniem, na podstawie identyfikatora operation_Id. Zapytanie, a następnie segmentów wyników przy użyciu algorytmu autocluster. 

Podczas tworzenia własnych zapytań, sprawdź, czy działają prawidłowo w usłudze Analytics przed dodaniem go do przepływu.

1. W **zapytania** Dodaj następujące zapytanie usługi Analytics: 

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

1. W **typ wykresu** wybierz opcję **tabeli Html**.

    ![Okno konfiguracji zapytania usługi Analytics](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Krok 6. Konfigurowanie aplikacji logiki do wysyłania wiadomości e-mail

1. Kliknij przycisk **nowy krok**, a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wpisz **usługi Office 365 Outlook**.

1. Kliknij przycisk **Office 365 Outlook — Wyślij wiadomość e-mail**.

    ![Wybór programu Outlook usługi Office 365](./media/automate-with-logic-apps/flow2b.png)

1. W **Wyślij wiadomość e-mail** okna, wykonaj następujące czynności:

   a. Wpisz adres e-mail adresata.

   b. Wpisz temat wiadomości e-mail.

   c. Kliknij w dowolnym miejscu **treści** polu, a następnie w menu zawartości dynamicznej, zostanie otwarty po prawej stronie, wybierz **treści**.

   d. Kliknij przycisk **Pokaż opcje zaawansowane**.

      ![Konfiguracja programu Outlook usługi Office 365](./media/automate-with-logic-apps/flow5.png)

1. W menu zawartości dynamicznej wykonaj następujące czynności:

    a. Wybierz **nazwą załącznika**.

    b. Wybierz **zawartość załącznika**.
    
    c. W **HTML jest** wybierz opcję **tak**.

      ![Ekran konfiguracji poczty e-mail usługi Office 365](./media/automate-with-logic-apps/flow7.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Krok 7: Zapisz i przetestuj aplikację logiki
* Kliknij przycisk **Zapisz** Aby zapisać zmiany.

Możesz poczekać, aż wyzwalacz, aby uruchomić aplikację logiki lub uruchom aplikację logiki natychmiast, wybierając **Uruchom**.

![Ekran tworzenia aplikacji logiki](./media/automate-with-logic-apps/step7.png)

Po uruchomieniu aplikacji logiki adresatów, do których określony na liście adres e-mail zostanie wysłana wiadomość e-mail, która wygląda podobnie do poniższego:

![Wiadomości e-mail aplikacja logiki](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej na temat tworzenia [zapytań analitycznych](../azure-monitor/log-query/get-started-queries.md).
- Dowiedz się więcej na temat usługi [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->





