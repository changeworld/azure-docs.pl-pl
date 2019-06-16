---
title: Pojęcia audio strumienia wejściowego zestawu SDK usługi mowy
titleSuffix: Azure Cognitive Services
description: Omówienie funkcji zestaw SDK rozpoznawania mowy strumienia wejściowego audio interfejsu API.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: fmegen
ms.openlocfilehash: f34504dddaa21cc162476d1fdb462e7be5c48629
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60697917"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Temat audio zestaw SDK rozpoznawania mowy danych wejściowych interfejsu API usługi stream

Zestaw SDK rozpoznawania mowy **Stream dane wejściowe dźwięk** interfejs API umożliwia przesyłanie strumieniowe strumieni audio do aparatów rozpoznawania gestów zamiast korzystać z mikrofonu lub pliku wejściowego interfejsów API.

Poniższe kroki są wymagane, gdy za pomocą audio strumienie wejściowe:

- Określ format strumienia audio. Format muszą być obsługiwane przez zestaw SDK rozpoznawania mowy i usługi rozpoznawania mowy. Aktualnie obsługiwana jest tylko następującą konfigurację:

  Próbki audio w formacie PCM, jeden kanał, 16000 próbki na sekundę, 32000 bajtów na sekundę, blok dwóch wyrównanie (16-bitowych tym dopełnienie przykład), 16 bitów na próbkę.

  Odpowiedni kod w zestawie SDK, aby utworzyć audio format wygląda następująco:

  ```
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Upewnij się, że Twój kod może zapewnić pierwotne dane audio zgodnie z tymi specyfikacjami. Jeśli Twoje źródło audio nie są zgodne z obsługiwanych formatów, audio, musi być transkodowane na wymagany format.

- Utwórz własne klasy strumienia wejściowego audio pochodną `PullAudioInputStreamCallback`. Zaimplementuj elementy członkowskie `Read()` i `Close()`. Podpis funkcji Porównaj jest zależne od języka, ale kod będzie wyglądać podobnie do tego przykładu kodu:

  ```
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

- Utwórz konfigurację adresu audio oparte na audio format i dane wejściowe strumienia. Przekaż konfigurację regularnego mowy i konfiguracja wejściowe audio podczas tworzenia usługi rozpoznawania. Na przykład:

  ```
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
