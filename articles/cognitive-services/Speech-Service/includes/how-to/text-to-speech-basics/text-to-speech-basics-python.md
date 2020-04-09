---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: be60a2f371148fabf73fc7fcdce114295775d71c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986254"
---
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [wypróbuj bezpłatną usługę Mowy.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Zanim będzie można coś zrobić, musisz zainstalować pakiet SDK mowy.

```Python
pip install azure-cognitiveservices-speech
```

Jeśli korzystasz z systemu macOS i występują problemy z instalacją, może być konieczne uruchomienie tego polecenia w pierwszej kolejności.

```Python
python3 -m pip install --upgrade pip
```

Po zainstalowaniu zestawie SDK mowy należy dołączyć następujące instrukcje importu u góry skryptu.

```Python
from azure.cognitiveservices.speech import AudioDataStream, SpeechConfig, SpeechSynthesizer, SpeechSynthesisOutputFormat
from azure.cognitiveservices.speech.audio import AudioOutputConfig
```

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby wywołać usługę mowy przy użyciu SDK [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)mowy, należy utworzyć plik . Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, host lub token autoryzacji.

> [!NOTE]
> Niezależnie od tego, czy wykonujesz rozpoznawanie mowy, syntezę mowy, tłumaczenie czy rozpoznawanie intencji, zawsze tworzysz konfigurację.

Istnieje kilka sposobów, które można [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)zainicjować:

* Z subskrypcją: przekaż klucz i skojarzony region.
* Z punktem końcowym: przekazać w punkcie końcowym usługi mowy. Klucz lub token autoryzacji jest opcjonalny.
* Z hostem: przekaż adres hosta. Klucz lub token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: przekaż w tokenie autoryzacji i skojarzonym regionie.

W tym przykładzie [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) można utworzyć przy użyciu klucza subskrypcji i regionu. Zobacz stronę [pomocy technicznej regionu,](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) aby znaleźć identyfikator regionu.

```python
speech_config = SpeechConfig(subscription="YourSubscriptionKey", region="YourServiceRegion")
```

## <a name="synthesize-speech-to-a-file"></a>Syntetyzowanie mowy do pliku

Następnie należy utworzyć [`SpeechSynthesizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python) obiekt, który wykonuje konwersje tekstu na mowę i wyjścia do głośników, plików lub innych strumieni wyjściowych. Akceptuje [`SpeechSynthesizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python) jako params [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) obiektu utworzonego w poprzednim [`AudioOutputConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig?view=azure-python) kroku i obiekt, który określa, jak wyniki wyjściowe powinny być obsługiwane.

Aby rozpocząć, `AudioOutputConfig` należy utworzyć, aby automatycznie `.wav` zapisać dane `filename` wyjściowe do pliku, przy użyciu konstruktora param.

```python
audio_config = AudioOutputConfig(filename="path/to/write/file.wav")
```

Następnie wystąpienia przez `SpeechSynthesizer` przekazanie `speech_config` obiektu i `audio_config` obiektu jako params. Następnie wykonywanie syntezy mowy i zapisywanie do `speak_text_async()` pliku jest tak proste, jak uruchamianie z ciągiem tekstu.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
synthesizer.speak_text_async("A simple test to write to a file.")
```

Uruchom program, a plik syntetyzowany `.wav` jest zapisywany w określonej lokalizacji. Jest to dobry przykład najbardziej podstawowego użycia, ale następnie spojrzeć na dostosowywanie danych wyjściowych i obsługi odpowiedzi wyjściowej jako strumień w pamięci do pracy z scenariuszy niestandardowych.

## <a name="synthesize-to-speaker-output"></a>Syntetyzowanie do wyjścia głośnika

W niektórych przypadkach można bezpośrednio wyprowadzić syntetyzowaną mowę bezpośrednio do głośnika. Aby to zrobić, użyj przykładu w poprzedniej `AudioOutputConfig` sekcji, `filename` ale zmień, `use_default_speaker=True`usuwając param i ustaw . Spowoduje to wyjście do bieżącego aktywnego urządzenia wyjściowego.

```python
audio_config = AudioOutputConfig(use_default_speaker=True)
```

## <a name="get-result-as-an-in-memory-stream"></a>Uzyskaj wynik jako strumień w pamięci

W wielu scenariuszach w rozwoju aplikacji mowy prawdopodobnie potrzebujesz wynikowych danych audio jako strumienia w pamięci, a nie bezpośrednio do pliku. Pozwoli to na tworzenie zachowań niestandardowych, w tym:

* Abstrakcja wynikowej tablicy bajtów jako strumienia w zakresie wyszukiwania dla niestandardowych usług podrzędnych.
* Zintegruj wynik z innymi interfejsami API lub usługami.
* Modyfikuj dane `.wav` audio, zapisuj niestandardowe nagłówki itp.

To proste, aby to zmienić z poprzedniego przykładu. Najpierw usuń `AudioConfig`, jak będzie zarządzać zachowanie danych wyjściowych ręcznie od tego punktu do zwiększenia kontroli. Następnie `None` przekazać `AudioConfig` w `SpeechSynthesizer` konstruktorze. 

> [!NOTE]
> Przechodząc `None` do `AudioConfig`, zamiast pomijając go jak w przykładzie wyjścia głośnika powyżej, nie będzie odtwarzać dźwięku domyślnie na bieżącym aktywnym urządzeniu wyjściowym.

Tym razem wynik jest zapisywany w zmiennej. [`SpeechSynthesisResult`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult?view=azure-python) Właściwość `audio_data` zawiera `bytes` obiekt danych wyjściowych. Można pracować z tym obiektem ręcznie lub [`AudioDataStream`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?view=azure-python) można użyć klasy do zarządzania strumieniem w pamięci. W tym przykładzie `AudioDataStream` można użyć konstruktora, aby uzyskać strumień z wyniku.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = synthesizer.speak_text_async("Getting the response as an in-memory stream.").get()
stream = AudioDataStream(result)
```

W tym miejscu można zaimplementować dowolne zachowanie niestandardowe przy użyciu wynikowego `stream` obiektu.

## <a name="customize-audio-format"></a>Dostosowywanie formatu audio

W poniższej sekcji pokazano, jak dostosować atrybuty wyjścia audio, w tym:

* Typ pliku audio
* Częstotliwość próbkowania
* Głębia bitowa

Aby zmienić format dźwięku, `set_speech_synthesis_output_format()` należy `SpeechConfig` użyć funkcji obiektu. Ta funkcja oczekuje `enum` typu [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python), którego używasz do wyboru formatu wyjściowego. Zobacz dokumenty referencyjne, aby uzyskać [listę dostępnych formatów audio.](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python)

Istnieją różne opcje dla różnych typów plików w zależności od wymagań. Należy zauważyć, że z `Raw24Khz16BitMonoPcm` definicji formaty nieprzetworzone, takie jak nie zawierają nagłówków audio. Formaty nieprzetworzone należy używać tylko wtedy, gdy wiadomo, że implementacja niższego rzędu może dekodować nieprzetworzony strumień bitów lub jeśli planujesz ręczne tworzenie nagłówków na podstawie głębi bitowej, częstotliwości próbkowania, liczby kanałów itp.

W tym przykładzie należy określić format `Riff24Khz16BitMonoPcm` RIFF `SpeechSynthesisOutputFormat` o `SpeechConfig` wysokiej wierności, ustawiając na obiekcie. Podobnie jak w przykładzie w [`AudioDataStream`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?view=azure-python) poprzedniej sekcji, można użyć, aby uzyskać strumień w pamięci wynik, a następnie zapisać go do pliku.


```python
speech_config.set_speech_synthesis_output_format(SpeechSynthesisOutputFormat["Riff24Khz16BitMonoPcm"])
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

result = synthesizer.speak_text_async("Customizing audio output format.").get()
stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

Ponowne uruchomienie programu spowoduje zapisanie dostosowanego `.wav` pliku do określonej ścieżki.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Dostosowywanie charakterystyk mowy za pomocą protokołu SSML

Język znaczników syntezy mowy (SSML) umożliwia precyzyjne dostosowanie tonowania tonów, wymowy, szybkości mówienia, głośności i innych danych wyjściowych tekstu na mowę, przesyłając żądania ze schematu XML. W tej sekcji przedstawiono kilka praktycznych przykładów użycia, ale bardziej szczegółowy przewodnik można znaleźć w [artykule instrukcjowym SSML](../../../speech-synthesis-markup.md).

Aby rozpocząć korzystanie z SSML do dostosowywania, należy wprowadzić prostą zmianę, która przełącza głos.
Najpierw utwórz nowy plik XML dla konfiguracji SSML w katalogu projektu `ssml.xml`głównego, w tym przykładzie . Element główny jest `<speak>`zawsze , a `<voice>` zawijanie tekstu w elemencie pozwala zmienić głos za pomocą `name` param. W tym przykładzie zmienia głos na męski głos w języku angielskim (UK). Należy pamiętać, że ten głos jest **standardowy** głos, który ma różne ceny i dostępność niż głosy **neuronowe.** Zobacz [pełną listę](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices) obsługiwanych **standardowych** głosów.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Następnie należy zmienić żądanie syntezy mowy, aby odwołać się do pliku XML. Żądanie jest w większości takie samo, ale `speak_text_async()` zamiast korzystać z funkcji, używasz `speak_ssml_async()`. Ta funkcja oczekuje ciągu XML, więc najpierw odczytuj konfigurę SSML jako ciąg. W tym miejscu obiekt wynik jest dokładnie taki sam jak poprzednie przykłady.

> [!NOTE]
> Jeśli `ssml_string` zawiera `ï»¿` na początku ciągu, należy odsunąć format BOM lub usługa zwróci błąd. Można to zrobić, `encoding` ustawiając `open("ssml.xml", "r", encoding="utf-8-sig")`parametr w następujący sposób: .

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

ssml_string = open("ssml.xml", "r").read()
result = synthesizer.speak_ssml_async(ssml_string).get()

stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

Wyjście działa, ale istnieje kilka prostych dodatkowych zmian, które można wprowadzić, aby pomóc mu brzmieć bardziej naturalnie. Ogólna szybkość mówienia jest trochę za szybka, `<prosody>` więc dodamy tag i zmniejszymy prędkość do **90%** domyślnej szybkości. Dodatkowo pauza po przecinkom w zdaniu jest trochę za krótka i nienaturalna. Aby rozwiązać ten problem, dodaj `<break>` znacznik, aby opóźnić mowę, i ustaw param czasu na **200ms**. Uruchom ponownie syntezę, aby zobaczyć, jak te dostosowania wpłynęły na dane wyjściowe.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Głosy neuronowe

Głosy neuronowe są algorytmami syntezy mowy zasilanymi przez głębokie sieci neuronowe. Podczas korzystania z głosu nerwowego, syntetyzowane mowy jest prawie nie do odróżnienia od ludzkich nagrań. Dzięki naturalnej prozodyi i wyraźnej artykulacji słów, głosy nerwowe znacznie zmniejszają zmęczenie słuchowe, gdy użytkownicy wchodzą w interakcje z systemami Sztucznej Inteligencji.

Aby przełączyć się na głos `name` neuronowy, zmień jedną z [opcji głosu neuronowego](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices). Następnie dodaj obszar nazw XML dla `mstts`programu i `<mstts:express-as>` zawiń tekst w znaczniku. Użyj `style` param, aby dostosować styl mówienia. W tym `cheerful`przykładzie użyto `customerservice` , `chat` ale spróbuj go ustawienie lub zobaczyć różnicę w stylu mówienia.

> [!IMPORTANT]
> Głosy neuronowe są obsługiwane **tylko** dla zasobów mowy utworzonych w regionach *Wschodnich Stanów Zjednoczonych,* *Azji Południowo-Wschodniej*i *Europy Zachodniej.*

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
