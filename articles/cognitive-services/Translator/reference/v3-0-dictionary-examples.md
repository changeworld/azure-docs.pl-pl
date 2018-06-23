---
title: Metody przykłady interfejsu API słownika tekstowych Microsoft Translator | Dokumentacja firmy Microsoft
description: Metoda przykłady słowników interfejsu API Microsoft Translator tekstu.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 9960f3be42090edaec1df935d70e4c1a0d25b691
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349269"
---
# <a name="text-api-30-dictionary-examples"></a>Tekst interfejsu API 3.0: Przykłady słowników

Przykłady, które pokazują, jak terminy w słowniku są używane w kontekście. Ta operacja jest używana w połączeniu z [wyszukiwania słownika](.\v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` żądanie:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Żądania są parametry przekazywane w ciągu kwerendy:

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>wersja interfejsu API</td>
    <td>*Wymagany parametr*.<br/>Wersja interfejsu API żądanego przez klienta. Wartość musi być `3.0`.</td>
  </tr>
  <tr>
    <td>z</td>
    <td>*Wymagany parametr*.<br/>Określa język tekstu wejściowego. Język źródłowy musi być jedną z [obsługiwanych języków](.\v3-0-languages.md) objęte `dictionary` zakresu.</td>
  </tr>
  <tr>
    <td>na</td>
    <td>*Wymagany parametr*.<br/>Określa język tekstu wyjściowego. Język docelowy musi mieć jedną z [obsługiwanych języków](.\v3-0-languages.md) objęte `dictionary` zakresu.</td>
  </tr>
</table>

Nagłówki żądania obejmują:

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>_Jeden autoryzacji_<br/>_header_</td>
    <td>*Nagłówek żądania wymagane*.<br/>Zobacz [dostępne opcje dla uwierzytelniania](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Nagłówek żądania wymagane*.<br/>Określa typ zawartości ładunku. Możliwe wartości to: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length.</td>
    <td>*Nagłówek żądania wymagane*.<br/>Długość treści żądania.</td>
  </tr>
  <tr>
    <td>X ClientTraceId</td>
    <td>*Opcjonalnie*.<br/>Generowane przez klientów identyfikator GUID, aby jednoznacznie zidentyfikować żądania. Jeśli dołączysz identyfikator śledzenia w ciągu zapytania za pomocą parametru zapytania o nazwie można pominąć ten nagłówek `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablica JSON. Każdy element tablicy jest obiektem JSON z następującymi właściwościami:

  * `Text`: Ciąg określający termin do wyszukiwania. Powinna to być wartość `normalizedText` pola tłumaczeń wstecz poprzedniej [wyszukiwania słownika](.\v3-0-dictionary-lookup.md) żądania. Można też wartość `normalizedSource` pola.

  * `Translation`: Ciąg określający tłumaczenia wcześniej zostały zwrócone przez [wyszukiwania słownika](.\v3-0-dictionary-lookup.md) operacji. Powinna to być wartość z `normalizedTarget` w `translations` lista [wyszukiwania słownika](.\v3-0-dictionary-lookup.md) odpowiedzi. Usługa będzie zwracać przykłady dla pary słów określony element docelowy źródła.

Przykładem jest:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Mają zastosowanie następujące ograniczenia:

* Tablica może mieć maksymalnie 10 elementów.
* Wartość tekstowego elementu tablicy nie może przekraczać 100 znaków, łącznie ze spacjami.

## <a name="response-body"></a>Treść odpowiedzi

Odpowiedź oznaczająca Powodzenie to tablica JSON o jeden wynik dla każdego ciągu w tablicy wejściowej. Obiekt wyniku zawiera następujące właściwości:

  * `normalizedSource`: Ciąg, podając znormalizowana postać warunku źródła. Ogólnie rzecz biorąc, to powinna być taka sama jak wartość `Text` pole pod indeksem listy dopasowywania w treści żądania.
    
  * `normalizedTarget`: Ciąg, podając znormalizowana postać warunku docelowej. Ogólnie rzecz biorąc, to powinna być taka sama jak wartość `Translation` pole pod indeksem listy dopasowywania w treści żądania.
  
  * `examples`: Lista przykłady (warunek źródła, terminu docelowego) pary. Każdy element listy jest obiektem z następującymi właściwościami:

    * `sourcePrefix`: Ciąg do łączenia _przed_ wartość `sourceTerm` do utworzenia pełny przykład. Nie dodawaj znaku spacji, ponieważ jest już istnieje w przypadku należy go. Ta wartość może być pustym ciągiem.

    * `sourceTerm`Ciąg równe rzeczywiste termin wyszukiwać. Dodaje ciąg się `sourcePrefix` i `sourceSuffix` do utworzenia pełny przykład. Wartość oddzielona tak go może być oznaczony za pomocą interfejsu użytkownika, np. przez pogrubienie go.

    * `sourceSuffix`: Ciąg do łączenia _po_ wartość `sourceTerm` do utworzenia pełny przykład. Nie dodawaj znaku spacji, ponieważ jest już istnieje w przypadku należy go. Ta wartość może być pustym ciągiem.

    * `targetPrefix`: Ciąg podobny do `sourcePrefix` , ale dla elementu docelowego.

    * `targetTerm`: Ciąg podobny do `sourceTerm` , ale dla elementu docelowego.

    * `targetSuffix`: Ciąg podobny do `sourceSuffix` , ale dla elementu docelowego.

    > [!NOTE]
    > Jeśli nie przedstawiono w słowniku, odpowiedź jest 200 (OK), ale `examples` lista jest pusta lista.

## <a name="examples"></a>Przykłady

W tym przykładzie przedstawiono sposób wyszukiwania przykłady dla pary składające się z angielski termin `fly` i jego hiszpańskim tłumaczenia `volar`.

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

---

Treść odpowiedzi (skrót dla jasności) jest:

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
