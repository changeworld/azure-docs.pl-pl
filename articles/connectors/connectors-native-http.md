---
title: Łączenie z punktami końcowymi HTTP lub HTTPS z usługi Azure Logic Apps
description: Monitorowanie punktów końcowych HTTP lub HTTPS w w zautomatyzowanych zadań, procesów i przepływów pracy przy użyciu usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: fa5fd3ef8b144826468f56ea2a14be592cef5dc1
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541325"
---
# <a name="call-http-or-https-endpoints-by-using-azure-logic-apps"></a>Wywoływanie punktów końcowych HTTP lub HTTPS za pomocą usługi Azure Logic Apps

Za pomocą [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md) i wbudowany łącznik protokołu HTTP, możesz zautomatyzować przepływy pracy, które regularnie wywołanie dowolnego punktu końcowego HTTP lub HTTPS, tworząc aplikacje logiki. Na przykład można monitorować punkt końcowy usługi dla witryny sieci Web, sprawdzając tego punktu końcowego zgodnie z określonym harmonogramem. W przypadku określonego zdarzenia, w tym punkcie końcowym, takich jak witryny sieci Web zostanie wyłączona, zdarzenie wyzwala przepływ pracy aplikacji logiki i uruchamia określone działania.

Aby sprawdzić lub *sondowania* punktu końcowego zgodnie z ustalonym harmonogramem, można użyć wyzwalacza HTTP jako pierwszy krok w przepływie pracy. W przypadku każdego wyboru wyzwalacz wysyła wywołania lub *żądania* do punktu końcowego. Odpowiedzi punktu końcowego określa, czy wykonywania przepływu pracy aplikacji logiki. Wyzwalacz jest przekazywane wraz z dowolnej zawartości z odpowiedzi na działania w aplikacji logiki.

Akcja HTTP można użyć innych krokiem w przepływie pracy wywoływania punktu końcowego, jeśli chcesz. Odpowiedzi punktu końcowego określa sposób uruchamiania pozostałych akcji Twój przepływ pracy.

Na podstawie możliwości docelowy punkt końcowy łącznik protokołu HTTP obsługuje zabezpieczeń TLS (Transport Layer) w wersjach 1.0, 1.1 i 1.2. Usługa Logic Apps negocjuje z punktem końcowym za pośrednictwem za pomocą najwyższej obsługiwana wersja to możliwe. Tak na przykład, jeśli punkt końcowy obsługuje 1.2, łącznik używa 1.2 najpierw. W przeciwnym razie łącznik używa dalej najwyższy obsługiwanej wersji.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Adres URL punktu końcowego docelowego, który ma zostać wywołana

* Podstawową wiedzę na temat o [jak tworzyć aplikacje logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Aplikacja logiki, z którym ma zostać wywołana docelowy punkt końcowy. Aby uruchomić z wyzwalaczem HTTP [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji HTTP, należy uruchomić swoją aplikację logiki z dowolnego wyzwalacza, który ma. W tym przykładzie użyto wyzwalacza HTTP w pierwszym kroku.

## <a name="add-an-http-trigger"></a>Dodaj wyzwalacz HTTP

Ten wyzwalacz wbudowanych sprawia, że wywołania HTTP pod określony adres URL dla punktu końcowego i zwraca odpowiedź.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Otwórz swoje pustej aplikacji logiki w Projektancie aplikacji logiki.

1. W projektancie w polu wyszukiwania wprowadź "http" jako filtr. Z **wyzwalaczy** listy wybierz **HTTP** wyzwalacza.

   ![Wybieranie wyzwalacza HTTP](./media/connectors-native-http/select-http-trigger.png)

   Ten przykład zmienia nazwę wyzwalacza "HTTP wyzwalacza", tak aby ten krok ma bardziej opisową nazwę. Ponadto w przykładzie dodano później akcji HTTP, a następnie obie nazwy muszą być unikatowe.

1. Podaj wartości dla [parametry wyzwalacza HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) , którą chcesz uwzględnić w wywołaniu docelowy punkt końcowy. Skonfiguruj cyklu dla jak często ma wyzwalacz, aby sprawdzić docelowy punkt końcowy.

   ![Wprowadź parametry wyzwalacza HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Aby uzyskać więcej informacji na temat dostępnych typów uwierzytelniania dla protokołu HTTP, zobacz [uwierzytelniania HTTP wyzwalacze i akcje](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Aby dodać inne dostępne parametry, otwórz **dodano nowy parametr** listy i wybierz parametry, które mają.

1. Kontynuuj tworzenie przepływu pracy aplikacji logiki, za pomocą akcji, które są uruchamiane po aktywowaniu wyzwalacza.

1. Gdy skończysz, to zrobione, pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi Projektanta wybierz **Zapisz**.

## <a name="add-an-http-action"></a>Dodawanie akcji HTTP

Ta akcja wbudowanych sprawia, że wywołania HTTP pod określony adres URL dla punktu końcowego i zwraca odpowiedź.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Otwórz aplikację logiki w Projektancie aplikacji logiki.

   W tym przykładzie użyto wyzwalacza HTTP w pierwszym kroku.

1. W obszarze danego kroku, w której chcesz dodać akcji HTTP, wybierz **nowy krok**.

   Aby dodać akcję między krokami, wskaźnik myszy nad strzałką znajdującą się między krokami. Wybierz znak plus ( **+** ) pojawia się, a następnie wybierz **Dodaj akcję**.

1. W projektancie w polu wyszukiwania wprowadź "http" jako filtr. Z **akcje** listy wybierz **HTTP** akcji.

   ![Wybieranie akcji HTTP](./media/connectors-native-http/select-http-action.png)

   Ten przykład zmienia nazwę akcji do akcji"HTTP", tak aby ten krok ma bardziej opisową nazwę.

1. Podaj wartości dla [parametry akcji HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) , którą chcesz uwzględnić w wywołaniu docelowy punkt końcowy.

   ![Wprowadź parametry akcji HTTP](./media/connectors-native-http/http-action-parameters.png)

   Aby uzyskać więcej informacji na temat dostępnych typów uwierzytelniania dla protokołu HTTP, zobacz [uwierzytelniania HTTP wyzwalacze i akcje](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Aby dodać inne dostępne parametry, otwórz **dodano nowy parametr** listy i wybierz parametry, które mają.

1. Gdy skończysz, pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi Projektanta wybierz **Zapisz**.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji na temat parametrów akcji i wyzwalaczy zobacz następujące sekcje:

* [Parametry wyzwalacza HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [Parametry akcji HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>Szczegóły danych wyjściowych

Poniżej przedstawiono więcej informacji na temat danych wyjściowych z wyzwalaczem HTTP lub akcji, która zwraca te informacje:

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
