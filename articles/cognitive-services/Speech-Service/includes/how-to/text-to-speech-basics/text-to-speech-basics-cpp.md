---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: c5d954cc2bdda0b1fcb67801fa948e1f56fb0364
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986240"
---
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [wypróbuj bezpłatną usługę Mowy.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Zanim będzie można coś zrobić, musisz zainstalować pakiet SDK mowy. W zależności od platformy należy korzystać z następujących instrukcji:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">Macos<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Importowanie zależności

Aby uruchomić przykłady w tym artykule, `using` należy uwzględnić następujące importowanie i instrukcje w górnej części skryptu.

```cpp
#include <iostream>
#include <fstream>
#include <string>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby wywołać usługę mowy przy użyciu SDK [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)mowy, należy utworzyć plik . Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, host lub token autoryzacji.

> [!NOTE]
> Niezależnie od tego, czy wykonujesz rozpoznawanie mowy, syntezę mowy, tłumaczenie czy rozpoznawanie intencji, zawsze tworzysz konfigurację.

Istnieje kilka sposobów, które można [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)zainicjować:

* Z subskrypcją: przekaż klucz i skojarzony region.
* Z punktem końcowym: przekazać w punkcie końcowym usługi mowy. Klucz lub token autoryzacji jest opcjonalny.
* Z hostem: przekaż adres hosta. Klucz lub token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: przekaż w tokenie autoryzacji i skojarzonym regionie.

W tym przykładzie [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) można utworzyć przy użyciu klucza subskrypcji i regionu. Zobacz stronę [pomocy technicznej regionu,](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) aby znaleźć identyfikator regionu. Można również utworzyć kilka podstawowych kod standardowy do użycia w pozostałej części tego artykułu, które można zmodyfikować dla różnych dostosowań.

```cpp
int wmain()
{
    try
    {
        synthesizeSpeech();
    }
    catch (exception e)
    {
        cout << e.what();
    }
    return 0;
}
    
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>Syntetyzowanie mowy do pliku

Następnie należy utworzyć [`SpeechSynthesizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer) obiekt, który wykonuje konwersje tekstu na mowę i wyjścia do głośników, plików lub innych strumieni wyjściowych. Akceptuje [`SpeechSynthesizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer) jako params [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) obiektu utworzonego w poprzednim [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) kroku i obiekt, który określa, jak wyniki wyjściowe powinny być obsługiwane.

Aby rozpocząć, `AudioConfig` utwórz, aby automatycznie `.wav` zapisać dane `FromWavFileOutput()` wyjściowe do pliku, za pomocą funkcji.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
}
```

Następnie smuszaj `SpeechSynthesizer`tworzenie `config` wystąpienia obiektu `audioConfig` , przekazując obiekt i obiekt jako params. Następnie wykonywanie syntezy mowy i zapisywanie do `SpeakTextAsync()` pliku jest tak proste, jak uruchamianie z ciągiem tekstu.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, audioConfig);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();
}
```

Uruchom program, a plik syntetyzowany `.wav` jest zapisywany w określonej lokalizacji. Jest to dobry przykład najbardziej podstawowego użycia, ale następnie spojrzeć na dostosowywanie danych wyjściowych i obsługi odpowiedzi wyjściowej jako strumień w pamięci do pracy z scenariuszy niestandardowych.

## <a name="synthesize-to-speaker-output"></a>Syntetyzowanie do wyjścia głośnika

W niektórych przypadkach można bezpośrednio wyprowadzić syntetyzowaną mowę bezpośrednio do głośnika. Aby to zrobić, po `AudioConfig` prostu pomiń `SpeechSynthesizer` param podczas tworzenia w powyższym przykładzie. Spowoduje to wyjście do bieżącego aktywnego urządzenia wyjściowego.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config);
    auto result = synthesizer->SpeakTextAsync("Synthesizing directly to speaker output.").get();
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Uzyskaj wynik jako strumień w pamięci

W wielu scenariuszach w rozwoju aplikacji mowy prawdopodobnie potrzebujesz wynikowych danych audio jako strumienia w pamięci, a nie bezpośrednio do pliku. Pozwoli to na tworzenie zachowań niestandardowych, w tym:

