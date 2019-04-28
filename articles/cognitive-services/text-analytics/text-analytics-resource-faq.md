---
title: Często zadawane pytania dotyczące interfejsu API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące interfejsu API analizy tekstu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: a85fa543a6b26a5ea6452ce99fb91dc1ce465db7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60827956"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-cognitive-service"></a>Często zadawane pytania (FAQ) dotyczących usługi Cognitive analizy tekstu

 Znajdź odpowiedzi na często zadawane pytania na temat pojęć, kodu i scenariusze związane z nim interfejsu API analizy tekstu usług Microsoft Cognitive Services na platformie Azure.

## <a name="can-text-analytics-identify-sarcasm"></a>Analiza tekstu, można zidentyfikować sarkazmu?

Analiza jest zamiast nastrój wykrywania tonacji dodatnie i ujemne.

Zawsze jest pewien stopień niedokładność analizy tonacji, ale model jest najbardziej przydatne w przypadku, gdy nie ma znaczenia ukryte lub podtekstu do zawartości. Irony, sarkazmu, humor i podobnie rozmowach zawartości zależy od normy w celu przekazania intencji i kultury kontekstu. Ten typ zawartości jest wśród mechanizmu jest dość trudne do analizowania. Zazwyczaj największy rozbieżność między wyniku danego generowanych przez analizator i subiektywna ocena przez człowieka jest zawartości przy użyciu rozmowach znaczenie.

## <a name="can-i-add-my-own-training-data-or-models"></a>Czy mogę dodać moje własne dane szkoleniowe lub modele?

Nie, modele są wstępnie przetrenowane. Jedyne operacje na przekazane dane są oceniania, wyodrębnianie kluczowych fraz oraz wykrywanie języka. Firma Microsoft nie obsługują niestandardowych modeli. Jeśli chcesz użyć do tworzenia i hostowania modeli uczenia maszynowego niestandardowe, należy wziąć pod uwagę [usługi machine learning możliwości programu Microsoft R Server](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Czy mogę zażądać dodatkowych języków?

Analiza tonacji i wyodrębnianie kluczowych fraz są dostępne dla [wybierz liczbę języków](text-analytics-supported-languages.md). Przetwarzanie języka naturalnego jest złożony i wymaga znacznej testowania, zanim będą dostępne nowe funkcje. Z tego powodu firma Microsoft unikać wstępnie ogłoszenie pomocy technicznej, aby nikt ma zależność od funkcji, który wymaga więcej czasu na dla dorosłych. 

Aby pomóc nam określić priorytety języki do pracy po kliknięciu przycisku Dalej, Zagłosuj dla określonych języków [User Voice](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Dlaczego wyodrębnianie kluczowych fraz zwraca niektórych słów, a nie innych?

Wyodrębnianie kluczowych fraz eliminuje wyrazy inne niż niezbędne i określeniem autonomicznych. Kombinacje przyjęcie jednolitej, takie jak "spektakularnych widoków" lub "nie wiesz, pogody", zwracane są ze sobą.

Ogólnie rzecz biorąc dane wyjściowe składa się z rzeczowniki i obiekty zdania. Dane wyjściowe znajduje się w kolejności ważności, z pierwszym frazy, które są najważniejsze. Znaczenie jest mierzony przez liczbę przypadków, gdy mowa koncepcja określonego lub relacji tego elementu do innych elementów w tekście.

## <a name="why-does-output-vary-given-identical-inputs"></a>Dlaczego zmienia dane wyjściowe w zależności od, biorąc pod uwagę identyczne dane wejściowe?

Ulepszenia algorytmów i modeli są anonsowane, jeśli zmiany jest istotne lub ciche preinstalowanych w usłudze aktualizacji pomocniczych. Wraz z upływem czasu może stwierdzisz, że te same wyniki wprowadzania tekstu w różnych tonacji lub frazy kluczowe dane wyjściowe. Jest to normalne i zamierzone konsekwencją w chmurze przy użyciu materiałów szkoleniowych zarządzanego komputera.

## <a name="next-steps"></a>Kolejne kroki

To pytanie dotyczące brakujących funkcji lub funkcje? Należy wziąć pod uwagę żądania lub głosowanie na jego w nasze [witryny sieci web usługi UserVoice](https://cognitive.uservoice.com/forums/555922-text-analytics).

## <a name="see-also"></a>Zobacz także

 [StackOverflow: Interfejs API analizy tekstu](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: Cognitive Services](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
