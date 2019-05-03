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
ms.date: 05/02/2019
ms.author: bidishac
ms.openlocfilehash: 4e9010bed54d0b2a7cb1a95b9e01e5ba02ea9fd5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027019"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>Szybki start: Utwórz Asystenta ustawień wirtualnego pierwszy głosu przy użyciu zestawu SDK rozpoznawania mowy, języka Java

W tym artykule, utworzysz aplikację konsolową w języku Java za pomocą [zestawem SDK mowy usług Cognitive](speech-sdk.md). Aplikacja połączy się z wcześniej utworzone robota skonfigurowany do używania kanału Direct mowy wiersza, Wyślij żądanie głosu i zwracać działania odpowiedzi głosowych (jeśli jest skonfigurowane). Aplikacja jest skompilowana przy użyciu pakietu Maven zestaw SDK rozpoznawania mowy i języka Java środowisku IDE programu Eclipse w systemie macOS lub Windows, Ubuntu Linux. Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* System operacyjny: Windows (64-bitowy), 16.04/18.04 Ubuntu Linux (64-bitowa) lub z systemem macOS 10.13 lub nowszy
* [Zintegrowane środowisko projektowe Eclipse Java](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) lub [zestaw JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).
* Wstępnie skonfigurowane robota utworzone przy użyciu platformy Bot Framework w wersji 4.2 lub nowszej. Bot musi subskrybować nowy kanał "Bezpośrednie wiersza mowa" do odbierania głosowych danych wejściowych.

    > [!NOTE]
    > W wersji zapoznawczej, kanału Direct mowy wiersza obsługuje obecnie tylko **westus2** regionu.

    > [!NOTE]
    > 30-dniowej wersji próbnej dla warstwy standardowa, opisanego w warstwie cenowej [bezpłatnego wypróbowania usługi mowy](get-started.md) jest ograniczony do **westus** (nie **westus2**) i dlatego nie jest zgodny z bezpośrednich Wiersz mowy. Warstwy bezpłatna i standardowa **westus2** subskrypcje są zgodne.

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

    import java.io.IOException;
    import java.io.PipedOutputStream;
    import java.util.HashMap;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import com.microsoft.cognitiveservices.speech.ResultReason;
    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.dialog.BotConnectorConfig;
    import com.microsoft.cognitiveservices.speech.dialog.SpeechBotConnector;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }
    }
    ```

1. W **głównego** metody, najpierw skonfiguruj swoje `BotConnectorConfig` i użyć go do utworzenia `SpeechBotConnector` wystąpienia. Spowoduje to połączenie do kanału Direct line mowy do interakcji z botem. `AudioConfig` Wystąpienie umożliwia również określić źródło wejścia audio. W tym przykładzie mikrofon domyślnej jest używana z `AudioConfig.fromDefaultMicrophoneInput()`.

    * Zastąp ciąg `YourSubscriptionKey` z kluczem subskrypcji, którą można pobrać z [tutaj](get-started.md).
    * Zastąp ciąg `YourServiceRegion` z [region](regions.md) skojarzonych z Twoją subskrypcją.
    * Zastąp ciąg `YourChannelSecret` z klucz tajny kanału mowy bezpośrednie wiersza.

    > [!NOTE]
    > W wersji zapoznawczej, kanału Direct mowy wiersza obsługuje obecnie tylko **westus2** regionu.

    > [!NOTE]
    > 30-dniowej wersji próbnej dla warstwy standardowa, opisanego w warstwie cenowej [bezpłatnego wypróbowania usługi mowy](get-started.md) jest ograniczony do **westus** (nie **westus2**) i dlatego nie jest zgodny z bezpośrednich Wiersz mowy. Warstwy bezpłatna i standardowa **westus2** subskrypcje są zgodne.

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // your subscription key
    final String region = "YourServiceRegion"; // Your service region. Currently assumed to be westus2
    final BotConnectorConfig botConnectorConfig = BotConnectorConfig.fromSecretKey(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a SpeechjBotConnector instance
    final SpeechBotConnector botConnector = new SpeechBotConnector(botConnectorConfig, audioConfig);
    ```

