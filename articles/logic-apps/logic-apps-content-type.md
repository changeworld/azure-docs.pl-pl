---
title: Obsługa typów zawartości — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Logic Apps obsługuje typy zawartości w czasie projektowania i w czasie wykonywania
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/20/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 2a9318317d5a01136a42b4fb6d580bafaf53ec4e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60685773"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Obsługa typów zawartości w usłudze Azure Logic Apps

Różnych typów zawartości może przepływać za pośrednictwem aplikacji logiki, na przykład, JSON, XML, pliki proste i danych binarnych. Logic Apps obsługuje wszystkie typy zawartości, niektóre zapewniają natywnej obsługi i nie wymagają rzutowania lub konwersji w aplikacjach logiki. Inne typy mogą wymagać rzutowania lub konwersji zgodnie z potrzebami. W tym artykule opisano, jak Logic Apps obsługuje typy zawartości i jak można prawidłowo rzutować lub konwersji tych typów, gdy jest to konieczne.

Aby określić odpowiedni sposób dla Obsługa typów zawartości, Logic Apps opiera się na `Content-Type` wywołuje wartość nagłówka HTTP, na przykład:

* [Application/json](#application-json) (typ natywny)
* [text/plain](#text-plain) (typ natywny)
* [Aplikacja/xml i application/octet-stream.](#application-xml-octet-stream)
* [Inne typy zawartości](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

Usługa Logic Apps są przechowywane i obsługuje każde żądanie z *application/json* zawartości typu co obiekt notacji JSON (JavaScript). Domyślnie można przeanalizować zawartość JSON bez żadnych rzutowania. Aby analizować żądanie, która zawiera nagłówek typu zawartości "application/json", można użyć wyrażenia. W tym przykładzie zwraca wartość `dog` z `animal-type` tablicy bez rzutowania: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Jeśli pracujesz z danymi JSON, który nie określa nagłówek, można ręcznie rzutować tych danych do formatu JSON przy użyciu [funkcja json()](../logic-apps/workflow-definition-language-functions-reference.md#json), na przykład: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Tworzenie tokenów dla właściwości kodu JSON

Usługa Logic Apps oferuje możliwości służących do generowania tokenów przyjazny dla użytkownika, które reprezentują właściwości w zawartości JSON, aby można było odwoływać się i korzystać z tych właściwości łatwiej w przepływie pracy aplikacji logiki.

* **Wyzwalacza żądania**

  W przypadku tego wyzwalacza można używać w Projektancie aplikacji logiki, można zapewnić schematu JSON, opisujący ładunek, który powinien być wyświetlany. 
  Projektant analizuje zawartość JSON za pomocą tego schematu i generuje tokeny przyjazny dla użytkownika, które reprezentują właściwości we własnych treściach JSON. 
  Można, a następnie łatwo odwoływać się i korzystać z tych właściwości w całym przepływie pracy aplikacji logiki. 
  
  Jeśli nie masz schematu, można wygenerować schematu. 
  
  1. W wyzwalaczu żądania wybierz **Użyj przykładowego ładunku do wygenerowania schematu**.  
  
  2. W obszarze **wpisz lub wklej przykładowy ładunek JSON**, podaj przykładowy ładunek, a następnie wybierz **gotowe**. Na przykład: 

     ![Podaj przykładowy ładunek JSON](./media/logic-apps-content-type/request-trigger.png)

     Wygenerowany schemat jest teraz wyświetlany w wyzwalacza.

     ![Podaj przykładowy ładunek JSON](./media/logic-apps-content-type/generated-schema.png)

     Poniżej przedstawiono podstawową definicję w wyzwalaczu żądania w edytorze kodu widoku:

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

  3. W żądaniu, upewnij się, możesz uwzględnić `Content-Type` nagłówka i ustaw wartość nagłówka `application/json`.

* **Analizowanie JSON akcji**

  Gdy używasz tej akcji w Projektancie aplikacji logiki możesz przeanalizować dane wyjściowe JSON i wygenerować tokeny przyjazny dla użytkownika, które reprezentują właściwości we własnych treściach JSON. 
  Można, a następnie łatwo odwoływać się i korzystać z tych właściwości w całym przepływie pracy aplikacji logiki. Podobnie jak w wyzwalaczu żądania, można zapewnić lub generowanie schematu JSON, który opisuje zawartość JSON, który chcesz przeanalizować. 
  W ten sposób można łatwiej danych można korzystać z usługi Azure Service Bus, usługa Azure Cosmos DB i tak dalej.

  ![Przeanalizuj dane JSON](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>zwykły tekst

Gdy Twoja aplikacja logiki odbiera wiadomości HTTP, które mają `Content-Type` wartość nagłówka `text/plain`, aplikacja logiki zapisuje te komunikaty w pierwotnej formie. Jeśli dołączysz te komunikaty w kolejnych akcjach bez rzutowania żądań przejść za pomocą `Content-Type` wartość nagłówka `text/plain`. 

Na przykład podczas pracy z pliku prostego, możesz otrzymać żądania HTTP z `Content-Type` wartość nagłówka `text/plain` typ zawartości:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Jeśli następnie należy wysłać to żądanie nowsze działa jako treść dla innego żądania, na przykład `@body('flatfile')`, że drugie żądanie ma również `Content-Type` nagłówek, który jest ustawiony na `text/plain`. Podczas pracy z danymi, które jest w formacie zwykłego tekstu, ale nie podano nagłówek, można ręcznie rzutować tych danych do tekstu za pomocą [funkcja string()](../logic-apps/workflow-definition-language-functions-reference.md#string) np. to wyrażenie: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>Aplikacja/xml i application/octet-stream.

Usługa Logic Apps zawsze zachowuje `Content-Type` w odebrane żądanie lub odpowiedź HTTP. Jeśli Twoja aplikacja logiki odbiera zawartość z `Content-Type` równa `application/octet-stream`, i zawierać, czy zawartość w późniejszym akcji bez rzutowania, wychodzące żądanie ma również `Content-Type` równa `application/octet-stream`. W ten sposób Logic Apps może zagwarantować, że dane nie uzyskać utracone podczas przechodzenia przez przepływ pracy. Jednak stan akcji lub danych wejściowych i wyjściowych, są przechowywane w obiekcie JSON podczas, gdy stan przechodzi przez przepływ pracy. 

## <a name="converter-functions"></a>Konwerter funkcji

Aby zachować niektóre typy danych, Logic Apps Konwertuje zawartość binarne ciągu zakodowanego algorytmem base64 z odpowiednie metadane, który zachowuje zarówno `$content` ładunku i `$content-type`, które są automatycznie konwertowane. 

Poniższa lista opisuje, jak Logic Apps Konwertuje zawartość, korzystając z tych [funkcje](../logic-apps/workflow-definition-language-functions-reference.md):

* `json()`: Dane rzutowania `application/json`
* `xml()`: Dane rzutowania `application/xml`
* `binary()`: Dane rzutowania `application/octet-stream`
* `string()`: Dane rzutowania `text/plain`
* `base64()`: Konwertuje zawartość ciągu base64
* `base64toString()`: Konwertuje ciąg zakodowany w formacie base64 `text/plain`
* `base64toBinary()`: Konwertuje ciąg zakodowany w formacie base64 `application/octet-stream`
* `encodeDataUri()`: Koduje ciąg w postaci tablicy bajtów identyfikator URI
* `decodeDataUri()`: Dekoduje `dataUri` do tablicy typu byte

Na przykład, jeśli pojawi się żądanie HTTP gdzie `Content-Type` równa `application/xml`, np. Ta zawartość:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Można rzutować tej zawartości przy użyciu `@xml(triggerBody())` wyrażenie `xml()` i `triggerBody()` funkcji, a następnie użyj później tej zawartości. Alternatywnie można użyć `@xpath(xml(triggerBody()), '/CustomerName')` wyrażenie `xpath()` i `xml()` funkcji. 

## <a name="other-content-types"></a>Inne typy zawartości

Usługa Logic Apps współpracuje z obsługuje inne typy zawartości, ale mogą wymagać ręcznie Pobierz treść komunikatu przez dekodowanie `$content` zmiennej.

Załóżmy, że Twoja aplikacja logiki pobiera wyzwalane przez żądanie przy użyciu `application/x-www-url-formencoded` typ zawartości. Aby zachować dane `$content` zmienna w treści żądania ma ładunek, który jest kodowany jako ciąg base64:

`CustomerName=Frank&Address=123+Avenue`

Ponieważ żądania nie jest w postaci zwykłego tekstu lub danych JSON, żądanie jest przechowywany w akcji w następujący sposób:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

Usługa Logic Apps oferuje funkcje natywne do obsługi danych formularza, na przykład: 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

Lub można ręcznie uzyskać dostęp do danych za pomocą wyrażenia, np. w tym przykładzie:

`@string(body('formdataAction'))` 

Jeżeli chcesz wychodzące żądanie mieć taką samą `application/x-www-url-formencoded` nagłówka typu zawartości, możesz dodać żądania do treści akcji bez żadnych rzutowanie za pomocą wyrażenia takie jak `@body('formdataAction')`. Jednak ta metoda działa tylko, jeśli treść jest jedynym parametrem w `body` danych wejściowych. Jeśli spróbujesz użyć `@body('formdataAction')` wyrażenia w `application/json` żądania, wystąpić błąd w czasie wykonywania, ponieważ treść jest wysyłany zakodowany.
