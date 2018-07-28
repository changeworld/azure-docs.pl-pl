---
title: Rozpoznawanie mowy za pomocą mowy zestawu SDK dla języka C++
titleSuffix: Microsoft Cognitive Services
description: >
  Ilustruje różne sposoby rozpoznawanie mowy (z pliku z mikrofonu, z modelem dostosowane, ciągle lub jednorazowej) przy użyciu zestaw SDK rozpoznawania mowy, języka C++.
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 27108285cff5081fdf31cfdc7e1d24ae1dda66d3
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331490"
---
# <a name="recognize-speech-by-using-the-speech-sdk-for-c"></a>Rozpoznawanie mowy za pomocą mowy zestawu SDK dla języka C++

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

[!include[Intro](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-intro.md)]

[!include[Intro - top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#toplevel)]

[!include[Intro - using microphone](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-microphone.md)]

[!code-cpp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#SpeechRecognitionWithMicrophone)]

[!include[Intro - customized](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-customized.md)]

[!code-cpp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#SpeechRecognitionUsingCustomizedModel)]

[!include[Intro - continuous file](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-continuous.md)]

[!code-cpp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#SpeechContinuousRecognitionWithFile)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj kodu w tym artykule w `samples/cpp/windows/console` folderu.

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoznawanie intencji z wypowiedzi](how-to-recognize-intents-from-speech-cpp.md)
- [Tłumaczenie mowy](how-to-translate-speech-cpp.md)

