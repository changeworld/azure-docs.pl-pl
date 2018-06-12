---
title: Wywołanie, wyzwalacza lub zagnieździć przepływy pracy z punktów końcowych HTTP - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Konfigurowanie punktów końcowych HTTP połączeń telefonicznych, wyzwalacza lub zagnieździć przepływy pracy dla usługi Azure Logic Apps
services: logic-apps
keywords: przepływy pracy, punktów końcowych HTTP
author: jeffhollan
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.author: klam; LADocs
ms.openlocfilehash: 9c02a0f540f52007412a850b9d69e337743fc35f
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300202"
---
# <a name="call-trigger-or-nest-workflows-with-http-endpoints-in-logic-apps"></a>Wywołaj wyzwalacz, lub zagnieżdżania przepływy pracy z punktów końcowych HTTP w aplikacji logiki

Natywnie mogą uwidaczniać synchroniczne końcowych HTTP jako Wyzwalacze w aplikacji logiki, aby można wyzwolić lub wywołać aplikacje logiki przy użyciu adresu URL. Można zagnieżdżać przepływów pracy w aplikacjach logiki za pomocą wzorca można wywołać punktów końcowych.

Do utworzenia punktów końcowych HTTP, można dodać te wyzwalacze, dzięki czemu aplikacje logiki może odbierać przychodzące żądania:

* [Żądanie](../connectors/connectors-native-reqres.md)

