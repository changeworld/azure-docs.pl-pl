---
title: Wywoływanie, wyzwalacza lub zagnieżdżanie przepływy pracy za pomocą punktów końcowych HTTP — Azure Logic Apps
description: Konfigurowanie punktów końcowych HTTP do wywołania wyzwalacza lub zagnieżdżanie przepływów pracy dla usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.workload: integration
author: ecfan
ms.author: klam; LADocs
ms.reviewer: jehollan, klam, LADocs
manager: carmonm
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.openlocfilehash: b091fb8c6f0b2b655ce0595188c362206f79d702
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495053"
---
# <a name="call-trigger-or-nest-workflows-with-http-endpoints-in-azure-logic-apps"></a>Wywołanie, wyzwalacza lub zagnieżdżonych przepływów pracy, za pomocą punktów końcowych HTTP w usłudze Azure Logic Apps

Synchroniczne punktów końcowych HTTP należy udostępnić jako wyzwalacze aplikacji logiki natywnie, tak, aby może wyzwalać i wywoływanie aplikacji logiki przy użyciu adresu URL. Można także zagnieżdżać przepływów pracy aplikacji logiki, za pomocą wzorca wywoływalne punkty końcowe.

Aby utworzyć punktów końcowych HTTP, możesz dodać te wyzwalacze tak, aby aplikacje Logic Apps może odbierać przychodzące żądania:

* [Żądanie](../connectors/connectors-native-reqres.md)

