---
title: Analiza tekstu interfejsu API - kognitywnych usług platformy Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące kognitywnych usług tekst analizy interfejsu API usługi Microsoft Azure.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 3/07/2018
ms.author: heidist
ms.openlocfilehash: bf82899b4317f0f5ce0f6ca5096dccef7cddd931
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349400"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-api"></a>Analiza tekstu interfejsu API — często zadawane pytania (FAQ)

 Odpowiedzi na często zadawane pytania dotyczące pojęć, kodu i scenariusze związane z interfejsu API z analizy tekstu kognitywnych usług Microsoft Azure.

## <a name="can-text-analytics-identify-sarcasm"></a>Analiza tekstu zidentyfikuje sarcasm?

Analiza jest dodatnie i ujemne wskaźniki nastrojów klientów, a nie w trybie wykrywania.

Zawsze jest pewien stopień błędu analizę wskaźniki nastrojów klientów, ale modelu jest najbardziej przydatne, gdy nie jest ukryty znaczenie ani subtext do zawartości. Irony, sarcasm humor i podobnie nuanced zawartości zależne od kultury kontekstu i norm w celu przedstawienia celem. Ten typ zawartości jest między najbardziej trudne do analizy. Zazwyczaj największy rozbieżność między wynik danej utworzonego przez analizatora i subiektywnej oceny przez człowieka jest zawartości przy nuanced znaczenie.

## <a name="can-i-add-my-own-training-data-or-models"></a>Można dodać własne dane szkoleniowe i modelach?

Nie, są pretrained modeli. Tylko operacje dostępne dla przekazywane dane są oceniania, wyodrębniania klucza frazy i wykrywania języka. Firma Microsoft nie obsługują niestandardowych modeli. Jeśli chcesz utworzyć i obsługiwać modele uczenia niestandardowych maszyny, należy wziąć pod uwagę [machine learning możliwości w programie Microsoft R Server](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Można zażądać dodatkowych języków?

Wskaźniki nastrojów klientów, analizy i wyodrębniania klucza frazy są dostępne dla [wybierz liczbę języków](text-analytics-supported-languages.md). Przetwarzanie języka naturalnego jest złożone i wymaga znacznej testowanie przed może być zwolnione nowych funkcji. Z tego powodu możemy uniknąć wstępnie o pomocy technicznej, aby nikt nie ma zależności na temat funkcji, który wymaga więcej czasu na dla dorosłych. 

W celu ułatwienia określenia priorytetu języki do pracy w następnej kolejności, głosowania dla określonych języków [User Voice](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Dlaczego wyodrębniania klucza wyrażenie zwraca część wyrazów, a innych nie?

Wyodrębniania klucza frazy eliminuje nieistotne słów i określeniem autonomicznych. Kombinacje przyjęcie jednolitej, takie jak "znaczny widoków" lub "mglisty pogody" zwracane są ze sobą.

Ogólnie rzecz biorąc dane wyjściowe składa się z rzeczowniki i obiektów zdania. Dane wyjściowe to wymienione w kolejności znaczenie, z pierwszym wyrażeniem są najważniejsze. Znaczenie jest podawana przez ile razy jest wymieniony w szczególności koncepcji lub relacji tego elementu do innych elementów w tekście.

## <a name="why-does-output-vary-given-identical-inputs"></a>Dlaczego czy dane wyjściowe różnią się od, biorąc pod uwagę identyczne dane wejściowe?

Ulepszenia modeli i algorytmy są ogłaszane Jeśli zmiana występuje główne lub ciche nośnika w ramach usługi aktualizacji pomocniczych. W czasie może być stwierdzisz, że takie same wyniki wejściowego tekstu w wyniku różnych wskaźniki nastrojów klientów lub wyjście hasło klucza. Jest to normalne i zamierzone konsekwencją w chmurze przy użyciu materiałów szkoleniowych zarządzanego komputera.

## <a name="next-steps"></a>Kolejne kroki

To pytanie dotyczące funkcji lub funkcjonalności? Należy wziąć pod uwagę żądania lub głosu dla niego naszych [witryny sieci web UserVoice](https://cognitive.uservoice.com/forums/555922-text-analytics).

## <a name="see-also"></a>Zobacz także

 [StackOverflow: Analiza tekstu interfejs API](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: Usługi kognitywnych](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
