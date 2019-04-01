---
title: Obraz Moderowanie — pakietu Content Moderator
titlesuffix: Azure Cognitive Services
description: Na użytek obraz wspomagane maszynowo Moderowanie i narzędzie do przeglądu człowieka w pętli do obrazów umiarkowane Content Moderator zawartości dla dorosłych.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 9f1df23d1f0f24787bb9267064ffd647eda2cb74
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756047"
---
# <a name="learn-image-moderation-concepts"></a>Pojęcia dotyczące moderowania obrazów

Użyj Content Moderator obraz wspomagane maszynowo Moderowanie i [narzędzie do przeglądu człowieka w pętli](Review-Tool-User-Guide/human-in-the-loop.md) do umiarkowanego obrazów dla dorosłych i zawartości erotycznej. Skanowanie obrazów w przypadku zawartości tekstowej i Wyodrębnij ten tekst, a wykrywanie twarzy. Można dopasować obrazów niestandardowych list i wykonywanie dodatkowych działań.

## <a name="evaluating-for-adult-and-racy-content"></a>Ocenianie zawartości dla dorosłych

**Evaluate** operacji zwraca współczynnik ufności z zakresu od 0 i 1. Również zwraca danych logicznych równa true lub false. Wartości te przewidywania, czy obraz, który zawiera potencjalne zawartości dla dorosłych lub erotycznej. Jeśli chcesz wywołać interfejs API przy użyciu obrazu (plik lub adres URL), zwrócona odpowiedź zawiera następujące informacje:

    "ImageModeration": {
      .............
      "adultClassificationScore": 0.019196987152099609,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.032390203326940536,
      "isImageRacyClassified": false,
      ............
      ],

> [!NOTE]
> 
> - Obiekt `isImageAdultClassified` reprezentuje potencjalną obecność obrazów, które mogą być uznane za seksualne lub zawierające treści dla dorosłych w niektórych sytuacjach.
> - Obiekt `isImageRacyClassified` reprezentuje potencjalną obecność obrazów, które mogą być uznane za potencjalnie seksualne lub zawierające treści dla dorosłych w niektórych sytuacjach.
> - Wyniki należą do zakresu od 0 do 1. Wyższą ocenę, tym większe modelu jest prognozowanie mogą dotyczyć kategorii. Ta wersja zapoznawcza opiera się na modelu statystycznych, a nie ręcznie zakodowane wyników. Zaleca się testowanie za pomocą własnej zawartości, aby określić, jak wyrównuje każdej kategorii wymagań.
> - Wartości logiczne to wartość PRAWDA lub FAŁSZ, w zależności od wewnętrznego wynik progów. Klientów należy ocenić, czy Użyj tej wartości lub wybrać progami niestandardowymi na podstawie ich zawartości zasad.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Wykrywanie tekstu za pomocą funkcji optycznego rozpoznawania znaków (OCR)

**Optyczne rozpoznawanie znaków (OCR)** operacji przewiduje obecności zawartości tekstowej w obrazie i wyodrębnia Moderowanie tekstu, oprócz innych zastosowań. Można określić język. Jeśli język nie jest określona, domyślnie wykrywania jest język angielski.

Odpowiedź zawiera następujące informacje:
- Oryginalny tekst.
- Elementy tekstowe z wykrytym ich oceny zaufania.

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


## <a name="detecting-faces"></a>Wykrywanie twarzy

Wykrywanie twarzy pomaga wykryć danych osobowych, takich jak twarzy w obrazach. Wykrywanie potencjalnych twarzy i liczbę potencjalnych twarzy każdego obrazu.

Odpowiedź zawiera te informacje:

- Liczba twarzy
- Lista lokalizacji wykryte twarze:

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

## <a name="creating-and-managing-custom-lists"></a>Tworzenie i zarządzanie nimi niestandardowych list

W wielu społeczności w trybie online po użytkownikom przekazywanie obrazów lub innego typu zawartości, obraźliwe elementy mogą uzyskać udostępnić wiele razy w następujące dni, tygodnie i miesiące. Koszty wielokrotnie skanowania i filtrowanie ten sam obraz lub nawet nieco zmodyfikowaną wersji obrazu z kilku miejscach może być kosztowne i obarczone ryzykiem błędów.

Zamiast Moderowanie ten sam obraz wiele razy, dodasz obraźliwe obrazy niestandardowe listy zablokowanych zawartości. W ten sposób system moderowanie zawartości porównuje przychodzące obrazów z list niestandardowych i zatrzymuje dalszego przetwarzania.

> [!NOTE]
> Istnieje maksymalny limit wynoszący **5 list obrazów**, a poszczególne listy **nie mogą przekraczać 10 000 obrazów**.
>

Content Moderator oferuje kompletne [interfejsu API zarządzania listy obrazów](try-image-list-api.md) z operacjami zarządzania listami obrazów niestandardowych. Rozpoczynać [Konsola interfejsu API listy obrazów](try-image-list-api.md) i przykłady kodu interfejsu API REST. Również szybko sprawdzić wydajność [Szybki Start .NET listy obrazów](image-lists-quickstart-dotnet.md) osoby znające program Visual Studio w języku C#.

## <a name="matching-against-your-custom-lists"></a>Dopasowywanie względem niestandardowych list

Operacja umożliwia dopasowywania rozmytego obrazy dla dowolnego z list niestandardowych, tworzone i zarządzane przy użyciu operacji listy.

Jeśli zostanie znalezione dopasowanie, operacja zwraca identyfikator i tagi Moderowanie dopasowane obrazu. Odpowiedź zawiera te informacje:

- Ocena dopasowania (od 0 do 1)
- Dopasowane obrazu
- Znaczniki obrazów (przypisana podczas poprzedniego Moderowanie)
- Obraz etykiety

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

Aby uzyskać więcej złożonych przypadkach użyciu usługi Content Moderator [narzędzie do przeglądu](Review-Tool-User-Guide/human-in-the-loop.md) i jego interfejsu API, aby przedstawić wyniki moderacji i zawartość w przeglądzie dla Twojego ludzi moderatorów. One Przejrzyj tagi przypisane do maszyny i potwierdzić swoje decyzje końcowej.

![Przeglądanie obrazu przez moderatorów-ludzi](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Kolejne kroki

Wersja testowa [konsoli interfejsu API moderowania obrazów](try-image-api.md) i przykłady kodu interfejsu API REST. Również szybko sprawdzić wydajność [Szybki Start .NET moderowania obrazów](image-moderation-quickstart-dotnet.md) osoby znające program Visual Studio w języku C#.
