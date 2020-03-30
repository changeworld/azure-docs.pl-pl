---
title: Wywoływanie, wyzwalanie lub zagnieżdżanie aplikacji logiki
description: Konfigurowanie punktów końcowych HTTP do wywoływania, wyzwalania lub zagnieżdżania przepływów pracy aplikacji logiki w usłudze Azure Logic Apps
services: logic-apps
ms.workload: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: d5b5a69c7927d07c0ae6b3b56ec97b6551e5d46b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191332"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Wywoływanie, wyzwalanie lub zagnieżdżanie aplikacji logiki przy użyciu punktów końcowych HTTP w usłudze Azure Logic Apps

Aby aplikacja logiki wywoływane za pośrednictwem adresu URL, dzięki czemu aplikacja logiki może odbierać żądania przychodzące z innych usług, można natywnie udostępnić synchronicznie punkt końcowy HTTP jako wyzwalacz w tej aplikacji logiki. Po skonfigurowaniu tej funkcji można również zagnieżdżać aplikację logiki w innych aplikacjach logiki, co umożliwia utworzenie wzorca wywoływanych punktów końcowych.

Aby skonfigurować punkt końcowy HTTP, można użyć dowolnego z tych typów wyzwalaczy, które umożliwiają aplikacjom logiki odbieranie żądań przychodzących:

* [Żądanie](../connectors/connectors-native-reqres.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)
* Wyzwalacze łącznika zarządzanego, które mają [typ ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) i mogą odbierać przychodzące żądania HTTP

> [!NOTE]
> W tych przykładach użyto wyzwalacza żądania, ale można użyć dowolnego wyzwalacza opartego na żądaniu HTTP, który znajduje się na poprzedniej liście. Wszystkie zasady identycznie mają zastosowanie do tych innych typów wyzwalaczy.

