---
title: 'Szybki Start: niestandardowy głos — pierwszy asystent wirtualny (wersja zapoznawcza), Java (Android) — Speech Service'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć głosową aplikację asystenta wirtualnego w języku Java w systemie Android przy użyciu zestawu Speech SDK
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: f5b8623c835b4188d098ab2acf842abef30138e9
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71800053"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-in-java-on-android-by-using-the-speech-sdk"></a>Szybki Start: tworzenie wirtualnego asystenta głosowego w języku Java w systemie Android przy użyciu zestawu Speech SDK

Przewodnik Szybki Start jest również dostępny dla [zamiany mowy na tekst](quickstart-java-android.md) i [zamiany tekstu na mowę](quickstart-text-to-speech-java-android.md).

W tym artykule opisano tworzenie wirtualnego asystenta głosowego przy użyciu języka Java dla systemu Android przy użyciu [zestawu Speech SDK](speech-sdk.md). Ta aplikacja będzie łączyć się z bot, który został już utworzony i skonfigurowany za pomocą [kanału bezpośredniej linii mowy](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech). Następnie wyśle żądanie głosowe do bot i zaprezentowanie działania odpowiedzi z obsługą głosu.

Ta aplikacja jest zbudowana z pakietem Maven zestawu mowy SDK i Android Studio 3,3. Zestaw Speech SDK jest obecnie zgodny z urządzeniami z systemem Android oraz 32 lub 64-bitowymi procesorami ARM i procesorami x86 lub x64 firmy Intel.

