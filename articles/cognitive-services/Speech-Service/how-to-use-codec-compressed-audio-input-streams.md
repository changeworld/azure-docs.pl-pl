---
title: Koder-dekoder audio skompresowany dźwięk przy użyciu zestawu mowy SDK-Speech Service
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przesyłać strumieniowo skompresowane audio do usługi Azure Speech Services przy użyciu zestawu Speech SDK. Dostępne dla C++systemów C#, i Java dla systemu Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: b29b42dea9522526d49c1bda017a522855946def
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559557"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Używanie kodera-dekoder skompresowanego audio z zestawem Speech SDK

Interfejs API **strumienia danych wejściowych audio** zestawu Speech SDK umożliwia przesyłanie strumieniowo skompresowanego dźwięku do usługi mowy przy użyciu PullStream lub PushStream.

> [!IMPORTANT]
> Przesyłane strumieniowo skompresowane audio jest obsługiwane C++tylko C#dla systemów, i Java w systemie Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9).
> Wymagany jest pakiet Speech SDK w wersji 1.4.0 lub nowszej.

W przypadku WAV/PCM zapoznaj się z dokumentacją mowy linii głównej.  Na zewnątrz WAV/PCM obsługiwane są następujące skompresowane formaty danych wejściowych kodera-dekoder:

- MP3
- OPUS/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>Wymagania wstępne dotyczące korzystania ze skompresowanego wejścia audio kodera-dekoder

Zainstaluj te dodatkowe zależności, aby użyć skompresowanego wejścia audio przy użyciu zestawu Speech SDK dla systemu Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Przykładowy kod przy użyciu kodera skompresowanego sygnału audio

Aby przesłać strumieniowo w skompresowanym formacie audio do usługi mowy, `PullAudioInputStream` Utwórz `PushAudioInputStream`lub. Następnie Utwórz obiekt `AudioConfig` z wystąpienia klasy strumienia, określając format kompresji strumienia.

Załóżmy, że masz klasę strumienia wejściowego o nazwie `myPushStream` i używają Opus/OGG. Twój kod może wyglądać następująco:

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

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Zobacz, jak rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
