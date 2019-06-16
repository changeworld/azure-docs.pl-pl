---
title: Stream skompresować kodera-dekodera audio z zestawem SDK mowy — usługi mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przesyłanie strumieniowe audio skompresowany do usług przetwarzania mowy platformy Azure przy użyciu zestawu SDK rozpoznawania mowy. Dostępne dla języka C++, C#i Java dla systemu Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: amishu
ms.openlocfilehash: 8717dad27786450e4275c9fd91523c0e9379f375
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073062"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Przy użyciu kodera-dekodera kompresji wejścia audio z zestawem SDK rozpoznawania mowy

Zestaw SDK rozpoznawania mowy **Stream dane wejściowe dźwięk Compressed** interfejs API umożliwia przesyłanie strumieniowe audio skompresowany do usługi rozpoznawania mowy za pomocą PullStream lub PushStream.

> [!IMPORTANT]
> Strumieniowe przesyłanie audio skompresowanym jest obsługiwana tylko w przypadku C++, C#i Java w systemie Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9).
> Mowy zestawu SDK w wersji 1.4.0 lub nowszy jest wymagany.

Liniowy format wav/PCM znajduje się w dokumentacji mowy linii głównej.  Poza wav PCM obsługiwane są następujące formaty danych wejściowych kodera-dekodera kompresji:

- MP3
- DZIELE/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>Wymagania wstępne dotyczące przy użyciu kodera-dekodera kompresji wejścia audio

Zainstaluj te dodatkowe zależności, aby używać skompresowany wejścia audio z zestawem SDK rozpoznawania mowy w systemie Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Przykładowy kod przy użyciu kodera-dekodera kompresji wejścia audio

Aby przesyłać strumieniowo w formacie skompresowanym audio do usług przetwarzania mowy, tworzenie `PullAudioInputStream` lub `PushAudioInputStream`. Następnie utwórz `AudioConfig` z wystąpienia klasy strumienia, określając format kompresji strumienia.

Załóżmy, że masz klasę strumienia wejściowego o nazwie `myPushStream` i korzystania z DZIELE/OGG. Twój kod może wyglądać następująco:

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

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Zobacz, jak rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
