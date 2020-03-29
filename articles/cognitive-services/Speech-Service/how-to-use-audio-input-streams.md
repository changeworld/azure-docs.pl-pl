---
title: Pojęcia dotyczące strumienia wejściowego audio SDK mowy
titleSuffix: Azure Cognitive Services
description: Omówienie możliwości interfejsu API strumienia audio sdk sdk audio.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 3039276a49e7bb41660d114e78ca047a3f77f279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74109932"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Interfejs API strumienia audio SDK speech SDK

Interfejs API **strumienia audio audio** interfejsu audio SDK umożliwia strumieniowe przesyłanie dźwięku do aparatów rozpoznawania zamiast używania interfejsu API mikrofonu lub pliku wejściowego.

Podczas korzystania ze strumieni wejściowych audio wymagane są następujące kroki:

- Zidentyfikuj format strumienia audio. Format musi być obsługiwany przez SDK mowy i usługi mowy. Obecnie obsługiwana jest tylko następująca konfiguracja:

  Próbki audio w formacie PCM, jeden kanał, 16000 próbek na sekundę, 32000 bajtów na sekundę, dwa wyrównywania bloków (16 bitów z dopełniewkem dla próbki), 16 bitów na próbkę.

  Odpowiedni kod w SDK do utworzenia formatu audio wygląda następująco:

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Upewnij się, że kod może dostarczyć dane audio RAW zgodnie z tymi specyfikacjami. Jeśli dane źródłowe audio nie są zgodne z obsługiwanymi formatami, dźwięk musi zostać przekodowany do wymaganego formatu.

- Utwórz własną klasę strumienia `PullAudioInputStreamCallback`wejściowego audio pochodzącą z pliku . Zaimplementuj elementy członkowskie `Read()` i `Close()`. Dokładna sygnatura funkcji jest zależna od języka, ale kod będzie wyglądać podobnie do tego przykładu kodu:

  ```csharp
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- Utwórz konfigurację audio na podstawie formatu audio i strumienia wejściowego. Podczas tworzenia aparatu rozpoznawania należy przechodzić zarówno w konfiguracji mowy regularnej, jak i w konfiguracji wprowadzania dźwięku. Przykład:

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Następne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Zobacz, jak rozpoznać mowę w języku C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
