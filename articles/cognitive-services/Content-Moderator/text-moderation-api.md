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
ms.openlocfilehash: 41e88dd5a08de485f770559959843ba3b54e590f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274014"
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

W poniższym przykładzie przedstawiono przykładową odpowiedź:

```json
"pii":{
  "email":[
      {
        "detected":"abcdef@abcd.com",
        "sub_type":"Regular",
        "text":"abcdef@abcd.com",
        "index":32
      }
  ],
  "ssn":[

  ],
  "ipa":[
      {
        "sub_type":"IPV4",
        "text":"255.255.255.255",
        "index":72
      }
  ],
  "phone":[
      {
        "country_code":"US",
        "text":"6657789887",
        "index":56
      }
  ],
  "address":[
      {
        "text":"1 Microsoft Way, Redmond, WA 98052",
        "index":89
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
