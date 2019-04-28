---
title: Moderowanie tekstu za pomocą interfejsu API moderowania tekstu - Content Moderator
titlesuffix: Azure Cognitive Services
description: Testuj Moderowanie tekstu przy użyciu interfejsu API moderowania tekstu w konsoli usługi online.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 794638496931f72a12fcb3bd8819b04c7e2e7c97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995724"
---
# <a name="moderate-text-from-the-api-console"></a>Moderowanie tekstu z poziomu konsoli interfejsu API

Użyj [interfejs API moderowania tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) w usłudze Azure Content Moderator do skanowania zawartości tekstu. Operacja skanowania zawartości pod kątem wulgarności i porównuje zawartość względem udostępnionej i niestandardowych list elementów zabronionych.


## <a name="get-your-api-key"></a>Uzyskaj klucz interfejsu API
Zanim można Testuj interfejs API w konsoli usługi online, należy się klucz subskrypcji. Ten element znajduje się na **ustawienia** na karcie **Ocp-Apim-Subscription-Key** pole. Aby uzyskać więcej informacji, zobacz [Omówienie](overview.md).

## <a name="navigate-to-the-api-reference"></a>Przejdź do dokumentacja interfejsu API
Przejdź do [dokumentacja interfejsu API moderowania tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  **Tekst — ekranu** zostanie otwarta strona.

## <a name="open-the-api-console"></a>Otwórz konsolę interfejsu API
Aby uzyskać **konsoli testowania interfejsu Open API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

  ![Tekst — wybór obszaru strony ekranu](images/test-drive-region.png)

  **Tekst — ekranu** zostanie otwarta konsola interfejsu API.

## <a name="select-the-inputs"></a>Zaznacz dane wejściowe

### <a name="parameters"></a>Parametry
Wybierz parametry zapytania, które ekranie tekst. Na przykład użyj wartości domyślnej dla **języka**. Można również pole pozostanie puste, ponieważ operacja automatycznie wykryje prawdopodobnie języka jako część jej wykonanie.

> [!NOTE]
> Dla **języka** parametru, Przypisz `eng` lub pozostaw to pole puste, aby zobaczyć wspomagane maszynowo **klasyfikacji** odpowiedzi (funkcja w wersji zapoznawczej). **Ta funkcja obsługuje tylko język angielski**.
>
> Dla **warunki wulgaryzmów** wykrywania, użyj [kod ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) z obsługiwanych języków, które są wymienione w tym artykule, lub pozostawić je puste.

Dla **Autokorekty**, **dane osobowe**, i **klasyfikowania (wersja zapoznawcza)**, wybierz opcję **true**. Pozostaw **ListId** pole puste.

  ![Tekst — parametry zapytania konsoli ekranu](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Typ zawartości
Aby uzyskać **Content-Type**, wybierz typ zawartości, które chcesz ekranu. Na przykład użyj wartości domyślnej **text/plain** typ zawartości. W **Ocp-Apim-Subscription-Key** wprowadź swój klucz subskrypcji.

### <a name="sample-text-to-scan"></a>Przykładowy tekst do skanowania
W **treść żądania** wprowadź jakiś tekst. Poniższy przykład pokazuje zamierzone błąd pisowni w tekście.

> [!NOTE]
> Nieprawidłowy numer ubezpieczenia społecznego w następujący przykładowy tekst jest celowe. Celem jest przekazać przykładowe dane wejściowe i wyjściowe formatu.

```
    Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
    These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
    Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="text-classification-feature"></a>Funkcji Klasyfikacja tekstu

W poniższym przykładzie zobaczysz odpowiedź klasyfikacji tekstu wspomagane maszynowo Content Moderator. Ułatwia wykrywanie potencjalnie niepożądane zawartości. Mogą zostać uznane za flagą zawartość, jako niestosowny w zależności od kontekstu. Oprócz przekazywania prawdopodobieństwo wystąpienia każdej kategorii, to może zalecić dokonywanie przeglądu zawartości. Funkcja używa uczonego modelu do identyfikowania możliwych obraźliwe, uwłaczającej lub porównawczego języka. W tym żargonu, skróconej słów, obraźliwych i celowo błędnie napisanych wyrazów do przeglądu. 

#### <a name="explanation"></a>Wyjaśnienie

- `Category1` reprezentuje potencjalnych obecności języka, który może być uznawany za przekleństwa jawne lub treści dla dorosłych w niektórych sytuacjach.
- `Category2` reprezentuje potencjalnych obecności języka, który może być uznawany za przekleństwa dwuznaczne lub dla dorosłych w niektórych sytuacjach.
- `Category3` reprezentuje potencjalnych obecności język, który może być uznane za obraźliwe w niektórych sytuacjach.
- `Score` to od 0 do 1. Wyższą ocenę, tym większe modelu jest prognozowanie mogą dotyczyć kategorii. Ta wersja zapoznawcza opiera się na modelu statystycznych, a nie ręcznie zakodowane wyników. Zaleca się testowanie za pomocą własnej zawartości, aby określić, jak wyrównuje każdej kategorii wymagań.
- `ReviewRecommended` jest wartość PRAWDA lub FAŁSZ, w zależności od wewnętrznego wynik progów. Klientów należy ocenić, czy Użyj tej wartości lub wybrać progami niestandardowymi na podstawie ich zawartości zasad.

### <a name="analyze-the-response"></a>Analizowanie odpowiedzi
Następującą odpowiedź zawiera różne szczegółowe informacje, z interfejsu API. Zawiera on wulgaryzmy, dane osobowe, klasyfikacji (wersja zapoznawcza) i poprawił wersji.

> [!NOTE]
> Funkcja "Klasyfikacja" wspomagane maszynowo jest w wersji zapoznawczej i obsługuje tylko język angielski.

```
{
    "OriginalText": "Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
    "NormalizedText": "Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
"Misrepresentation": null,
    "PII": {
            "Email": [{
                "Detected": "abcdef@abcd.com",
                "SubType": "Regular",
                "Text": "abcdef@abcd.com",
                "Index": 32
                }],
            "IPA": [{
                "SubType": "IPV4",
                "Text": "255.255.255.255",
                "Index": 72
                }],
            "Phone": [{
                "CountryCode": "US",
                "Text": "6657789887",
                "Index": 56
                }, {
                "CountryCode": "US",
                "Text": "870 608 4000",
                "Index": 211
                }, {
                "CountryCode": "UK",
                "Text": "+44 870 608 4000",
                "Index": 207
                }, {
                "CountryCode": "UK",
                "Text": "0344 800 2400",
                "Index": 227
                }, {
                "CountryCode": "UK",
                "Text": "0800 820 3300",
                "Index": 244
            }],
         "Address": [{
                 "Text": "1 Microsoft Way, Redmond, WA 98052",
                "Index": 89
            }],
            "SSN": [{
                "Text": "999999999",
                "Index": 56
            }, {
                "Text": "999-99-9999",
                "Index": 266
            }]
        },
    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
        },
        "Category2": {
            "Score": 0.12747249007225037
        },
        "Category3": {
            "Score": 0.98799997568130493
        }
        },
    "Language": "eng",
    "Terms": [{
            "Index": 21,
            "OriginalIndex": 21,
            "ListId": 0,
         "Term": "crap"
        }],
    "Status": {
            "Code": 3000,
            "Description": "OK",
            "Exception": null
        },
     "TrackingId": "2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

Aby uzyskać szczegółowy opis wszystkie sekcje w odpowiedzi JSON, zobacz [Przegląd interfejs API moderowania tekstu](text-moderation-api.md).

## <a name="next-steps"></a>Kolejne kroki

W kodzie za pomocą interfejsu API REST lub rozpoczynać się [Szybki Start .NET Moderowanie tekstu](text-moderation-quickstart-dotnet.md) do integracji z aplikacją.
