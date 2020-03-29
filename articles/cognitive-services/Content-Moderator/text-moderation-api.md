---
title: Moderowanie tekstu - Moderator zawartości
titleSuffix: Azure Cognitive Services
description: Użyj moderowania tekstu dla ewentualnego niechcianego tekstu, danych osobowych i niestandardowych list terminów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 5a07f0749b59efc96b67df3ad5ed2fbf353be614
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74538842"
---
# <a name="learn-text-moderation-concepts"></a>Poznaj pojęcia dotyczące moderowania tekstu

Moderowanie tekstu wspomagane maszynowo przez moderatora zawartości i funkcje [przeglądania opinii przez człowieka.](Review-Tool-User-Guide/human-in-the-loop.md)

Zawartość można blokować, zatwierdzać lub przeglądać na podstawie ustawionych zasad i progów. Użyj go, aby zwiększyć ludzkie umiar środowisk, w których partnerzy, pracownicy i konsumenci generują treści tekstowe. Te środowiska obejmują pokoje czatów, tablice dyskusyjne, czatboty, katalogi handlu elektronicznego i dokumenty. 

Odpowiedź usługi zawiera następujące informacje:

- Wulgaryzmy: dopasowywanie terminowe z wbudowaną listą wulgaryzmów w różnych językach
- Klasyfikacja: klasyfikacja wspomagana maszynowo na trzy kategorie
- Dane osobowe
- Tekst z automatyczną poprawą
- Tekst oryginalny
- Język

## <a name="profanity"></a>Wulgaryzmy

Jeśli api wykryje żadnych wulgaryzmów w dowolnym z [obsługiwanych języków,](Text-Moderation-API-Languages.md)terminy te są zawarte w odpowiedzi. Odpowiedź zawiera również ich`Index`lokalizację ( ) w oryginalnym tekście. W `ListId` poniższej przykładzie JSON odnosi się do terminów znalezionych na [listach terminów niestandardowych,](try-terms-list-api.md) jeśli są dostępne.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Dla **language** parametru języka `eng` przypisz lub pozostaw go pusty, aby wyświetlić odpowiedź **klasyfikacji** wspomaganej maszynowo (funkcja podglądu). **Ta funkcja obsługuje tylko język angielski**.
>
> W przypadku wykrywania **wulgaryzmów** należy użyć [kodu ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) obsługiwanych języków wymienionych w tym artykule lub pozostawić go pustym.

## <a name="classification"></a>Klasyfikacja

**Funkcja klasyfikacji tekstu** wspomagana maszynowo przez moderatora zawartości obsługuje tylko język **angielski**i pomaga wykrywać potencjalnie niepożądaną zawartość. Oflagowana zawartość może zostać oceniona jako nieodpowiednia w zależności od kontekstu. To przekazuje prawdopodobieństwo każdej kategorii i może zalecić przegląd człowieka. Funkcja wykorzystuje wyszkolony model do identyfikacji ewentualnego obraźliwego, uwłaczające lub dyskryminującego języka. Obejmuje to slang, skrócone słowa, obraźliwe i celowo błędnie napisane słowa do sprawdzenia. 

Poniższy wyciąg w wyciągu JSON pokazuje przykładowe dane wyjściowe:

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

- `Category1`odnosi się do potencjalnej obecności języka, który w pewnych sytuacjach może być uznany za wyraźny pod względem seksualnym lub dorosły.
- `Category2`odnosi się do potencjalnej obecności języka, który może być uznany za sugestywny seksualnie lub dojrzały w pewnych sytuacjach.
- `Category3`odnosi się do potencjalnej obecności języka, który w niektórych sytuacjach może zostać uznany za obraźliwy.
- `Score`wynosi od 0 do 1. Im wyższy wynik, tym wyższy model przewiduje, że kategoria może mieć zastosowanie. Ta funkcja opiera się na modelu statystycznym, a nie ręcznie kodowane wyniki. Zalecamy przetestowanie z własną zawartością, aby określić, jak każda kategoria jest zgodna z Twoimi wymaganiami.
- `ReviewRecommended`jest true lub false w zależności od wewnętrznych progów wynik. Klienci powinni ocenić, czy użyć tej wartości lub zdecydować o progach niestandardowych na podstawie ich zasad zawartości.

## <a name="personal-data"></a>Dane osobowe

Funkcja danych osobowych wykrywa potencjalną obecność tych informacji:

- Adres e-mail
- Adres korespondujący w USA
- Adres IP
- Numer telefonu w USA
- Numer telefonu w Wielkiej Brytanii
- Numer ubezpieczenia społecznego (SSN)

W poniższym przykładzie przedstawiono przykładową odpowiedź:

```json
"PII":{ 
  "Email":[ 
    { 
      "Detected":"abcdef@abcd.com",
      "SubType":"Regular",
      "Text":"abcdef@abcd.com",
      "Index":32
    }
  ],
  "IPA":[ 
    { 
      "SubType":"IPV4",
      "Text":"255.255.255.255",
      "Index":72
    }
  ],
  "Phone":[ 
    { 
      "CountryCode":"US",
      "Text":"4255550111",
      "Index":56
    },
    { 
      "CountryCode":"US",
      "Text":"425 555 0111",
      "Index":212
    },
    { 
      "CountryCode":"UK",
      "Text":"+123 456 7890",
      "Index":208
    },
    { 
      "CountryCode":"UK",
      "Text":"0234 567 8901",
      "Index":228
    },
    { 
      "CountryCode":"UK",
      "Text":"0456 789 0123",
      "Index":245
    }
  ],
  "Address":[ 
    { 
      "Text":"1234 Main Boulevard, Panapolis WA 96555",
      "Index":89
    }
  ],
  "SSN":[ 
    { 
      "Text":"999999999",
      "Index":56
    },
    { 
      "Text":"999-99-9999",
      "Index":267
    }
  ]
}
```

## <a name="auto-correction"></a>Automatyczna korekta

Załóżmy, że tekst wejściowy jest ("lzay" i "f0x" są zamierzone):

    The qu!ck brown f0x jumps over the lzay dog.

Jeśli poprosisz o automatyczną korektę, odpowiedź zawiera poprawioną wersję tekstu:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Tworzenie niestandardowych list terminów i zarządzanie nimi

Chociaż domyślna, globalna lista terminów działa świetnie w większości przypadków, możesz chcieć sprawdzać terminy, które są specyficzne dla twoich potrzeb biznesowych. Możesz na przykład odfiltrować konkurencyjne nazwy marek z postów użytkowników.

> [!NOTE]
> Istnieje maksymalny limit wynoszący **5 list terminów**, a poszczególne listy **nie mogą przekraczać 10 000 terminów**.
>

W poniższym przykładzie przedstawiono pasujący identyfikator listy:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Moderator zawartości udostępnia [interfejs API listy terminów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) z operacjami zarządzania niestandardowymi listami terminów. Zacznij od [konsoli interfejsu API listy terminów](try-terms-list-api.md) i użyj przykładów kodu interfejsu API REST. Sprawdź również [terminy listy .NET Szybki start,](term-lists-quickstart-dotnet.md) jeśli znasz visual studio i C#.

## <a name="next-steps"></a>Następne kroki

Przetestuj dysk [konsoli interfejsu API moderowania tekstu](try-text-api.md) i użyj przykładów kodu interfejsu API REST. Sprawdź również sekcję moderowanie tekstu w [programie Szybki start .NET SDK,](dotnet-sdk-quickstart.md) jeśli znasz program Visual Studio i C#.
