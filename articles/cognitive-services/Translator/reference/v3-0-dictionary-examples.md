---
title: Przykłady interfejs API tłumaczenia tekstu w usłudze Translator słownika
titleSuffix: Azure Cognitive Services
description: Użyj metody przykładów słownika interfejs API tłumaczenia tekstu w usłudze Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: 15d23016df9b0c85b9d252b4c4a9ea48d3608f75
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595056"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Interfejs API tłumaczenia tekstu w usłudze Translator 3,0: Przykłady słowników

Zawiera przykłady pokazujące, jak terminy w słowniku są używane w kontekście. Ta operacja jest używana wspólnie z funkcją [Lookup dictionary](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Adres URL żądania

`POST` Wyślij żądanie do:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przesłane na ciągu zapytania są następujące:

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>api-version</td>
    <td>*Wymagany parametr*.<br/>Wersja interfejsu API żądana przez klienta. Wartość musi być `3.0`.</td>
  </tr>
  <tr>
    <td>from</td>
    <td>*Wymagany parametr*.<br/>Określa język tekstu wejściowego. Język źródłowy musi być jednym z [obsługiwanych języków](./v3-0-languages.md) zawartych w `dictionary` zakresie.</td>
  </tr>
  <tr>
    <td>to</td>
    <td>*Wymagany parametr*.<br/>Określa język tekstu wyjściowego. Język docelowy musi być jednym z [obsługiwanych języków](./v3-0-languages.md) zawartych w `dictionary` zakresie.</td>
  </tr>
</table>

Nagłówki żądań obejmują:

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>Nagłówki uwierzytelniania</td>
    <td><em>Wymagany nagłówek żądania</em>.<br/>Zobacz <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostępne opcje uwierzytelniania</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Wymagany nagłówek żądania*.<br/>Określa typ zawartości ładunku. Możliwe wartości to: `application/json`.</td>
  </tr>
  <tr>
    <td>Długość zawartości</td>
    <td>*Wymagany nagłówek żądania*.<br/>Długość treści żądania.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Opcjonalnie*.<br/>Wygenerowany przez klienta identyfikator GUID służący do unikatowej identyfikacji żądania. Możesz pominąć ten nagłówek, jeśli dołączysz identyfikator śledzenia w ciągu zapytania przy użyciu parametru zapytania o nazwie `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiektem JSON o następujących właściwościach:

  * `Text`: Ciąg określający termin do wyszukania. Powinna to być wartość `normalizedText` pola z retłumaczenia poprzedniego żądania [wyszukiwania słownika](./v3-0-dictionary-lookup.md) . Może być również wartością `normalizedSource` pola.

  * `Translation`: Ciąg określający przetłumaczony tekst, który został wcześniej zwrócony przez operację [wyszukiwania słownika](./v3-0-dictionary-lookup.md) . Powinna to być wartość z `normalizedTarget` pola `translations` na liście odpowiedzi na [Wyszukiwanie słownika](./v3-0-dictionary-lookup.md) . Usługa zwróci przykłady dla określonej pary wyrazów dla docelowej lokalizacji źródłowej.

Przykład:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Obowiązują następujące ograniczenia:

* Tablica może zawierać co najwyżej 10 elementów.
* Wartość tekstowa elementu tablicy nie może być dłuższa niż 100 znaków, w tym spacje.

## <a name="response-body"></a>Treść odpowiedzi

Pomyślna odpowiedź to tablica JSON z jednym wynikiem dla każdego ciągu w tablicy wejściowej. Obiekt wynikowy zawiera następujące właściwości:

  * `normalizedSource`: Ciąg przedstawiający znormalizowaną formę okresu źródłowego. Ogólnie rzecz biorąc powinna być taka sama jak wartość `Text` pola w indeksie zgodnej listy w treści żądania.
    
  * `normalizedTarget`: Ciąg przedstawiający znormalizowaną formę okresu docelowego. Ogólnie rzecz biorąc powinna być taka sama jak wartość `Translation` pola w indeksie zgodnej listy w treści żądania.
  
  * `examples`: Lista przykładów dla pary (termin źródłowy, termin docelowy). Każdy element listy jest obiektem o następujących właściwościach:

    * `sourcePrefix`: Ciąg, który ma zostać połączony _przed_ wartością `sourceTerm` w celu utworzenia kompletnego przykładu. Nie należy dodawać znaku spacji, ponieważ jest już tam, gdy powinien być. Ta wartość może być pustym ciągiem.

    * `sourceTerm`: Ciąg równy rzeczywistemu wyszukiwanemu okresowi. Ciąg zostanie dodany z `sourcePrefix` i `sourceSuffix` w celu utworzenia kompletnego przykładu. Jego wartość jest oddzielona, aby można było ją oznaczyć w interfejsie użytkownika, np. przez pogrubienie.

    * `sourceSuffix`: Ciąg, który ma zostać połączony _po_ wartości `sourceTerm` w celu utworzenia kompletnego przykładu. Nie należy dodawać znaku spacji, ponieważ jest już tam, gdy powinien być. Ta wartość może być pustym ciągiem.

    * `targetPrefix`: Ciąg podobny do `sourcePrefix` dla elementu docelowego.

    * `targetTerm`: Ciąg podobny do `sourceTerm` dla elementu docelowego.

    * `targetSuffix`: Ciąg podobny do `sourceSuffix` dla elementu docelowego.

    > [!NOTE]
    > Jeśli w słowniku nie ma przykładów, odpowiedź jest 200 (ok), ale `examples` lista jest pusta.

## <a name="examples"></a>Przykłady

Ten przykład pokazuje, jak wyszukiwać przykłady dla pary składające się z okresu `fly` angielskiego i jego tłumaczenia `volar`hiszpańskiego.

# <a name="curltabcurl"></a>[odsłon](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

---

Treść odpowiedzi (skróci do przejrzystości) to:

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```
