---
title: Wywoływanie, wyzwalanie lub zagnieżdżanie aplikacji logiki — Azure Logic Apps
description: Konfigurowanie punktów końcowych HTTP do wywoływania, wyzwalania lub zagnieżdżania przepływów pracy aplikacji logiki w Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.workload: integration
author: ecfan
ms.author: klam
ms.reviewer: jehollan, klam, LADocs
manager: carmonm
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.openlocfilehash: 4fc20c4b1314d953ea979192c81b2c264292d3af
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73041923"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Wywoływanie, wyzwalanie lub zagnieżdżanie aplikacji logiki za pomocą punktów końcowych HTTP w Azure Logic Apps

Można natywnie uwidocznić synchroniczne punkty końcowe HTTP jako Wyzwalacze w usłudze Logic Apps, dzięki czemu można wyzwalać lub wywoływać aplikacje logiki za pośrednictwem adresu URL. Możesz również zagnieżdżać przepływy pracy w aplikacjach logiki przy użyciu wzorca wywoływanych punktów końcowych.

Aby utworzyć punkty końcowe HTTP, można dodać te wyzwalacze, aby aplikacje logiki mogły odbierać przychodzące żądania:

* [Żądanie](../connectors/connectors-native-reqres.md)

* [Element webhook połączenia interfejsu API](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [Element webhook protokołu HTTP](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > Chociaż te przykłady używają wyzwalacza **żądania** , można użyć dowolnego z wymienionych na liście wyzwalaczy opartych na żądaniach, a wszystkie zasady identycznie stosują się do innych typów wyzwalacza.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>Konfigurowanie punktu końcowego HTTP dla aplikacji logiki

Aby utworzyć punkt końcowy HTTP, Dodaj wyzwalacz, który może odbierać żądania przychodzące.

1. Zaloguj się do [portalu Azure](https://portal.azure.com "Azure Portal"). Przejdź do aplikacji logiki i Otwórz projektanta aplikacji logiki.

1. Dodaj wyzwalacz, który umożliwia aplikacji logiki odbieranie żądań przychodzących. Na przykład Dodaj wyzwalacz **żądania** do aplikacji logiki.

1. W obszarze **schemat JSON treści żądania**można opcjonalnie wprowadzić schemat JSON dla ładunku (danych), który oczekuje wyzwalacza.

   Projektant używa tego schematu do generowania tokenów, które mogą być używane przez aplikację logiki do używania, analizowania i przekazywania danych z wyzwalacza za pośrednictwem przepływu pracy. Dowiedz się więcej o [tokenach wygenerowanych na podstawie schematów JSON](#generated-tokens).

   Na potrzeby tego przykładu wprowadź ten schemat, jak pokazano w projektancie:

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

   ![Podaj schemat JSON dla akcji żądania](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   > [!TIP]
   >
   > Schemat przykładowego ładunku JSON można wygenerować za pomocą narzędzia, takiego jak [jsonschema.NET](https://jsonschema.net/) lub w wyzwalaczu **żądania** , wybierając pozycję **Użyj przykładowego ładunku do wygenerowania schematu**. Wprowadź przykładowy ładunek i wybierz pozycję **gotowe**.

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

1. Zapisz aplikację logiki. W obszarze **http post na ten adres URL**należy teraz znaleźć wygenerowany adres URL wywołania zwrotnego, taki jak w poniższym przykładzie:

   ![Wygenerowany adres URL wywołania zwrotnego dla punktu końcowego](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   Ten adres URL zawiera klucz sygnatury dostępu współdzielonego (SAS) w parametrach zapytania, które są używane do uwierzytelniania. Możesz również uzyskać adres URL punktu końcowego HTTP z poziomu aplikacji logiki w Azure Portal. W obszarze **historia wyzwalacza**Wybierz wyzwalacz:

   ![Pobierz adres URL punktu końcowego HTTP z Azure Portal](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   Możesz też uzyskać adres URL, wykonując następujące wywołanie:

    ```http
    POST https://management.azure.com/{logic-app-resource-ID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>Zmiana metody HTTP dla wyzwalacza

Domyślnie wyzwalacz **żądania** oczekuje na żądanie HTTP POST, ale można użyć innej metody http.

> [!NOTE]
> Można określić tylko jeden typ metody.

1. W wyzwalaczu **żądania** wybierz pozycję **Pokaż opcje zaawansowane**.

2. Otwórz listę **metod** . Na potrzeby tego przykładu wybierz pozycję **Pobierz** , aby później można było przetestować adres URL punktu końcowego http.

   > [!NOTE]
   > Można wybrać dowolną inną metodę HTTP lub określić metodę niestandardową dla własnej aplikacji logiki.

   ![Wybierz metodę HTTP, która ma być używana na potrzeby żądania](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Akceptuj parametry za pośrednictwem adresu URL punktu końcowego HTTP

Jeśli chcesz, aby adres URL punktu końcowego HTTP mógł akceptować parametry, Dostosuj ścieżkę względną wyzwalacza.

1. W wyzwalaczu **żądania** wybierz pozycję **Pokaż opcje zaawansowane**. 

2. W obszarze **Metoda**Określ metodę http, która ma być używana przez żądanie. Na potrzeby tego przykładu wybierz metodę **Get** , jeśli jeszcze tego nie zrobiono, aby można było przetestować adres URL punktu końcowego http.

   > [!NOTE]
   > Po określeniu ścieżki względnej dla wyzwalacza należy również jawnie określić metodę HTTP dla wyzwalacza.

3. W obszarze **ścieżka względna**określ ścieżkę względną dla parametru, który powinien zostać zaakceptowany przez adres URL, na przykład `customers/{customerID}`.

   ![Określ metodę HTTP i ścieżkę względną dla parametru](./media/logic-apps-http-endpoint/relative-path-url-value.png)

4. Aby użyć parametru, Dodaj akcję **odpowiedzi** do aplikacji logiki. (W obszarze wyzwalacza wybierz pozycję **nowy krok**  > **Dodaj akcję**  > **odpowiedź**) 

5. W **treści**odpowiedzi Dołącz token dla parametru, który został określony w ścieżce względnej wyzwalacza.

   Na przykład, aby zwrócić `Hello {customerID}`, zaktualizuj **treść** odpowiedzi przy użyciu `Hello {customerID token}`. Powinna zostać wyświetlona lista zawartości dynamicznej, na której można wybrać token `customerID`.

   ![Dodaj parametr do treści odpowiedzi](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   **Treść** powinna wyglądać podobnie do tego przykładu:

   ![Przykładowa treść odpowiedzi z parametrem](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Zapisz aplikację logiki. 

    Adres URL punktu końcowego protokołu HTTP zawiera teraz ścieżkę względną, na przykład: 

    ```http
    https://prod-00.southcentralus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/customers/{customerID}...
    ```

7. Aby przetestować punkt końcowy HTTP, skopiuj i wklej zaktualizowany adres URL do innego okna przeglądarki, ale Zastąp `{customerID}` `123456` i naciśnij klawisz ENTER.

   W przeglądarce powinien zostać wyświetlony następujący tekst: `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>Tokeny wygenerowane na podstawie schematów JSON dla aplikacji logiki

Po podaniu schematu JSON w wyzwalaczu **żądania** projektant aplikacji logiki generuje tokeny dla właściwości w tym schemacie. Następnie można użyć tych tokenów do przekazywania danych za pomocą przepływu pracy aplikacji logiki.

Jeśli na przykład dodasz właściwości `title` i `name` do schematu JSON, ich tokeny będą teraz dostępne do użycia w późniejszych krokach przepływu pracy. 

Oto kompletny schemat JSON:

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

## <a name="create-nested-workflows-for-logic-apps"></a>Tworzenie zagnieżdżonych przepływów pracy dla aplikacji logiki

Przepływy pracy w aplikacji logiki można zagnieżdżać, dodając inne aplikacje logiki, które mogą odbierać żądania. Aby dołączyć te aplikacje logiki, Dodaj **Azure Logic Apps — wybierz akcję przepływu pracy Logic Apps** do wyzwalacza. Następnie możesz wybrać spośród kwalifikujących się aplikacji logiki.

![Zagnieżdżanie aplikacji logiki w bieżącej aplikacji logiki](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Wywoływanie lub wyzwalanie aplikacji logiki za pośrednictwem punktów końcowych HTTP

Po utworzeniu punktu końcowego HTTP można wyzwolić aplikację logiki za pomocą metody `POST` do pełnego adresu URL. Aplikacje logiki mają wbudowaną obsługę punktów końcowych dostępu bezpośredniego.

> [!NOTE] 
> Aby ręcznie uruchomić aplikację logiki w dowolnym momencie, na pasku narzędzi projektant aplikacji logiki lub widok kodu aplikacji logiki wybierz pozycję **Uruchom**.

## <a name="reference-content-from-an-incoming-request"></a>Odwołuje się do zawartości z przychodzącego żądania

Jeśli typ zawartości to `application/json`, można odwoływać się do właściwości z żądania przychodzącego. W przeciwnym razie zawartość jest traktowana jako pojedyncza jednostka binarna, którą można przekazać do innych interfejsów API. Aby odwołać się do tej zawartości w przepływie pracy, należy przekonwertować tę zawartość. Na przykład, Jeśli przekażesz `application/xml` zawartość, możesz użyć `@xpath()` do wyodrębniania XPath lub `@json()` konwersji XML na format JSON. Dowiedz się więcej [na temat pracy z typami zawartości](../logic-apps/logic-apps-content-type.md).

Aby uzyskać dane wyjściowe z przychodzącego żądania, można użyć funkcji `@triggerOutputs()`. Dane wyjściowe mogą wyglądać podobnie do tego przykładu:

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

Aby uzyskać dostęp do właściwości `body` w specjalny sposób, można użyć skrótu `@triggerBody()`.

## <a name="respond-to-requests"></a>Odpowiadanie na żądania

Możesz chcieć odpowiedzieć na określone żądania, które uruchamiają aplikację logiki, zwracając zawartość do obiektu wywołującego. Do konstruowania kodu stanu, nagłówka i treści odpowiedzi można użyć akcji **odpowiedzi** . Ta akcja może pojawić się w dowolnym miejscu w aplikacji logiki, a nie tylko na końcu przepływu pracy.

> [!NOTE] 
> Jeśli aplikacja logiki nie zawiera **odpowiedzi**, punkt końcowy HTTP odpowie *natychmiast* po **zaakceptowanym stanie 202** . Ponadto w przypadku oryginalnego żądania pobrania odpowiedzi wszystkie kroki wymagane do odpowiedzi muszą zakończyć się w ramach limitu [czasu żądania](./logic-apps-limits-and-config.md) , chyba że jest to wywołanie przepływu pracy jako zagnieżdżonej aplikacji logiki. Jeśli w tym limicie nie ma odpowiedzi, żądanie przychodzące **przekroczy limit czasu klienta**i odbierze odpowiedź HTTP 408. W przypadku zagnieżdżonych aplikacji logiki aplikacja nadrzędna aplikacji logiki kontynuuje oczekiwanie na odpowiedź, dopóki nie zostanie ukończona, niezależnie od tego, ile czasu jest wymagane.

### <a name="construct-the-response"></a>Konstruowanie odpowiedzi

W treści odpowiedzi można uwzględnić więcej niż jeden nagłówek i dowolny typ zawartości. W przykładzie odpowiedzi nagłówek określa, że odpowiedź ma typ zawartości `application/json`. treść zawiera `title` i `name`, w oparciu o schemat JSON zaktualizowany wcześniej dla wyzwalacza **żądania** .

![Podaj zawartość odpowiedzi dla akcji odpowiedzi HTTP](./media/logic-apps-http-endpoint/content-for-response-action.png)

Odpowiedzi mają następujące właściwości:

| Właściwość | Opis |
| --- | --- |
| Stanu |Określa kod stanu HTTP odpowiadający na żądanie przychodzące. Ten kod może być dowolnym prawidłowym kodem stanu zaczynającym się od 2xx, 4xx lub 5xx. Jednak kody stanu 3xx są niedozwolone. |
| Nagłówka |Definiuje dowolną liczbę nagłówków do uwzględnienia w odpowiedzi. |
| jednostce |Określa obiekt treści, który może być ciągiem, obiektem JSON lub nawet zawartością binarną, do której odwołuje się poprzedni krok. |

Oto, co jest teraz schematem JSON dla akcji **odpowiedzi** :

``` json
"Response": {
   "type": "Response",
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
   "runAfter": {}
}
```

> [!TIP]
> Aby wyświetlić pełną definicję JSON dla aplikacji logiki, w Projektancie aplikacji logiki wybierz **Widok kod**.

## <a name="q--a"></a>Pytania i odpowiedzi

#### <a name="q-what-about-url-security"></a>P: Jakie są zabezpieczenia adresów URL?

Odp.: usługa Azure bezpiecznie generuje adresy URL wywołania zwrotnego aplikacji logiki przy użyciu sygnatury dostępu współdzielonego (SAS). Ten podpis przechodzi przez parametr zapytania i musi być sprawdzony, aby można było uruchomić aplikację logiki. Platforma Azure generuje podpis przy użyciu unikatowej kombinacji klucza tajnego na aplikację logiki, nazwę wyzwalacza i wykonywaną operację. Jeśli jednak ktoś nie ma dostępu do klucza aplikacji logiki tajnej, nie może wygenerować prawidłowego podpisu.

   > [!IMPORTANT]
   > W przypadku systemów produkcyjnych i zabezpieczających zdecydowanie zalecamy wywołanie aplikacji logiki bezpośrednio z przeglądarki, ponieważ:
   > 
   > * Klucz dostępu współdzielonego zostanie wyświetlony w adresie URL.
   > * Nie można zarządzać bezpiecznymi zasadami zawartości ze względu na domeny udostępnione przez klientów aplikacji logiki.

#### <a name="q-can-i-configure-http-endpoints-further"></a>P: Czy można już skonfigurować punkty końcowe HTTP?

Odp.: tak, punkty końcowe HTTP obsługują bardziej zaawansowaną konfigurację za pośrednictwem [usługi Azure API Management](../api-management/api-management-key-concepts.md). Ta usługa oferuje również możliwość spójnego zarządzania wszystkimi interfejsami API, w tym aplikacjami logiki, Konfigurowanie niestandardowych nazw domen, korzystanie z większej liczby metod uwierzytelniania i inne, na przykład:

* [Zmień metodę żądania](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Zmiana segmentów adresu URL żądania](../api-management/api-management-transformation-policies.md#RewriteURL)
* Skonfiguruj domeny API Management w [Azure Portal](https://portal.azure.com/)
* Konfigurowanie zasad do sprawdzania uwierzytelniania podstawowego

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>P: Co zmieniono, gdy schemat został zmigrowany z wersji zapoznawczej 1 grudnia, 2014?

Odp.: Oto podsumowanie dotyczące tych zmian:

| 1 grudnia, wersja zapoznawcza 2014 | 1 czerwca 2016 |
| --- | --- |
| Kliknij pozycję Aplikacja interfejsu API **odbiornika http** |Kliknij pozycję **wyzwalacz ręczny** (nie jest wymagana żadna aplikacja interfejsu API) |
| Ustawienie odbiornika HTTP "*wysyła odpowiedź automatycznie*" |Dołącz akcję **odpowiedzi** lub nie w definicji przepływu pracy |
| Konfigurowanie uwierzytelniania podstawowego lub OAuth |za pośrednictwem API Management |
| Konfiguruj metodę HTTP |W obszarze **Pokaż opcje zaawansowane**wybierz metodę http |
| Konfiguruj ścieżkę względną |W obszarze **Pokaż opcje zaawansowane**Dodaj ścieżkę względną |
| Odwołuje się do przychodzącej treści za pomocą `@triggerOutputs().body.Content` |Informacje `@triggerOutputs().body` |
| **Wyślij akcję odpowiedzi HTTP** na ODBIORNIKu http |Kliknij pozycję **Odpowiedz na żądanie HTTP** (nie jest wymagana żadna aplikacja interfejsu API) |

## <a name="next-steps"></a>Następne kroki

* [Tworzenie definicji aplikacji logiki](logic-apps-author-definitions.md)
* [Obsługa błędów i wyjątków](logic-apps-exception-handling.md)