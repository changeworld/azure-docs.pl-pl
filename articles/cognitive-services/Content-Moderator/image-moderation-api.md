---
title: Moderatora zawartości platformy Azure — obraz łagodzenia | Dokumentacja firmy Microsoft
description: Średnie nieodpowiednie obrazów za pomocą łagodzenia obrazu
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/20/2018
ms.author: sajagtap
ms.openlocfilehash: c7cbc343c6e9113642d0ac79f4a4d60a404e8171
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349336"
---
# <a name="image-moderation"></a>Moderowanie obrazów

Użyj łagodzenia wspierana maszyny obrazu moderatora zawartości i [narzędzie przeglądu człowieka](Review-Tool-User-Guide/human-in-the-loop.md) na średni obrazy do treści dla dorosłych i luksusowych. Skanowanie obrazów dla zawartości tekstowej i Wyodrębnij tekst i wykrywa powierzchni. Zgodne obrazów z listy i wykonywanie dodatkowych działań.

## <a name="evaluating-for-adult-and-racy-content"></a>Ocena zawartości dla dorosłych i luksusowych

**Evaluate** operacja zwraca wynik zaufania pomiędzy 0 a 1. Również zwraca danych logicznych równa true lub false. Te wartości prognozowania, czy obraz zawiera potencjalne zawartość dla dorosłych lub luksusowych. Po wywołaniu interfejsu API z obrazu (plik lub adres URL), zwrócona odpowiedź zawiera następujące informacje:

    "ImageModeration": {
      .............
      "adultClassificationScore": 0.019196987152099609,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.032390203326940536,
      "isImageRacyClassified": false,
      ............
      ],

> [!NOTE]

> - `isImageAdultClassified` reprezentuje potencjalnych obecności obrazów, które mogą być uznawane za płciowo jawne lub dla dorosłych w niektórych sytuacjach.
> - `isImageRacyClassified` reprezentuje potencjalnych obecności obrazów, które mogą być uznawane za płciowo sugerujących lub dojrzałe w niektórych sytuacjach.
> - Wyniki należą do zakresu od 0 do 1. Im wyższa jest ocena wyższa modelu jest prognozowanie kategorii można stosować. Ta wersja zapoznawcza zależy od statystyczne modelu zamiast wyników kodowane ręcznie. Zaleca się testowanie na własną zawartość, aby określić, jak każda kategoria wyrównuje do własnych potrzeb.
> - Wartościami logicznymi są true lub false, w zależności od wewnętrznego wynik progi. Klienci powinna ocenić, czy ta wartość lub zdecydować o progami niestandardowymi na podstawie ich zawartości zasad.
>

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Wykrywanie tekstu z OCR (Rozpoznawania)

**OCR (Rozpoznawania)** operacja prognozuje obecności zawartości tekstowej w obrazie i wyodrębnia tekst łagodzenia, oprócz innych zastosowań. Można określić języka. Jeśli język nie jest określony, domyślnie wykrywania jest język angielski.

Odpowiedź zawiera następujące informacje:
- Oryginalny tekst.
- Elementy wykryte tekst wyniki zaufania.

Przykład wyodrębniania:

    "TextDetection": {
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      },
      .........
      "language": "eng",
      "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
      "candidates": []
    },


## <a name="detecting-faces"></a>Wykrywanie powierzchni

Wykrywanie kroje pomaga wykryć informacji umożliwiających identyfikację użytkownika (dane osobowe), takich jak kroje obrazów. Wykrywanie potencjalnych kroje i liczbę potencjalnych kroje w każdego obrazu.

Odpowiedź zawiera te informacje:

- Liczba powierzchni
- Lista lokalizacji kroje wykryto

Przykład wyodrębniania:


    "FaceDetection": {
       ......
      "result": true,
      "count": 2,
      "advancedInfo": [
      .....
      ],
      "faces": [
        {
          "bottom": 598,
          "left": 44,
          "right": 268,
          "top": 374
        },
        {
          "bottom": 620,
          "left": 308,
          "right": 532,
          "top": 396
        }
      ]
    }

## <a name="creating-and-managing-custom-lists"></a>Tworzenie i zarządzanie nimi list niestandardowych

W wielu społeczności online po użytkowników przekazywać obrazy lub innego typu zawartości, obraźliwe elementów może pobrać udostępnionego wiele razy w następujące dni, tygodnie i miesiące. Koszty wielokrotnie skanowania i filtrowanie takiego samego obrazu lub nawet nieco zmodyfikowany wersji obrazu w wielu miejscach może być kosztowne i podatne na błędy.

Zamiast Moderowanie wiele razy ten sam obraz, możesz dodać obraźliwe obrazy niestandardowe listy zablokowanych zawartości. W ten sposób systemu zawartości łagodzenia porównuje przychodzące obrazów przed niestandardowych list i zatrzymuje dalsze przetwarzanie.

> [!NOTE]
> Istnieje limit maksymalny wynoszący **5 obrazu list** z każdej listy **przekracza 10 000 obrazów**.
>

Moderatora zawartości zapewnia pełnego [interfejs API zarządzania listy obrazów](try-image-list-api.md) z operacjami na zarządzanie listami obrazów niestandardowych. Rozpoczynać [obrazu wyświetla interfejsu API konsoli](try-image-list-api.md) i użyj przykłady kodu interfejsu API REST. Sprawdź również [szybkiego startu .NET listy obrazów](image-lists-quickstart-dotnet.md) Jeśli znasz program Visual Studio i C#.

## <a name="matching-against-your-custom-lists"></a>Porównanie z list niestandardowych

Operacja dopasowania umożliwia dopasowywania rozmytego obrazy od wszelkich niestandardowych list, tworzenia i zarządzania nimi przy użyciu operacji listy.

W przypadku odnalezienia pasującego operacji zwraca identyfikator i tagi łagodzenia dopasowane obrazu. Odpowiedź zawiera te informacje:

- Wynik dopasowania (między 0 a 1)
- Dopasowany obrazu
- Tagi obrazu (przypisany podczas poprzedniego łagodzenia)
- Etykiety obrazu

Przykład wyodrębniania:

    {
    ..............,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    ....
    }

## <a name="human-review-tool"></a>Narzędzie do przeglądu przez ludzi

Więcej nuanced przypadków użyć moderatora zawartości [Przegląd narzędzia](Review-Tool-User-Guide/human-in-the-loop.md) i jego interfejs API do powierzchni łagodzenia wyniki i zawartość w przeglądzie dla Twojego moderatorów człowieka. One Przejrzyj tagi maszyna przypisana i Potwierdź swoje decyzje końcowego.

![Przejrzyj obrazu dla człowieka moderatorów](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Kolejne kroki

Przetestuj [konsolę obrazu łagodzenia API](try-image-api.md) i użyj przykłady kodu interfejsu API REST. Sprawdź również [szybkiego startu .NET łagodzenia obrazu](image-moderation-quickstart-dotnet.md) Jeśli znasz program Visual Studio i C#.
