---
title: Moderowanie filmów wideo z przeglądu przez ludzi - Content Moderator
titlesuffix: Azure Cognitive Services
description: Użyj wspomagane maszynowo Moderowanie filmów wideo oraz narzędziom do przeglądu przez ludzi do umiarkowanego nieodpowiednią zawartość
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: sajagtap
ms.openlocfilehash: a6c467d3153400815e37a5d461766140abd1fa32
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228131"
---
# <a name="video-moderation-with-human-review"></a>Moderowanie filmów wideo z przeglądu przez ludzi

Użyj Content Moderator wspomagane maszynowo [Moderowanie filmów wideo](video-moderation-api.md) i [narzędzie do przeglądu przez ludzi](Review-Tool-User-Guide/human-in-the-loop.md) umiarkowane filmów wideo i transkrypcje dla dorosłych (jawne) i erotycznej zawartości (dwuznaczne), aby uzyskać najlepsze wyniki dla Twojej firmy.

## <a name="video-trained-classifier-preview"></a>Klasyfikator skonfigurowanych pod kątem wideo (wersja zapoznawcza)

Wspomagane maszynowo klasyfikacji wideo albo odbywa się przy użyciu modeli skonfigurowanych pod kątem obrazów lub wideo, przeszkolone modele. W odróżnieniu od skonfigurowanych pod kątem obrazu wideo klasyfikatorów firmy Microsoft dla dorosłych klasyfikatora wideo jest uczony z filmów wideo. Ta metoda powoduje lepszą jakość dopasowania.

## <a name="shot-detection"></a>Wykrywanie ujęć

Podczas wyprowadzania szczegóły klasyfikacji, dodatkowe analizy wideo pomaga większą elastyczność w analizę filmów wideo. Zamiast podawania tylko ramki, usługa moderowania klipów wideo firmy Microsoft zapewnia informacje na poziomie zrzut zbyt. Masz teraz możliwość analizować filmy wideo na poziomie strzał i ramki.

## <a name="key-frame-detection"></a>Wykrywanie ramki kluczowe

Zamiast podawania ramki w regularnych odstępach czasu, usługa moderowania klipów wideo identyfikuje i wyświetla tylko potencjalnie zakończone ramek (dobra). Ta funkcja umożliwia generowanie wydajne ramki dla ramki poziom analizy dla dorosłych.

Następujące wyodrębniania pokazuje to częściowa odpowiedź z potencjalnymi zrzuty, klatek kluczowych i wyniki dla dorosłych:

```json
"fragments":[  
  {  
    "start":0,
    "duration":18000
  },
  {  
    "start":18000,
    "duration":3600,
    "interval":3600,
    "events":[  
      [  
        {  
          "reviewRecommended":false,
          "adultScore":0.00001,
          "racyScore":0.03077,
          "index":5,
          "timestamp":18000,
          "shotIndex":0
        }
      ]
    ]
  },
  {  
    "start":18386372,
    "duration":119149,
    "interval":119149,
    "events":[  
      [  
        {  
          "reviewRecommended":true,
          "adultScore":0.00000,
          "racyScore":0.91902,
          "index":5085,
          "timestamp":18386372,
          "shotIndex":62
        }
      ]
    ]
```

## <a name="visualization-for-human-reviews"></a>Wizualizacja umożliwiającymi ludziom dokonywanie

Aby uzyskać więcej złożonych przypadkach firma potrzebuje rozwiązania przeglądu przez ludzi renderowania wideo, jego ramki i tagi przypisane do maszyny. Moderatorzy ludzi, przeglądanie filmów wideo i ramki uzyskać pełen wgląd w szczegółowe dane, zmienianie tagów i prześlij swoje decyzje.

![Widok domyślny narzędzie do przeglądu wideo](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Widok Player do przeglądu poziomie filmu wideo

Wideo na poziomie binarnym decyzji możliwego widokiem odtwarzacz wideo, który pokazuje potencjalne ramki dla dorosłych. Recenzenci ludzi Przejdź wideo z różnymi opcjami prędkości, aby sprawdzić w tle. Potwierdzeniu swoje decyzje, przełączając tagów.

![Widok player narzędzia przeglądu wideo](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Widok ramek, aby uzyskać szczegółowe

Szczegółowy przegląd wideo dla analizy klatka po klatce, jest możliwe przy użyciu widoku opartych na klatkach. Recenzenci ludzi Przejrzyj i wybierz jedną lub więcej ramek i Przełącz znaczniki, aby potwierdzić swoje decyzje. Opcjonalnie, następnym krokiem jest redakcyjne ramek obraźliwe lub zawartości.

![Widok ramek narzędzia przeglądu wideo](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Moderowanie transkrypcji

Filmy wideo, zwykle dokonują głosowych za pośrednictwem, których potrzebuje Moderowanie także obraźliwe rozpoznawania mowy. Usługa Azure Media Indexer umożliwia konwertowanie mowy na tekst i użyj interfejsu API pakietu Content Moderator przeglądu, aby przesłać transkrypcji na potrzeby moderowania tekstu w obrębie narzędzie do przeglądu.

![Widok transkrypcji narzędzia przeglądu wideo](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Kolejne kroki

- Rozpoczynanie pracy z usługą [Moderowanie filmów wideo przewodnika Szybki Start](video-moderation-api.md).
- Dowiedz się, jak wygenerować [przeglądy wideo](video-reviews-quickstart-dotnet.md) dla człowieka recenzentów z moderowanych danych wyjściowych.
- Dodaj [przeglądy transkrypcji wideo](video-transcript-reviews-quickstart-dotnet.md) do Twojego wideo przeglądów.
- Zapoznaj się z szczegółowy samouczek dotyczący sposobu tworzenia [kompletne rozwiązanie Moderowanie filmów wideo](video-transcript-moderation-review-tutorial-dotnet.md).