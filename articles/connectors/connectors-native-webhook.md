---
title: Tworzenie zadania oparte na zdarzeniach i przepływów pracy w usłudze Azure Logic Apps
description: Wyzwalanie, wstrzymywania i wznawiania automatycznych zadań, procesów i przepływów pracy opartych na zdarzenia zachodzące w punkcie końcowym za pomocą usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: c2658df185d4836210c496d2c46a00a3541257a2
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541344"
---
# <a name="automate-event-based-tasks-and-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Automatyzowanie zadań opartych na zdarzeniach i przepływów pracy przy użyciu elementów webhook protokołu HTTP w usłudze Azure Logic Apps

Za pomocą [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md) i wbudowanego łącznika element Webhook protokołu HTTP, możesz zautomatyzować przepływy pracy, które oczekiwania i uruchomienia w oparciu o na określonych zdarzeń, które odbywa się na punkt końcowy HTTP lub HTTPS, tworząc aplikacje logiki. Na przykład można utworzyć aplikację logiki, która monitoruje punktu końcowego usługi przez oczekiwania dla określonego zdarzenia przed wyzwalania przepływu pracy i uruchamianie określonych akcji, a nie regularne sprawdzanie lub *sondowania* tego punktu końcowego.

Poniżej przedstawiono niektóre opartego na zdarzeniach — przepływ pracy — przykład:

