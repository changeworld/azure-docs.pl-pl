---
title: Przykład dla mowy na tekst | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Poniżej przedstawiono przykładowe dla mowy na tekst.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 2a1850e6a4f3c8eebd1b947aabe1374bdaab3fc8
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030254"
---
# <a name="sample-for-speech-to-text"></a>Przykładowe mowy do tekstu

> [!NOTE]
> Aby uzyskać instrukcje dotyczące pobierania w tym przykładzie i innych użytkowników, zobacz [przykłady dotyczące zestawu SDK mowy](samples.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Wszystkie przykłady poniżej, aby uzyskać następujące deklaracje najwyższego poziomu powinny być stosowane:
>
> [!code-csharp[](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#toplevel)]
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="speech-recognition-using-the-microphone"></a>Rozpoznawanie mowy przy użyciu mikrofon

Poniższy fragment kodu ilustruje sposób rozpoznawania mowy dane wejściowe mikrofon w języku domyślnym (`en-US`).

[!code-csharp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionWithMicrophone)]

[!code-cpp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionWithMicrophone)]

- - -

## <a name="speech-recognition-from-a-file"></a>Rozpoznawanie mowy z pliku

Poniższy fragment kodu rozpoznaje wejście mowy z pliku audio w języku domyślnym (`en-US`), obsługiwanego formatu jest jeden kanał (mono) WAV / PCM z 16 KHz próbkowania.

[!include[Sample Audio](includes/sample-audio.md)]

[!code-csharp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs?name=recognitionFromFile)]

[!code-cpp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp?name=SpeechRecognitionWithFile)]

- - -

## <a name="speech-recognition-using-a-customized-model"></a>Rozpoznawanie mowy przy użyciu dostosowanych modelu

[Niestandardowej usługi rozpoznawania mowy (CRI)](https://www.cris.ai/) umożliwia dostosowywania aparat rozpoznawania mowy na tekst firmy Microsoft dla aplikacji. Poniższy fragment ilustruje sposób rozpoznawania mowy z mikrofonu przy użyciu modelu CRI; Wprowadź klucz CRI subskrypcji i własny identyfikator wdrożenia przed jego uruchomieniem.

[!code-csharp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionCustomized)]

[!code-cpp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionUsingCustomizedModel)]

- - -

## <a name="continuous-speech-recognition"></a>Rozpoznawanie mowy

[!code-csharp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionContinuous)]

[!code-cpp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>Przykładowy kod źródłowy

Najnowszą wersję przykłady i jeszcze bardziej zaawansowane przykłady znajdują się w dedykowanej [repozytorium GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk).

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoznawanie intencji mowy](./intent.md)

- [Tłumaczenie](./translation.md)