* [Element Webhook połączenia interfejsu API](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [Element webhook protokołu HTTP](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > Mimo że te przykłady **żądania** wyzwalacz, możesz użyć dowolnej z listy wyzwalaczy HTTP, a wszystkie zasady mają zastosowanie identycznie do innych typów wyzwalaczy.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>Skonfiguruj punkt końcowy HTTP dla aplikacji logiki

Aby utworzyć punkt końcowy HTTP, Dodaj wyzwalacz, który może odbierać żądania przychodzące.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com "Azure Portal"). Przejdź do aplikacji logiki, a następnie otwórz projektanta aplikacji logiki.

2. Dodaj wyzwalacz, który umożliwia aplikacji logiki odbierania żądań przychodzących. Na przykład dodać **żądania** wyzwalacz aplikacji logiki.

3.  W obszarze **schemat JSON treści żądania**, można opcjonalnie wprowadź schemat JSON dla ładunku (dane), których oczekujesz wyzwalacza do odbierania.

    Projektant używa ten schemat do generowania tokenów, których Twoja aplikacja logiki korzystać, analizowania i przekazywanie danych od wyzwalacza, za pomocą przepływu pracy. 
    Więcej informacji o [tokenów wygenerowany na podstawie schematów JSON](#generated-tokens).

    W tym przykładzie wprowadź schemat wyświetlany w Projektancie:

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

    ![Dodawanie akcji żądania][1]

    > [!TIP]
    > 
    > Można wygenerować schematu, aby uzyskać przykładowy ładunek JSON z narzędzia, takiego jak [jsonschema.net](https://jsonschema.net/), lub **żądania** wyzwalacza, wybierając **Użyj przykładowego ładunku do wygenerowania schematu**. 
    > Wprowadź przykładowy ładunek, a następnie wybierz **gotowe**.

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

4.  Zapisz aplikację logiki. W obszarze **żądania HTTP POST do tego adresu URL**, powinny teraz znajdować wygenerowanego wywołania zwrotnego adresu URL, np. w tym przykładzie:

    ![Wywołanie zwrotne wygenerowany adres URL punktu końcowego](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

    Ten adres URL zawiera klucz sygnatury dostępu współdzielonego (SAS) w parametrach zapytania, które są używane do uwierzytelniania. 
    Można również uzyskać adres URL punktu końcowego HTTP z przeglądu aplikacji logiki w witrynie Azure portal. W obszarze **historii wyzwalania**, wybierz wyzwalacza:

    ![Adres URL punktu końcowego HTTP GET z witryny Azure portal][2]

    Można także uzyskać adres URL za sprawą tego wywołania:

    ```
    POST https://management.azure.com/{logic-app-resourceID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>Zmień metodę HTTP dla wyzwalacza

Domyślnie **żądania** wyzwalacza oczekuje żądania HTTP POST, ale można użyć innej metody HTTP. 

> [!NOTE]
> Można określić tylko jedną metodę typu.

1. Na Twojej **żądania** wyzwalacza, wybierz polecenie **Pokaż opcje zaawansowane**.

2. Otwórz **metoda** listy. W tym przykładzie wybierz **UZYSKAĆ** tak, aby adres URL usługi HTTP punktu końcowego można sprawdzić później.

    > [!NOTE]
    > Możesz wybrać dowolną metodę HTTP, lub określić niestandardową metodę dla aplikacji logiki.

    ![Zmień metodę HTTP](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Akceptuje parametrów za pomocą Twojego adresu URL punktu końcowego HTTP

Adres URL punktu końcowego HTTP, aby akceptować parametry, należy dostosować ścieżki względnej tego wyzwalacza.

1. Na Twojej **żądania** wyzwalacza, wybierz polecenie **Pokaż opcje zaawansowane**. 

2. W obszarze **metoda**, określ metodę HTTP, która ma żądania do użycia. W tym przykładzie wybierz **UZYSKAĆ** metody, jeśli jeszcze nie, tak aby mógł testować punkt końcowy HTTP URL.

      > [!NOTE]
      > Po określeniu ścieżki względnej wyzwalacza, musi również jawnie określić metody HTTP dla wyzwalacza.

3. W obszarze **ścieżki względnej**, określ ścieżkę względną do parametru, który adres URL powinien zaakceptować, na przykład `customers/{customerID}`.

    ![Określ metodę HTTP oraz ścieżkę względną do parametru](./media/logic-apps-http-endpoint/relativeurl.png)

4. Aby użyć parametru, Dodaj **odpowiedzi** akcji aplikacji logiki. (W obszarze wyzwalacza, wybierz opcję **nowy krok** > **Dodaj akcję** > **odpowiedzi**) 

5. Do odpowiedzi **treści**, zawierać token parametru, który określiłeś w ścieżce względnej tego wyzwalacza.

    Na przykład, aby zwrócić `Hello {customerID}`, zaktualizuj Twoją odpowiedź **treści** z `Hello {customerID token}`. 
    Lista zawartości dynamicznej należy pojawiają się i Pokaż `customerID` tokenu do wyboru.

    ![Dodaj parametr do treści odpowiedzi](./media/logic-apps-http-endpoint/relativeurlresponse.png)

    Twoje **treści** powinien wyglądać następująco:

    ![Treść odpowiedzi za pomocą parametru](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Zapisz aplikację logiki. 

    Adres URL punktu końcowego HTTP zawiera teraz ścieżki względnej, na przykład: 

    https&#58;//prod-00.southcentralus.logic.azure.com/workflows/f90cb66c52ea4e9cabe0abf4e197deff/triggers/manual/paths/invoke/customers/{customerID}...

7. Aby przetestować punktu końcowego HTTP, skopiuj i wklej adres URL zaktualizowany w innym oknie przeglądarki, ale zastąp `{customerID}` z `123456`, i naciśnij klawisz Enter.

    Przeglądarki powinien być wyświetlony następujący tekst: 

    `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>Tokeny wygenerowany na podstawie schematów JSON aplikacji logiki

Jeśli podasz schematu JSON w swojej **żądania** wyzwalacza, Projektant aplikacji logiki generuje tokeny dla właściwości, w tym schemacie. Można następnie użyć tych tokenów przekazywania danych za pomocą przepływu pracy aplikacji logiki.

Na przykład, jeśli dodasz `title` i `name` właściwości schematu JSON, tokeny są teraz dostępne do użycia w kolejnych krokach przepływu pracy. 

Oto kompletny schematu JSON:

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

## <a name="create-nested-workflows-for-logic-apps"></a>Tworzenie zagnieżdżonych przepływach pracy dla usługi logic apps

Można zagnieżdżać przepływów pracy w aplikacji logiki, dodając inne aplikacje logiki, które mogą odbierać żądania. Aby dołączyć te aplikacje logiki, należy dodać **usługi Azure Logic Apps — wybierz przepływ pracy usługi Logic Apps** akcję wyzwalacza. Następnie można wybrać z kwalifikujących się logiki aplikacji.

![Dodaj inną aplikację logiki](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Wywoływanie lub wyzwalacza aplikacji logiki za pomocą punktów końcowych HTTP

Po utworzeniu punktu końcowego HTTP, można uruchomić aplikację logiki, za pośrednictwem `POST` metody pełny adres URL. Aplikacje logiki mają wbudowaną obsługę punktów końcowych bezpośredniego dostępu.

> [!NOTE] 
> Aby ręcznie uruchomić aplikację logiki w dowolnym momencie, na pasku narzędzi Projektanta aplikacji logiki lub widok kodu aplikacji logiki wybierz pozycję **Uruchom**.

## <a name="reference-content-from-an-incoming-request"></a>Odwołania do zawartości z przychodzącego żądania

Jeśli typ zawartości to `application/json`, mogą przywoływać właściwości z żądania przychodzącego. W przeciwnym razie zawartość jest traktowany jako pojedyncza jednostka binarne, które można przekazać do innych interfejsów API. Aby odwoływać się do tej zawartości wewnątrz przepływu pracy, należy przekonwertować tej zawartości. Na przykład w przypadku przekazania `application/xml` zawartość, możesz użyć `@xpath()` do wyodrębnienia XPath lub `@json()` konwersji XML do formatu JSON. Dowiedz się więcej o [Praca z typami zawartości](../logic-apps/logic-apps-content-type.md).

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

Aby uzyskać dostęp do `body` właściwość ściślej mówiąc, możesz użyć `@triggerBody()` skrótów. 

## <a name="respond-to-requests"></a>Odpowiadanie na żądania

Należy odpowiedzieć na niektórych żądań, które uruchamiają aplikację logiki, zwracając zawartości do obiektu wywołującego. Aby utworzyć kod stanu, nagłówek i treść odpowiedzi, można użyć **odpowiedzi** akcji. Ta akcja może występować w dowolnym miejscu w aplikacji logiki, nie tylko na końcu przepływu pracy.

> [!NOTE] 
> Jeśli Twoja aplikacja logiki nie zawiera **odpowiedzi**, punkt końcowy HTTP odpowiada *natychmiast* z **202 zaakceptowano** stanu. Ponadto oryginalnego żądania sposobem uzyskania odpowiedzi wszystkich kroków wymaganych do odpowiedzi musi zakończyć się w obrębie [limit czasu żądania](./logic-apps-limits-and-config.md) chyba, że należy wywołać przepływ pracy jako zagnieżdżoną aplikację logiki. Jeśli odpowiedź nie występuje w ramach tego limitu, upłynie limit czasu żądania przychodzącego i odbiera odpowiedź HTTP **408 Limit czasu klienta**. Dla aplikacji logiki zagnieżdżonych aplikacja logiki nadrzędnego w dalszym ciągu oczekiwania na odpowiedź do czasu ukończenia, niezależnie od tego, ile czasu jest wymagana.

### <a name="construct-the-response"></a>Konstrukcja odpowiedzi

W treści odpowiedzi może zawierać więcej niż jeden nagłówek i dowolnego typu zawartości. W odpowiedzi na przykład nagłówek Określa, czy odpowiedź ma typ zawartości `application/json`. zawiera treść `title` i `name`, zależnie od schematu JSON, dodano wcześniej **żądania** wyzwalacza.

![Akcja odpowiedzi HTTP][3]

Odpowiedzi korzystać z tych właściwości:

| Właściwość | Opis |
| --- | --- |
| statusCode |Określa kod stanu HTTP do odpowiadania na żądania przychodzącego. Ten kod może być prawidłowym stanem kodu, który rozpoczyna się od 2xx, 4xx lub 5xx. Kody stanu 3xx nie są dozwolone. |
| Nagłówki |Definiuje dowolną liczbę nagłówków do uwzględnienia w odpowiedzi. |
| Treść |Określa obiekt treści, która może być ciąg, obiekt JSON lub zawartości nawet binarnej przywoływany w poprzednim kroku. |

Oto jak schematu JSON wygląda teraz **odpowiedzi** akcji:

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
> Aby wyświetlić kompletną definicję JSON aplikacji logiki Projektant aplikacji logiki, wybierz **widok kodu**.

## <a name="q--a"></a>Pytania i odpowiedzi

#### <a name="q-what-about-url-security"></a>Pyt.: Co adres URL zabezpieczeń?

Odp.: Azure generuje bezpieczne logiki wywołania zwrotnego adresy URL aplikacji przy użyciu sygnatury dostępu współdzielonego (SAS). Ta sygnatura przechodzi przez jako parametr zapytania i muszą zostać zatwierdzone, zanim aplikacja logiki może wyzwalać. Azure generuje podpisu przy użyciu unikatowego połączenia klucz tajny dla aplikacji logiki, nazwa wyzwalacza i operacji, która jest wykonywana. Dlatego jeśli osoba ma dostęp do klucza tajnego logiki aplikacji, nie można wygenerować prawidłowego podpisu.

   > [!IMPORTANT]
   > Dla celów produkcyjnych i systemów zabezpieczeń zdecydowanie zalecamy względem wywoływania aplikacji logiki bezpośrednio z przeglądarki, ponieważ:
   > 
   > * Klucz dostępu współdzielonego zostanie wyświetlony w adresie URL.
   > * Nie można zarządzać bezpiecznego zasad dotyczących zawartości z powodu domeny udostępnione wielu odbiorców aplikacji logiki.

#### <a name="q-can-i-configure-http-endpoints-further"></a>Pyt.: Można skonfigurować więcej punktów końcowych HTTP?

Odp.: Tak, punktów końcowych HTTP obsługuje bardziej zaawansowanych konfiguracji, za pośrednictwem [ **usługi API Management**](../api-management/api-management-key-concepts.md). Usługa ta oferuje również możliwości, które umożliwiają spójne zarządzanie wszystkimi interfejsami API, w tym aplikacje logiki, konfigurowanie niestandardowych nazw domen, użyj więcej metod uwierzytelniania i uzyskać więcej informacji, na przykład:

* [Metoda żądania zmiany](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#SetRequestMethod)
* [Segmenty adresu URL żądania zmiany](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#RewriteURL)
* Konfigurowanie domen usługi API Management na [witryny Azure portal](https://portal.azure.com/ "witryny Azure portal")
* Konfigurowanie zasad, aby sprawdzić, czy uwierzytelnianie podstawowe

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>Pyt.: Co się zmieniło, gdy schemat migracji z wersji zapoznawczej 1 grudnia 2014?

Odp.: Poniżej przedstawiono podsumowanie dotyczące tych zmian:

| 1 grudnia 2014 r. (wersja zapoznawcza) | 1 czerwca 2016 r. |
| --- | --- |
| Kliknij przycisk **odbiornika HTTP** aplikacji interfejsu API |Kliknij przycisk **wyzwalacza ręcznego** (nie aplikacji interfejsu API wymagana) |
| Ustawienia odbiornika HTTP "*automatycznie wysyła odpowiedź*" |Zawierają **odpowiedzi** akcji lub nie ma w definicji przepływu pracy |
| Skonfiguruj uwierzytelnianie Basic lub OAuth |za pomocą usługi API Management |
| Konfigurowanie metody HTTP |W obszarze **Pokaż opcje zaawansowane**, wybierz metodę HTTP |
| Konfigurowanie ścieżki względnej |W obszarze **Pokaż opcje zaawansowane**, Dodaj ścieżkę względną |
| Treść przychodzących za pomocą odwołania `@triggerOutputs().body.Content` |Odwołanie za pośrednictwem `@triggerOutputs().body` |
| **Czy wysłać odpowiedź HTTP** akcji odbiornik HTTP |Kliknij przycisk **odpowiadanie na żądania HTTP** (nie aplikacji interfejsu API wymagana) |

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby zadawać pytania, odpowiadać na nie i patrzeć, co robią inni użytkownicy usługi Azure Logic Apps, odwiedź [forum usługi Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Aby pomóc w ulepszaniu usługi Azure Logic Apps, przesyłaj pomysły lub głosuj na nie w [witrynie opinii użytkowników usługi Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie definicji aplikacji logiki](./logic-apps-author-definitions.md)
* [Obsługa błędów i wyjątków](./logic-apps-exception-handling.md)

[1]: ./media/logic-apps-http-endpoint/manualtrigger.png
[2]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[3]: ./media/logic-apps-http-endpoint/response.png