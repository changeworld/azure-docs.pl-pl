---
title: Pojęcia dotyczące AudioInputStream | Dokumentacja firmy Microsoft
description: Omówienie funkcji AudioInputStream interfejsu API.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: fmegen
manager: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: fmegen
ms.openlocfilehash: 0eafa7e88df5d00a67646ca7f82ca027602a40b3
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071450"
---
# <a name="about-the-audio-input-stream-api"></a>Temat audio danych wejściowych interfejsu API usługi stream

**Stream dane wejściowe dźwięk** interfejs API umożliwia przesyłanie strumieniowe strumieni audio do aparatów rozpoznawania gestów zamiast korzystać z mikrofonu lub pliku wejściowego interfejsów API.

## <a name="api-overview"></a>Przegląd interfejsu API

Interfejs API korzysta z dwóch składników `AudioInputStream` (nieprzetworzone dane audio) i `AudioInputStreamFormat`.

`AudioInputStreamFormat` Definiuje format danych audio. Można porównać ze standardem `WAVEFORMAT` struktury pliki wave na Windows.

  - `FormatTag`

    Format audio. Zestaw SDK rozpoznawania mowy aktualnie obsługuje tylko `format 1` (PCM - little-endian).

  - `Channels`

    Liczba kanałów. Bieżąca usługa mowy obsługuje tylko jeden kanał (mono) materiały audio.

  - `SamplesPerSec`

    Częstotliwość próbkowania. Nagrywanie z mikrofonu typowe ma 16000 próbki na sekundę.

  - `AvgBytesPerSec`

    Średnia liczba bajtów na sekundę, obliczany jako `SamplesPerSec * Channels * ceil(BitsPerSample, 8)`. Średnia liczba bajtów na sekundę mogą być różne dla strumieni audio, które używają zmiennej szybkości transmisji.

  - `BlockAlign`

    Rozmiar jednej ramce, obliczany jako `Channels * ceil(wBitsPerSample, 8)`. Ze względu na dopełnienie, rzeczywista wartość może być wyższy niż ta wartość.

  - `BitsPerSample`

    Bitów na próbkę. Typowe strumienia audio używa 16 bitów na próbkę (CD jakość).

`AudioInputStream` Klasy podstawowej zostaną zastąpione przez karty niestandardowej strumienia. Ta karta ma do implementacji tych funkcji:

   - `GetFormat()`

     Ta funkcja jest wywoływana, aby pobrać format strumienia audio. Pobiera wskaźnik do buforu AudioInputStreamFormat.

   - `Read()`

     Ta funkcja jest wywoływana, aby pobrać dane ze strumienia audio. Jeden parametr jest wskaźnikiem do buforu, który można skopiować danych do. Drugi parametr jest rozmiar buforu. Funkcja zwraca liczbą bajtów skopiowanych w buforze. Zwracana wartość wynosząca `0` wskazuje koniec strumienia.

   - `Close()`

     Ta funkcja jest wywoływana, aby zamknąć strumień audio.

## <a name="usage-examples"></a>Przykłady użycia

Korzystając z strumienie wejściowe audio ogólnie rzecz biorąc, obejmuje następujące kroki:

  - Określ format strumienia audio. Format muszą być obsługiwane przez zestaw SDK i usługi rozpoznawania mowy. Obecnie obsługiwane jest następująca konfiguracja:

    Tag formatu audio (PCM), jednego kanału, 16000 próbki na sekundę, 32000 bajtów na sekundę, blok dwóch align (16-bitowych tym dopełnienie przykład), 16 bitów na próbkę

  - Upewnij się, że Twój kod może zapewnić pierwotne dane audio do specyfikacji określonych powyżej. Jeśli Twoje źródło audio nie są zgodne z obsługiwanych formatów, audio, musi być transkodowane na wymagany format.

  - Pochodzi z klasy niestandardowej audio strumienia wejściowego z `AudioInputStream`. Implementowanie `GetFormat()`, `Read()`, i `Close()` operacji. Podpis funkcji Porównaj jest zależne od języka, ale kod będzie wyglądać podobnie do tego przykładu kodu::

    ```
     public class ContosoAudioStream : AudioInputStream {
        ContosoConfig config;

        public ContosoAudioStream(const ContosoConfig& config) {
            this.config = config;
        }

        public void GetFormat(AudioInputStreamFormat& format) {
            // returns format data to the caller.
            // e.g. format.FormatTag = config.XXX;
            // ...
        }

        public size_t Read(byte *buffer, size_t size) {
            // returns audio data to the caller.
            // e.g. return read(config.YYY, buffer, size);
        }

        public void Close() {
            // close and cleanup resources.
        }
     };
    ```

  - Użyj audio strumienia danych wejściowych:

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    var result = await recognizer.RecognizeAsync();

    var text = result.GetText();

    // In some languages you need to delete the stream explicitly.
    // delete contosoStream;
    ```

  - W przypadku niektórych języków `contosoStream` muszą zostać jawnie usunięte po zakończeniu rozpoznawanie. Nie można zwolnić AudioStream, zanim pełną danych wejściowych jest do odczytu. W scenariuszu za pomocą `StopContinuousRecognitionAsync` i `StopContinuousRecognitionAsync` wymaga koncepcji przedstawionych w tym przykładzie:

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    await recognizer.StartContinuousRecognitionAsync();

    // ERROR: do not delete the contosoStream before ending recognition!
    // delete contosoStream;

    await recognizer.StopContinuousRecognitionAsync();

    // OK: Safe to delete the contosoStream.
    // delete contosoStream;
    ```

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
