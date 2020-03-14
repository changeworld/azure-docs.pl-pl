---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: c8354a67d26210035355f4947cb5f17cee934508
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372823"
---
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie:

* Masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji — [Wypróbuj usługę mowy bezpłatnie](../../../get-started.md).

## <a name="install-and-import-the-speech-sdk"></a>Instalowanie i importowanie zestawu Speech SDK

Przed wykonaniem jakichkolwiek czynności należy zainstalować zestaw Speech SDK.

```Python
pip install azure-cognitiveservices-speech
```

Jeśli pracujesz w programie macOS i masz problemy z instalacją, może być konieczne uruchomienie tego polecenia jako pierwsze.

```Python
python3 -m pip install --upgrade pip
```

Po zainstalowaniu zestawu Speech SDK zaimportuj go do projektu języka Python za pomocą tej instrukcji.

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby wywołać usługę mowy przy użyciu zestawu Speech SDK, należy utworzyć [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python). Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, Host lub Token autoryzacji.

> [!NOTE]
> Bez względu na to, czy wykonujesz rozpoznawanie mowy, synteza mowy, tłumaczenie czy rozpoznawanie intencji, zawsze utworzysz konfigurację.

Istnieje kilka sposobów inicjowania [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python):

* Z subskrypcją: Przekaż klucz i skojarzony region.
* Z punktem końcowym: Pass w punkcie końcowym usługi mowy. Klucz lub Token autoryzacji jest opcjonalny.
* Z hostem: Przekaż adres hosta. Klucz lub Token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: Przekaż Token autoryzacji i skojarzony region.

Przyjrzyjmy się, jak [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) jest tworzony przy użyciu klucza i regionu.

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

## <a name="initialize-a-recognizer"></a>Inicjowanie aparatu rozpoznawania

Po utworzeniu [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)następnym krokiem jest zainicjowanie [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python). Po zainicjowaniu [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)musisz przekazać go do `speech_config`. Zapewnia to poświadczenia wymagane przez usługę mowy do zweryfikowania Twojego żądania.

Jeśli rozpoznajesz mowę przy użyciu domyślnego mikrofonu urządzenia, Oto [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) powinny wyglądać następująco:

```Python
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
```

Jeśli chcesz określić urządzenie wejściowe audio, musisz utworzyć [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python) i podać parametr `audio_config` podczas inicjowania [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python).

> [!TIP]
> [Dowiedz się, jak uzyskać identyfikator urządzenia dla wejściowego urządzenia audio](../../../how-to-select-audio-input-devices.md).

```Python
audio_config = AudioConfig(device_name="<device id>");
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Jeśli chcesz podać plik audio zamiast używać mikrofonu, nadal musisz podać `audio_config`. Jednak podczas tworzenia [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python), zamiast podawania `device_name`, należy użyć parametru `filename`.

```Python
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.AudioConfig(filename=audio_filename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
```

## <a name="recognize-speech"></a>Rozpoznawanie mowy

[Klasa aparatu rozpoznawania](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python) dla zestawu Speech SDK dla języka Python udostępnia kilka metod, których można użyć do rozpoznawania mowy.

* Rozpoznawanie pojedynczego zrzutu (synchronizacja) — wykonuje rozpoznawanie w trybie blokowania (synchronicznym). Zwraca po rozpoznaniu jednego wypowiedźu. Koniec pojedynczej wypowiedź jest określany przez nasłuchiwanie na końcu lub do czasu przetworzenia maksymalnie 15 sekund. Zadanie zwraca tekst rozpoznawania jako wynik.
* Rozpoznawanie pojedynczego zrzutu (Async) — wykonuje rozpoznawanie w trybie nieblokującym (asynchronicznym). Spowoduje to rozpoznanie pojedynczego wypowiedź. Koniec pojedynczej wypowiedź jest określany przez nasłuchiwanie na końcu lub do czasu przetworzenia maksymalnie 15 sekund.
* Ciągłe rozpoznawanie (synchronizacja) — synchronicznie inicjuje ciągłe rozpoznawanie. Klient musi połączyć się z `EventSignal`, aby otrzymywać wyniki rozpoznawania. Aby zatrzymać rozpoznawanie, wywołaj [stop_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--).
* Stałe rozpoznawanie (asynchroniczne) — asynchronicznie Inicjuje operację ciągłego rozpoznawania. Użytkownik musi nawiązać połączenie z usługą EventSignal, aby otrzymywać wyniki rozpoznawania. Aby zatrzymać asynchroniczne rozpoznawanie ciągłe, wywołaj [stop_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--).

> [!NOTE]
> Dowiedz się więcej na temat [wybierania trybu rozpoznawania mowy](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Rozpoznawanie pojedynczego zrzutu

Oto przykład synchronicznego rozpoznawania pojedynczego zrzutu przy użyciu [`recognize_once()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once):

```Python
result = speech_recognizer.recognize_once()
```

