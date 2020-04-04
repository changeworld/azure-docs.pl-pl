---
title: Czekaj i odpowiadaj na zdarzenia
description: Automatyzuj przepływy pracy wyzwalające, wstrzymywały i wznawiały na podstawie zdarzeń w punkcie końcowym usługi przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/06/2020
tags: connectors
ms.openlocfilehash: 0a3fb9a8a72b384d2af4af38bdc382e541ddf535
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656277"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Tworzenie i uruchamianie zautomatyzowanych przepływów pracy opartych na zdarzeniach przy użyciu elementów webhook HTTP w usłudze Azure Logic Apps

Za pomocą [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md) i wbudowanego łącznika http webhook można zautomatyzować przepływy pracy, które czekają i są uruchamiane na podstawie określonych zdarzeń, które mają miejsce w punkcie końcowym HTTP lub HTTPS, tworząc aplikacje logiki. Na przykład można utworzyć aplikację logiki, która monitoruje punkt końcowy usługi, oczekując na określone zdarzenie przed wyzwoleniem przepływu pracy i uruchomieniem określonych akcji, zamiast regularnie sprawdzać lub *sondować* ten punkt końcowy.

Oto kilka przykładowych przepływów pracy opartych na zdarzeniach:

* Poczekaj na przybycie elementu z [usługi Azure Event Hub](https://github.com/logicappsio/EventHubAPI) przed wyzwoleniem uruchomienia aplikacji logiki.
* Poczekaj na zatwierdzenie przed kontynuowaniem przepływu pracy.

## <a name="how-do-webhooks-work"></a>Jak działają webhooks?

Wyzwalacz elementu webhook HTTP jest oparty na zdarzeniach, co nie zależy od regularnego sprawdzania lub sondowania nowych elementów. Po zapisaniu aplikacji logiki, która rozpoczyna się od wyzwalacza elementu webhook lub po zmianie aplikacji logiki z wyłączone na włączone, wyzwalacz elementu webhook *subskrybuje* określoną usługę lub punkt końcowy, rejestrując *adres URL wywołania zwrotnego* z tej usługi lub punktu końcowego. Wyzwalacz następnie czeka na tej usługi lub punktu końcowego do wywołania adresu URL, który rozpoczyna uruchamianie aplikacji logiki. Podobnie jak [wyzwalacz żądania,](connectors-native-reqres.md)aplikacja logiki jest uruchamiana natychmiast po wykonaniu określonego zdarzenia. Wyzwalacz *powoduje anulowanie subskrypcji* usługi lub punktu końcowego po usunięciu wyzwalacza i zapisaniu aplikacji logiki lub zmianie aplikacji logiki z włączonej na wyłączoną.

Akcja http webhook jest również oparta na zdarzeniach i *subskrybuje* określoną usługę lub punkt końcowy, rejestrując *adres URL wywołania zwrotnego* w tej usłudze lub punkcie końcowym. Akcja elementu webhook wstrzymuje przepływ pracy aplikacji logiki i czeka, aż usługa lub punkt końcowy wywoła adres URL, zanim aplikacja logiki wznowi działanie. Aplikacja logiki akcji *wypisuje się* z usługi lub punktu końcowego w następujących przypadkach:

* Po pomyślnym zakończeniu akcji elementu webhook
* Jeśli uruchomienie aplikacji logiki zostanie anulowane podczas oczekiwania na odpowiedź
* Zanim aplikacja logiki się przesunie

Na przykład akcja [**wiadomości e-mail wysyłania zatwierdzenia przez**](connectors-create-api-office365-outlook.md) łącznik programu Office 365 Outlook jest przykładem akcji elementu webhook, która jest zgodna z tym wzorcem. Ten wzorzec można rozszerzyć na dowolną usługę przy użyciu akcji elementu webhook.

> [!NOTE]
> Aplikacja logiki wymusza transport layer security (TLS) 1.2 podczas odbierania wywołania z powrotem do wyzwalacza lub akcji elementu webhook HTTP. Jeśli widzisz błędy uzgadniania TLS, upewnij się, że używasz protokołu TLS 1.2. W przypadku połączeń przychodzących oto obsługiwane mechanizmy szyfrowania:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

Więcej informacji można znaleźć w następujących tematach:

* [Parametry wyzwalacza elementu Webhook HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Elementów webhook i subskrypcji](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Tworzenie niestandardowych interfejsów API obsługujących element webhook](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Adres URL już wdrożonego punktu końcowego lub interfejsu API obsługującego wzorzec subskrybowania i anulowania subskrypcji dla [wyzwalaczy elementu webhook w aplikacjach logiki](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) lub [akcjach elementu webhook w aplikacjach logiki,](../logic-apps/logic-apps-create-api-app.md#webhook-actions) stosownie do przypadku

* Podstawowa wiedza na temat [tworzenia aplikacji logicznych](../logic-apps/quickstart-create-first-logic-app-workflow.md). Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Aplikacja logiki, w której chcesz czekać na określone zdarzenia w docelowym punkcie końcowym. Aby rozpocząć od wyzwalacza http webhook, [utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji HTTP Webhook, uruchom aplikację logiki z dowolnym wyzwalaczem, który chcesz. W tym przykładzie użyto wyzwalacza HTTP jako pierwszego kroku.

## <a name="add-an-http-webhook-trigger"></a>Dodawanie wyzwalacza elementu Webhook HTTP

Ten wbudowany wyzwalacz wywołuje punkt końcowy subskrybowania w usłudze docelowej i rejestruje adres URL wywołania zwrotnego z usługą docelową. Następnie aplikacja logiki czeka na usługę `HTTP POST` docelową, aby wysłać żądanie do adresu URL wywołania zwrotnego. Gdy to zdarzenie się dzieje, wyzwalacz uruchamia i przekazuje wszelkie dane w żądaniu wraz z przepływem pracy.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Otwórz pustą aplikację logiki w logice App Designer.

1. W polu wyszukiwania projektanta `http webhook` wprowadź jako filtr. Z listy **Wyzwalacze** wybierz wyzwalacz **elementu Webhook HTTP.**

   ![Wybieranie wyzwalacza elementu Webhook HTTP](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   W tym przykładzie `HTTP Webhook trigger` zmienia nazwę wyzwalacza, tak aby krok miał bardziej opisową nazwę. Ponadto w przykładzie później dodaje akcję http webhook, a obie nazwy muszą być unikatowe.

1. Podaj wartości [parametrów wyzwalacza http webhook,](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) których chcesz użyć do subskrybowania i anulowania subskrypcji połączeń.

   W tym przykładzie wyzwalacz zawiera metody, identyfikatory URI i treści wiadomości do użycia podczas wykonywania operacji subskrybowania i anulowania subskrypcji.

   ![Wprowadź parametry wyzwalacza elementu Webhook HTTP](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Subskrypcja — metoda** | Tak | Metoda używana podczas subskrybowania docelowego punktu końcowego |
   | **Subskrybuj - URI** | Tak | Adres URL używany do subskrybowania docelowego punktu końcowego |
   | **Subskrybuj - Body** | Nie | Wszelkie treści wiadomości do uwzględnienia w żądaniu subskrypcji. W tym przykładzie znajduje się adres URL wywołania zwrotnego, który jednoznacznie `@listCallbackUrl()` identyfikuje subskrybenta, który jest aplikacją logiki, przy użyciu wyrażenia, aby pobrać adres URL wywołania zwrotnego aplikacji logiki. |
   | **Rezygnacja z subskrypcji - Metoda** | Nie | Metoda, której należy użyć podczas anulowania subskrypcji z docelowego punktu końcowego |
   | **Anulowanie subskrypcji — identyfikator URI** | Nie | Adres URL używany do anulowania subskrypcji z docelowego punktu końcowego |
   | **Wypisuj się - Ciało** | Nie | Opcjonalna treść wiadomości do uwzględnienia w żądaniu anulowania subskrypcji <p><p>**Uwaga:** Ta właściwość nie `listCallbackUrl()` obsługuje przy użyciu funkcji. Jednak wyzwalacz automatycznie zawiera i wysyła `x-ms-client-tracking-id` nagłówki `x-ms-workflow-operation-name`oraz , których usługa docelowa może używać do jednoznacznej identyfikacji subskrybenta. |
   ||||

1. Aby dodać inne właściwości wyzwalacza, otwórz listę **Dodaj nowy parametr.**

   ![Dodawanie kolejnych właściwości wyzwalacza](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   Na przykład, jeśli chcesz użyć uwierzytelniania, możesz dodać właściwości **Subskrybuj — Uwierzytelnianie** i **Anuluj subskrypcję — Uwierzytelnianie.** Aby uzyskać więcej informacji na temat typów uwierzytelniania dostępnych dla elementu HTTP Webhook, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Kontynuuj tworzenie przepływu pracy aplikacji logiki z akcji, które są uruchamiane po uruchomieniu wyzwalacza.

1. Po zakończeniu, należy pamiętać, aby zapisać aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Zapisywanie aplikacji logiki wywołuje punkt końcowy subskrypcji w usłudze docelowej i rejestruje adres URL wywołania zwrotnego. Następnie aplikacja logiki czeka na usługę `HTTP POST` docelową, aby wysłać żądanie do adresu URL wywołania zwrotnego. Gdy to zdarzenie się dzieje, wyzwalacz uruchamia i przekazuje wszelkie dane w żądaniu wraz z przepływem pracy. Jeśli ta operacja zakończy się pomyślnie, wyzwalacz wypisuje się z punktu końcowego, a aplikacja logiki kontynuuje pozostały przepływ pracy.

## <a name="add-an-http-webhook-action"></a>Dodawanie akcji http webhook

Ta wbudowana akcja wywołuje punkt końcowy subskrybowania w usłudze docelowej i rejestruje adres URL wywołania zwrotnego z usługą docelową. Aplikacja logiki następnie wstrzymuje i `HTTP POST` czeka na usługę docelową, aby wysłać żądanie do adresu URL wywołania zwrotnego. W przypadku wystąpienia tego zdarzenia akcja przekazuje wszystkie dane w żądaniu wraz z przepływem pracy. Jeśli operacja zakończy się pomyślnie, akcja zostanie anulowana z punktu końcowego, a aplikacja logiki kontynuuje uruchamianie pozostałego przepływu pracy.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Otwórz aplikację logiki w logic app designer.

   W tym przykładzie użyto wyzwalacza http webhook jako pierwszego kroku.

1. W obszarze kroku, w którym chcesz dodać akcję HTTP Webhook, wybierz pozycję **Nowy krok**.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy na strzałkę między krokami. Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania projektanta `http webhook` wprowadź jako filtr. Z listy **Akcje** wybierz akcję **HTTP Webhook.**

   ![Wybieranie akcji http webhook](./media/connectors-native-webhook/select-http-webhook-action.png)

   W tym przykładzie zmienia nazwę akcji na "Akcja http webhook", dzięki czemu krok ma bardziej opisową nazwę.

1. Podaj wartości parametrów akcji HTTP Webhook, które są podobne do [parametrów wyzwalacza http webhook,](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)które mają być używane do subskrybowania i anulowania subskrypcji połączeń.

   W tym przykładzie akcja obejmuje metody, identyfikatory URI i treści wiadomości do użycia podczas wykonywania operacji subskrybowania i anulowania subskrypcji.

   ![Wprowadź parametry akcji http webhook](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Subskrypcja — metoda** | Tak | Metoda używana podczas subskrybowania docelowego punktu końcowego |
   | **Subskrybuj - URI** | Tak | Adres URL używany do subskrybowania docelowego punktu końcowego |
   | **Subskrybuj - Body** | Nie | Wszelkie treści wiadomości do uwzględnienia w żądaniu subskrypcji. W tym przykładzie znajduje się adres URL wywołania zwrotnego, który jednoznacznie `@listCallbackUrl()` identyfikuje subskrybenta, który jest aplikacją logiki, przy użyciu wyrażenia, aby pobrać adres URL wywołania zwrotnego aplikacji logiki. |
   | **Rezygnacja z subskrypcji - Metoda** | Nie | Metoda, której należy użyć podczas anulowania subskrypcji z docelowego punktu końcowego |
   | **Anulowanie subskrypcji — identyfikator URI** | Nie | Adres URL używany do anulowania subskrypcji z docelowego punktu końcowego |
   | **Wypisuj się - Ciało** | Nie | Opcjonalna treść wiadomości do uwzględnienia w żądaniu anulowania subskrypcji <p><p>**Uwaga:** Ta właściwość nie `listCallbackUrl()` obsługuje przy użyciu funkcji. Jednak akcja automatycznie zawiera i wysyła nagłówki `x-ms-client-tracking-id` `x-ms-workflow-operation-name`oraz , których usługa docelowa może używać do jednoznacznej identyfikacji subskrybenta. |
   ||||

1. Aby dodać inne właściwości akcji, otwórz listę **Dodaj nowy parametr.**

   ![Dodaj więcej właściwości akcji](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   Na przykład, jeśli chcesz użyć uwierzytelniania, możesz dodać właściwości **Subskrybuj — Uwierzytelnianie** i **Anuluj subskrypcję — Uwierzytelnianie.** Aby uzyskać więcej informacji na temat typów uwierzytelniania dostępnych dla elementu HTTP Webhook, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Po zakończeniu pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Teraz po uruchomieniu tej akcji aplikacja logiki wywołuje punkt końcowy subskrypcji w usłudze docelowej i rejestruje adres URL wywołania zwrotnego. Aplikacja logiki następnie wstrzymuje przepływ pracy i `HTTP POST` czeka na usługę docelową, aby wysłać żądanie do adresu URL wywołania zwrotnego. W przypadku wystąpienia tego zdarzenia akcja przekazuje wszystkie dane w żądaniu wraz z przepływem pracy. Jeśli operacja zakończy się pomyślnie, akcja zostanie anulowana z punktu końcowego, a aplikacja logiki kontynuuje uruchamianie pozostałego przepływu pracy.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji na temat parametrów wyzwalacza i akcji, które są do siebie podobne, zobacz [Parametry http webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger).

### <a name="output-details"></a>Szczegóły wydruku

Oto więcej informacji na temat wyjść z wyzwalacza lub akcji http webhook, który zwraca te informacje:

| Nazwa właściwości | Typ | Opis |
|---------------|------|-------------|
| Nagłówki | obiekt | Nagłówki z żądania |
| body | obiekt | Obiekt JSON | Obiekt z zawartością treści z żądania |
| kod stanu | int | Kod stanu z żądania |
|||

| Kod stanu | Opis |
|-------------|-------------|
| 200 | OK |
| 202 | Zaakceptowane |
| 400 | Zła prośba |
| 401 | Brak autoryzacji |
| 403 | Forbidden |
| 404 | Nie znaleziono |
| 500 | Wewnętrzny błąd serwera. Wystąpił nieznany błąd. |
|||

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
