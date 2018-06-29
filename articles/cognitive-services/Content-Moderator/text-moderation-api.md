---
title: Azure moderatora zawartości - łagodzenia tekstu | Dokumentacja firmy Microsoft
description: Użyć łagodzenia tekstu w przypadku możliwości niechciane tekstu, dane osobowe, listy warunków niestandardowe.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/30/2018
ms.author: sajagtap
ms.openlocfilehash: 6924807a64cec074d9688eaad158bb9bb638f6bb
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085763"
---
# <a name="text-moderation"></a>Moderowanie tekstu

Użyj łagodzenia tekst pomocy maszyny moderatora zawartości i [człowieka przeglądu](Review-Tool-User-Guide/human-in-the-loop.md) możliwości na średni zawartości tekstowej.

Możesz zablokować, Zatwierdź albo przejrzyj zawartość, w oparciu o progi i zasady. Użyj, aby rozszerzyć człowieka łagodzenia środowisk gdzie partnerów, pracowników i konsumentów Generowanie zawartości tekstowej. Obejmują one pokoje rozmów, tablice dyskusyjne chatbots, katalogi handlu elektronicznego i dokumenty. 

Odpowiedź usługi zawiera następujące informacje:

- Niestosownych wyrażeń: na podstawie terminu dopasowywanie wbudowanej listy profanujących warunki w różnych językach
- Klasyfikacja: wspierana maszyny klasyfikacji na trzy kategorie
- Dane osobowe (dane osobowe)
- Poprawił tekstu
- Oryginalny tekst
- Język

## <a name="profanity"></a>Niestosownych wyrażeń

W przypadku wykrycia przez interfejs API wszelkie warunki profanujących w żadnym z [obsługiwanych języków](Text-Moderation-API-Languages.md), te zasady są uwzględnione w odpowiedzi. Odpowiedź zawiera również ich lokalizacji (`Index`) w oryginalny tekst. `ListId` w następującym przykładowym JSON, który odwołuje się do warunków w [termin niestandardowych list](try-terms-list-api.md) Jeśli jest dostępna.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Dla **języka** parametru, Przypisz `eng` lub pozostaw to pole puste, aby zobaczyć wspierana maszyny **klasyfikacji** odpowiedzi (funkcja w wersji zapoznawczej). **Ta funkcja obsługuje tylko język angielski**.
>
> Dla **warunki niestosownych wyrażeń** wykrywania, użyj [kod ISO 639 3](http://www-01.sil.org/iso639-3/codes.asp) z obsługiwanych języków, które są wymienione w tym artykule, lub pozostaw puste.

## <a name="classification"></a>Klasyfikacja

Moderatora zawartości elementu wspierana maszyny **funkcji klasyfikacji tekst** obsługuje **tylko angielski**, co ułatwia wykrywanie potencjalnie niepożądane zawartości. Oflagowane zawartość może zostać oceniona jako nieodpowiednie w zależności od kontekstu. Przekazuje prawdopodobieństwo wystąpienia każdej kategorii, a może zalecić człowieka przeglądu. Funkcja używa uczonego modelu do identyfikowania możliwych obraźliwe, derogacyjnego lub porównawczego języka. W tym żargon, skróconej wyrazy, obraźliwe i celowo błędnie wpisane słowa do przeglądu. 

Następujące wyodrębniania w wyodrębniania JSON przedstawiono przykładowe dane wyjściowe:

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

- `Category1` odnosi się do potencjalnych obecności język, który można uznać za płciowo jawne lub dla dorosłych w niektórych sytuacjach.
- `Category2` odnosi się do potencjalnych obecności język, który można uznać za płciowo sugerujących lub dojrzałe w niektórych sytuacjach.
- `Category3` odnosi się do potencjalnych obecności język, który może zostać uznany za obraźliwy w niektórych sytuacjach.
- `Score` to od 0 do 1. Im wyższa jest ocena wyższa modelu jest prognozowanie kategorii można stosować. Ta wersja zapoznawcza zależy od statystyczne modelu zamiast wyników kodowane ręcznie. Zaleca się testowanie na własną zawartość, aby określić, jak każda kategoria wyrównuje do własnych potrzeb.
- `ReviewRecommended` jest wartość PRAWDA lub FAŁSZ w zależności od wewnętrznego wynik progów. Klienci powinna ocenić, czy ta wartość lub zdecydować o progami niestandardowymi na podstawie ich zawartości zasad.

## <a name="personally-identifiable-information-pii"></a>Dane osobowe (dane osobowe)

Funkcja dane osobowe wykrywa potencjalne obecności te informacje:

- Adres e-mail
- Adres wysyłkowy stany USA
- Adres IP
- Amerykański numer telefonu
- Numer telefonu UK
- Numer ubezpieczenia społecznego (SSN)

W poniższym przykładzie przedstawiono przykładowa odpowiedź:

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

## <a name="auto-correction"></a>Automatyczne naprawianie

Załóżmy, że tekst wejściowy jest ("lzay" i "f0x" są zamierzone):

    The qu!ck brown f0x jumps over the lzay dog.

Jeśli zadajesz dla korekty automatycznej odpowiedzi zawiera poprawiony wersję tekst:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Tworzenie i zarządzanie nimi z listy warunków

Gdy domyślne, globalnej listy warunków działa dobrze w większości przypadków, warto ekranu względem warunki, które są specyficzne dla potrzeb biznesowych. Na przykład można odfiltrować żadnych konkurencyjnych firmowe z wpisów przez użytkowników.

> [!NOTE]
> Istnieje limit maksymalny wynoszący **wymieniono 5 termin** z każdej listy **przekracza 10 000 warunki**.
>

W poniższym przykładzie przedstawiono pasujących identyfikator listy:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Udostępnia zawartości moderatora [interfejsu API listy termin](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) z operacjami zarządzania termin niestandardowe zawiera listę. Rozpoczynać [termin wymieniono interfejsu API konsoli](try-terms-list-api.md) i użyj przykłady kodu interfejsu API REST. Sprawdź również [szybkiego startu .NET wymieniono termin](term-lists-quickstart-dotnet.md) Jeśli znasz program Visual Studio i C#.

## <a name="next-steps"></a>Kolejne kroki

Przetestuj [tekst łagodzenia interfejsu API konsoli](try-text-api.md) i użyj przykłady kodu interfejsu API REST. Sprawdź również [szybkiego startu .NET łagodzenia tekst](text-moderation-quickstart-dotnet.md) Jeśli znasz program Visual Studio i C#.
