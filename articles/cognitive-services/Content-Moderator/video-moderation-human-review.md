---
title: Azure moderatora zawartości - łagodzenia wideo | Dokumentacja firmy Microsoft
description: Średnie nieodpowiedniej zawartości za pomocą wspierana maszyny łagodzenia wideo i narzędzia człowieka przeglądu
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/20/2018
ms.author: sajagtap
ms.openlocfilehash: fb26c9af55381c80a3f520b1a0068d8f72c91061
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "35346972"
---
# <a name="video-moderation"></a>Moderowanie filmów wideo

Użyj zawartości moderatora maszyny przy pomocy [wideo łagodzenia](video-moderation-api.md) i [narzędzie przeglądu człowieka](Review-Tool-User-Guide/human-in-the-loop.md) umiarkowane wideo i zapisy dla dorosłych (jawne) i luksusowych zawartości (sugerującą), aby uzyskać najlepsze wyniki dla firmy.

## <a name="video-trained-classifier"></a>Klasyfikator uczony wideo

Asystowane maszyny wideo klasyfikacji albo odbywa się z modeli uczenia obrazu lub wideo przeszkolone modeli. W przeciwieństwie do uczenia obrazu wideo klasyfikatory firmy Microsoft dla dorosłych i luksusowych klasyfikatora wideo jest uczony z wideo. Ta metoda powoduje lepszą jakość dopasowania.

## <a name="shot-detection"></a>Zrzut wykrywania

Wyprowadzanie szczegóły klasyfikacji, dodatkowe analizy wideo ułatwiające większą elastyczność podczas analizowania plików wideo. Zamiast Generowanie tylko ramki, usługi łagodzenia wideo Microsoft informacje zrzut poziomie zbyt. Masz teraz opcję, aby analizować filmy wideo zrzut poziomie i ramki.
 
## <a name="key-frame-detection"></a>Wykrywanie klatki

Zamiast Generowanie ramki w regularnych odstępach czasu, usługa wideo łagodzenia identyfikuje i wyprowadza tylko potencjalnie zakończone ramki (pomyślne). Ta funkcja umożliwia generowanie wydajne ramki do analizy dla dorosłych i luksusowych poziom ramki.

Następujące wyodrębniania przedstawia częściowej odpowiedzi z potencjalnych zrzuty, klatek kluczowych i wyniki dla dorosłych i luksusowych:

    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]


## <a name="visualization-for-human-reviews"></a>Wizualizacja dla człowieka przeglądów

Aby uzyskać więcej nuanced przypadków, firmach potrzebuje rozwiązania przeglądu człowieka podczas odtwarzania wideo, jego ramki i tagi maszyna przypisana. Moderatorów ludzi, klipów wideo i ramki pełną wyświetlania szczegółowych danych, zmień tagów i przesłać swoje decyzje.

![Widok domyślny narzędzie wideo przeglądu](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Widok Player do przeglądu poziomu wideo

Decyzje binarne poziom wideo są możliwe z widokiem odtwarzacza wideo, pokazujący potencjalnych dla dorosłych i luksusowych ramki. Recenzenci człowieka Przejdź wideo z różnymi opcjami szybkości w celu zbadania w tle. One potwierdzić swoje decyzje przełączając tagi.

![Przejrzyj wideo narzędzia player widoku](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Widok ramki szczegółowe przeglądów

Szczegółowy przegląd wideo do analizy przez klatka jest możliwe z widokiem na podstawie ramki. Człowieka recenzentów Przejrzyj i wybierz jedną lub więcej ramek i Przełącz znaczniki, aby potwierdzić swoje decyzje. Opcjonalne, następnym krokiem jest redakcyjne ramki obraźliwe lub zawartości.

![Przejrzyj wideo narzędzia ramki widoku](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Wykaz łagodzenia

Filmy wideo zwykle mają głosu wymagający łagodzenia również obraźliwe rozpoznawania mowy. Korzystasz z usługi Azure Media indeksatora konwersji mowy na tekst i przesłać wykaz dla łagodzenia tekstu w narzędziu przeglądu za pomocą interfejsu API przeglądu moderatora zawartości.

![Wyświetl wykaz narzędzia przeglądu wideo](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy z [szybkiego startu wideo łagodzenia](video-moderation-api.md). 

Dowiedz się, jak Generowanie [przegląda wideo](video-reviews-quickstart-dotnet.md) dla człowieka recenzentów z moderowane dane wyjściowe.

Dodaj [przegląda wideo wykaz](video-transcript-reviews-quickstart-dotnet.md) do swoje recenzje wideo.

Zapoznaj się z szczegółowy samouczek dotyczący tworzenia [ukończyć rozwiązania wideo łagodzenia](video-transcript-moderation-review-tutorial-dotnet.md). 
