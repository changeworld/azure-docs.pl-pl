---
title: Dźwięk Stream skompresowane przy użyciu zestawu SDK mowy — usługi mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przesyłanie strumieniowe audio skompresowany do usług przetwarzania mowy platformy Azure przy użyciu zestawu SDK rozpoznawania mowy. Dostępne dla języka C++, C#i Java dla systemu Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 2066dc3e20ab9fc92b23fd071728ea6a920d3324
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60697683"
---
# <a name="stream-compressed-audio-with-the-speech-sdk"></a>Dźwięk Stream skompresowane przy użyciu zestawu SDK rozpoznawania mowy

Zestaw SDK rozpoznawania mowy **Stream dane wejściowe dźwięk Compressed** interfejs API umożliwia przesyłanie strumieniowe audio skompresowany do usługi rozpoznawania mowy za pomocą PullStream lub PushStream.

> [!IMPORTANT]
> Strumieniowe przesyłanie audio skompresowany jest obsługiwana tylko w języku C++, C#i Java w systemie Linux (Ubuntu 16.04 lub Ubuntu 18.04).
> Obsługa jest ograniczona do MP3 oraz DZIELE/OGG.

## <a name="prerequisites"></a>Wymagania wstępne

Należy zainstalować te zależności, aby używać skompresowany wejścia audio z zestawem SDK rozpoznawania mowy w systemie Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="streaming-compressed-audio"></a>Strumieniowe przesyłanie audio skompresowane

Aby przesyłać strumieniowo w formacie skompresowanym audio do usług przetwarzania mowy, tworzenie `PullAudioInputStream` lub `PushAudioInputStream`. Następnie utwórz `AudioConfig` z wystąpienia klasy strumienia, określając format kompresji strumienia.

Załóżmy, że masz klasę strumienia wejściowego o nazwie `myPushStream` i korzystania z DZIELE/OGG. Jest to, jak może wyglądać kod: 

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

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