1. `SpeechBotConnector` opiera się na kilka zdarzeń do komunikowania się jego działania robota, wyniki rozpoznawania mowy i inne informacje. Następnie dodaj te detektorów zdarzeń.

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    botConnector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    botConnector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    botConnector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    botConnector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    botConnector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        botConnector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    botConnector.activityReceived.addEventListener((o, activityEventArgs) -> {
        String act = activityEventArgs.getActivity().serialize();
        log.info("Received activity: {}", act);
    });
    ```

1. Połącz `SpeechBotConnector` do bezpośredniego mowy wiersza za pomocą wywołania `connectAsync()` metody. Aby przetestować bota, można wywołać `listenOnceAsync` metodę, aby wysłać dane wejściowe audio z mikrofonu. Ponadto można również użyć `sendActivityAsync` metody do wysyłania niestandardowych działań jako ciąg serializacji. Te niestandardowe działania może zapewnić dodatkowe dane, który także Twój bot będzie używany w konwersacji.

    ```java
    botConnector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    botConnector.listenOnceAsync();

    // botConnector.sendActivityAsync(...)
    ```

1. Zapisać zmiany w `Main` pliku.

1. Do obsługi odtwarzania odpowiedzi, należy dodać dodatkowe klasy, która będzie zawierać metody narzędziowe do obsługi audio. Aby włączyć dźwięk, Dodaj nową pustą klasę do projektu języka Java: Wybierz **pliku** > **New** > **klasy**.

1. W **Nowa Klasa Java** oknie wprowadź **speechsdk.quickstart** do **pakietu** pola i **AudioPlayer** do **Nazwa** pola.

   ![Zrzut ekranu okna Nowa klasa Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Otwórz nowo utworzony **AudioPlayer** klasy, a następnie zastąpić za pomocą kodu podanego poniżej.

    ```java
    import static javax.sound.sampled.AudioFormat.Encoding.PCM_SIGNED;

    import java.io.InputStream;
    import java.io.PipedInputStream;
    import java.io.PipedOutputStream;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    import java.util.concurrent.atomic.AtomicBoolean;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;


    public class AudioPlayer {

        public static final int SAMPLE_RATE = 16000; // 16Hz sampling rate
        public static final int SAMPLE_SIZE_IN_BITS = 16; // 16 bit PCM
        public static final int CHANNELS = 1; // Use Mono / Single channel

        public static final int FRAME_RATE = 16000;
        public static final int FRAME_SIZE = 2;

        private static final Logger log = LoggerFactory.getLogger(AudioPlayer.class);
        private AtomicBoolean isPlaying = new AtomicBoolean(false);
        private ExecutorService executorService = Executors.newSingleThreadExecutor();

        public boolean isPlaying() {
            return isPlaying.get();
        }

        public void stopPlaying() {
            isPlaying.set(false);
        }

        public void play(final PipedOutputStream pipedOutputStream) {
            // The current audio supported by the Microsoft Bot framework ~ 16-bit PCM encoding, 16KHz sampling rate.
            final AudioFormat defaultFormat = new AudioFormat(PCM_SIGNED, SAMPLE_RATE, SAMPLE_SIZE_IN_BITS, CHANNELS, FRAME_SIZE, FRAME_RATE, false);
            try {
                final PipedInputStream inputStream = new PipedInputStream(pipedOutputStream);

                executorService.submit(() -> {
                    try {
                        isPlaying.set(true);
                        play(inputStream, defaultFormat);
                        inputStream.close();
                    } catch (Exception e) {
                        log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
                    }
                });
            } catch (Exception e) {
                log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
            }
        }

        private void play(final InputStream inputStream, final AudioFormat targetFormat) throws Exception {
            final byte[] buffer = new byte[1024];
            final DataLine.Info info = new DataLine.Info(SourceDataLine.class, targetFormat);
            final SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
            line.open();
            if (line != null) {
                line.start();
                int bytesRead = 0;
                while (bytesRead != -1) {
                    bytesRead = inputStream.read(buffer, 0, buffer.length);
                    if (bytesRead != -1) {
                        line.write(buffer, 0, bytesRead);
                    }
                }
                line.drain();
                line.stop();
                line.close();
            }
        }
    }
    ```

1. Zapisać zmiany w `AudioPlayer` pliku.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

Naciśnij klawisz F11 lub wybierz kolejno pozycje **Uruchom** > **Debugowanie**.
Na konsoli zostanie wyświetlony komunikat "Załóżmy, że coś, co" w tym momencie, mogą mówić frazę w języku angielskim lub zdanie, które będzie zrozumiałe dla bota. Mowa będzie przekazywane bota za pośrednictwem kanału Direct mowy wiersza, gdzie go zostanie rozpoznany, przetwarzane przez bota i odpowiedzi, które zostaną zwrócone jako działania. Jeśli Twój bot zwraca mowy jako odpowiedzi, będą odtwarzane audio ponownie za pomocą `AudioPlayer` klasy.

![Zrzut ekranu przedstawiający dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Kolejne kroki

Dodatkowe przykłady, dotyczące m.in. odczytywania mowy z pliku audio, są dostępne w serwisie GitHub.

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka Java w serwisie GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Szybki start: tłumaczenie mowy, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