* Abstrakcja wynikowej tablicy bajtów jako strumienia w zakresie wyszukiwania dla niestandardowych usług podrzędnych.
* Zintegruj wynik z innymi interfejsami API lub usługami.
* Modyfikuj dane `.wav` audio, zapisuj niestandardowe nagłówki itp.

To proste, aby to zmienić z poprzedniego przykładu. Najpierw usuń `AudioConfig`, jak będzie zarządzać zachowanie danych wyjściowych ręcznie od tego punktu do zwiększenia kontroli. Następnie `NULL` przekazać `AudioConfig` w `SpeechSynthesizer` konstruktorze. 

> [!NOTE]
> Przechodząc `NULL` do `AudioConfig`, zamiast pomijając go jak w przykładzie wyjścia głośnika powyżej, nie będzie odtwarzać dźwięku domyślnie na bieżącym aktywnym urządzeniu wyjściowym.

Tym razem wynik jest zapisywany w zmiennej. [`SpeechSynthesisResult`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesisresult) Getter `GetAudioData` zwraca `byte []` a danych wyjściowych. Można pracować z `byte []` tym ręcznie lub można [`AudioDataStream`](https://docs.microsoft.com/cpp/cognitive-services/speech/audiodatastream) użyć klasy do zarządzania strumieniem w pamięci. W tym przykładzie `AudioDataStream.FromResult()` można użyć funkcji statycznej, aby uzyskać strumień z wyniku.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    
    auto result = synthesizer->SpeakTextAsync("Getting the response as an in-memory stream.").get();
    auto stream = AudioDataStream::FromResult(result);
}
```

W tym miejscu można zaimplementować dowolne zachowanie niestandardowe przy użyciu wynikowego `stream` obiektu.

## <a name="customize-audio-format"></a>Dostosowywanie formatu audio

W poniższej sekcji pokazano, jak dostosować atrybuty wyjścia audio, w tym:

* Typ pliku audio
* Częstotliwość próbkowania
* Głębia bitowa

Aby zmienić format dźwięku, `SetSpeechSynthesisOutputFormat()` należy `SpeechConfig` użyć funkcji obiektu. Ta funkcja oczekuje `enum` typu [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat), którego używasz do wyboru formatu wyjściowego. Zobacz dokumenty referencyjne, aby uzyskać [listę dostępnych formatów audio.](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat)

Istnieją różne opcje dla różnych typów plików w zależności od wymagań. Należy zauważyć, że z `Raw24Khz16BitMonoPcm` definicji formaty nieprzetworzone, takie jak nie zawierają nagłówków audio. Formaty nieprzetworzone należy używać tylko wtedy, gdy wiadomo, że implementacja niższego rzędu może dekodować nieprzetworzony strumień bitów lub jeśli planujesz ręczne tworzenie nagłówków na podstawie głębi bitowej, częstotliwości próbkowania, liczby kanałów itp.

W tym przykładzie należy określić format `Riff24Khz16BitMonoPcm` RIFF `SpeechSynthesisOutputFormat` o `SpeechConfig` wysokiej wierności, ustawiając na obiekcie. Podobnie jak w przykładzie w [`AudioDataStream`](https://docs.microsoft.com/cpp/cognitive-services/speech/audiodatastream) poprzedniej sekcji, można użyć, aby uzyskać strumień w pamięci wynik, a następnie zapisać go do pliku.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config->SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat::Riff24Khz16BitMonoPcm);

    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();
    
    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
}
```

Ponowne uruchomienie programu spowoduje `.wav` zapisanie pliku do określonej ścieżki.

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

Następnie należy zmienić żądanie syntezy mowy, aby odwołać się do pliku XML. Żądanie jest w większości takie samo, ale `SpeakTextAsync()` zamiast korzystać z funkcji, używasz `SpeakSsmlAsync()`. Ta funkcja oczekuje ciągu XML, więc najpierw załadować config SSML jako ciąg. W tym miejscu obiekt wynik jest dokładnie taki sam jak poprzednie przykłady.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    
    std::ifstream file("./ssml.xml");
    std::string ssml, line;
    while (std::getline(file, line))
    {
        ssml += line;
        ssml.push_back('\n');
    }
    auto result = synthesizer->SpeakSsmlAsync(ssml).get();
    
    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
}
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
