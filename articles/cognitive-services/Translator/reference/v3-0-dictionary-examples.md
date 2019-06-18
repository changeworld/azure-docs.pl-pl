---
title: Metoda przykłady słownika interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Metoda przykłady słowników interfejs API tekstu usługi Translator.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-pawal
ms.openlocfilehash: 526fc3e51691773e27530a0fa775f4f07ee94acd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389772"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Translator Text API 3.0: Przykłady słowników

Przykłady pokazujące, jak terminy w słowniku są używane w kontekście. Ta operacja jest używana w połączeniu z sekcją [wyszukiwania słownika](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` limit czasu żądania:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Żądania, parametry przekazane w ciągu zapytania są:

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>api-version</td>
    <td>*Wymagany parametr*.<br/>Wersja interfejsu API zażądane przez klienta. Wartość musi być `3.0`.</td>
  </tr>
  <tr>
    <td>from</td>
    <td>*Wymagany parametr*.<br/>Określa język tekstu wejściowego. Język źródłowy musi być jednym z [obsługiwane języki](./v3-0-languages.md) objęte `dictionary` zakresu.</td>
  </tr>
  <tr>
    <td>na</td>
    <td>*Wymagany parametr*.<br/>Określa język tekstu wyjściowego. Język docelowy musi mieć jedną z [obsługiwane języki](./v3-0-languages.md) objęte `dictionary` zakresu.</td>
  </tr>
</table>

Nagłówki żądania obejmują:

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>Nagłówki uwierzytelniania</td>
    <td><em>Nagłówek żądania wymagane</em>.<br/>Zobacz <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostępne opcje uwierzytelniania</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Nagłówek żądania wymagane*.<br/>Określa typ zawartości ładunku. Możliwe wartości to: `application/json`.</td>
  </tr>
  <tr>
    <td>Długość zawartości</td>
    <td>*Nagłówek żądania wymagane*.<br/>Długość treści żądania.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Opcjonalnie*.<br/>Generowane przez klienta identyfikator GUID do unikatowego identyfikowania żądania. Możesz pominąć ten nagłówek, jeśli zawierają identyfikator śledzenia w ciągu zapytania za pomocą parametru zapytania o nazwie `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiekt JSON z następującymi właściwościami:

  * `Text`: Ciąg określający termin do wyszukiwania. Powinna to być wartość `normalizedText` pola z powrotem tłumaczeń do poprzedniej [wyszukiwania słownika](./v3-0-dictionary-lookup.md) żądania. Może to być także wartością `normalizedSource` pola.

  * `Translation`: Ciąg określający przetłumaczonego tekstu wcześniej zwracany przez [wyszukiwania słownika](./v3-0-dictionary-lookup.md) operacji. Powinna to być wartość z `normalizedTarget` pole `translations` listę [wyszukiwania słownika](./v3-0-dictionary-lookup.md) odpowiedzi. Usługa zwróci przykłady pary słów określone źródło cel.

Przykładem jest:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Obowiązują następujące ograniczenia:

* Tablica może mieć co najwyżej 10 elementów.
* Wartość tekstowa elementu tablicy nie może przekraczać 100 znaków, łącznie ze spacjami.

## <a name="response-body"></a>Treść odpowiedzi

Odpowiedź oznaczająca Powodzenie to tablica JSON z jeden wynik dla każdego ciągu w tabeli wejściowej. Obiekt wyniku zawiera następujące właściwości:

  * `normalizedSource`: Ciąg, zapewniając znormalizowana postać okresu źródła. Ogólnie rzecz biorąc, powinna to być taka sama jak wartość `Text` pole pod indeksem listy dopasowywania w treści żądania.
    
  * `normalizedTarget`: Ciąg, zapewniając znormalizowana postać warunku docelowego. Ogólnie rzecz biorąc, powinna to być taka sama jak wartość `Translation` pole pod indeksem listy dopasowywania w treści żądania.
  
  * `examples`: Lista przykłady (warunek źródła, terminu docelowego) pary. Każdy element listy jest obiektem z następującymi właściwościami:

    * `sourcePrefix`: Ciąg do łączenia _przed_ wartość `sourceTerm` w celu utworzenia kompletny przykład. Nie należy dodawać znak spacji, ponieważ jest już istnieje w przypadku powinien być. Ta wartość może być ciągiem pustym.

    * `sourceTerm`: Wyszukiwane ciągu równa rzeczywisty okres. Ten ciąg jest dodawana z `sourcePrefix` i `sourceSuffix` w celu utworzenia kompletny przykład. Jego wartość jest oddzielona, dzięki czemu może być oznaczony w interfejsie użytkownika, np. przez pogrubienie go.

    * `sourceSuffix`: Ciąg do łączenia _po_ wartość `sourceTerm` w celu utworzenia kompletny przykład. Nie należy dodawać znak spacji, ponieważ jest już istnieje w przypadku powinien być. Ta wartość może być ciągiem pustym.

    * `targetPrefix`: Ciąg podobny do następującego `sourcePrefix` , ale dla obiektu docelowego.

    * `targetTerm`: Ciąg podobny do następującego `sourceTerm` , ale dla obiektu docelowego.

    * `targetSuffix`: Ciąg podobny do następującego `sourceSuffix` , ale dla obiektu docelowego.

    > [!NOTE]
    > W przypadku żadnych przykładów w słowniku, odpowiedzi to 200 (OK), ale `examples` lista jest pusta lista.

## <a name="examples"></a>Przykłady

W tym przykładzie przedstawiono przykłady dla pary składające się z angielskiego termin wyszukiwania `fly` hiszpański tłumaczenie `volar`.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

---

Treść odpowiedzi (skrót dla jasności) to:

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
