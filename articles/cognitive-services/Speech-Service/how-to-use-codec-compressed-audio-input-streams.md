---
title: Koder-dekoder audio skompresowany dźwięk przy użyciu zestawu mowy SDK-Speech Service
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przesyłać strumieniowo skompresowane audio do usługi Azure Speech Services przy użyciu zestawu Speech SDK. Dostępne dla C++systemów C#, i Java dla systemu Linux, Java w systemach Android i w środowisku C w systemie iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 8f2896a6289ecaf4046d705da106636258cdadc5
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802241"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Używanie kodera-dekoder skompresowanego audio z zestawem Speech SDK

Interfejs API **strumienia danych wejściowych audio** zestawu Speech SDK umożliwia przesyłanie strumieniowo skompresowanego dźwięku do usługi mowy przy użyciu PullStream lub PushStream.

> [!IMPORTANT]
> Przesyłane strumieniowo skompresowane audio wejściowe jest obecnie C++obsługiwane C#dla systemów, i Java w systemie Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9). Jest ona również obsługiwana w przypadku [języka Java w systemach Android](how-to-use-codec-compressed-audio-input-streams-android.md) i [C na platformie iOS](how-to-use-codec-compressed-audio-input-streams-ios.md) .
> Wymagany jest pakiet Speech SDK w wersji 1.7.0 lub nowszej.

W przypadku WAV/PCM zapoznaj się z dokumentacją mowy linii głównej.  Na zewnątrz WAV/PCM obsługiwane są następujące skompresowane formaty danych wejściowych kodera-dekoder:

- FORMATU
- OPUS/OGG
- FLAC
- ALAW w kontenerze WAV
- MULAW w kontenerze WAV

## <a name="prerequisites"></a>Wymagania wstępne

Obsługa skompresowanego dźwięku jest implementowana przy użyciu [GStreamer](https://gstreamer.freedesktop.org). W przypadku danych binarnych GStreamer z przyczyn licencjonowania nie są kompilowane i połączone z zestawem Speech SDK. Deweloper aplikacji musi zainstalować następujące elementy w systemach 18,04, 16,04 i Debian 9, aby użyć skompresowanego audio wejściowego.

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Przykładowy kod przy użyciu kodera skompresowanego sygnału audio

Aby przesłać strumieniowo w skompresowanym formacie audio do usługi mowy, Utwórz `PullAudioInputStream` lub `PushAudioInputStream`. Następnie utwórz `AudioConfig` z wystąpienia klasy strumienia, określając format kompresji strumienia.

Załóżmy, że masz klasę strumienia wejściowego o nazwie `myPushStream` i używają OPUS/OGG. Twój kod może wyglądać następująco:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
var audioFormat = AudioStreamFormat.GetCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
var audioConfig = AudioConfig.FromStreamInput(myPushStream, audioFormat);

var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

var result = await recognizer.RecognizeOnceAsync();

var text = result.GetText();
```

## <a name="next-steps"></a>Następne kroki

- [Uzyskaj subskrypcję wersji próbnej usługi Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Zobacz, jak rozpoznać mowęC#](quickstart-csharp-dotnet-windows.md)