* Poczekaj, aż element zostanie dostarczona z [usługi Azure Event Hub](https://github.com/logicappsio/EventHubAPI) przed wyzwoleniem przebiegu aplikacji logiki.
* Oczekiwanie na zatwierdzenie przed kontynuowaniem przepływu pracy.

## <a name="how-do-webhooks-work"></a>Jak działają elementów webhook?

Wyzwalacza elementu webhook protokołu HTTP jest oparty na zdarzeniach, które nie są zależne od sprawdzania lub regularnie sondowania dla nowych elementów. Po zapisaniu aplikacji logiki, która rozpoczyna się od wyzwalacza elementu webhook lub po zmianie aplikacji logiki z wyłączonego na włączony wyzwalacza elementu webhook *subskrybuje* do określonej usługi lub punktu końcowego, rejestrując *adresów URL wywołania zwrotnego* od danej usługi lub punktu końcowego. Wyzwalacz jest następnie czeka na zatwierdzenie tej usługi lub punkt końcowy do wywołania adresu URL, który uruchamiania aplikacji logiki. Podobnie jak [wyzwalacza żądania](connectors-native-reqres.md), aplikacja logiki jest uruchamiany natychmiast po określonym zdarzenie. Wyzwalacz *anulowań subskrypcji* z usługi lub punktu końcowego Usuń wyzwalacz i zapisywanie aplikacji logiki lub gdy zmienią się Twoja aplikacja logiki z włączony na wyłączony.

Akcja elementu webhook protokołu HTTP jest również oparte na zdarzeniach i *subskrybuje* do określonej usługi lub punktu końcowego, rejestrując *adresów URL wywołania zwrotnego* od danej usługi lub punktu końcowego. Ta akcja elementu webhook wstrzymuje przepływ pracy aplikacji logiki i będzie czekać na usługi lub punkt końcowy wywołania adresu URL przed wznawia aplikacji logiki, które są uruchomione. Akcja aplikacji logiki *anulowań subskrypcji* z usługi lub punktu końcowego w następujących przypadkach:

* Po pomyślnym zakończeniu działania elementu webhook
* Jeśli przebieg aplikacji logiki jest anulowane podczas oczekiwania na odpowiedź
* Przed logiki aplikacji upłynie limit czasu

Na przykład usługi Office 365 Outlook łącznika [ **Wyślij wiadomość e-mail dotyczącą zatwierdzenia** ](connectors-create-api-office365-outlook.md) akcji znajduje się przykład akcji elementu webhook, która wynika z tego wzorca. Ten wzorzec można rozszerzyć na dowolną usługę przy użyciu akcji elementu webhook.

Aby uzyskać więcej informacji zobacz następujące tematy:

* [Parametry wyzwalacza elementu Webhook protokołu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Elementy Webhook i subskrypcje](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Tworzenie niestandardowych interfejsów API, który obsługuje element webhook](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Adres URL dla już wdrożonej punktu końcowego lub interfejsu API obsługującego element webhook subskrybowanie i anulowanie subskrypcji wzorzec [wyzwalaczy elementu webhook w usłudze logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) lub [Akcje elementu webhook w usłudze logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-actions) odpowiednio

* Podstawową wiedzę na temat o [jak tworzyć aplikacje logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Aplikacja logiki gdzie chcesz poczekać dla określonych zdarzeń w punkcie końcowym docelowego. Można uruchomić za pomocą wyzwalacza elementu Webhook protokołu HTTP, [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji elementu Webhook protokołu HTTP, należy uruchomić swoją aplikację logiki z dowolnego wyzwalacza, który ma. W tym przykładzie użyto wyzwalacza HTTP w pierwszym kroku.

## <a name="add-an-http-webhook-trigger"></a>Dodawanie wyzwalacza elementu Webhook protokołu HTTP

Ten wyzwalacz wbudowanych rejestruje adres URL wywołania zwrotnego z określonej usługi i oczekuje za daną usługę, możesz wysłać żądanie HTTP POST do tego adresu URL. Gdy to zdarzenie występuje, aktywuje się i natychmiast uruchamia aplikację logiki.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Otwórz swoje pustej aplikacji logiki w Projektancie aplikacji logiki.

1. W projektancie w polu wyszukiwania wprowadź "element webhook protokołu http" jako filtr. Z **wyzwalaczy** listy wybierz **element Webhook protokołu HTTP** wyzwalacza.

   ![Wybieranie wyzwalacza elementu Webhook protokołu HTTP](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Ten przykład zmienia nazwę wyzwalacza "Wyzwalacza elementu Webhook protokołu HTTP", aby ten krok ma bardziej opisowe nazwy. Ponadto w przykładzie dodano później akcję element Webhook protokołu HTTP, a następnie obie nazwy muszą być unikatowe.

1. Podaj wartości dla [parametry wyzwalacza elementu Webhook protokołu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) chcesz użyć dla subskrypcji i anulować wywołań, na przykład:

   ![Wprowadź parametry wyzwalacza elementu Webhook protokołu HTTP](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. Aby dodać inne dostępne parametry, otwórz **dodano nowy parametr** listy i wybierz parametry, które mają.

   Aby uzyskać więcej informacji na temat dostępnych typów uwierzytelniania dla elementu Webhook protokołu HTTP, zobacz [uwierzytelniania HTTP wyzwalacze i akcje](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Kontynuuj tworzenie przepływu pracy aplikacji logiki, za pomocą akcji, które są uruchamiane po aktywowaniu wyzwalacza.

1. Gdy skończysz, to zrobione, pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi Projektanta wybierz **Zapisz**.

   Trwa zapisywanie aplikacji logiki wywołuje punkt końcowy subskrypcji i rejestruje adres URL wywołania zwrotnego do wyzwalania tej aplikacji logiki.

1. Teraz, gdy Usługa docelowa wysyła `HTTP POST` żądanie do adresu URL wywołania zwrotnego generowane aplikację logiki i zawiera żadnych danych, który jest przekazywany za pomocą żądania.

## <a name="add-an-http-webhook-action"></a>Dodawanie akcji elementu Webhook protokołu HTTP

Ta akcja wbudowanych rejestruje adres URL wywołania zwrotnego z określonej usługi wstrzymuje przepływ pracy aplikacji logiki i czeka na tę usługę, możesz wysłać żądanie HTTP POST do tego adresu URL. Gdy to zdarzenie występuje, wznawia działanie akcji uruchamiania aplikacji logiki.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Otwórz aplikację logiki w Projektancie aplikacji logiki.

   W tym przykładzie użyto wyzwalacza elementu Webhook protokołu HTTP jako pierwszy krok.

1. W obszarze danego kroku, w której chcesz dodać akcję element Webhook protokołu HTTP, wybierz **nowy krok**.

   Aby dodać akcję między krokami, wskaźnik myszy nad strzałką znajdującą się między krokami. Wybierz znak plus ( **+** ) pojawia się, a następnie wybierz **Dodaj akcję**.

1. W projektancie w polu wyszukiwania wprowadź "element webhook protokołu http" jako filtr. Z **akcje** listy wybierz **element Webhook protokołu HTTP** akcji.

   ![Wybierz akcję element Webhook protokołu HTTP](./media/connectors-native-webhook/select-http-webhook-action.png)

   Ten przykład zmienia nazwę akcji "Akcja elementu Webhook protokołu HTTP", aby krok polega na bardziej opisową nazwę.

1. Podaj wartości dla elementu Webhook protokołu HTTP, parametry akcji, które są podobne do [parametry wyzwalacza elementu Webhook protokołu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger) chcesz użyć dla subskrypcji i anulować wywołań, na przykład:

   ![Wprowadź parametry akcji elementu Webhook protokołu HTTP](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   Podczas wykonywania aplikacji logiki wywołuje punkt końcowy subskrypcji, podczas uruchamiania tej akcji. Aplikacja logiki następnie wstrzymuje przepływ pracy i czeka na docelową usługę do wysłania `HTTP POST` żądania na adres URL wywołania zwrotnego. Jeśli akcja zakończy się pomyślnie, akcja anulowań subskrypcji z punktu końcowego i wznowieniu działania aplikacji logiki uruchamiania przepływu pracy.

1. Aby dodać inne dostępne parametry, otwórz **dodano nowy parametr** listy i wybierz parametry, które mają.

   Aby uzyskać więcej informacji na temat dostępnych typów uwierzytelniania dla elementu Webhook protokołu HTTP, zobacz [uwierzytelniania HTTP wyzwalacze i akcje](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Gdy skończysz, pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi Projektanta wybierz **Zapisz**.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji o parametrach akcji i wyzwalaczy, które są podobne do siebie nawzajem, zobacz [parametrów elementu Webhook protokołu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger).

### <a name="output-details"></a>Szczegóły danych wyjściowych

Poniżej przedstawiono więcej informacji na temat danych wyjściowych z wyzwalacza elementu Webhook protokołu HTTP lub akcji, która zwraca te informacje:

| Nazwa właściwości | Typ | Opis |
|---------------|------|-------------|
| Nagłówki | object | Nagłówki w żądaniu |
| Treść | object | Obiekt JSON | Obiekt z zawartości treści z żądania |
| Kod stanu: | int | Kod stanu z żądania |
|||

| Kod stanu | Opis |
|-------------|-------------|
| 200 | OK |
| 202 | Zaakceptowane |
| 400 | Nieprawidłowe żądanie |
| 401 | Brak autoryzacji |
| 403 | Zabroniony |
| 404 | Nie można odnaleźć |
| 500 | Wewnętrzny błąd serwera. Wystąpił nieznany błąd. |
|||

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
