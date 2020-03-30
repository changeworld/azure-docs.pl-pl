---
title: Automatyzacja procesów usługi Azure Application Insights przy użyciu aplikacji logiki
description: Dowiedz się, jak szybko zautomatyzować powtarzalne procesy, dodając łącznik usługi Application Insights do aplikacji logiki.
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: 4a0944e661932d86fac75f78c4faf5be751806c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473170"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatyzacja procesów usługi Application Insights przy użyciu aplikacji logiki

Czy wielokrotnie uruchamiasz te same zapytania dotyczące danych telemetrycznych, aby sprawdzić, czy usługa działa prawidłowo? Czy chcesz zautomatyzować te zapytania do znajdowania trendów i anomalii, a następnie tworzyć własne przepływy pracy wokół nich? Łącznik usługi Azure Application Insights dla aplikacji logiki jest właściwym narzędziem do tego celu.

> [!NOTE]
> Łącznik usługi Azure Application Insights został zastąpiony [łącznikiem usługi Azure Monitor,](../platform/logicapp-flow-connector.md) który jest zintegrowany z usługą Azure Active Directory zamiast wymagać klucza interfejsu API, a także umożliwia pobieranie danych z obszaru roboczego usługi Log Analytics.

Dzięki tej integracji można zautomatyzować wiele procesów bez pisania jednego wiersza kodu. Aplikację logiki można utworzyć za pomocą łącznika usługi Application Insights, aby szybko zautomatyzować dowolny proces usługi Application Insights. 

Można również dodać dodatkowe akcje. Funkcja Aplikacji logiki usługi Azure App Service udostępnia setki akcji. Na przykład za pomocą aplikacji logiki, można automatycznie wysłać powiadomienie e-mail lub utworzyć błąd w usłudze Azure DevOps. Można również użyć jednego z wielu dostępnych [szablonów,](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) aby przyspieszyć proces tworzenia aplikacji logiki. 

## <a name="create-a-logic-app-for-application-insights"></a>Tworzenie aplikacji logiki dla usługi Application Insights

W tym samouczku dowiesz się, jak utworzyć aplikację logiki, która używa algorytmu autoklaster Analytics do grupowanie atrybutów w danych dla aplikacji sieci web. Przepływ automatycznie wysyła wyniki pocztą e-mail, tylko jeden przykład, jak można używać application insights analytics i aplikacji logiki razem. 

### <a name="step-1-create-a-logic-app"></a>Krok 1: Tworzenie aplikacji logiki
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Kliknij **pozycję Utwórz zasób**, wybierz pozycję **Sieć Web + Telefon komórkowy**, a następnie wybierz pozycję Aplikacja **logiki**.

    ![Nowe okno aplikacji logiki](./media/automate-with-logic-apps/1createlogicapp.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Krok 2: Tworzenie wyzwalacza dla aplikacji logiki
1. W oknie **Projektant aplikacji logiki** w obszarze **Start ze wspólnym wyzwalaczem**wybierz pozycję **Cykl**.

    ![Okno Projektanta aplikacji logiki](./media/automate-with-logic-apps/2logicappdesigner.png)

1. W polu **Interwał** wpisz **1,** a następnie zaznacz**pole Częstotliwość,** wybierz **dzień**.

    ![Projektant aplikacji logiki "Cykl" okno](./media/automate-with-logic-apps/3recurrence.png)

### <a name="step-3-add-an-application-insights-action"></a>Krok 3: Dodawanie akcji usługi Application Insights
1. Kliknij **pozycję Nowy krok**.

1. W polu Wyszukiwania **akcji wybierz pozycję,** wpisz usługę **Azure Application Insights**.

1. W obszarze **Akcje**kliknij pozycję **Usługa Azure Application Insights — Visualize Analytics query**.

    ![Projektant aplikacji logiki "Wybierz akcję" okno](./media/automate-with-logic-apps/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Krok 4: Łączenie się z zasobem usługi Application Insights

Aby wykonać ten krok, potrzebny jest identyfikator aplikacji i klucz interfejsu API dla zasobu. Można je pobrać z witryny Azure portal, jak pokazano na poniższym diagramie:

![Identyfikator aplikacji w witrynie Azure portal](./media/automate-with-logic-apps/5apiaccess.png)

![Identyfikator aplikacji w witrynie Azure portal](./media/automate-with-logic-apps/6apikey.png)

Podaj nazwę połączenia, identyfikator aplikacji i klucz interfejsu API.

![Okno połączenia przepływu projektanta aplikacji logiki](./media/automate-with-logic-apps/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Krok 5: Określ kwerendę Analytics i typ wykresu
W poniższym przykładzie kwerenda wybiera żądania nie powiodło się w ciągu ostatniego dnia i koreluje je z wyjątkami, które wystąpiły w ramach operacji. Analytics koreluje nieudane żądania na podstawie identyfikatora operation_Id. Następnie kwerenda segmentuje wyniki przy użyciu algorytmu autoklastrów. 

Podczas tworzenia własnych zapytań sprawdź, czy działają poprawnie w Analytics, zanim dodasz je do przepływu.

1. W polu **Kwerenda** dodaj następującą kwerendę Analytics:

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

1. W polu **Typ wykresu** wybierz pozycję **Tabela html**.

    ![Okno konfiguracji kwerendy analytics](./media/automate-with-logic-apps/8query.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Krok 6: Konfigurowanie aplikacji logiki do wysyłania wiadomości e-mail

1. Kliknij **pozycję Nowy krok**.

1. W polu wyszukiwania wpisz pozycję **Office 365 Outlook**.

1. Kliknij **pozycję Office 365 Outlook — wyślij wiadomość e-mail**.

    ![Wybór usługi Office 365 Outlook](./media/automate-with-logic-apps/9sendemail.png)

1. W oknie **Wyślij wiadomość e-mail** wykonaj następujące czynności:

   a. Wpisz adres e-mail adresata.

   b. Wpisz temat wiadomości e-mail.

   d. Kliknij dowolne miejsce w polu **Treść,** a następnie w dynamicznym menu zawartości otwieranym po prawej stronie wybierz polecenie **Ciało**.
    
   d. Kliknij pozycję **rozwijaj Dodaj nowy parametr** i wybierz pozycję Załączniki i kod HTML.

      ![Konfiguracja usługi Office 365 Outlook](./media/automate-with-logic-apps/10emailbody.png)

      ![Konfiguracja usługi Office 365 Outlook](./media/automate-with-logic-apps/11emailparameter.png)

1. W menu zawartości dynamicznej wykonaj następujące czynności:

    a. Wybierz pozycję **Nazwa załącznika**.

    b. Wybierz **pozycję Zawartość załącznika**.
    
    d. W polu **Jest HTML** wybierz pozycję **Tak**.

      ![Ekran konfiguracji wiadomości e-mail w usłudze Office 365](./media/automate-with-logic-apps/12emailattachment.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Krok 7: Zapisz i przetestuj aplikację logiczną
* Kliknij **przycisk Zapisz,** aby zapisać zmiany.

Możesz poczekać na uruchomienie aplikacji logiki przez wyzwalacz lub natychmiast uruchomić aplikację logiki, wybierając pozycję **Uruchom**.

![Ekran tworzenia aplikacji logiki](./media/automate-with-logic-apps/13save.png)

Po uruchomieniu aplikacji logiki adresaci określone na liście wiadomości e-mail otrzymają wiadomość e-mail, która wygląda następująco:

![Komunikat e-mail aplikacji logiki](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o tworzeniu [zapytań Analytics](../../azure-monitor/log-query/get-started-queries.md).
- Dowiedz się więcej na temat usługi [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->