> [!NOTE]
> W przypadku zestawu Speech Devices SDK i urządzenia Roobo zobacz [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji platformy Azure dla usługi Speech Services. [Uzyskaj jeden bezpłatnie](get-started.md) lub utwórz go na [Azure Portal](https://portal.azure.com).
* Wcześniej utworzono bot skonfigurowany za pomocą [kanału Direct line](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
* [Android Studio](https://developer.android.com/studio/) v 3.3 lub nowszy

    > [!NOTE]
    > Bezpośredni wiersz mowy (wersja zapoznawcza) jest obecnie dostępny w podzestawie regionów usługi Speech Services. Zapoznaj się z [listą obsługiwanych regionów dla wirtualnych asystentów głosowych](regions.md#Voice-first virtual assistants) i upewnij się, że zasoby są wdrożone w jednym z tych regionów.

## <a name="create-and-configure-a-project"></a>Tworzenie i konfigurowanie projektu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Tworzenie interfejsu użytkownika

W tej sekcji utworzymy podstawowy interfejs użytkownika dla aplikacji. Zacznijmy od otwarcia głównego działania: `activity_main.xml`. Szablon podstawowy zawiera pasek tytułu z nazwą aplikacji oraz `TextView` z komunikatem "Hello World!".

Następnie zastąp zawartość `activity_main.xml` następującym kodem:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:onClick="onBotButtonClicked"
        android:text="Talk to your bot" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Recognition Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/recoText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="  \n(Recognition goes here)\n" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Activity Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/activityText"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical"
        android:text="  \n(Activities go here)\n" />

   </LinearLayout>
   ```

Ten plik XML definiuje prosty interfejs użytkownika do współpracy z bot.

* Element `button` inicjuje interakcję i wywołuje metodę `onBotButtonClicked` po kliknięciu.
* Element `recoText` będzie wyświetlał wyniki zamiany mowy na tekst podczas rozmowy z bot.
* Element `activityText` będzie wyświetlał ładunek JSON dla najnowszej aktywności bot Framework z bot.

Tekst i graficzna reprezentacja interfejsu użytkownika powinny teraz wyglądać następująco:

![](media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz `MainActivity.java` i Zastąp zawartość następującym kodem:

   ```java
    package samples.speech.cognitiveservices.microsoft.com;

    import android.media.AudioFormat;
    import android.media.AudioManager;
    import android.media.AudioTrack;
    import android.support.v4.app.ActivityCompat;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.text.method.ScrollingMovementMethod;
    import android.view.View;
    import android.widget.TextView;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;

    import org.json.JSONException;
    import org.json.JSONObject;

    import static android.Manifest.permission.*;

    public class MainActivity extends AppCompatActivity {
        // Replace below with your bot's own Direct Line Speech channel secret
        private static String channelSecret = "YourChannelSecret";
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region (note: only a subset of regions are currently supported)
        private static String serviceRegion = "YourSpeechServiceRegion";

        private DialogServiceConnector connector;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            TextView recoText = (TextView) this.findViewById(R.id.recoText);
            TextView activityText = (TextView) this.findViewById(R.id.activityText);
            recoText.setMovementMethod(new ScrollingMovementMethod());
            activityText.setMovementMethod(new ScrollingMovementMethod());

            // Note: we need to request permissions for audio input and network access
            int requestCode = 5; // unique code for the permission request
            ActivityCompat.requestPermissions(MainActivity.this, new String[]{RECORD_AUDIO, INTERNET}, requestCode);
        }

        public void onBotButtonClicked(View v) {
            // Recreate the DialogServiceConnector on each button press, ensuring that the existing one is closed
            if (connector != null) {
                connector.close();
                connector = null;
            }

            // Create the DialogServiceConnector from the channel and speech subscription information
            DialogServiceConfig config = DialogServiceConfig.fromBotSecret(channelSecret, speechSubscriptionKey, serviceRegion);
            connector = new DialogServiceConnector(config, AudioConfig.fromDefaultMicrophoneInput());

            // Optional step: preemptively connect to reduce first interaction latency
            connector.connectAsync();

            // Register the DialogServiceConnector's event listeners
            registerEventListeners();

            // Begin sending audio to your bot
            connector.listenOnceAsync();
        }

        private void registerEventListeners() {
            TextView recoText = (TextView) this.findViewById(R.id.recoText); // 'recoText' is the ID of your text view
            TextView activityText = (TextView) this.findViewById(R.id.activityText); // 'activityText' is the ID of your text view

            // Recognizing will provide the intermediate recognized text while an audio stream is being processed
            connector.recognizing.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognizing: " + recoArgs.getResult().getText());
            });

            // Recognized will provide the final recognized text once audio capture is completed
            connector.recognized.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognized: " + recoArgs.getResult().getText());
            });

            // SessionStarted will notify when audio begins flowing to the service for a turn
            connector.sessionStarted.addEventListener((o, sessionArgs) -> {
                recoText.setText("Listening...");
            });

            // SessionStopped will notify when a turn is complete and it's safe to begin listening again
            connector.sessionStopped.addEventListener((o, sessionArgs) -> {
            });

            // Canceled will be signaled when a turn is aborted or experiences an error condition
            connector.canceled.addEventListener((o, canceledArgs) -> {
                recoText.setText("Canceled (" + canceledArgs.getReason().toString() + ") error details: {}" + canceledArgs.getErrorDetails());
                connector.disconnectAsync();
            });

            // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
            connector.activityReceived.addEventListener((o, activityArgs) -> {
                try {
                    // Here we use JSONObject only to "pretty print" the condensed Activity JSON
                    String rawActivity = activityArgs.getActivity().serialize();
                    String formattedActivity = new JSONObject(rawActivity).toString(2);
                    activityText.setText(formattedActivity);
                } catch (JSONException e) {
                    activityText.setText("Couldn't format activity text: " + e.getMessage());
                }

                if (activityArgs.hasAudio()) {
                    // Text-to-speech audio associated with the activity is 16 kHz 16-bit mono PCM data
                    final int sampleRate = 16000;
                    int bufferSize = AudioTrack.getMinBufferSize(sampleRate, AudioFormat.CHANNEL_OUT_MONO, AudioFormat.ENCODING_PCM_16BIT);

                    AudioTrack track = new AudioTrack(
                            AudioManager.STREAM_MUSIC,
                            sampleRate,
                            AudioFormat.CHANNEL_OUT_MONO,
                            AudioFormat.ENCODING_PCM_16BIT,
                            bufferSize,
                            AudioTrack.MODE_STREAM);

                    track.play();

                    PullAudioOutputStream stream = activityArgs.getAudio();

                    // Audio is streamed as it becomes available. Play it as it arrives.
                    byte[] buffer = new byte[bufferSize];
                    long bytesRead = 0;

                    do {
                        bytesRead = stream.read(buffer);
                        track.write(buffer, 0, (int) bytesRead);
                    } while (bytesRead == bufferSize);

                    track.release();
                }
            });
        }
    }
   ```

   * Metoda `onCreate` zawiera kod, który żąda dostępu do mikrofonu i Internetu.

   * Metoda `onBotButtonClicked` oznacza, jak wspomniano wcześniej, procedurę obsługi naciskania przycisku. Naciśnięcie przycisku powoduje wyzwolenie pojedynczej interakcji ("skręt") na bot.

   * Metoda `registerEventListeners` demonstruje zdarzenia używane przez `DialogServiceConnector` i podstawową obsługę działań przychodzących.

1. W tym samym pliku Zastąp ciągi konfiguracyjne, aby były zgodne z zasobami:

    * Zastąp wartość `YourChannelSecret` własnym wpisem tajnym kanału mowy dla bot.

    * Zastąp element `YourSpeechSubscriptionKey` kluczem subskrypcji.

    * Zastąp `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją tylko podzbiór regionów usługi Speech Services jest obecnie obsługiwany w przypadku bezpośredniego rozpoznawania mowy w wierszu. Aby uzyskać więcej informacji, zobacz [regiony](regions.md#voice-first-virtual-assistants).

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Połącz urządzenie z systemem Android do komputera projektowego. Upewnij się, że na urządzeniu włączono [tryb projektowania i debugowanie USB](https://developer.android.com/studio/debug/dev-options).

1. Aby skompilować aplikację, naciśnij klawisze Ctrl + F9 lub wybierz na pasku menu pozycje **Build** > **Make Project** (Kompilacja) (Utwórz projekt).

1. Aby uruchomić aplikację, naciśnij klawisze Shift + F10 lub wybierz pozycje **Run** > **Run 'app'** (Uruchom) (Uruchom „aplikację”).

1. W oknie docelowym wdrożenia, które zostanie wyświetlone, wybierz urządzenie Android.

   ![Zrzut ekranu okna Deployment Target (Cel wdrożenia)](media/sdk/qs-java-android-12-deploy.png)

Po uruchomieniu aplikacji i jej działania kliknij przycisk, aby rozpocząć rozmowę z bot. Tekst uzyskanego pojawi się w miarę mówienia, a najnowsze działania otrzymane z bot pojawią się po odebraniu. Jeśli bot jest skonfigurowany do dostarczania mówionych odpowiedzi, funkcja zamiany mowy na tekst zostanie automatycznie przegrana.

![Zrzut ekranu aplikacji Android](media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie i wdrażanie podstawowego bota](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Zobacz także
- [Informacje o Asystencie wirtualnym w pierwszej kolejności głosowej](voice-first-virtual-assistants.md)
- [Uzyskaj bezpłatnie klucz subskrypcji usługi Speech Services](get-started.md)
- [Niestandardowe słowa wznawiania](speech-devices-sdk-create-kws.md)
- [Połącz bezpośrednią mowę wiersza z bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Zapoznaj się z przykładami dla języka Java w usłudze GitHub](https://aka.ms/csspeech/samples)
