---
title: Średnie tekstu przy użyciu interfejsu API łagodzenia tekstu w zawartości moderatora Azure | Dokumentacja firmy Microsoft
description: Test-Drive łagodzenia tekstu przy użyciu interfejsu API łagodzenia tekstu w konsoli usługi online.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: ed696c31a886626819414c45eb7995edaf161fff
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347765"
---
# <a name="moderate-text-from-the-api-console"></a>Umiarkowany tekstu z konsoli interfejsu API

Użyj [API łagodzenia tekst](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) w Azure moderatora zawartości do skanowania zawartości tekstu. Operacja skanowania zawartości dla niestosownych wyrażeń i porównuje zawartości przed blacklists udostępnionego i niestandardowych.


## <a name="get-your-api-key"></a>Uzyskaj swój klucz interfejsu API
Zanim można test-drive interfejsu API w konsoli usługi online, należy klucz subskrypcji. Ten element znajduje się na **ustawienia** karcie **Ocp-Apim-subskrypcji — klucz** pole. Aby uzyskać więcej informacji, zobacz [omówienie](overview.md).

## <a name="navigate-to-the-api-reference"></a>Przejdź do dokumentacji interfejsu API
Przejdź do [dokumentacja interfejsu API łagodzenia tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  **Tekst — ekranu** zostanie otwarta strona.

## <a name="open-the-api-console"></a>Otwórz konsolę interfejsu API
Aby uzyskać **konsoli testowania otwarty interfejs API**, wybierz region, który najlepiej opisuje Twojej lokalizacji. 

  ![Tekst — wybór obszaru strony ekranu](images/test-drive-region.png)

  **Tekst — ekranu** zostanie otwarta konsola interfejsu API.

## <a name="select-the-inputs"></a>Wybierz dane wejściowe

### <a name="parameters"></a>Parametry
Wybierz parametry zapytania, które mają być wykorzystywane w ekranie tekstu. Na przykład użyj wartości domyślnej dla **języka**. Można także pole pozostanie puste, ponieważ operacja automatycznie wykryje prawdopodobnie języka jako część jej wykonywania.

> [!NOTE]
> Dla **języka** parametru, Przypisz `eng` lub pozostaw to pole puste, aby zobaczyć wspierana maszyny **klasyfikacji** odpowiedzi (funkcja w wersji zapoznawczej). **Ta funkcja obsługuje tylko język angielski**.
>
> Dla **warunki niestosownych wyrażeń** wykrywania, użyj [kod ISO 639 3](http://www-01.sil.org/iso639-3/codes.asp) z obsługiwanych języków, które są wymienione w tym artykule, lub pozostaw puste.

Dla **Autokorekty**, **dane osobowe**, i **klasyfikowania (wersja zapoznawcza)**, wybierz pozycję **true**. Pozostaw **identyfikatora listy można wpisać** pole puste.

  ![Tekst — parametry zapytania ekranu konsoli](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Typ zawartości
Aby uzyskać **Content-Type**, wybierz typ zawartości ma ekranu. Na przykład użyj domyślnej **zwykły tekst** typ zawartości. W **Ocp-Apim-subskrypcji — klucz** wprowadź klucz subskrypcji.

### <a name="sample-text-to-scan"></a>Przykładowy tekst do skanowania
W **treść żądania** wprowadź tekst. Poniższy przykład przedstawia zamierzone Literówka w tekście.

> [!NOTE]
> Nieprawidłowy numer ubezpieczenia społecznego w następujący przykładowy tekst jest zamierzone. Ma na celu przedstawienia przykładowe dane wejściowe i wyjściowe formatu.

```
    Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
    These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
    Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="text-classification-feature"></a>Funkcja klasyfikacji tekstu

W poniższym przykładzie widać zawartości moderatora tekst pomocy maszyny klasyfikacji odpowiedzi. Pomaga wykryć potencjalnie niepożądane zawartości. Zawartość oflagowane uzna jako niestosowną w zależności od kontekstu. Oprócz przekazywania prawdopodobieństwo wystąpienia każdej kategorii, to może zalecić człowieka przeglądu zawartości. Funkcja używa uczonego modelu do identyfikowania możliwych obraźliwe, derogacyjnego lub porównawczego języka. W tym żargon, skróconej wyrazy, obraźliwe i celowo błędnie wpisane słowa do przeglądu. 

#### <a name="explanation"></a>Wyjaśnienie

- `Category1` reprezentuje potencjalnych obecności język, który można uznać za płciowo jawne lub dla dorosłych w niektórych sytuacjach.
- `Category2` reprezentuje potencjalnych obecności język, który można uznać za płciowo sugerujących lub dojrzałe w niektórych sytuacjach.
- `Category3` reprezentuje potencjalnych obecności język, który może zostać uznany za obraźliwy w niektórych sytuacjach.
- `Score` to od 0 do 1. Im wyższa jest ocena wyższa modelu jest prognozowanie kategorii można stosować. Ta wersja zapoznawcza zależy od statystyczne modelu zamiast wyników kodowane ręcznie. Zaleca się testowanie na własną zawartość, aby określić, jak każda kategoria wyrównuje do własnych potrzeb.
- `ReviewRecommended` jest wartość PRAWDA lub FAŁSZ w zależności od wewnętrznego wynik progów. Klienci powinna ocenić, czy ta wartość lub zdecydować o progami niestandardowymi na podstawie ich zawartości zasad.

### <a name="analyze-the-response"></a>Analizowanie odpowiedzi
Następującą odpowiedź zawiera różne wgląd z interfejsu API. Zawiera on potencjalne niestosownych wyrażeń, dane osobowe klasyfikacji (wersja zapoznawcza) i wersji rozwiązany automatycznie.

> [!NOTE]
> Funkcja "Klasyfikacja" wspierana maszyny jest w wersji zapoznawczej i obsługuje tylko język angielski.

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

Aby uzyskać szczegółowy opis wszystkich sekcji w odpowiedzi JSON, zapoznaj się [omówienie łagodzenia interfejsu API tekstu](text-moderation-api.md).

## <a name="next-steps"></a>Kolejne kroki

W kodzie za pomocą interfejsu API REST lub rozpoczynać [szybkiego startu .NET łagodzenia tekst](text-moderation-quickstart-dotnet.md) do integracji z aplikacji.
