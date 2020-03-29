---
title: Często zadawane pytania dotyczące interfejsu API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Znajdź odpowiedzi na często zadawane pytania dotyczące pojęć, kodu i scenariuszy związanych z interfejsem API analizy tekstu dla usług Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: aca3b0bea65923cee1bae25020058666c973922c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837119"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-cognitive-service"></a>Często zadawane pytania dotyczące usługi Cognitive Service analizy tekstu

 Znajdź odpowiedzi na często zadawane pytania dotyczące pojęć, kodu i scenariuszy związanych z interfejsem API analizy tekstu dla usług Microsoft Cognitive Services na platformie Azure.

## <a name="can-text-analytics-identify-sarcasm"></a>Czy analiza tekstu może identyfikować sarkazm?

Analiza dotyczy nastrojów pozytywnych i negatywnych, a nie wykrywania nastroju.

W analizie tonacji zawsze występuje pewien stopień niedokładności, ale model jest najbardziej przydatny, gdy zawartość nie ma ukrytego znaczenia ani podtekstu. Ironia, sarkazm, humor i podobnie zniuansowane treści opierają się na kontekście kulturowym i normach, aby przekazać intencje. Ten typ treści jest jednym z najtrudniejszych do przeanalizowania. Zazwyczaj największą rozbieżność między danym wynikiem wytwarzanym przez analizator a subiektywną oceną przez człowieka jest zadowalające się treścią o zniuansowanym znaczeniu.

## <a name="can-i-add-my-own-training-data-or-models"></a>Czy mogę dodać własne dane szkoleniowe lub modele?

Nie, modele są wstępnie przeszkolone. Jedyne operacje dostępne na przekazanych danych to punktacja, wyodrębnianie fraz kluczowych i wykrywanie języka. Nie hostujemy modeli niestandardowych. Jeśli chcesz tworzyć i obsługiwać niestandardowe modele uczenia maszynowego, należy wziąć pod uwagę [możliwości uczenia maszynowego w programie Microsoft R Server](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Czy mogę poprosić o dodatkowe języki?

Analiza tonacji i wyodrębnianie fraz kluczowych są dostępne dla [wybranej liczby języków](text-analytics-supported-languages.md). Przetwarzanie języka naturalnego jest złożone i wymaga znacznych testów przed wydaniem nowych funkcji. Z tego powodu unikamy wstępnego ogłaszania pomocy technicznej, aby nikt nie uwzględniał funkcjonalności, które wymagają więcej czasu na dojrzewanie. 

Aby pomóc nam ustalić priorytety języków, nad którymi należy pracować, zagłosuj na określone języki w [uzyt.](https://cognitive.uservoice.com/forums/555922-text-analytics) 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Dlaczego wyodrębnianie fraz kluczowych zwraca niektóre słowa, ale nie inne?

Wyodrębnianie fraz kluczowych eliminuje nieistotne słowa i przymiotniki autonomiczne. Kombinacje przymiotników-rzeczowników, takie jak "spektakularne widoki" lub "mglista pogoda" są zwracane razem.

Ogólnie rzecz biorąc, dane wyjściowe składają się z rzeczowników i obiektów zdania. Dane wyjściowe są wymienione w kolejności ważności, przy czym pierwsza fraza jest najważniejsza. Znaczenie jest mierzone przez liczbę razy określone pojęcie jest wymienione, lub relacji tego elementu do innych elementów w tekście.

## <a name="why-does-output-vary-given-identical-inputs"></a>Dlaczego dane wyjściowe są różne, biorąc pod uwagę identyczne wejścia?

Ulepszenia modeli i algorytmów są ogłaszane, jeśli zmiana jest poważna lub cicho slipstreamed do usługi, jeśli aktualizacja jest niewielka. Z biegiem czasu może się okazać, że ten sam tekst powoduje inny wynik tonacji lub dane wyjściowe frazy kluczowej. Jest to normalna i zamierzona konsekwencja korzystania z zarządzanych zasobów uczenia maszynowego w chmurze.

## <a name="next-steps"></a>Następne kroki

Czy masz pytanie dotyczące brakującej funkcji lub funkcji? Rozważ zgłoszenie lub oddanie na nią głosu na naszej [stronie internetowej UserVoice.](https://cognitive.uservoice.com/forums/555922-text-analytics)

## <a name="see-also"></a>Zobacz też

 [StackOverflow: Interfejs API analizy tekstu](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: Usługi kognitywne](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