Oto przykład synchronicznego rozpoznawania pojedynczego zrzutu przy użyciu [`recognize_once_async()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once-async------azure-cognitiveservices-speech-resultfuture):

```Python
result = speech_recognizer.recognize_once_async()
```

Bez względu na to, czy użyto metody synchronicznej, czy asynchronicznej, należy napisać kod, aby wykonać iterację w wyniku. Ten przykład szacuje [`result.reason`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason?view=azure-python):

* Drukuje wynik rozpoznawania: `speechsdk.ResultReason.RecognizedSpeech`
* Jeśli nie ma dopasowania do rozpoznawania, należy poinformować użytkownika: `speechsdk.ResultReason.NoMatch `
* Jeśli wystąpi błąd, Wydrukuj komunikat o błędzie: `speechsdk.ResultReason.Canceled`

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

### <a name="continuous-recognition"></a>Ciągłe rozpoznawanie

Ciągłe rozpoznawanie jest nieco większe niż w przypadku rozpoznawania pojedynczego zrzutu. Wymaga nawiązania połączenia z `EventSignal` w celu uzyskania wyników rozpoznawania, a w celu zatrzymania rozpoznawania należy wywołać [stop_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) lub [stop_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--). Oto przykład sposobu ciągłego rozpoznawania w pliku wejściowym audio.

Zacznijmy od definiowania danych wejściowych i inicjowania [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python):

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Następnie utwórz zmienną służącą do zarządzania stanem rozpoznawania mowy. Aby rozpocząć, ustawimy tę wartość na `False`, ponieważ na początku rozpoznawania firma Microsoft może bezpiecznie założyć, że nie została zakończona.

```Python
done = False
```

Teraz tworzymy wywołanie zwrotne, aby zatrzymać ciągłe rozpoznawanie po odebraniu `evt`. Należy pamiętać o kilku kwestiach.

* Po odebraniu `evt` zostanie wydrukowany komunikat `evt`.
* Po odebraniu `evt` jest wywoływana [stop_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) w celu zatrzymania rozpoznawania.
* Stan rozpoznawania zostanie zmieniony na `True`.

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

Ten przykładowy kod pokazuje, jak połączyć wywołania zwrotne z zdarzeniami wysyłanymi z [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#start-continuous-recognition--).

* [`recognizing`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognizing): sygnał dla zdarzeń zawierających pośrednie wyniki rozpoznawania.
* [`recognized`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognized): sygnał dla zdarzeń zawierających końcowe wyniki rozpoznawania (wskazujący na pomyślną próbę rozpoznania).
* [`session_started`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-started): sygnał dla zdarzeń wskazujących początek sesji rozpoznawania (operacji).
* [`session_stopped`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped): sygnał dla zdarzeń wskazujących koniec sesji rozpoznawania (operacji).
* [`canceled`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#canceled): sygnał dla zdarzeń zawierających anulowane wyniki rozpoznawania (wskazujący próbę rozpoznania, która została anulowana jako wynik lub bezpośrednie żądanie anulowania lub, Alternatywnie, błąd transportu lub protokołu).

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

Za pomocą wszystkich konfiguracji możemy wywoływać [start_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped).

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>Tryb dyktowania

W przypadku korzystania z ciągłego rozpoznawania można włączyć przetwarzanie dyktowania przy użyciu odpowiedniej funkcji "Włącz dyktowanie". Ten tryb spowoduje, że wystąpienie konfiguracji mowy interpretuje opisy wyrazów struktur zdań, takich jak interpunkcja. Na przykład "wypowiedź" czy "czy" na żywo "jest interpretowany jako tekst" czy jesteś w mieście? ".

Aby włączyć tryb dyktowania, użyj metody [`enable_dictation()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--) w [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python).

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>Zmień język źródłowy

Typowym zadaniem rozpoznawania mowy jest określenie języka danych wejściowych (lub źródłowych). Przyjrzyjmy się sposobom zmiany języka wejściowego na niemiecki. Znajdź swój SpeechConfig w kodzie, a następnie Dodaj ten wiersz bezpośrednio poniżej.

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-recognition-language) to parametr, który przyjmuje ciąg jako argument. Możesz podać dowolną wartość na liście obsługiwanych [ustawień regionalnych/języków](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Popraw dokładność rozpoznawania

Istnieje kilka sposobów na poprawienie dokładności rozpoznawania przy użyciu zestawu Speech SDK. Przyjrzyjmy się listom fraz. Listy fraz są używane do identyfikowania znanych fraz w danych audio, takich jak nazwa osoby lub określonej lokalizacji. Pojedyncze słowa lub kompletne wyrażenia można dodać do listy fraz. Podczas rozpoznawania jest używany wpis na liście frazy, jeśli dokładne dopasowanie dla całej frazy jest zawarte w dźwięku. Jeśli nie znaleziono dokładnego dopasowania do frazy, rozpoznawanie nie jest wspierane.

> [!IMPORTANT]
> Funkcja listy fraz jest dostępna tylko w języku angielskim.

Aby użyć listy fraz, najpierw Utwórz obiekt [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) , a następnie Dodaj określone słowa i frazy z [`addPhrase`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python#addphrase-phrase--str-).

Wszelkie zmiany [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) zaczną obowiązywać przy następnym rozpoznaniu lub po ponownym nawiązaniu połączenia z usługą mowy.

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

Jeśli musisz wyczyścić listę fraz: 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Inne opcje poprawiające dokładność rozpoznawania

Listy fraz są tylko jedną opcją w celu zwiększenia dokładności rozpoznawania. Możesz również wykonać następujące czynności: 

* [Zwiększ dokładność dzięki Custom Speech](../../../how-to-custom-speech.md)
* [Poprawianie dokładności przy użyciu modeli dzierżawców](../../../tutorial-tenant-model.md)