---
title: Moderowanie tekstu — usługi Content Moderator
description: Użyj Moderowanie tekstu dla możliwości niechciane tekst, dane osobowe, listy i niestandardowe warunków.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 5a1007f2408b48c96f5eeaf585b94c8caa7ceb45
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607122"
---
# <a name="learn-text-moderation-concepts"></a>Pojęcia dotyczące moderowania tekstu

Użyj Content Moderator tekstu wspomagane maszynowo Moderowanie i [przeglądu przez ludzi](Review-Tool-User-Guide/human-in-the-loop.md) możliwości moderowanie zawartości tekstowej.

Możesz zablokować, Zatwierdź albo przejrzyj zawartość, na podstawie zasad i progów. Dzięki niemu rozszerzyć ludzi Moderowanie środowisk których partnerów, pracowników i klientów generują zawartości tekstowej. Obejmują one, pokoje rozmów, dyskusyjnymi, czatbotów, wykazy handlu elektronicznego i dokumentów. 

Odpowiedź usługi zawiera następujące informacje:

- Wulgaryzmów: termin, na podstawie dopasowania z wbudowanej listy obsceniczne warunki w różnych językach
- Klasyfikacja: wspomagane maszynowo klasyfikacji na trzy kategorie
- Dane osobowe
- Poprawił tekstu
- Oryginalny tekst
- Język

## <a name="profanity"></a>Profanity

Jeśli interfejs API wykryje postanowieniami obsceniczne w żadnym z [obsługiwane języki](Text-Moderation-API-Languages.md), te zasady znajdują się w odpowiedzi. Odpowiedź zawiera również ich lokalizacji (`Index`) w oryginalny tekst. `ListId` w następującym przykładzie JSON, który odwołuje się do warunków w [listy terminów niestandardowe](try-terms-list-api.md) Jeśli jest dostępny.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Dla **języka** parametru, Przypisz `eng` lub pozostaw to pole puste, aby zobaczyć wspomagane maszynowo **klasyfikacji** odpowiedzi (funkcja w wersji zapoznawczej). **Ta funkcja obsługuje tylko język angielski**.
>
> Dla **warunki wulgaryzmów** wykrywania, użyj [kod ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) z obsługiwanych języków, które są wymienione w tym artykule, lub pozostawić je puste.

## <a name="classification"></a>Klasyfikacja

Pakietu Content Moderator użytkownika wspomagane maszynowo **funkcji Klasyfikacja tekstu** obsługuje **tylko w języku angielskim**, co ułatwia wykrywanie potencjalnie niepożądane zawartości. Oflagowane zawartość może zostać oceniona jako niestosowny w zależności od kontekstu. On wywołuje prawdopodobieństwo wystąpienia każdej kategorii i może zalecić przeglądu przez ludzi. Funkcja używa uczonego modelu do identyfikowania możliwych obraźliwe, uwłaczającej lub porównawczego języka. W tym żargonu, skróconej słów, obraźliwych i celowo błędnie napisanych wyrazów do przeglądu. 

Następujące wyodrębniania w instrukcji extract JSON przedstawiono przykładowe dane wyjściowe:

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
    }

### <a name="explanation"></a>Wyjaśnienie

- `Category1` odnosi się do potencjalnego obecności języka, który może być uznawany za przekleństwa jawne lub treści dla dorosłych w niektórych sytuacjach.
- `Category2` odnosi się do potencjalnego obecności języka, który może być uznawany za przekleństwa dwuznaczne lub dla dorosłych w niektórych sytuacjach.
- `Category3` odnosi się do potencjalnego obecności język, który może być uznane za obraźliwe w niektórych sytuacjach.
- `Score` to od 0 do 1. Wyższą ocenę, tym większe modelu jest prognozowanie mogą dotyczyć kategorii. Ta funkcja opiera się na modelu statystycznych, a nie ręcznie zakodowane wyników. Zaleca się testowanie za pomocą własnej zawartości, aby określić, jak wyrównuje każdej kategorii wymagań.
- `ReviewRecommended` jest wartość PRAWDA lub FAŁSZ, w zależności od wewnętrznego wynik progów. Klientów należy ocenić, czy Użyj tej wartości lub wybrać progami niestandardowymi na podstawie ich zawartości zasad.

## <a name="personal-data"></a>Dane osobowe

Funkcja dane osobowe wykrywa potencjalne obecności te informacje:

- Adres e-mail
- Adres wysyłkowy Stanów Zjednoczonych
- Adres IP
- Numer telefonu w Stanach Zjednoczonych
- Numer telefonu Zjednoczone Królestwo
- Numer ubezpieczenia społecznego (SSN)

Poniższy przykład pokazuje przykładowa odpowiedź:

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
            "Index": 212
            }, {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 208
            }, {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 228
            }, {
            "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 245
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
            "Index": 267
            }]
        }

## <a name="auto-correction"></a>Korekta automatyczna

Załóżmy, że tekst wejściowy jest ("lzay" i "f0x" są zamierzone):

    The qu!ck brown f0x jumps over the lzay dog.

Jeśli poprosisz o Autokorektę, odpowiedź zawiera poprawionej wersji tekst:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Tworzenie i zarządzanie nimi z list niestandardowych warunków

Gdy ustawienie domyślne, globalne listy warunków działa doskonale nadaje się do większości przypadków, możesz chcieć ekranu względem warunki, które są specyficzne dla potrzeb biznesowych. Na przykład można odfiltrować wszelkie konkurencyjne nazw marek z wpisy według użytkowników.

> [!NOTE]
> Istnieje maksymalny limit wynoszący **5 list terminów**, a poszczególne listy **nie mogą przekraczać 10 000 terminów**.
>

Dopasowania Identyfikatora listy można znaleźć w poniższym przykładzie:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Content Moderator oferuje [API listy terminów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) z operacjami zarządzania termin niestandardowej listy. Rozpoczynać [Konsola interfejsu API listy terminów](try-terms-list-api.md) i przykłady kodu interfejsu API REST. Sprawdź również [termin .NET zawiera przewodnik Szybki Start](term-lists-quickstart-dotnet.md) osoby znające program Visual Studio w języku C#.

## <a name="next-steps"></a>Kolejne kroki

Wersja testowa [konsoli interfejs API moderowania tekstu](try-text-api.md) i przykłady kodu interfejsu API REST. Również szybko sprawdzić wydajność [Szybki Start .NET Moderowanie tekstu](text-moderation-quickstart-dotnet.md) Jeśli znasz program Visual Studio w języku C#.
