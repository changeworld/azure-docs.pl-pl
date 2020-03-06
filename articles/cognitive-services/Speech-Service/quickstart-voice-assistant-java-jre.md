---
title: 'Szybki Start: niestandardowy asystent głosowy dla języka Java (Windows, Linux) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start dowiesz się, jak używać zestawu SDK mowy Cognitive Services w aplikacji konsolowej Java. Dowiesz się, jak połączyć aplikację kliencką z wcześniej utworzonym bot Framework bot skonfigurowanym do korzystania z kanału mowy linii Direct i włączyć asystenta głosowego.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 6baa98d50c50146e93b4832053f63f3bead90a6d
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330837"
---
# <a name="quickstart-create-a-voice-assistant-with-the-speech-sdk-java-preview"></a>Szybki Start: Tworzenie asystenta głosowego za pomocą zestawu Speech SDK, Java (wersja zapoznawcza)

Przewodniki Szybki Start są również dostępne w przypadku [zamiany mowy na tekst](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre), zamiany [tekstu na mowę](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=jre)i [tłumaczenia mowy](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre).

W tym artykule opisano tworzenie aplikacji konsolowej Java za pomocą [zestawu Azure Cognitive Services Speech SDK](speech-sdk.md). Aplikacja nawiązuje połączenie z wcześniej utworzonym bot skonfigurowanym do używania kanału bezpośredniej linii mowy, wysyła żądanie głosowe i zwraca działanie odpowiedzi głosowej (jeśli jest skonfigurowane). Aplikacja została skompilowana przy użyciu pakietu Maven SDK mowy i środowiska IDE języka Java w systemie Windows, Linux lub macOS. Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