Jeśli jesteś nowy w aplikacjach logiki, zobacz [Co to jest Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) i szybki [start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja logiki, w której chcesz skonfigurować punkt końcowy HTTP jako wyzwalacz. Możesz zacząć od pustej aplikacji logiki lub istniejącej aplikacji logiki, w której chcesz zastąpić bieżący wyzwalacz. W tym przykładzie rozpoczyna się od pustej aplikacji logiki.

## <a name="create-a-callable-endpoint"></a>Tworzenie punktu końcowego, który można wywołać

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Tworzenie i otwieranie pustej aplikacji logiki w projektancie aplikacji logiki.

   W tym przykładzie użyto wyzwalacza żądania, ale można użyć dowolnego wyzwalacza, który może odbierać przychodzące żądania HTTP. Wszystkie zasady identycznie odnoszą się do tych wyzwalaczy. Aby uzyskać więcej informacji na temat wyzwalacza żądania, zobacz [Odbieranie i odpowiadanie na przychodzące wywołania HTTPS przy użyciu usługi Azure Logic Apps](../connectors/connectors-native-reqres.md).

1. W polu wyszukiwania wybierz pozycję **Wbudowane**. W polu wyszukiwania `request` wprowadź jako filtr. Z listy wyzwalaczy wybierz pozycję **Po odebraniu żądania HTTP**.

   ![Znajdowanie i wybieranie wyzwalacza żądania](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. Opcjonalnie w polu **Schemat JSON treści żądania** można wprowadzić schemat JSON opisujący ładunek lub dane, które mają być odbierane przez wyzwalacz.

   Projektant używa tego schematu do generowania tokenów, które reprezentują dane wyjściowe wyzwalaczy. Następnie można łatwo odwoływać się do tych danych wyjściowych w całym przepływie pracy aplikacji logiki. Dowiedz się więcej o [tokenach generowanych ze schematów JSON](#generated-tokens).

   W tym przykładzie wprowadź ten schemat:

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![Podaj schemat JSON dla akcji Żądanie](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   Można też wygenerować schemat JSON, udostępniając ładunek próbki:

   1. W wyzwalaczu **Żądanie** wybierz opcję **Użyj przykładowego ładunku do wygenerowania schematu**.

   1. W polu **Wprowadź lub wklej przykładową ładowność JSON** wprowadź przykładową ładowność, na przykład:

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. Gdy wszystko będzie gotowe, wybierz pozycję **Gotowe**.

      Pole **Schemat JSON treści żądania** pokazuje teraz wygenerowany schemat.

1. Zapisz aplikację logiki.

   **Wpis HTTP do tego** adresu URL pole teraz pokazuje wygenerowany adres URL wywołania zwrotnego, który inne usługi mogą używać do wywoływania i wyzwalania aplikacji logiki. Ten adres URL zawiera klucz sygnatury dostępu współdzielonego (SAS), który jest używany do uwierzytelniania, w parametrach kwerendy, na przykład:

   ![Wygenerowany adres URL wywołania zwrotnego dla punktu końcowego](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   Możesz również uzyskać adres URL punktu końcowego HTTP z okienka **Przegląd** aplikacji logiki.

   1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

   1. W sekcji **Podsumowanie** wybierz pozycję **Zobacz historię wyzwalaczy**.

      ![Pobierz adres URL punktu końcowego HTTP z witryny Azure portal](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   1. W obszarze **Adres URL wywołania zwrotnego [POST]** skopiuj adres URL:

      ![Kopiowanie adresu URL punktu końcowego HTTP z witryny Azure portal](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url.png)

      Możesz też uzyskać adres URL, nawiązując następujące połączenie:

      ```http
      POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01
      ```

<a name="set-method"></a>

## <a name="set-expected-http-method"></a>Ustawianie oczekiwanej metody HTTP

Domyślnie wyzwalacz żądania oczekuje żądania HTTP POST. Można jednak określić inną metodę, której można oczekiwać, ale tylko jedną metodę.

1. W wyzwalaczu Żądanie otwórz listę **Dodaj nowy parametr** i wybierz opcję **Metoda**, która dodaje tę właściwość do wyzwalacza.

   ![Dodaj właściwość "Metoda" do wyzwolenia](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. Z **listy Metoda** wybierz inną metodę, która wyzwalacza oczekuje zamiast tego. Można też określić metodę niestandardową.

   Na przykład wybierz **GET** metody, dzięki czemu można przetestować adres URL punktu końcowego HTTP później.

   ![Wybierz metodę HTTP, która będzie używana dla wyzwalacza](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-in-endpoint-url"></a>Akceptowanie parametrów w adresie URL punktu końcowego

Jeśli chcesz, aby adres URL punktu końcowego akceptował parametry, określ ścieżkę względną w wyzwalaczu. Należy również jawnie [ustawić metodę,](#set-method) której oczekuje żądanie HTTP.

1. W wyzwalaczu Żądanie otwórz listę **Dodaj nowy parametr** i wybierz **ścieżkę względną**, która dodaje tę właściwość do wyzwalacza.

   ![Dodaj właściwość "Ścieżka względna" do wyzwolenia](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. We właściwości **Ścieżka względna** określ ścieżkę względną parametru w schemacie JSON, `address/{postalCode}`który ma zaakceptować adres URL, na przykład .

   ![Określ ścieżkę względną dla parametru](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. Aby użyć tego parametru, znajdź i dodaj akcję **Odpowiedź** do aplikacji logiki.

   1. W obszarze wyzwalacza Żądania wybierz pozycję **Nowy krok** > **Dodaj akcję**.

   1. W obszarze **Wybierz akcję**w polu `response` wyszukiwania wprowadź jako filtr.

   1. Z listy akcje wybierz akcję **Odpowiedź.**

1. W Response akcji **Body** właściwości, uwzględnić token, który reprezentuje parametr, który został określony w ścieżce względnej wyzwalacza.

   Załóżmy na przykład, że akcja `Postal Code: {postalCode}`Odpowiedź ma zostać przywrócą .

   We właściwości **Body** `Postal Code: ` wprowadź z przestrzenią końcową. Z wyświetlona dynamiczna lista zawartości wybierz token **postalCode.**

   ![Dodawanie określonego parametru do treści odpowiedzi](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   **Właściwość Body** zawiera teraz wybrany parametr:

   ![Przykładowa treść odpowiedzi z parametrem](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Zapisz aplikację logiki.

    Adres URL punktu końcowego HTTP zawiera teraz ścieżkę względną, na przykład:

    ```http
    https://prod-25.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}
    ```

1. Aby przetestować punkt końcowy HTTP, skopiuj i wklej `123456`zaktualizowany adres URL do innego okna przeglądarki, ale zamień `{postalCode}` go na klawisz Enter i naciśnij klawisz Enter.

   Twoja przeglądarka pokazuje ten tekst:`Postal Code: 123456`

## <a name="call-logic-app-through-http-endpoint"></a>Wywoływanie aplikacji logiki za pośrednictwem punktu końcowego HTTP

Po utworzeniu punktu końcowego HTTP, można wyzwolić `POST` aplikację logiki, wysyłając żądanie HTTP do pełnego adresu URL punktu końcowego. Aplikacje logiki mają wbudowaną obsługę punktów końcowych bezpośredniego dostępu.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Tokeny wygenerowane ze schematu

Po podaniu schematu JSON w wyzwalaczu żądania, projektant aplikacji logiki generuje tokeny dla właściwości w tym schemacie. Następnie można użyć tych tokenów do przekazywania danych za pośrednictwem przepływu pracy aplikacji logiki.

Na przykład jeśli dodasz więcej właściwości, takich jak `"suite"`, do schematu JSON, tokeny dla tych właściwości są dostępne do użycia w kolejnych krokach dla aplikacji logiki. Oto kompletny schemat JSON:

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>Tworzenie zagnieżdżonych aplikacji logiki

Przepływy pracy można zagnieżdżać w aplikacji logiki, dodając inne aplikacje logiki, które mogą odbierać żądania. Aby uwzględnić te aplikacje logiki, wykonaj następujące kroki:

1. W obszarze kroku, w którym chcesz wywołać inną aplikację logiki, wybierz pozycję **Nowy krok** > **Dodaj akcję**.

1. W obszarze **Wybierz akcję**wybierz pozycję **Wbudowane**. W polu wyszukiwania `logic apps` wprowadź jako filtr. Z listy akcji wybierz pozycję **Wybierz przepływ pracy Aplikacje logiki**.

   ![Nest aplikacji logiki wewnątrz bieżącej aplikacji logiki](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   Projektant pokazuje odpowiednie aplikacje logiki do wyboru.

1. Wybierz aplikację logiki do wywołania z bieżącej aplikacji logiki.

   ![Wybieranie aplikacji logiki do wywołania z bieżącej aplikacji logiki](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Informacje o zawartości z żądania przychodzącego

Jeśli typem zawartości żądania `application/json`przychodzącego jest , można odwoływać się do właściwości w żądaniu przychodzącym. W przeciwnym razie ta zawartość jest traktowana jako pojedyncza jednostka binarna, która może być przekazycona do innych interfejsów API. Aby odwołać się do tej zawartości w przepływie pracy aplikacji logiki, należy najpierw przekonwertować tę zawartość.

Na przykład jeśli przekazujesz zawartość, `application/xml` która ma typ, można użyć [ `@xpath()` wyrażenia](../logic-apps/workflow-definition-language-functions-reference.md#xpath) do wykonania wyodrębniania XPath lub użyć [ `@json()` wyrażenia](../logic-apps/workflow-definition-language-functions-reference.md#json) do konwersji XML do JSON. Dowiedz się więcej o pracy z obsługiwanymi [typami zawartości](../logic-apps/logic-apps-content-type.md).

Aby uzyskać dane wyjściowe z żądania przychodzącego, można użyć [ `@triggerOutputs` wyrażenia](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs). Załóżmy na przykład, że masz dane wyjściowe, które wygląda jak w tym przykładzie:

```json
{
   "headers": {
      "content-type" : "application/json"
   },
   "body": {
      "myProperty" : "property value"
   }
}
```

Aby uzyskać dostęp `body` w szczególności do właściwości, można użyć [ `@triggerBody()` wyrażenia](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) jako skrótu.

## <a name="respond-to-requests"></a>Odpowiadanie na żądania

Czasami chcesz odpowiedzieć na niektóre żądania, które wyzwalają aplikację logiki, zwracając zawartość do obiektu wywołującego. Aby skonstruować kod stanu, nagłówek i treść odpowiedzi, użyj akcji Odpowiedź. Ta akcja może pojawić się w dowolnym miejscu w aplikacji logiki, a nie tylko na końcu przepływu pracy. Jeśli aplikacja logiki nie zawiera odpowiedzi akcji, punkt końcowy HTTP odpowiada *natychmiast* ze stanem **202 Akceptowane.**

Dla oryginalnego obiektu wywołującego, aby pomyślnie uzyskać odpowiedź, wszystkie kroki wymagane dla odpowiedzi musi zakończyć się w [terminie limitu czasu żądania,](./logic-apps-limits-and-config.md) chyba że wyzwalana aplikacja logiki jest wywoływana jako zagnieżdżona aplikacja logiki. Jeśli żadna odpowiedź nie jest zwracana w ramach tego limitu, limit czasu żądania przychodzącego i odbiera odpowiedź **limitu czasu klienta 408.**

W przypadku zagnieżdżonych aplikacji logiki nadrzędna aplikacja logiki nadal czekać na odpowiedź, aż wszystkie kroki zostaną zakończone, niezależnie od tego, ile czasu jest wymagane.

### <a name="construct-the-response"></a>Skonstruuj odpowiedź

W treści odpowiedzi można dołączyć wiele nagłówków i dowolnego typu zawartości. Na przykład nagłówek tej odpowiedzi określa, że typ zawartości `application/json` odpowiedzi jest i że `town` `postalCode` treść zawiera wartości dla i właściwości, na podstawie schematu JSON opisane wcześniej w tym temacie dla wyzwalacza żądania.

![Dostarczanie zawartości odpowiedzi dla akcji Odpowiedzi HTTP](./media/logic-apps-http-endpoint/content-for-response-action.png)

Odpowiedzi mają następujące właściwości:

| Właściwość (wyświetlacz) | Właściwość (JSON) | Opis |
|--------------------|-----------------|-------------|
| **Kod stanu** | `statusCode` | Kod stanu HTTP do użycia w odpowiedzi dla żądania przychodzącego. Ten kod może być dowolny prawidłowy kod stanu, który zaczyna się od 2xx, 4xx lub 5xx. Jednakże kody stanu 3xx nie są dozwolone. |
| **Nagłówki** | `headers` | Co najmniej jeden nagłówek do uwzględnienia w odpowiedzi |
| **Treść** | `body` | Obiekt treści, który może być ciągiem, obiektem JSON, a nawet zawartością binarną, do którego odwołuje się poprzedni krok |
||||

Aby wyświetlić definicję JSON dla akcji Odpowiedź i pełną definicję JSON aplikacji logiki, na pasku narzędzi Projektanta aplikacji logiki wybierz **widok Kod**.

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>Pytania i odpowiedzi

#### <a name="q-what-about-url-security"></a>P: Co z zabezpieczeniami adresów URL?

**A:** Platforma Azure bezpiecznie generuje adresy URL wywołania zwrotnego aplikacji logiki przy użyciu [sygnatury dostępu współdzielonego (SYGNATURA DOSTĘPU Współdzielonego).](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature) Ten podpis przechodzi jako parametr zapytania i musi zostać zweryfikowany przed uruchomieniem aplikacji logiki. Platforma Azure generuje podpis przy użyciu unikatowej kombinacji klucza tajnego na aplikację logiki, nazwę wyzwalacza i operację, która jest wykonywana. Więc jeśli ktoś ma dostęp do klucza aplikacji logiki tajnej, nie można wygenerować prawidłowego podpisu.

> [!IMPORTANT]
> W przypadku systemów produkcyjnych i wyższych zabezpieczeń zdecydowanie odradzamy wywoływanie aplikacji logiki bezpośrednio z przeglądarki z następujących powodów:
>
> * Klucz dostępu współdzielonego pojawi się w adresie URL.
> * Nie można zarządzać zasadami zawartości zabezpieczeń ze względu na udostępnione domeny u klientów usługi Azure Logic Apps.

#### <a name="q-can-i-configure-http-endpoints-further"></a>Pyt.: Czy mogę skonfigurować punkty końcowe HTTP dalej?

**A:** Tak, punkty końcowe HTTP obsługują bardziej zaawansowaną konfigurację za pośrednictwem [usługi Azure API Management](../api-management/api-management-key-concepts.md). Ta usługa oferuje również możliwość spójnego zarządzania wszystkimi interfejsami API, w tym aplikacjami logiki, konfigurowania niestandardowych nazw domen, używania większej liczby metod uwierzytelniania i innych, na przykład:

* [Zmienianie metody żądania](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Zmienianie segmentów adresów URL żądania](../api-management/api-management-transformation-policies.md#RewriteURL)
* Konfigurowanie domen zarządzania interfejsami API w [witrynie Azure portal](https://portal.azure.com/)
* Konfigurowanie zasad w celu sprawdzenia uwierzytelniania podstawowego

## <a name="next-steps"></a>Następne kroki

* [Odbieranie przychodzących wywołań HTTPS i odpowiadanie na nie przy użyciu aplikacji Azure Logic Apps](../connectors/connectors-native-reqres.md)