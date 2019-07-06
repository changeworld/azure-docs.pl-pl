---
title: 'Szybki start: Niestandardowe głosu pierwszego wirtualnego Asystenta ustawień (wersja zapoznawcza) języka Java (Windows, Linux) — usług przetwarzania mowy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki Start dowiesz się, jak używać Cognitive Services mowy Software Development Kit (SDK) w aplikacji konsolowej Java. Dowiesz się, jak połączyć aplikację kliencką do utworzonej wcześniej bot platformy Bot Framework skonfigurowany do używania kanału Direct mowy wiersza i włączyć środowisko głosu pierwszego wirtualnego Asystenta ustawień.
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: bidishac
ms.openlocfilehash: 78e80b276a13ee6e27fdf0515f2901fdeaa20c5d
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604924"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>Szybki start: Utwórz Asystenta ustawień wirtualnego pierwszy głosu przy użyciu zestawu SDK rozpoznawania mowy, języka Java

Przewodniki Szybki Start są również dostępne dla [mowy na tekst](quickstart-java-jre.md) i [tłumaczenia mowy](quickstart-translate-speech-java-jre.md).

W tym artykule, utworzysz aplikację konsolową w języku Java za pomocą [zestawem SDK mowy usług Cognitive](speech-sdk.md). Aplikacja połączy się z wcześniej utworzone robota skonfigurowany do używania kanału Direct mowy wiersza, Wyślij żądanie głosu i zwracać działania odpowiedzi głosowych (jeśli jest skonfigurowane). Aplikacja jest skompilowana przy użyciu pakietu Maven zestaw SDK rozpoznawania mowy i języka Java środowisku IDE programu Eclipse w systemie macOS lub Windows, Ubuntu Linux. Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* System operacyjny: Windows (64-bitowy), 16.04/18.04 Ubuntu Linux (64-bitowa) lub z systemem macOS 10.13 lub nowszy
* [Zintegrowane środowisko projektowe Eclipse Java](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) lub [zestaw JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Klucz subskrypcji platformy Azure dla usług przetwarzania mowy. [Bezpłatnie Uzyskaj](get-started.md) lub utwórz go na [witryny Azure portal](https://portal.azure.com).
* Wstępnie skonfigurowane robota utworzone przy użyciu platformy Bot Framework w wersji 4.2 lub nowszej. Bot musi subskrybować nowy kanał "Bezpośrednie wiersza mowa" do odbierania głosowych danych wejściowych.

    > [!NOTE]
    > Bezpośrednie wiersza Speech (wersja zapoznawcza) jest obecnie dostępna w regionach usług przetwarzania mowy przez. Zapoznaj się [listę obsługiwanych regionów dla wirtualnych asystentów głosowych pierwszy](regions.md#Voice-first virtual assistants) i upewnij się, Twoje zasoby są wdrażane w jednym z tych regionów.

Jeśli korzystasz z Ubuntu 16.04/18.04, upewnij się, że te zależności są zainstalowane przed uruchomieniem programu Eclipse:

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Jeśli korzystasz z Windows (64-bitowy), upewnij się, został zainstalowany program Microsoft Visual C++ pakiet redystrybucyjny dla danej platformy:
* [Pobierz pakiet Microsoft Visual C++ Redistributable dla programu Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Opcjonalnie: Szybkie rozpoczęcie pracy

Ten przewodnik Szybki Start opisano krok po kroku, jak aplikacja kliencka proste połączyć się z botem obsługujące rozpoznawanie mowy. Jeśli chcesz od razu po prawej, jest dostępna w kodzie źródłowym kompletny, gotowy do kompilacji, używanych w tym przewodniku Szybki Start [przykładowych zestawach SDK mowy](https://aka.ms/csspeech/samples) w obszarze `quickstart` folderu.

## <a name="create-and-configure-project"></a>Tworzenie i konfigurowanie projektu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Ponadto, aby włączyć rejestrowanie, należy zaktualizować **pom.xml** pliku, aby uwzględnić następujące zależności.

   ```xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.5</version>
    </dependency>
   ```

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Aby dodać nową pustą klasę do projektu języka Java, wybierz kolejno pozycje **Plik** > **Nowy** > **Klasa**.

1. W oknie **Nowa klasa Java** wprowadź ciąg **speechsdk.quickstart** w polu **Pakiet** i **Main** w polu **Nazwa**.

   ![Zrzut ekranu okna Nowa klasa Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Otwórz nowo utworzony **Main** klasy i Zastąp zawartość `Main.java` pliku następującym kodem wyjścia.

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

1. W **głównego** metody, najpierw skonfiguruj swoje `DialogServiceConfig` i użyć go do utworzenia `DialogServiceConnector` wystąpienia. Spowoduje to połączenie do kanału Direct line mowy do interakcji z botem. `AudioConfig` Wystąpienie umożliwia również określić źródło wejścia audio. W tym przykładzie mikrofon domyślnej jest używana z `AudioConfig.fromDefaultMicrophoneInput()`.

    * Zastąp ciąg `YourSubscriptionKey` z kluczem subskrypcji, którą można pobrać z [tutaj](get-started.md).
    * Zastąp ciąg `YourServiceRegion` z [region](regions.md) skojarzonych z Twoją subskrypcją.
    * Zastąp ciąg `YourChannelSecret` z klucz tajny kanału mowy bezpośrednie wiersza.

    > [!NOTE]
    > Bezpośrednie wiersza Speech (wersja zapoznawcza) jest obecnie dostępna w regionach usług przetwarzania mowy przez. Zapoznaj się [listę obsługiwanych regionów dla wirtualnych asystentów głosowych pierwszy](regions.md#voice-first-virtual-assistants) i upewnij się, Twoje zasoby są wdrażane w jednym z tych regionów.

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // Your subscription key
    final String region = "YourServiceRegion"; // Your speech subscription service region. Note: only a subset of regions are currently supported
    final DialogServiceConfig botConfig = DialogServiceConfig.fromBotSecret(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a DialogServiceConnector instance
    final DialogServiceConnector connector = new DialogServiceConnector(botConfig, audioConfig);
    ```

1. `DialogServiceConnector` opiera się na kilka zdarzeń do komunikowania się jego działania robota, wyniki rozpoznawania mowy i inne informacje. Następnie dodaj te detektorów zdarzeń.

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    connector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    connector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    connector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    connector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        connector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
    connector.activityReceived.addEventListener((o, activityEventArgs) -> {
        final String act = activityEventArgs.getActivity().serialize();
            log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
            if (activityEventArgs.hasAudio()) {
                playAudioStream(activityEventArgs.getAudio());
            }
        });
    ```

1. Połącz `DialogServiceConnector` do bezpośredniego mowy wiersza za pomocą wywołania `connectAsync()` metody. Aby przetestować bota, można wywołać `listenOnceAsync` metodę, aby wysłać dane wejściowe audio z mikrofonu. Ponadto można również użyć `sendActivityAsync` metody do wysyłania niestandardowych działań jako ciąg serializacji. Te niestandardowe działania może zapewnić dodatkowe dane, który także Twój bot będzie używany w konwersacji.

    ```java
    connector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    connector.listenOnceAsync();

    // connector.sendActivityAsync(...)
    ```

1. Zapisać zmiany w `Main` pliku.

1. Do obsługi odtwarzania odpowiedzi, należy dodać dodatkowe klasy, który przekształci obiektu PullAudioOutputStream zwróciło getAudio() interfejsu API języka Java InputStream w celu ułatwienia obsługi. Ta ActivityAudioStream to wyspecjalizowane klasy, która będzie obsługiwać audio odpowiedzi z "kanał bezpośredniego mowy wiersza". Dostarcza metod dostępu do pobierania informacji o formacie audio wymagane do obsługi odtwarzania: W tym wybierz **pliku** > **New** > **klasy**.

1. W **Nowa Klasa Java** oknie wprowadź **speechsdk.quickstart** do **pakietu** pola i **ActivityAudioStream** do  **Nazwa** pola.

1. Otwórz nowo utworzony **ActivityAudioStream** klasy, a następnie zastąpić za pomocą kodu podanego poniżej.

    ```java
    package com.speechsdk.quickstart;

    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;

    import java.io.IOException;
    import java.io.InputStream;


    public final class ActivityAudioStream extends InputStream {
        /**
         * The number of samples played per second. (16 kHz)
         */
        public static final long SAMPLE_RATE = 16000;
        /**
         * The number of bits in each sample of a sound that has this format. (16 bits)
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
         * Reads up to a specified maximum number of bytes of data from the activity audio stream
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
         * Fetch the audio format for the ActivityAudioStream. The ActivityAudioFormat defines the sample rate, bits per sample and the # channels
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
         * As this implementation does not buffer this will be defaulted to 0
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
         * Enum defining the types of audio encoding supported by this stream
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

1. Zapisać zmiany w `ActivityAudioStream` pliku.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

Naciśnij klawisz F11 lub wybierz kolejno pozycje **Uruchom** > **Debugowanie**.
Na konsoli zostanie wyświetlony komunikat "Załóżmy, że coś, co" w tym momencie, mogą mówić frazę w języku angielskim lub zdanie, które będzie zrozumiałe dla bota. Mowa będzie przekazywane bota za pośrednictwem kanału Direct mowy wiersza, gdzie go zostanie rozpoznany, przetwarzane przez bota i odpowiedzi, które zostaną zwrócone jako działania. Jeśli Twój bot zwraca mowy jako odpowiedzi, będą odtwarzane audio ponownie za pomocą `AudioPlayer` klasy.

![Zrzut ekranu przedstawiający dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>Kolejne kroki

Dodatkowe przykłady, dotyczące m.in. odczytywania mowy z pliku audio, są dostępne w serwisie GitHub.

> [!div class="nextstepaction"]
> [Tworzenie i wdrażanie podstawowy bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Zobacz także

- [Temat wirtualnych asystentów głosowych pierwszy](voice-first-virtual-assistants.md)
- [Bezpłatnie Uzyskaj klucz subskrypcji usług przetwarzania mowy](get-started.md)
- [Wyrazy niestandardowego](speech-devices-sdk-create-kws.md)
- [Nawiązywanie bezpośredniego mowy wiersza bota](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Zapoznaj się z przykładami dla języka Java w usłudze GitHub](https://aka.ms/csspeech/samples)
