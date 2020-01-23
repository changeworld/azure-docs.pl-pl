---
title: Przykłady interfejs API tłumaczenia tekstu w usłudze Translator słownika
titleSuffix: Azure Cognitive Services
description: Przykłady interfejs API tłumaczenia tekstu w usłudze Translator słownika zawierają przykłady pokazujące, w jaki sposób terminy w słowniku są używane w kontekście.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: b3551a8df19e47178c7bacd9218cfa60b66d81f9
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548071"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Interfejs API tłumaczenia tekstu w usłudze Translator 3,0: przykłady słownika

Zawiera przykłady pokazujące, jak terminy w słowniku są używane w kontekście. Ta operacja jest używana wspólnie z funkcją [Lookup dictionary](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Adres URL żądania

Wyślij żądanie `POST` do:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przesłane na ciągu zapytania są następujące:

| Parametr zapytania | Opis |
| --------- | ----------- |
| api-version <img width=200/> | **Wymagany parametr**.<br/>Wersja interfejsu API żądana przez klienta. Wartość musi być `3.0`. |
| z | **Wymagany parametr**.<br/>Określa język tekstu wejściowego. Język źródłowy musi być jednym z [obsługiwanych języków](./v3-0-languages.md) znajdujących się w zakresie `dictionary`. |
| na | **Wymagany parametr**.<br/>Określa język tekstu wyjściowego. Język docelowy musi być jednym z [obsługiwanych języków](./v3-0-languages.md) uwzględnionych w zakresie `dictionary`.  | 

Nagłówki żądań obejmują:

| Nagłówki  | Opis |
| ------ | ----------- |
| Nagłówki uwierzytelniania <img width=200/>  | **Wymagany nagłówek żądania**.<br/>Zobacz <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostępne opcje uwierzytelniania</a>. |
| Content-Type | **Wymagany nagłówek żądania**.<br/>Określa typ zawartości ładunku. Możliwe wartości to: `application/json`. |
| Długość zawartości   | **Wymagany nagłówek żądania**.<br/>Długość treści żądania. |
| X-ClientTraceId   | **Opcjonalnie**.<br/>Wygenerowany przez klienta identyfikator GUID służący do unikatowej identyfikacji żądania. Możesz pominąć ten nagłówek, jeśli dołączysz identyfikator śledzenia w ciągu zapytania przy użyciu parametru zapytania o nazwie `ClientTraceId`. |

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiektem JSON o następujących właściwościach:

  * `Text`: ciąg określający termin do wyszukania. Powinna to być wartość pola `normalizedText` z przetłumaczeń z tyłu poprzedniego żądania [wyszukiwania słownika](./v3-0-dictionary-lookup.md) . Może to być również wartość pola `normalizedSource`.

  * `Translation`: ciąg określający przetłumaczony tekst, który został wcześniej zwrócony przez operację [wyszukiwania słownika](./v3-0-dictionary-lookup.md) . Powinna to być wartość pola `normalizedTarget` na liście `translations` odpowiedzi [wyszukiwania słownika](./v3-0-dictionary-lookup.md) . Usługa zwróci przykłady dla określonej pary wyrazów dla docelowej lokalizacji źródłowej.

Przykład:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Mają zastosowanie następujące ograniczenia:

* Tablica może zawierać co najwyżej 10 elementów.
* Wartość tekstowa elementu tablicy nie może być dłuższa niż 100 znaków, w tym spacje.

## <a name="response-body"></a>Treść odpowiedzi

Pomyślna odpowiedź to tablica JSON z jednym wynikiem dla każdego ciągu w tablicy wejściowej. Obiekt wynikowy zawiera następujące właściwości:

  * `normalizedSource`: ciąg dający znormalizowaną formę okresu źródłowego. Ogólnie rzecz biorąc powinna być taka sama jak wartość pola `Text` w indeksie zgodnej listy w treści żądania.
    
  * `normalizedTarget`: ciąg dający znormalizowaną postać terminu docelowego. Ogólnie rzecz biorąc powinna być taka sama jak wartość pola `Translation` w indeksie zgodnej listy w treści żądania.
  
  * `examples`: Lista przykładów dla pary (termin źródłowy, termin docelowy). Każdy element listy jest obiektem o następujących właściwościach:

    * `sourcePrefix`: ciąg do łączenia _przed_ wartością `sourceTerm`, aby utworzyć kompletny przykład. Nie należy dodawać znaku spacji, ponieważ jest już tam, gdy powinien być. Ta wartość może być pustym ciągiem.

    * `sourceTerm`: ciąg równy rzeczywistemu wyszukiwanemu okresowi. Ciąg zostanie dodany z `sourcePrefix` i `sourceSuffix`, aby utworzyć kompletny przykład. Jego wartość jest oddzielona, aby można było ją oznaczyć w interfejsie użytkownika, np. przez pogrubienie.

    * `sourceSuffix`: ciąg, który ma zostać połączony _po_ wartości `sourceTerm`, aby utworzyć kompletny przykład. Nie należy dodawać znaku spacji, ponieważ jest już tam, gdy powinien być. Ta wartość może być pustym ciągiem.

    * `targetPrefix`: ciąg podobny do `sourcePrefix`, ale dla elementu docelowego.

    * `targetTerm`: ciąg podobny do `sourceTerm`, ale dla elementu docelowego.

    * `targetSuffix`: ciąg podobny do `sourceSuffix`, ale dla elementu docelowego.

    > [!NOTE]
    > Jeśli w słowniku nie ma przykładów, odpowiedź to 200 (OK), ale lista `examples` jest pustą listą.

## <a name="examples"></a>Przykłady

Ten przykład pokazuje, jak odszukać przykłady dla pary składającej się z `fly` terminów w języku angielskim i `volar`tłumaczenia hiszpańskiego.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

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
