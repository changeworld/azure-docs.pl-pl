---
title: Odbieranie i odpowiadanie na połączenia przy użyciu protokołu HTTPS
description: Obsługa przychodzących żądań HTTPS z usług zewnętrznych przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewers: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 1885d7f8713b3801ce0c9846b7a8509b3864032a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656300"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Odbieranie i odpowiadanie na przychodzące żądania HTTPS w usłudze Azure Logic Apps

Dzięki [usłudze Azure Logic Apps](../logic-apps/logic-apps-overview.md) i wbudowanej akcji wyzwalacza lub odpowiedzi żądania można tworzyć zautomatyzowane zadania i przepływy pracy odbierające przychodzące żądania HTTPS i odpowiadać na nie. Na przykład możesz mieć aplikację logiki:

* Odbieranie i odpowiadanie na żądanie HTTPS dotyczące danych w lokalnej bazie danych.
* Wyzwalanie przepływu pracy, gdy nastąpi zewnętrzne zdarzenie elementu webhook.
* Odbieranie i odpowiadanie na wywołanie HTTPS z innej aplikacji logiki.

> [!NOTE]
> Wyzwalacz żądania obsługuje *tylko* transport warstwy zabezpieczeń (TLS) 1.2 dla połączeń przychodzących. Połączenia wychodzące nadal obsługują TLS 1.0, 1.1 i 1.2. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemu z TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).
>
> Jeśli widzisz błędy uzgadniania TLS, upewnij się, że używasz protokołu TLS 1.2. W przypadku połączeń przychodzących oto obsługiwane mechanizmy szyfrowania:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [założyć bezpłatne konto platformy Azure.](https://azure.microsoft.com/free/)

* Podstawowa wiedza na temat [aplikacji logicznych](../logic-apps/logic-apps-overview.md). Jeśli jesteś nowy w aplikacjach logiki, dowiedz się, [jak utworzyć pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Dodaj wyzwalacz żądania

Ten wbudowany wyzwalacz tworzy ręcznie wywoływany punkt końcowy HTTPS, który może odbierać *tylko* przychodzące żądania HTTPS. Gdy to zdarzenie się dzieje, wyzwalacz uruchamia i uruchamia aplikację logiki. Aby uzyskać więcej informacji na temat podstawowej definicji JSON wyzwalacza i sposobu wywoływania tego wyzwalacza, zobacz [Typ wyzwalacza żądania](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) i [przepływy pracy Wywołania, wyzwalania lub zagnieżdżania z punktami końcowymi HTTP w usłudze Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Tworzenia pustej aplikacji logiki.

1. Po otwarciu projektanta aplikacji logiki w polu wyszukiwania wprowadź "żądanie http" jako filtr. Z listy wyzwalaczy wybierz wyzwalacz **po odebraniu żądania HTTP,** który jest pierwszym krokiem w przepływie pracy aplikacji logiki.

   ![Wybierz wyzwalacz żądania](./media/connectors-native-reqres/select-request-trigger.png)

   Wyzwalacz żądania pokazuje następujące właściwości:

   ![Wyzwalacz żądania](./media/connectors-native-reqres/request-trigger.png)

   | Nazwa właściwości | Nazwa właściwości JSON | Wymagany | Opis |
   |---------------|--------------------|----------|-------------|
   | **Adres URL WPISU HTTP** | {brak} | Tak | Adres URL punktu końcowego, który jest generowany po zapisaniu aplikacji logiki i służy do wywoływania aplikacji logiki |
   | **Schemat JSON treści żądania** | `schema` | Nie | Schemat JSON opisujący właściwości i wartości w treści żądania przychodzącego |
   |||||

1. W polu **Schemat JSON treści żądania** opcjonalnie wprowadź schemat JSON, który opisuje treść w żądaniu przychodzącym, na przykład:

   ![Przykładowy schemat JSON](./media/connectors-native-reqres/provide-json-schema.png)

   Projektant używa tego schematu do generowania tokenów dla właściwości w żądaniu. W ten sposób aplikacja logiki może analizować, zużywać i przekazywać dane z żądania za pośrednictwem wyzwalacza do przepływu pracy.

   Oto przykładowy schemat:

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   Po wprowadzeniu schematu JSON projektant pokazuje przypomnienie o `Content-Type` dołączeniu nagłówka do żądania `application/json`i ustawienie tej wartości nagłówka na . Aby uzyskać więcej informacji, zobacz [Obsługa typów zawartości](../logic-apps/logic-apps-content-type.md).

   ![Przypomnienie o dołączeniu nagłówka "Typ zawartości"](./media/connectors-native-reqres/include-content-type.png)

   Oto jak ten nagłówek wygląda w formacie JSON:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Aby wygenerować schemat JSON oparty na oczekiwanym ładunku (danych), można użyć narzędzia, takiego jak [JSONSchema.net,](https://jsonschema.net)lub wykonać następujące kroki:

   1. W wyzwalaczu Żądanie wybierz opcję **Użyj przykładowego ładunku do wygenerowania schematu**.

      ![Generowanie schematu z ładunku](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Wprowadź ładunek próbki i wybierz pozycję **Gotowe**.

      ![Generowanie schematu z ładunku](./media/connectors-native-reqres/enter-payload.png)

      Oto przykładowy ładunek:

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": { 
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. Aby określić dodatkowe właściwości, otwórz listę **Dodaj nowy parametr** i wybierz parametry, które chcesz dodać.

   | Nazwa właściwości | Nazwa właściwości JSON | Wymagany | Opis |
   |---------------|--------------------|----------|-------------|
   | **Metoda** | `method` | Nie | Metoda, której żądanie przychodzące musi używać do wywoływania aplikacji logiki |
   | **Ścieżka względna** | `relativePath` | Nie | Ścieżka względna parametru, który może zaakceptować adres URL punktu końcowego aplikacji logiki |
   |||||

   W tym przykładzie dodaje **Method** właściwości:

   ![Parametr Dodaj metodę](./media/connectors-native-reqres/add-parameters.png)

   **Method** Właściwość pojawia się w wyzwalaczu, dzięki czemu można wybrać metodę z listy.

   ![Wybierz metodę](./media/connectors-native-reqres/select-method.png)

1. Teraz dodaj kolejną akcję jako następny krok w przepływie pracy. W obszarze wyzwalacza wybierz **pozycję Następny krok,** aby znaleźć akcję, którą chcesz dodać.

   Na przykład można odpowiedzieć na żądanie, [dodając akcję Odpowiedź](#add-response), której można użyć do zwrócenia dostosowanej odpowiedzi i jest opisana w dalszej części tego tematu.

   Aplikacja logiki utrzymuje żądanie przychodzące otwarte tylko przez jedną minutę. Zakładając, że przepływ pracy aplikacji logiki zawiera response akcji, jeśli aplikacja logiki nie zwraca `504 GATEWAY TIMEOUT` odpowiedzi po tym czasie przechodzi, aplikacja logiki zwraca do obiektu wywołującego. W przeciwnym razie jeśli aplikacja logiki nie zawiera response akcji, aplikacja logiki natychmiast zwraca `202 ACCEPTED` odpowiedź do wywołującego.

1. Po zakończeniu zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**. 

   Ten krok generuje adres URL do użycia do wysyłania żądania, które wyzwala aplikację logiki. Aby skopiować ten adres URL, wybierz ikonę kopiowania obok adresu URL.

   ![Adres URL do użycia wyzwalania aplikacji logiki](./media/connectors-native-reqres/generated-url.png)

1. Aby wyzwolić aplikację logiki, wyślij wpis HTTP do wygenerowanego adresu URL. Na przykład można użyć narzędzia, takiego jak [Listonosz](https://www.getpostman.com/).

### <a name="trigger-outputs"></a>Wyjścia wyzwalające

Oto więcej informacji na temat wyjść z wyzwalacza żądania:

| Nazwa właściwości JSON | Typ danych | Opis |
|--------------------|-----------|-------------|
| `headers` | Obiekt | Obiekt JSON opisujący nagłówki z żądania |
| `body` | Obiekt | Obiekt JSON opisujący zawartość treści z żądania |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Dodawanie akcji Odpowiedź

Akcji Odpowiedź można użyć, aby odpowiedzieć ładunkiem (danymi) na przychodzące żądanie HTTPS, ale tylko w aplikacji logiki, która jest wyzwalana przez żądanie HTTPS. Akcję Odpowiedź można dodać w dowolnym momencie przepływu pracy. Aby uzyskać więcej informacji na temat podstawowej definicji JSON dla tego wyzwalacza, zobacz [typ akcji Odpowiedź](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action).

Aplikacja logiki utrzymuje żądanie przychodzące otwarte tylko przez jedną minutę. Zakładając, że przepływ pracy aplikacji logiki zawiera response akcji, jeśli aplikacja logiki nie zwraca `504 GATEWAY TIMEOUT` odpowiedzi po tym czasie przechodzi, aplikacja logiki zwraca do obiektu wywołującego. W przeciwnym razie jeśli aplikacja logiki nie zawiera response akcji, aplikacja logiki natychmiast zwraca `202 ACCEPTED` odpowiedź do wywołującego.

> [!IMPORTANT]
> Jeśli akcja Odpowiedź zawiera te nagłówki, aplikacje logiki usuwa te nagłówki z wygenerowanego komunikatu odpowiedzi bez wyświetlania ostrzeżenia lub błędu:
>
> * `Allow`
> * `Content-*`z tymi wyjątkami: `Content-Disposition`, `Content-Encoding`, i`Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Mimo że aplikacje logiki nie powstrzyma cię od zapisywania aplikacji logiki, które mają akcję Odpowiedź z tymi nagłówkami, aplikacje logiki ignoruje te nagłówki.

1. W projektancie aplikacji logiki w kroku, w którym chcesz dodać akcję Odpowiedź, wybierz pozycję **Nowy krok**.

   Na przykład przy użyciu wyzwalacza żądania z wcześniej:

   ![Dodaj nowy krok](./media/connectors-native-reqres/add-response.png)

   Aby dodać akcję między krokami, przesuń wskaźnik myszy na strzałkę między tymi krokami. Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**w polu wyszukiwania wprowadź jako filtr "odpowiedź" i wybierz akcję **Odpowiedź.**

   ![Wybierz akcję Odpowiedź](./media/connectors-native-reqres/select-response-action.png)

   Wyzwalacz żądania jest zwinięty w tym przykładzie dla prostoty.

1. Dodaj wszystkie wartości, które są wymagane dla wiadomości odpowiedzi. 

   W niektórych polach kliknięcie w ich polach powoduje otwarcie dynamicznej listy zawartości. Następnie można wybrać tokeny, które reprezentują dostępne dane wyjściowe z poprzednich kroków w przepływie pracy. Właściwości ze schematu określonego we wcześniejszym przykładzie są teraz wyświetlane na liście zawartości dynamicznej.

   Na przykład dla **nagłówków** pole, uwzględnić `Content-Type` jako nazwę klucza `application/json` i ustawić wartość klucza, jak wspomniano wcześniej w tym temacie. W polu **Treść** można wybrać dane wyjściowe treści wyzwalacza z listy zawartości dynamicznej.

   ![Szczegóły akcji odpowiedzi](./media/connectors-native-reqres/response-details.png)

   Aby wyświetlić nagłówki w formacie JSON, wybierz pozycję **Przełącz do widoku tekstu**.

   ![Nagłówki — przełączanie do widoku tekstu](./media/connectors-native-reqres/switch-to-text-view.png)

   Oto więcej informacji na temat właściwości, które można ustawić w odpowiedzi akcji. 

   | Nazwa właściwości | Nazwa właściwości JSON | Wymagany | Opis |
   |---------------|--------------------|----------|-------------|
   | **Kod stanu** | `statusCode` | Tak | Kod stanu do zwrócenia w odpowiedzi |
   | **Nagłówki** | `headers` | Nie | Obiekt JSON opisujący jeden lub więcej nagłówków do uwzględnienia w odpowiedzi |
   | **Treść** | `body` | Nie | Organ reagowania |
   |||||

1. Aby określić dodatkowe właściwości, takie jak schemat JSON dla treści odpowiedzi, otwórz listę **Dodaj nowy parametr** i wybierz parametry, które chcesz dodać.

1. Po zakończeniu zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**. 

## <a name="next-steps"></a>Następne kroki

* [Łączniki dla usługi Logic Apps](../connectors/apis-list.md)
