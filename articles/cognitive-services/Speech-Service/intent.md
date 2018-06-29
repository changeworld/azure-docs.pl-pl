---
title: Przykład dla opcji rozpoznawania | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Poniżej przedstawiono przykładowe rozpoznawania konwersji.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 38f7f038a803546adb83245519efc5de0c0d1599
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045014"
---
# <a name="sample-for-intent-recognition"></a>Przykładowe rozpoznawania konwersji

> [!NOTE]
> Aby uzyskać instrukcje dotyczące pobierania w tym przykładzie i innych użytkowników, zobacz [przykłady dotyczące zestawu SDK mowy](samples.md).

> [!NOTE]
> Najpierw uzyskać klucz subskrypcji. W przeciwieństwie do innych usług obsługiwanych przez zestaw SDK kognitywnych mowy usługi usługi rozpoznawania zamiar wymaga klucza określonej subskrypcji. [W tym miejscu](https://www.luis.ai) można znaleźć dodatkowe informacje na temat technologii rozpoznawania konwersji, a także informacje o sposobie pozyskania klucza subskrypcji. Zastąp klucz subskrypcji, region usługi i identyfikator AppId konwersji modelu w odpowiednim miejscu w przykładach.

> [!NOTE]
> Dla wszystkich przykłady poniżej następujące deklaracje najwyższego poziomu powinny być stosowane:
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="intent-recognition-using-microphone"></a>Rozpoznawanie konwersji przy użyciu mikrofon

Poniższy fragment kodu ilustruje sposób rozpoznawania zamiar z mikrofon wejścia w języku domyślnym (`en-US`).

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

- - -

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>Rozpoznawanie konwersji w języku określonym za pomocą mikrofon

Poniższy fragment kodu ilustruje sposób rozpoznawania zamiar z mikrofon wejścia w określonym języku, w tym przypadku w języku niemieckim (`de-de`).

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

- - -

## <a name="intent-recognition-from-a-file"></a>Rozpoznawanie konwersji z pliku

Poniższy fragment kodu rozpoznaje zamiar z pliku audio w języku domyślnym (`en-US`), obsługiwanego formatu jest jeden kanał (mono) WAV / PCM z 16 KHz próbkowania.

[!include[Sample Audio](includes/sample-audio.md)]

[!code-cpp[Intent Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithFile)]

- - -

## <a name="intent-recognition-using-events"></a>Rozpoznawanie konwersji za pomocą zdarzeń

Fragment kodu pokazano, jak rozpoznać zamiar w sposób ciągły. Ten kod umożliwia dostęp do dodatkowych informacji, takich jak pośrednich wyników. 

[!code-cpp[Intent Recognition Using Events](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>Przykładowy kod źródłowy

Dla zestawu najnowsze przykłady, zobacz [repozytorium GitHub próbki SDK kognitywnych usług mowy](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoznawanie mowy](./speech-to-text-sample.md)

- [Tłumaczenie](./translation.md)
