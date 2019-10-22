---
title: Automatyzacja procesów Application Insights platformy Azure za pomocą Microsoft Flow
description: Dowiedz się, jak można użyć Microsoft Flow, aby szybko zautomatyzować powtarzalne procesy przy użyciu łącznika Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/29/2019
ms.openlocfilehash: ff0896498c0270b8eb43b762228916985f924def
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678327"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatyzacja procesów Application Insights platformy Azure za pomocą łącznika dla Microsoft Flow

Czy na danych telemetrycznych można wielokrotnie uruchamiać te same zapytania, aby sprawdzić, czy usługa działa prawidłowo? Czy chcesz zautomatyzować te zapytania do znajdowania trendów i anomalii, a następnie utworzyć własne przepływy pracy? Łącznik usługi Azure Application Insights dla Microsoft Flow jest odpowiednim narzędziem do tego celu.

Dzięki tej integracji można zautomatyzować wiele procesów bez konieczności pisania jednego wiersza kodu. Po utworzeniu przepływu przy użyciu akcji Application Insights przepływ automatycznie uruchamia zapytanie Application Insights Analytics.

Można również dodać dodatkowe akcje. Microsoft Flow zapewnia setki dostępnych akcji. Na przykład możesz użyć Microsoft Flow, aby automatycznie wysłać powiadomienie e-mail lub utworzyć usterkę w usłudze Azure DevOps. Można również użyć jednego z wielu [szablonów](https://ms.flow.microsoft.com/connectors/shared_applicationinsights/?slug=azure-application-insights) , które są dostępne dla łącznika Microsoft Flow. Te szablony przyspieszają proces tworzenia przepływu.

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). -->

## <a name="create-a-flow-for-application-insights"></a>Tworzenie przepływu dla Application Insights

W tym samouczku dowiesz się, jak utworzyć przepływ, który używa algorytmu autoklastrów analizy do grupowania atrybutów w danych aplikacji sieci Web. Przepływ automatycznie wysyła wyniki za pośrednictwem poczty e-mail, a tylko jeden przykład użycia Microsoft Flow i Application Insights analizy.

### <a name="step-1-create-a-flow"></a>Krok 1. Tworzenie przepływu

1. Zaloguj się do [Microsoft Flow](https://flow.microsoft.com), a następnie wybierz pozycję **Moje przepływy**.
2. Kliknij pozycję Nowy **zaplanowana — od zera**.

    ![Utwórz nowy przepływ z zaplanowanym ciągiem pustym](./media/automate-with-flow/1-create.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>Krok 2. Tworzenie wyzwalacza dla przepływu

1. W oknie podręcznym **Utwórz zaplanowany przepływ**, podaj nazwę przepływu i częstotliwość uruchamiania przepływu.

    ![Konfigurowanie cyklu harmonogramu z wejściem częstotliwości i interwału](./media/automate-with-flow/2-schedule.png)

1. Kliknij przycisk **Utwórz**.

### <a name="step-3-add-an-application-insights-action"></a>Krok 3. Dodawanie akcji Application Insights

1. Wyszukaj **Application Insights**.
2. Kliknij pozycję **Azure Application Insights — Wizualizuj zapytanie analityczne**.

    ![Wybierz akcję: zapytanie dotyczące wizualizacji Application Insights platformy Azure](./media/automate-with-flow/3-visualize.png)

3. Wybierz pozycję **Nowy krok**.

### <a name="step-4-connect-to-an-application-insights-resource"></a>Krok 4. Nawiązywanie połączenia z zasobem Application Insights

Aby ukończyć ten krok, potrzebny jest identyfikator aplikacji i klucz interfejsu API dla zasobu. Można je pobrać z Azure Portal, jak pokazano na poniższym diagramie:

![Identyfikator aplikacji w Azure Portal](./media/automate-with-flow/5apiaccess.png)

![Klucz interfejsu API w Azure Portal](./media/automate-with-flow/6apikey.png)

Podaj nazwę połączenia wraz z IDENTYFIKATORem aplikacji i kluczem interfejsu API.

   ![Okno połączenia Microsoft Flow](./media/automate-with-flow/4-connection.png)

Jeśli pole połączenie nie pojawia się od razu, a zamiast tego nastąpi bezpośrednie wprowadzenie do zapytania, kliknij wielokropek w prawym górnym rogu pola. Następnie wybierz pozycję Moje połączenia lub Użyj istniejącego.

Kliknij przycisk **Utwórz**.

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Krok 5. Określanie typu zapytania i wykresu analitycznego
To przykładowe zapytanie wybiera żądania zakończone niepowodzeniem w ciągu ostatniego dnia i skorelowanie ich z wyjątkami, które wystąpiły w ramach operacji. Analiza jest skorelowane z uwzględnieniem identyfikatora parametrów. Kwerenda następnie segmentuje wyniki przy użyciu algorytmu Autocluster.

Podczas tworzenia własnych zapytań Sprawdź, czy działają prawidłowo w analizie, zanim dodasz ją do przepływu.

- Dodaj następujące zapytanie analityczne i wybierz typ wykresu tabeli HTML. Następnie wybierz pozycję **nowy krok**.

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
    
    ![Okno konfiguracji zapytania analitycznego](./media/automate-with-flow/5-query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Krok 6. Konfigurowanie przepływu do wysyłania wiadomości e-mail

1. Wyszukaj **pakiet Office 365 Outlook**.
2. Kliknij pozycję **Office 365 Outlook — Wyślij wiadomość e-mail**.

    ![Okno wyboru pakietu Office 365 w programie Outlook](./media/automate-with-flow/6-outlook.png)

1. W oknie **wysyłanie wiadomości e-mail** :

   a. Wpisz adres e-mail adresata.

   b. Wpisz temat wiadomości e-mail.

   d. Kliknij w dowolnym miejscu w polu **treść** , a następnie w menu zawartość dynamiczna, które jest otwierane po prawej stronie, wybierz pozycję **treść**.

   e. Wybierz pozycję **Pokaż opcje zaawansowane**

1. W menu zawartość dynamiczna:

    a. Wybierz pozycję **nazwa załącznika**.

    b. Wybierz **zawartość załącznika**.
    
    d. W polu **kod HTML** wybierz pozycję **tak**.

    ![Konfiguracja programu Outlook pakietu Office 365](./media/automate-with-flow/7-email.png)

### <a name="step-7-save-and-test-your-flow"></a>Krok 7. Zapisywanie i testowanie przepływu

Kliknij przycisk **Save** (Zapisz).

Możesz poczekać na uruchomienie tej akcji przez wyzwalacz lub kliknąć przycisk ![beaker ikonę testu ](./media/automate-with-flow/testicon.png) **test** w górnej części.

Po wybraniu **testu**:

1. Wybierz opcję **Chcę wykonać akcję wyzwalacza**.
2. Wybierz pozycję **Uruchom przepływ**.

Gdy przepływ zostanie uruchomiony, adresaci określeni na liście wiadomości e-mail otrzymają wiadomość e-mail podobną do poniższej.

![Przykładowa wiadomość e-mail](./media/automate-with-flow/flow9.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat tworzenia [zapytań analitycznych](../../azure-monitor/log-query/get-started-queries.md).
- Dowiedz się więcej o [Microsoft Flow](https://ms.flow.microsoft.com).

<!--Link references-->