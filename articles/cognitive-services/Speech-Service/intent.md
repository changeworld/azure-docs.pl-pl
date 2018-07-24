---
title: Przykład rozpoznawanie intencji | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Oto przykład rozpoznawanie intencji.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 3e9afc990d6bfa73eb045e7ed76dfd194df309c6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213207"
---
# <a name="sample-for-intent-recognition"></a>Przykład rozpoznawanie intencji

Najpierw Uzyskaj klucz subskrypcji. W przeciwieństwie do innych usług obsługiwanych przez zestaw SDK rozpoznawania mowy usługi usługi Cognitive services rozpoznawanie intencji wymaga klucza określonej subskrypcji. [W tym miejscu](https://www.luis.ai) możesz znaleźć dodatkowe informacje na temat technologii funkcja rozpoznawania celu, a także informacje o tym, jak uzyskać klucz subskrypcji. Zastąp własnym kluczem subskrypcji Language Understanding [region subskrypcji](regions.md)i AppId intencji modelu w odpowiednich miejscach w przykładach.

## <a name="top-level-declarations"></a>Deklaracje najwyższego poziomu

Wszystkie przykłady poniżej następujące deklaracje najwyższego poziomu powinny być stosowane:

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#toplevel)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#toplevel)]

## <a name="intent-recognition-using-microphone"></a>Funkcja rozpoznawania celu przy użyciu mikrofonu

Poniższy fragment kodu pokazuje, jak rozpoznawać intencję z mikrofonu wejścia w języku domyślnym (`en-US`).

[!code-csharp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

[!code-java[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithMicrophone)]

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>Funkcja rozpoznawania celu przy użyciu mikrofonu w określonym języku.

Poniższy fragment kodu pokazuje, jak rozpoznać intencje z mikrofonu wejścia w określonym języku, w tym przypadku w języku niemieckim (`de-de`).

[!code-csharp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithLanguage)]

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

[!code-java[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithLanguage)]

## <a name="intent-recognition-from-a-file-using-events"></a>Funkcja rozpoznawania celu z plikiem za pomocą zdarzeń

Fragment kodu pokazuje, jak rozpoznawać intencję w języku domyślnym (`en-US`) w sposób ciągły. Ten kod umożliwia dostęp do dodatkowych informacji, takich jak wyniki pośrednie. Dane wejściowe jest pobierana z pliku audio, obsługiwany format WAV jednego kanału (mono) / PCM z 16 KHz próbkowania.

[!code-csharp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

[!code-cpp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentContinuousRecognitionWithFile)]

[!code-java[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentContinuousRecognitionWithFile)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoznawanie mowy](./speech-to-text-sample.md)

- [Tłumaczenie](./translation.md)