- System operacyjny: Windows (64-bitowy), Ubuntu Linux 16.04/18.04 (64-bitowy), RHEL/CentOS 8 (x64) lub macOS 10,13 lub nowszy.
- [Zaćmienie IDE Java](https://www.eclipse.org/downloads/).
- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) lub [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
- Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj jeden bezpłatnie](get-started.md) lub utwórz go w [Azure Portal](https://portal.azure.com).
- Wstępnie skonfigurowany bot utworzony przy użyciu platformy bot Framework w wersji 4,2 lub nowszej. Bot musi subskrybować nowy kanał mowy linii bezpośredniej do odbierania danych wejściowych głosowych.

  > [!NOTE]
  > Zapoznaj się z [listą obsługiwanych regionów dla asystentów głosowych](regions.md#voice-assistants) i upewnij się, że zasoby zostały wdrożone w jednym z tych regionów.

Jeśli korzystasz z programu Ubuntu 16.04/18.04, upewnij się, że te zależności są zainstalowane przed rozpoczęciem przezaćmienia:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

W systemie RHEL/CentOS 8:

```sh
sudo yum update
sudo yum groupinstall "Development tools"
sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl wget
```

> [!NOTE]
> W systemie RHEL/CentOS 8 postępuj zgodnie z instrukcjami dotyczącymi [sposobu konfigurowania OpenSSL dla systemu Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

Jeśli korzystasz z systemu Windows (64-bitowego), upewnij się, że zainstalowano pakiet redystrybucyjny Microsoft Visual C++ dla swojej platformy:

- [Pobierz pakiet Microsoft Visual C++ Redistributable dla programu Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Opcjonalne: szybkie rozpoczynanie pracy

W tym przewodniku szybki start opisano krok po kroku, jak utworzyć prostą aplikację kliencką do łączenia się z botem z obsługą mowy. Jeśli chcesz szczegółowe bezpośrednio w programie, kompletny, gotowy do skompilowania kod źródłowy użyty w tym przewodniku Szybki Start jest dostępny w [przykładach zestawu Speech SDK](https://aka.ms/csspeech/samples) w folderze `quickstart`.

## <a name="create-and-configure-project"></a>Tworzenie i konfigurowanie projektu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Ponadto aby włączyć rejestrowanie, zaktualizuj plik _pliku pom. XML_ w celu uwzględnienia następujących zależności:

```xml
 <dependency>
     <groupId>org.slf4j</groupId>
     <artifactId>slf4j-simple</artifactId>
     <version>1.7.5</version>
 </dependency>
```

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Aby dodać nową pustą klasę do projektu języka Java, wybierz kolejno pozycje **Plik** > **Nowy** > **Klasa**.

1. W **nowym oknie klasy Java** wprowadź _speechsdk. szybkiego startu_ w polu **pakiet** i _Main_ w polu **Nazwa** .

   ![Zrzut ekranu okna Nowa klasa Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Otwórz nowo utworzoną klasę `Main` i Zastąp zawartość pliku `Main.java` następującym kodem początkowym:

   ```java
   package speechsdk.quickstart;

   import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
   import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
   import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
   import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;

   import javax.sound.sampled.AudioFormat;
   import javax.sound.sampled.AudioSystem;
   import javax.sound.sampled.DataLine;
   import javax.sound.sampled.SourceDataLine;
   import java.io.InputStream;

   public class Main {
       final Logger log = LoggerFactory.getLogger(Main.class);

       public static void main(String[] args) {
           // New code will go here
       }

       private void playAudioStream(PullAudioOutputStream audio) {
           ActivityAudioStream stream = new ActivityAudioStream(audio);
           final ActivityAudioStream.ActivityAudioFormat audioFormat = stream.getActivityAudioFormat();
           final AudioFormat format = new AudioFormat(
                   AudioFormat.Encoding.PCM_SIGNED,
                   audioFormat.getSamplesPerSecond(),
                   audioFormat.getBitsPerSample(),
                   audioFormat.getChannels(),
                   audioFormat.getFrameSize(),
                   audioFormat.getSamplesPerSecond(),
                   false);
           try {
               int bufferSize = format.getFrameSize();
               final byte[] data = new byte[bufferSize];

               SourceDataLine.Info info = new DataLine.Info(SourceDataLine.class, format);
               SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
               line.open(format);

               if (line != null) {
                   line.start();
                   int nBytesRead = 0;
                   while (nBytesRead != -1) {
                       nBytesRead = stream.read(data);
                       if (nBytesRead != -1) {
                           line.write(data, 0, nBytesRead);
                       }
                   }
                   line.drain();
                   line.stop();
                   line.close();
               }
               stream.close();

           } catch (Exception e) {
               e.printStackTrace();
           }
       }

   }
   ```

1. W metodzie `main` należy najpierw skonfigurować `DialogServiceConfig` i użyć jej do utworzenia wystąpienia `DialogServiceConnector`. To wystąpienie nawiązuje połączenie z kanałem mowy linii bezpośredniej w celu korzystania z bot. Wystąpienie `AudioConfig` jest również używane do określania źródła danych wejściowych audio. W tym przykładzie domyślny mikrofon jest używany z `AudioConfig.fromDefaultMicrophoneInput()`.

   - Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji, który można uzyskać z [tej witryny sieci Web](get-started.md).
   - Zastąp ciąg `YourServiceRegion`m [regionem](regions.md) skojarzonym z subskrypcją.

   > [!NOTE]
   > Zapoznaj się z [listą obsługiwanych regionów dla asystentów głosowych](regions.md#voice-assistants) i upewnij się, że zasoby zostały wdrożone w jednym z tych regionów.

   ```java
   final String subscriptionKey = "YourSubscriptionKey"; // Your subscription key
   final String region = "YourServiceRegion"; // Your speech subscription service region
   final BotFrameworkConfig botConfig = BotFrameworkConfig.fromSubscription(subscriptionKey, region);

   // Configure audio input from a microphone.
   final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

   // Create a DialogServiceConnector instance.
   final DialogServiceConnector connector = new DialogServiceConnector(botConfig, audioConfig);
   ```

1. Łącznik `DialogServiceConnector` opiera się na kilku zdarzeniach, aby komunikować swoje działania bot, wyniki rozpoznawania mowy i inne informacje. Dodaj te detektory zdarzeń dalej.

   ```java
   // Recognizing will provide the intermediate recognized text while an audio stream is being processed.
   connector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
       log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
   });

   // Recognized will provide the final recognized text once audio capture is completed.
   connector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
       log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
   });

   // SessionStarted will notify when audio begins flowing to the service for a turn.
   connector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
       log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
   });

   // SessionStopped will notify when a turn is complete and it's safe to begin listening again.
   connector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
       log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
   });

   // Canceled will be signaled when a turn is aborted or experiences an error condition.
   connector.canceled.addEventListener((o, canceledEventArgs) -> {
       log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
       connector.disconnectAsync();
   });

   // ActivityReceived is the main way your bot will communicate with the client and uses Bot Framework activities.
   connector.activityReceived.addEventListener((o, activityEventArgs) -> {
       final String act = activityEventArgs.getActivity().serialize();
           log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
           if (activityEventArgs.hasAudio()) {
               playAudioStream(activityEventArgs.getAudio());
           }
       });
   ```

1. Połącz `DialogServiceConnector`, aby skierować mowę liniową przez wywołanie metody `connectAsync()`. Aby przetestować bot, możesz wywołać metodę `listenOnceAsync`, aby wysyłać dane wejściowe audio z mikrofonu. Ponadto można również użyć metody `sendActivityAsync`, aby wysłać działanie niestandardowe jako ciąg serializowany. Te niestandardowe działania mogą zapewnić dodatkowe dane używane przez bot w konwersacji.

   ```java
   connector.connectAsync();
   // Start listening.
   System.out.println("Say something ...");
   connector.listenOnceAsync();

   // connector.sendActivityAsync(...)
   ```

1. Zapisz zmiany w pliku `Main`.

1. Aby obsłużyć odtwarzanie odpowiedzi, należy dodać dodatkową klasę, która przekształca obiekt PullAudioOutputStream zwrócony z interfejsu API getaudio () na InputStream języka Java w celu ułatwienia obsługi. Ta `ActivityAudioStream` jest wyspecjalizowaną klasą, która obsługuje odpowiedź audio z kanału Direct Speech line. Zapewnia metody dostępu do pobierania informacji o formacie audio, które są wymagane do obsługi odtwarzania. W tym celu wybierz pozycję **plik** > **nowej** **klasy** > .

1. W **nowym oknie klasy Java** wprowadź _speechsdk. szybkiego startu_ w polu **pakiet** i _ActivityAudioStream_ w polu **Nazwa** .

1. Otwórz nowo utworzoną klasę `ActivityAudioStream` i Zastąp ją następującym kodem:

   ```java
   package com.speechsdk.quickstart;

   import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;

   import java.io.IOException;
   import java.io.InputStream;

    public final class ActivityAudioStream extends InputStream {
        /**
         * The number of samples played per second (16 kHz).
         */
        public static final long SAMPLE_RATE = 16000;
        /**
         * The number of bits in each sample of a sound that has this format (16 bits).
         */
        public static final int BITS_PER_SECOND = 16;
        /**
         * The number of audio channels in this format (1 for mono).
         */
        public static final int CHANNELS = 1;
        /**
         * The number of bytes in each frame of a sound that has this format (2).
         */
        public static final int FRAME_SIZE = 2;

        /**
         * Reads up to a specified maximum number of bytes of data from the audio
         * stream, putting them into the given byte array.
         *
         * @param b   the buffer into which the data is read
         * @param off the offset, from the beginning of array <code>b</code>, at which
         *            the data will be written
         * @param len the maximum number of bytes to read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b, int off, int len) {
            byte[] tempBuffer = new byte[len];
            int n = (int) this.pullStreamImpl.read(tempBuffer);
            for (int i = 0; i < n; i++) {
                if (off + i > b.length) {
                    throw new ArrayIndexOutOfBoundsException(b.length);
                }
                b[off + i] = tempBuffer[i];
            }
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Reads the next byte of data from the activity audio stream if available.
         *
         * @return the next byte of data, or -1 if the end of the stream is reached
         * @see #read(byte[], int, int)
         * @see #read(byte[])
         * @see #available
         * <p>
         */
        @Override
        public int read() {
            byte[] data = new byte[1];
            int temp = read(data);
            if (temp <= 0) {
                // we have a weird situation if read(byte[]) returns 0!
                return -1;
            }
            return data[0] & 0xFF;
        }

        /**
         * Reads up to a specified maximum number of bytes of data from the activity audio stream,
         * putting them into the given byte array.
         *
         * @param b the buffer into which the data is read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b) {
            int n = (int) pullStreamImpl.read(b);
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Skips over and discards a specified number of bytes from this
         * audio input stream.
         *
         * @param n the requested number of bytes to be skipped
         * @return the actual number of bytes skipped
         * @throws IOException if an input or output error occurs
         * @see #read
         * @see #available
         */
        @Override
        public long skip(long n) {
            if (n <= 0) {
                return 0;
            }
            if (n <= Integer.MAX_VALUE) {
                byte[] tempBuffer = new byte[(int) n];
                return read(tempBuffer);
            }
            long count = 0;
            for (long i = n; i > 0; i -= Integer.MAX_VALUE) {
                int size = (int) Math.min(Integer.MAX_VALUE, i);
                byte[] tempBuffer = new byte[size];
                count += read(tempBuffer);
            }
            return count;
        }

        /**
         * Closes this audio input stream and releases any system resources associated
         * with the stream.
         */
        @Override
        public void close() {
            this.pullStreamImpl.close();
        }

        /**
         * Fetch the audio format for the ActivityAudioStream. The ActivityAudioFormat defines the sample rate, bits per sample, and the # channels.
         *
         * @return instance of the ActivityAudioFormat associated with the stream
         */
        public ActivityAudioStream.ActivityAudioFormat getActivityAudioFormat() {
            return activityAudioFormat;
        }

        /**
         * Returns the maximum number of bytes that can be read (or skipped over) from this
         * audio input stream without blocking.
         *
         * @return the number of bytes that can be read from this audio input stream without blocking.
         * As this implementation does not buffer, this will be defaulted to 0
         */
        @Override
        public int available() {
            return 0;
        }

        public ActivityAudioStream(final PullAudioOutputStream stream) {
            pullStreamImpl = stream;
            this.activityAudioFormat = new ActivityAudioStream.ActivityAudioFormat(SAMPLE_RATE, BITS_PER_SECOND, CHANNELS, FRAME_SIZE, AudioEncoding.PCM_SIGNED);
        }

        private PullAudioOutputStream pullStreamImpl;

        private ActivityAudioFormat activityAudioFormat;

        /**
         * ActivityAudioFormat is an internal format which contains metadata regarding the type of arrangement of
         * audio bits in this activity audio stream.
         */
        static class ActivityAudioFormat {

            private long samplesPerSecond;
            private int bitsPerSample;
            private int channels;
            private int frameSize;
            private AudioEncoding encoding;

            public ActivityAudioFormat(long samplesPerSecond, int bitsPerSample, int channels, int frameSize, AudioEncoding encoding) {
                this.samplesPerSecond = samplesPerSecond;
                this.bitsPerSample = bitsPerSample;
                this.channels = channels;
                this.encoding = encoding;
                this.frameSize = frameSize;
            }

            /**
             * Fetch the number of samples played per second for the associated audio stream format.
             *
             * @return the number of samples played per second
             */
            public long getSamplesPerSecond() {
                return samplesPerSecond;
            }

            /**
             * Fetch the number of bits in each sample of a sound that has this audio stream format.
             *
             * @return the number of bits per sample
             */
            public int getBitsPerSample() {
                return bitsPerSample;
            }

            /**
             * Fetch the number of audio channels used by this audio stream format.
             *
             * @return the number of channels
             */
            public int getChannels() {
                return channels;
            }

            /**
             * Fetch the default number of bytes in a frame required by this audio stream format.
             *
             * @return the number of bytes
             */
            public int getFrameSize() {
                return frameSize;
            }

            /**
             * Fetch the audio encoding type associated with this audio stream format.
             *
             * @return the encoding associated
             */
            public AudioEncoding getEncoding() {
                return encoding;
            }
        }

        /**
         * Enum defining the types of audio encoding supported by this stream.
         */
        public enum AudioEncoding {
            PCM_SIGNED("PCM_SIGNED");

            String value;

            AudioEncoding(String value) {
                this.value = value;
            }
        }
    }

   ```

1. Zapisz zmiany w pliku `ActivityAudioStream`.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

Wybierz polecenie F11 lub wybierz polecenie **uruchom** > **Debuguj**.
W konsoli programu jest wyświetlany komunikat "Powiedz coś".
W tym momencie Porozmawiaj z angielską frazą lub zdaniem bot. Twoja Zamiana jest przekazywana do bot za pośrednictwem kanału bezpośredniej linii mowy, który jest rozpoznawany i przetwarzany przez bot. Odpowiedź jest zwracana jako działanie. Jeśli bot zwraca mowę jako odpowiedź, dźwięk jest odtwarzany przy użyciu klasy `AudioPlayer`.

![Zrzut ekranu przedstawiający dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>Następne kroki

Dodatkowe przykłady, dotyczące m.in. odczytywania mowy z pliku audio, są dostępne w serwisie GitHub.

> [!div class="nextstepaction"]
> [Tworzenie i wdrażanie podstawowego bota](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Zobacz też

- [Asystenci głosu — informacje](voice-assistants.md)
- [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](get-started.md)
- [Niestandardowe słowa kluczowe](speech-devices-sdk-create-kws.md)
- [Połącz bezpośrednią mowę wiersza z bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Zapoznaj się z przykładami dla języka Java w serwisie GitHub](https://aka.ms/csspeech/samples)