* [Element Webhook połączenia interfejsu API](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [Element webhook protokołu HTTP](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > Chociaż te przykłady użycia **żądania** wyzwalacza, można użyć dowolnego z wymienionych wyzwalaczy HTTP, a wszystkie zasady tak samo dotyczą innych typów wyzwalacza.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>Konfigurowanie punktu końcowego HTTP dla aplikacji logiki

Aby utworzyć punkt końcowy HTTP, należy dodać wyzwalacz, który może odbierać żądań przychodzących.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com "Azure Portal"). Przejdź do aplikacji logiki i otwórz projektanta aplikacji logiki.

2. Dodaj wyzwalacz, który umożliwia aplikacji logiki odbierania żądań przychodzących. Na przykład dodać **żądania** do aplikacji logiki wyzwalacza.

3.  W obszarze **żądania schematu JSON treści**, można opcjonalnie wprowadzić schematu JSON dla ładunku (dane) spodziewasz się wyzwalacz do odbierania.

    Projektant używa tego schematu do generowania tokenów używanych przez aplikację logiki do korzystać, analizuje i przekazywania danych z wyzwalacza przez przepływ pracy. 
    Więcej informacji o [tokeny generowane na podstawie schematów JSON](#generated-tokens).

    Na przykład wprowadź widoczne w Projektancie schematu:

    ```json
    {
      "type": "object",
      "properties": {
        "address": {
          "type": "string"
        }
      },
      "required": [
        "address"
      ]
    }
    ```

    ![Dodaj akcję żądania][1]

    > [!TIP]
    > 
    > Schemat przykładowy ładunek JSON można generować za pomocą narzędzia, takie jak [jsonschema.net](http://jsonschema.net/), lub w **żądania** wyzwalacza, wybierając **ładunku próbki używany do generowania schematu**. 
    > Wprowadź przykładowy ładunek, a następnie wybierz pozycję **gotowe**.

    Na przykład ten przykładowy ładunek:

    ```json
    {
       "address": "21 2nd Street, New York, New York"
    }
    ```

    generuje ten schemat:

    ```json
    {
       "type": "object",
       "properties": {
          "address": {
             "type": "string" 
          }
       }
    }
    ```

4.  Zapisz aplikację logiki. W obszarze **HTTP POST do tego adresu URL**, powinna być teraz widoczna wygenerowanego wywołania zwrotnego adresu URL, tak jak ten przykład:

    ![Wywołanie zwrotne wygenerowany adres URL punktu końcowego](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

    Ten adres URL zawiera klucz dostępu sygnatury dostępu Współdzielonego w parametry zapytania, które są używane do uwierzytelniania. 
    Można również uzyskać adres URL punktu końcowego HTTP z Twojej Omówienie aplikacji logiki w portalu Azure. W obszarze **historii wyzwalacza**, wybierz wyzwalacz:

    ![Adres URL punktu końcowego HTTP GET z portalu Azure][2]

    Można także uzyskać adres URL za sprawą tego wywołania:

    ```
    POST https://management.azure.com/{logic-app-resourceID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>Zmień metodę HTTP wyzwalacza

Domyślnie **żądania** wyzwalacza oczekuje żądania HTTP POST, ale można użyć innej metody HTTP. 

> [!NOTE]
> Można określić tylko jedną metodę typu.

1. W Twojej **żądania** wyzwalania, wybierz **Pokaż zaawansowane opcje**.

2. Otwórz **metody** listy. Na przykład wybierz **UZYSKAĆ** tak, aby można było testować punkt końcowy HTTP URL później.

    > [!NOTE]
    > Wybierz inne metody HTTP lub określić metodę niestandardowych aplikacji logiki.

    ![Zmień metodę HTTP](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Akceptować parametry przez adres URL punktu końcowego HTTP

Adres URL punktu końcowego HTTP, aby zaakceptować parametrów, należy dostosować ścieżkę względną do tego wyzwalacza.

1. W Twojej **żądania** wyzwalania, wybierz **Pokaż zaawansowane opcje**. 

2. W obszarze **metody**, określ metodę HTTP, która ma żądania do użycia. Na przykład wybierz **UZYSKAĆ** metody, jeśli nie jest jeszcze, dzięki czemu można sprawdzić adres URL punkt końcowy HTTP.

      > [!NOTE]
      > Po określeniu ścieżki względnej wyzwalacza metody HTTP należy również jawnie określić wyzwalacza.

3. W obszarze **ścieżki względnej**, określ ścieżkę względną w parametrze adres URL powinien akceptują, na przykład `customers/{customerID}`.

    ![Określ metodę HTTP i ścieżka względna dla parametru](./media/logic-apps-http-endpoint/relativeurl.png)

4. Aby używać parametru, Dodaj **odpowiedzi** akcji do aplikacji logiki. (W obszarze wyzwalacz, wybierz **nowy krok** > **Dodaj akcję** > **odpowiedzi**) 

5. Do odpowiedzi **treści**, obejmuje tokenu dla parametru określonej ścieżki względnej do tego wyzwalacza.

    Na przykład, aby zwrócić `Hello {customerID}`, aktualizacja do odpowiedzi **treści** z `Hello {customerID token}`. 
    Listy zawartości dynamicznej powinna występować i Pokaż `customerID` token do wyboru.

    ![Dodawanie parametru do treści odpowiedzi](./media/logic-apps-http-endpoint/relativeurlresponse.png)

    Twoje **treści** powinna wyglądać następująco:

    ![Treść odpowiedzi z parametrem](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Zapisz aplikację logiki. 

    Adres URL punktu końcowego HTTP zawiera teraz ścieżki względnej, na przykład: 

    https&#58;//prod-00.southcentralus.logic.azure.com/workflows/f90cb66c52ea4e9cabe0abf4e197deff/triggers/manual/paths/invoke/customers/{customerID}...

7. Aby przetestować punktu końcowego HTTP, skopiuj i wklej adres URL zaktualizowane w innym oknie przeglądarki, ale zastępuje `{customerID}` z `123456`, i naciśnij klawisz Enter.

    Ten tekst powinien być wyświetlony w przeglądarce: 

    `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>Tokeny generowane na podstawie schematów JSON aplikacji logiki

Jeśli podasz schematu JSON w Twojej **żądania** wyzwalacza, Projektant aplikacji logiki generuje tokeny na potrzeby właściwości w tym schemacie. Można następnie użyć tych tokenów przekazywania danych za pomocą przepływu pracy aplikacji logiki.

Na przykład, jeśli dodasz `title` i `name` właściwości do schematu JSON, tokenów są teraz dostępne do użycia w kolejnych krokach przepływu pracy. 

Poniżej przedstawiono pełną schematu JSON:

```json
{
   "type": "object",
   "properties": {
      "address": {
         "type": "string"
      },
      "title": {
         "type": "string"
      },
      "name": {
         "type": "string"
      }
   },
   "required": [
      "address",
      "title",
      "name"
   ]
}
```

## <a name="create-nested-workflows-for-logic-apps"></a>Utwórz zagnieżdżone przepływy pracy dla usługi logic apps

Przepływy pracy można zagnieździć w aplikacji logiki przez dodanie innych aplikacji logiki, które mogą odbierać żądań. Aby uwzględnić te aplikacje logiki, Dodaj **Azure Logic Apps — wybierz przepływ pracy aplikacji logiki** akcji wyzwalacz. Następnie możesz wybrać z kwalifikujących się logiki aplikacji.

![Dodaj inną aplikację logiki](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Wyzwalacza logiki aplikacji za pomocą punktów końcowych HTTP lub wywołać

Po utworzeniu punktu końcowego HTTP, możesz wyzwolić aplikację logiki za pośrednictwem `POST` metodę pełny adres URL. Aplikacje logiki mają wbudowaną obsługę dostęp bezpośredni punktów końcowych.

> [!NOTE] 
> Aby ręcznie uruchomić aplikację logiki w dowolnym momencie, na pasku narzędzi Projektanta aplikacji logiki lub widoku kodu aplikacji logiki, wybierz **Uruchom**.

## <a name="reference-content-from-an-incoming-request"></a>Odwołanie do zawartości z przychodzącego żądania

Jeśli typ zawartości jest `application/json`, możesz odwoływać właściwości z żądania przychodzącego. W przeciwnym razie zawartość jest traktowany jako pojedyncza jednostka binarne, które można przekazać do innych interfejsów API. Aby odwołać tej zawartości w przepływie pracy, należy przekonwertować tej zawartości. Na przykład w przypadku przekazania `application/xml` zawartości, można użyć `@xpath()` do wyodrębnienia XPath lub `@json()` do konwertowania XML do formatu JSON. Dowiedz się więcej o [Praca z typami zawartości](../logic-apps/logic-apps-content-type.md).

Aby uzyskać dane wyjściowe z żądania przychodzącego, można użyć `@triggerOutputs()` funkcji. Dane wyjściowe może wyglądać następująco:

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

Aby uzyskać dostęp do `body` właściwości w szczególności służy `@triggerBody()` skrótów. 

## <a name="respond-to-requests"></a>Odpowiadanie na żądania

Możesz chcieć odpowiadają na określone żądania, które uruchamiają aplikację logiki, zwracając zawartości do obiektu wywołującego. Aby utworzyć kod stanu, nagłówek i treść odpowiedzi, można użyć **odpowiedzi** akcji. Ta akcja może występować w dowolnym miejscu w aplikacji logiki, nie tylko na końcu przepływu pracy.

> [!NOTE] 
> Jeśli nie zawiera aplikacji logiki **odpowiedzi**, odpowiada punkt końcowy HTTP *natychmiast* z **202 zaakceptowane** stanu. Ponadto oryginalnego żądania pobrać odpowiedzi wszystkich kroków wymaganych do odpowiedzi musi zakończyć w ciągu [limit czasu żądania](./logic-apps-limits-and-config.md) chyba, że wywołanie przepływu pracy jako aplikację logiki zagnieżdżonych. Jeśli się nie dzieje nie otrzymano odpowiedzi w ramach tego limitu, żądania przychodzącego, jeśli upłynie limit czasu i odbiera odpowiedź HTTP **408 Limit czasu klienta**. Dla aplikacji logiki zagnieżdżonych nadrzędnej aplikacji logiki w dalszym ciągu oczekiwania na odpowiedź do czasu ukończenia, niezależnie od tego, ile czasu jest wymagana.

### <a name="construct-the-response"></a>Konstrukcja odpowiedzi

W treści odpowiedzi może zawierać więcej niż jeden nagłówek i dowolnego typu zawartości. W odpowiedzi na przykład nagłówek Określa, czy odpowiedź ma typ zawartości `application/json`. i treść zawiera `title` i `name`na schematu JSON wcześniej zaktualizowane na podstawie **żądania** wyzwalacza.

![Akcja odpowiedzi HTTP][3]

Odpowiedzi ma następujące właściwości:

| Właściwość | Opis |
| --- | --- |
| statusCode |Określa kod stanu HTTP dla odpowiada na żądania przychodzącego. Ten kod może być prawidłowym stanem kodu, który rozpoczyna się od 2xx, 4xx lub 5xx. Kody stanu 3xx nie są dozwolone. |
| nagłówki |Definiuje dowolną liczbę nagłówków do uwzględnienia w odpowiedzi. |
| treść |Określa obiekt treści, która może być ciągiem, obiekt JSON lub nawet binarny zawartości przywoływanej z poprzedniego kroku. |

Oto co schematu JSON wygląda teraz **odpowiedzi** akcji:

``` json
"Response": {
   "inputs": {
      "body": {
         "title": "@{triggerBody()?['title']}",
         "name": "@{triggerBody()?['name']}"
      },
      "headers": {
           "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {},
   "type": "Response"
}
```

> [!TIP]
> Aby wyświetlić pełną definicji JSON aplikację logiki w Projektancie aplikacji logiki, wybierz **widok Kod**.

## <a name="q--a"></a>Pytania i odpowiedzi

#### <a name="q-what-about-url-security"></a>Pytanie: jak adres URL zabezpieczeń?

A: azure generuje bezpieczne logiki wywołania zwrotnego adresy URL aplikacji przy użyciu udostępnionych podpis dostępu (SAS). Ta sygnatura przekazuje jako parametr zapytania i musi zostać zweryfikowany przed mogą wyzwalać aplikację logiki. Platforma Azure generuje podpisu przy użyciu unikatowej kombinacji wartości klucza tajnego dla każdej aplikacji logiki, nazwa wyzwalacza i operacji, która jest wykonywana. Dlatego jeśli ktoś nie ma dostępu do klucza tajnego logikę aplikacji, nie można wygenerować prawidłowego podpisu.

   > [!IMPORTANT]
   > Produkcyjnego i systemów zabezpieczeń zaleca przed wywołaniem aplikację logiki bezpośrednio z przeglądarki, ponieważ:
   > 
   > * Klucz dostępu współdzielonego pojawia się w adresie URL.
   > * Nie można zarządzać bezpieczna zasady zawartości z powodu domeny udostępnione wielu odbiorców aplikacji logiki.

#### <a name="q-can-i-configure-http-endpoints-further"></a>Pytanie: czy można skonfigurować dodatkowe punkty końcowe HTTP?

Odpowiedź: tak punktów końcowych HTTP obsługuje bardziej zaawansowanych konfiguracji za pomocą [ **zarządzanie interfejsami API**](../api-management/api-management-key-concepts.md). Usługa ta oferuje również możliwość można spójnie zarządzać wszystkie swoje interfejsy API, łącznie z aplikacji logiki, konfigurowanie niestandardowych nazw domen, użyj więcej metod uwierzytelniania i inne, na przykład:

* [Metoda żądania zmiany](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#SetRequestMethod)
* [Segmenty adresu URL żądania zmiany](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#RewriteURL)
* Konfigurowanie domeny zarządzanie interfejsami API w [portalu Azure](https://portal.azure.com/ "portalu Azure")
* Skonfiguruj zasady, aby sprawdzić, czy uwierzytelnianie podstawowe

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>Pytanie: jakie zmieniony podczas migracji schematu w 1 grudnia 2014 r. w wersji zapoznawczej?

Odpowiedź: w tym miejscu znajduje się podsumowanie dotyczące tych zmian:

| Wersja zapoznawcza 1 grudnia 2014 r. | 1 czerwca 2016 r. |
| --- | --- |
| Kliknij przycisk **odbiornik HTTP** aplikacji interfejsu API |Kliknij przycisk **ręczne wyzwalacza** (wymagane dla żadnej aplikacji interfejsu API) |
| Ustawienia odbiornika HTTP "*automatycznie wysyła odpowiedź*" |Zawierają **odpowiedzi** akcji lub nie znajduje się w definicji przepływu pracy |
| Skonfiguruj uwierzytelnianie podstawowe lub OAuth |za pomocą interfejsu API zarządzania |
| Skonfiguruj HTTP — metoda |W obszarze **Pokaż zaawansowane opcje**, wybierz metodę HTTP |
| Konfigurowanie ścieżki względnej |W obszarze **Pokaż zaawansowane opcje**, Dodaj ścieżkę względną |
| Treść przychodzących przez odwołanie `@triggerOutputs().body.Content` |Odwołanie za pośrednictwem `@triggerOutputs().body` |
| **Wyślij odpowiedź HTTP** akcji na odbiornik HTTP |Kliknij przycisk **odpowiadać na żądania HTTP** (wymagane dla żadnej aplikacji interfejsu API) |

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby zadawać pytania, odpowiadać na nie i patrzeć, co robią inni użytkownicy usługi Azure Logic Apps, odwiedź [forum usługi Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Aby pomóc w ulepszaniu usługi Azure Logic Apps, przesyłaj pomysły lub głosuj na nie w [witrynie opinii użytkowników usługi Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie definicji aplikacji logiki](./logic-apps-author-definitions.md)
* [Obsługa błędów i wyjątków](./logic-apps-exception-handling.md)

[1]: ./media/logic-apps-http-endpoint/manualtrigger.png
[2]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[3]: ./media/logic-apps-http-endpoint/response.png