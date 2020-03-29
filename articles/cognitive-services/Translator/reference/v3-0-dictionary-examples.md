---
title: Metoda przykładów słownika interfejsu API tłumacza
titleSuffix: Azure Cognitive Services
description: Metoda Przykłady słownika interfejsu API tłumacza zawiera przykłady, które pokazują, jak terminy w słowniku są używane w kontekście.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: b3551a8df19e47178c7bacd9218cfa60b66d81f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76548071"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Translator Text API 3.0: Przykłady słownika

Zawiera przykłady, które pokazują, jak terminy w słowniku są używane w kontekście. Ta operacja jest używana w połączeniu z [wyszukiwaniem słownika](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` prośbę na:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przekazywane na ciąg zapytania są:

| Parametr kwerendy | Opis |
| --------- | ----------- |
| api-version <img width=200/> | **Wymagany parametr**.<br/>Wersja interfejsu API żądana przez klienta. Wartość musi `3.0`być . |
| Z | **Wymagany parametr**.<br/>Określa język tekstu wejściowego. Język źródłowy musi być jednym z [obsługiwanych języków zawartych](./v3-0-languages.md) w `dictionary` zakresie. |
| na | **Wymagany parametr**.<br/>Określa język tekstu wyjściowego. Język docelowy musi być jednym z [obsługiwanych języków zawartych](./v3-0-languages.md) w `dictionary` zakresie.  | 

Nagłówki żądań obejmują:

| Nagłówki  | Opis |
| ------ | ----------- |
| Nagłówki uwierzytelniania <img width=200/>  | **Wymagany nagłówek żądania**.<br/>Zobacz <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostępne opcje uwierzytelniania</a>. |
| Content-Type | **Wymagany nagłówek żądania**.<br/>Określa typ zawartości ładunku. Możliwe wartości `application/json`to: . |
| Długość zawartości   | **Wymagany nagłówek żądania**.<br/>Długość treści żądania. |
| Identyfikator X-ClientTraceId   | **Opcjonalnie**.<br/>Identyfikator GUID wygenerowany przez klienta, aby jednoznacznie zidentyfikować żądanie. Ten nagłówek można pominąć, jeśli identyfikator śledzenia zostanie uwzględniony w `ClientTraceId`ciągu zapytania przy użyciu parametru kwerendy o nazwie . |

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiektem JSON o następujących właściwościach:

  * `Text`: Ciąg określający termin do wyszukiwania. Powinna to być wartość `normalizedText` pola z wstecznych tłumaczeń poprzedniego żądania [wyszukiwania słownika.](./v3-0-dictionary-lookup.md) Może to być również `normalizedSource` wartość pola.

  * `Translation`: Ciąg określający przetłumaczony tekst wcześniej [zwrócony](./v3-0-dictionary-lookup.md) przez operację wyszukiwania słownika. Powinna to być wartość `normalizedTarget` z `translations` pola na liście odpowiedzi [odnośnika słownika.](./v3-0-dictionary-lookup.md) Usługa zwróci przykłady dla pary wyrazów określonego źródła docelowego.

Przykładem jest:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Obowiązują następujące ograniczenia:

* Tablica może mieć co najwyżej 10 elementów.
* Wartość tekstowa elementu tablicy nie może przekraczać 100 znaków, w tym spacji.

## <a name="response-body"></a>Treść odpowiedzi

Pomyślna odpowiedź jest tablicą JSON z jednym wynikiem dla każdego ciągu w tablicy wejściowej. Obiekt wynikowy zawiera następujące właściwości:

  * `normalizedSource`: Ciąg dający znormalizowaną formę terminu źródłowego. Ogólnie rzecz biorąc powinno to być `Text` identyczne z wartością pola w indeksie listy dopasowania w treści żądania.
    
  * `normalizedTarget`: Ciąg dający znormalizowaną formę terminu docelowego. Ogólnie rzecz biorąc powinno to być `Translation` identyczne z wartością pola w indeksie listy dopasowania w treści żądania.
  
  * `examples`: Lista przykładów pary (termin źródłowy, termin docelowy). Każdy element listy jest obiektem o następujących właściwościach:

    * `sourcePrefix`: Ciąg do łączenia _przed_ `sourceTerm` wartością w celu utworzenia pełnego przykładu. Nie dodawaj znaku spacji, ponieważ jest już tam, kiedy powinien być. Ta wartość może być pusty ciąg.

    * `sourceTerm`: Ciąg równy rzeczywistemu terminowi spojrzał w górę. Ciąg jest dodawany z `sourcePrefix` i `sourceSuffix` tworzą pełny przykład. Jego wartość jest oddzielona, dzięki czemu można ją oznaczyć w interfejsie użytkownika, na przykład poprzez pogrubienie go.

    * `sourceSuffix`: Ciąg do łączenia _po_ `sourceTerm` wartości, aby utworzyć pełny przykład. Nie dodawaj znaku spacji, ponieważ jest już tam, kiedy powinien być. Ta wartość może być pusty ciąg.

    * `targetPrefix`: Ciąg podobny `sourcePrefix` do ale dla celu.

    * `targetTerm`: Ciąg podobny `sourceTerm` do ale dla celu.

    * `targetSuffix`: Ciąg podobny `sourceSuffix` do ale dla celu.

    > [!NOTE]
    > Jeśli w słowniku nie ma żadnych przykładów, odpowiedź wynosi 200 (OK), ale `examples` lista jest pustą listą.

## <a name="examples"></a>Przykłady

W tym przykładzie pokazano, jak wyszukać przykłady `fly` dla pary `volar`składającej się z terminu angielskiego i jego tłumaczenia na język hiszpański .

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

Organ odpowiedzi (w skrócie dla jasności) jest:

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
