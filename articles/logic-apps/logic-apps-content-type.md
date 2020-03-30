---
title: Obsługa typów zawartości
description: Dowiedz się, jak obsługiwać różne typy zawartości w przepływach pracy w czasie projektowania i czasie wykonywania w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: ae0abe288edda2ce01311d8533b1f104409efce0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666877"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Obsługa typów zawartości w aplikacjach logiki azure

Różne typy zawartości mogą przepływać za pośrednictwem aplikacji logiki, na przykład JSON, XML, pliki płaskie i dane binarne. Aplikacja logiki obsługuje wszystkie typy zawartości, niektóre mają natywną obsługę i nie wymagają rzutowania lub konwersji w aplikacjach logiki. Inne typy mogą wymagać rzutowania lub konwersji w razie potrzeby. W tym artykule opisano, jak aplikacje logiki obsługuje typy zawartości i jak można poprawnie rzutowania lub konwertowania tych typów, gdy jest to konieczne.

Aby określić odpowiedni sposób obsługi typów zawartości, `Content-Type` aplikacje logiki opiera się na wartości nagłówka w wywołaniach HTTP, na przykład:

* [application/json](#application-json) (typ macierzysty)
* [tekst/zwykły](#text-plain) (typ macierzysty)
* [aplikacja/xml i aplikacja/oktet-strumień](#application-xml-octet-stream)
* [Inne typy zawartości](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

Logic Apps przechowuje i obsługuje każde żądanie z typem zawartości *aplikacji/json* jako obiektem notacji JavaScript (JSON). Domyślnie można przeanalizować zawartość JSON bez rzutowania. Aby przeanalizować żądanie, które ma nagłówek z typem zawartości "application/json", można użyć wyrażenia. W tym przykładzie zwraca wartość `dog` z tablicy `animal-type` bez rzutowania: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Jeśli pracujesz z danymi JSON, które nie określają nagłówka, możesz ręcznie przerzucać te dane do JSON, korzystając na przykład z [funkcji json(),](../logic-apps/workflow-definition-language-functions-reference.md#json)na przykład: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Tworzenie tokenów dla właściwości JSON

Logic Apps zapewnia możliwość generowania tokenów przyjaznych dla użytkownika, które reprezentują właściwości w treści JSON, dzięki czemu można odwoływać się i używać tych właściwości łatwiej w przepływie pracy aplikacji logiki.

* **Wyzwalacz żądania**

  Korzystając z tego wyzwalacza w logice App Designer, można podać schemat JSON, który opisuje ładunek, którego oczekujesz, aby otrzymać. 
  Projektant analizuje zawartość JSON przy użyciu tego schematu i generuje tokeny przyjazne dla użytkownika, które reprezentują właściwości w zawartości JSON. 
  Następnie można łatwo odwoływać się i używać tych właściwości w całym przepływie pracy aplikacji logiki. 
  
  Jeśli nie masz schematu, można wygenerować schemat. 
  
  1. W wyzwalaczu Żądanie wybierz opcję **Użyj przykładowego ładunku do wygenerowania schematu**.  
  
  2. W obszarze **Wprowadzanie lub wklejanie przykładowego ładunku JSON**podaj ładunek próbki, a następnie wybierz pozycję **Gotowe**. Przykład: 

     ![Podaj przykładową ładowność JSON](./media/logic-apps-content-type/request-trigger.png)

     Wygenerowany schemat jest teraz wyświetlany w wyzwalaczu.

     ![Podaj przykładową ładowność JSON](./media/logic-apps-content-type/generated-schema.png)

     Oto podstawowa definicja wyzwalacza żądania w edytorze widoku kodu:

     ```json
     "triggers": { 
        "manual": {
           "type": "Request",
           "kind": "Http",
           "inputs": { 
              "schema": {
                 "type": "object",
                 "properties": {
                    "client": {
                       "type": "object",
                       "properties": {
                          "animal-type": {
                             "type": "array",
                             "items": {
                                "type": "string"
                             },
                          },
                          "name": {
                             "type": "string"
                          }
                       }
                    }
                 }
              }
           }
        }
     }
     ```

  3. W żądaniu upewnij się, `Content-Type` że dodajesz nagłówek i `application/json`ustaw wartość nagłówka na .

* **Akcja Analizuj JSON**

  Korzystając z tej akcji w logic app designer, można przeanalizować dane wyjściowe JSON i generowania tokenów przyjaznych dla użytkownika, które reprezentują właściwości w zawartości JSON. 
  Następnie można łatwo odwoływać się i używać tych właściwości w całym przepływie pracy aplikacji logiki. Podobnie jak wyzwalacz żądania, można podać lub wygenerować schemat JSON, który opisuje zawartość JSON, którą chcesz przeanalizować. 
  W ten sposób można łatwiej korzystać z danych z usługi Azure Service Bus, Usługi Azure Cosmos DB i tak dalej.

  ![Przeanalizuj dane JSON](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>tekst/zwykły

Gdy aplikacja logiki odbiera wiadomości `Content-Type` HTTP, `text/plain`które mają nagłówek ustawiony na , aplikacja logiki przechowuje te wiadomości w postaci nieprzetworzonej. Jeśli te wiadomości zostaną uwzględnione w kolejnych akcjach bez rzutowania, żądania wychodzą z nagłówkiem ustawionym `Content-Type` na `text/plain`. 

Na przykład podczas pracy z plikiem płaskim może pojawić się `Content-Type` żądanie `text/plain` HTTP z nagłówkiem ustawionym na typ zawartości:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Jeśli następnie wyślesz to żądanie w późniejszej akcji jako treść `@body('flatfile')`innego żądania, na `Content-Type` przykład, to `text/plain`drugie żądanie ma również nagłówek, który jest ustawiony na . Jeśli pracujesz z danymi, które są zwykłym tekstem, ale nie określono nagłówka, możesz ręcznie przerzucać te dane do tekstu za pomocą [funkcji string(),](../logic-apps/workflow-definition-language-functions-reference.md#string) takiej jak to wyrażenie: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>aplikacja/xml i aplikacja/oktet-strumień

Aplikacje logiki `Content-Type` zawsze zachowuje w odebranym żądaniu HTTP lub odpowiedzi. Jeśli więc aplikacja logiki `Content-Type` odbiera `application/octet-stream`zawartość z zestawem na , a zawartość zostanie `Content-Type` uwzględniona `application/octet-stream`w późniejszej akcji bez rzutowania, żądanie wychodzące również ma ustawioną na . W ten sposób aplikacje logiki mogą zagwarantować, że dane nie zgubią się podczas przechodzenia przez przepływ pracy. Jednak stan akcji lub dane wejściowe i wyjściowe są przechowywane w obiekcie JSON, podczas gdy stan przechodzi przez przepływ pracy. 

## <a name="converter-functions"></a>Funkcje konwertera

Aby zachować niektóre typy danych, aplikacje logiki konwertuje zawartość do binarnego `$content` ciągu kodowanego `$content-type`base64 z odpowiednimi metadanymi, które zachowuje zarówno ładunek, jak i , które są automatycznie konwertowane. 

Na tej liście opisano sposób, w jaki aplikacje logiki konwertują zawartość podczas korzystania z tych [funkcji:](../logic-apps/workflow-definition-language-functions-reference.md)

* `json()`: Przesyła dane do`application/json`
* `xml()`: Przesyła dane do`application/xml`
* `binary()`: Przesyła dane do`application/octet-stream`
* `string()`: Przesyła dane do`text/plain`
* `base64()`: Konwertuje zawartość na ciąg zakodowany w kodowaniu podstawowym64
* `base64toString()`: Konwertuje ciąg zakodowany w base64 na`text/plain`
* `base64toBinary()`: Konwertuje ciąg zakodowany w base64 na`application/octet-stream`
* `dataUri()`: Konwertuje ciąg na identyfikator URI danych
* `dataUriToBinary()`: Konwertuje identyfikator URI danych na ciąg binarny
* `dataUriToString()`: Konwertuje identyfikator URI danych na ciąg

Na przykład, jeśli zostanie wyświetlenie żądania HTTP, gdzie `Content-Type` jest ustawiona na `application/xml`, na przykład, na przykład:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Tę zawartość można rzutować `@xml(triggerBody())` przy `xml()` użyciu `triggerBody()` wyrażenia z i funkcji, a następnie użyć tej zawartości później. Lub można użyć `@xpath(xml(triggerBody()), '/CustomerName')` wyrażenia z `xpath()` `xml()` i funkcji. 

## <a name="other-content-types"></a>Inne typy zawartości

Logic Apps współpracuje z innymi typami zawartości i obsługuje je, ale może `$content` wymagać ręcznego pobrania treści wiadomości przez dekodowanie zmiennej.

Załóżmy na przykład, że aplikacja logiki `application/x-www-url-formencoded` zostanie wyzwolona przez żądanie z typem zawartości. Aby zachować wszystkie `$content` dane, zmienna w treści żądania ma ładunek, który jest zakodowany jako ciąg base64:

`CustomerName=Frank&Address=123+Avenue`

Ponieważ żądanie nie jest zwykły tekst lub JSON, żądanie jest przechowywane w akcji w następujący sposób:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

Logic Apps udostępnia natywne funkcje do obsługi danych formularza, na przykład: 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

Można też ręcznie uzyskać dostęp do danych przy użyciu wyrażenia, takiego jak w tym przykładzie:

`@string(body('formdataAction'))` 

Jeśli żądanie wychodzące miało mieć `application/x-www-url-formencoded` ten sam nagłówek typu zawartości, możesz dodać żądanie do treści akcji `@body('formdataAction')`bez rzutowania przy użyciu wyrażenia, takiego jak . Jednak ta metoda działa tylko wtedy, gdy `body` treść jest jedynym parametrem w danych wejściowych. Jeśli spróbujesz `@body('formdataAction')` użyć wyrażenia `application/json` w żądaniu, zostanie wyświetlony błąd środowiska uruchomieniowego, ponieważ treść jest wysyłana zakodowana.
