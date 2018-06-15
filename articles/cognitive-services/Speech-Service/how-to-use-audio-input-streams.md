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
ms.openlocfilehash: 528356473c4221a815fa68cbec3426866c4cbd23
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "35356292"
---
# <a name="about-the-audio-input-stream-api"></a>O wejście audio strumienia interfejsu API

**Strumienia danych wejściowych Audio** interfejsu API umożliwia strumienia strumieni audio w aparatów rozpoznawania zamiast mikrofon lub interfejsów API plików wejściowych.

## <a name="api-overview"></a>Przegląd interfejsu API

Interfejs API korzysta z dwóch składników `AudioInputStream` (nieprzetworzone dane audio) i `AudioInputStreamFormat`.

`AudioInputStreamFormat` Definiuje format danych audio. Mogą być porównywane ze standardem `WAVEFORMAT` struktury plików wave w systemie Windows.

  - `FormatTag`

    Format audio. Zestaw SDK mowy aktualnie obsługuje tylko `format 1` (PCM - little endian).

  - `Channels`

    Liczba kanałów. Bieżąca usługa mowy obsługuje tylko jeden kanał (mono) materiałów audio.

  - `SamplesPerSec`

    Częstotliwość próbkowania. Nagrywanie mikrofon typowe ma 16000 próbek na sekundę.

  - `AvgBytesPerSec`

    Średnią liczbę bajtów na sekundę, obliczany jako `SamplesPerSec * Channels * ceil(BitsPerSample, 8)`. Średnią liczbę bajtów na sekundę mogą być różne dla strumieni audio, które używają zmiennej szybkości transmisji bitów.

  - `BlockAlign`

    Rozmiar jedną ramkę w przeliczeniu na `Channels * ceil(wBitsPerSample, 8)`. Z powodu wypełnienia, rzeczywista wartość może być większa niż wartość.

  - `BitsPerSample`

    Bity na próbkę. Typowy strumieniem audio używa 16 bitów na próbkę (jakość CD).

`AudioInputStream` Klasy podstawowej zostaną przesłonięte przez Twojego adaptera niestandardowych strumieni. Ta karta ma do wykonania tych funkcji:

   - `GetFormat()`

     Ta funkcja jest wywoływana uzyskanie format strumieniem audio. Pobiera wskaźnik do buforu AudioInputStreamFormat.

   - `Read()`

     Ta funkcja jest wywoływana można pobrać danych z strumieniem audio. Jeden parametr ma postać wskaźnika do buforu można skopiować danych do. Drugi parametr jest rozmiar buforu. Funkcja zwraca liczbę bajtów kopiowania do buforu. Zwracana wartość `0` wskazuje koniec strumienia.

   - `Close()`

     Ta funkcja jest wywoływana, aby zamknąć strumieniem audio.

## <a name="usage-examples"></a>Przykłady użycia

Korzystając z strumienie wejściowe audio ogólnie rzecz biorąc, obejmuje następujące kroki:

  - Określ format strumieniem audio. Format musi obsługiwany przez zestaw SDK i usługi rozpoznawania mowy. Aktualnie obsługiwana jest następująca konfiguracja:

    Znacznik jeden format audio (PCM), jeden kanał, 16000 próbek na sekundę, 32000 bajtów na sekundę, dwa bloku Dopasuj (w tym dopełnienie przykładowe 16 bitów), 16 bitów na próbkę

  - Upewnij się, że kod może zapewnić nieprzetworzonych danych audio dotyczące specyfikacji określonych powyżej. Jeśli dane źródłowe audio nie odpowiada obsługiwanych formatów, nagrania audio musi być przekodowane w wymaganym formacie.

  - Pochodzi z klasy niestandardowej strumienia wejściowego audio `AudioInputStream`. Implementowanie `GetFormat()`, `Read()`, i `Close()` operacji. Podpis dokładne funkcja jest zależne od języka, ale kod będzie wyglądać podobnie do poniższego przykładu kodu::

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

  - Użyj strumienia wejściowego audio:

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

  - W przypadku niektórych języków `contosoStream` muszą zostać jawnie usunięte po zakończeniu uznania. Nie można zwolnić AudioStream, zanim zakończenie danych wejściowych jest do odczytu. W scenariuszu przy użyciu `StopContinuousRecognitionAsync` i `StopContinuousRecognitionAsync` wymaga koncepcji przedstawione w tym przykładzie:

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

* [Pobierz wersję próbną subskrypcji mowy](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz rozpoznawanie mowy w języku C#](quickstart-csharp-windows.md)
