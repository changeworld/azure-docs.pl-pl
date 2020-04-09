---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: 8679d6d4c8ff0a6abdf045187c284ca65f43ee7e
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986261"
---
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [wypróbuj bezpłatną usługę Mowy.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Zanim będzie można coś zrobić, musisz zainstalować pakiet SDK mowy. W zależności od platformy należy korzystać z następujących instrukcji:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">Rdzeń .NET<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Jedność<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">Uwp<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Importowanie zależności

Aby uruchomić przykłady w tym artykule, należy dołączyć następujące `using` instrukcje w górnej części skryptu.

```csharp
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby wywołać usługę mowy przy użyciu SDK [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)mowy, należy utworzyć plik . Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, host lub token autoryzacji.

> [!NOTE]
> Niezależnie od tego, czy wykonujesz rozpoznawanie mowy, syntezę mowy, tłumaczenie czy rozpoznawanie intencji, zawsze tworzysz konfigurację.

Istnieje kilka sposobów, które można [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)zainicjować:

* Z subskrypcją: przekaż klucz i skojarzony region.
* Z punktem końcowym: przekazać w punkcie końcowym usługi mowy. Klucz lub token autoryzacji jest opcjonalny.
* Z hostem: przekaż adres hosta. Klucz lub token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: przekaż w tokenie autoryzacji i skojarzonym regionie.

W tym przykładzie [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) można utworzyć przy użyciu klucza subskrypcji i regionu. Zobacz stronę [pomocy technicznej regionu,](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) aby znaleźć identyfikator regionu. Można również utworzyć kilka podstawowych kod standardowy do użycia w pozostałej części tego artykułu, które można zmodyfikować dla różnych dostosowań.

```csharp
public class Program 
{
    static async Task Main()
    {
        await SynthesizeAudioAsync();
    }

    static async Task SynthesizeAudioAsync() 
    {
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>Syntetyzowanie mowy do pliku

Następnie należy utworzyć [`SpeechSynthesizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet) obiekt, który wykonuje konwersje tekstu na mowę i wyjścia do głośników, plików lub innych strumieni wyjściowych. Akceptuje [`SpeechSynthesizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet) jako params [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) obiektu utworzonego w poprzednim [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet) kroku i obiekt, który określa, jak wyniki wyjściowe powinny być obsługiwane.

Aby rozpocząć, `AudioConfig` utwórz, aby automatycznie `.wav` zapisać dane `FromWavFileOutput()` wyjściowe do pliku, za `using` pomocą funkcji i utworzyć wystąpienia z instrukcją. Instrukcja `using` w tym kontekście automatycznie usuwa zasobów niezarządzanych i powoduje, że obiekt wykracza poza zakres po disposal.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
}
```

Następnie wystąpienia z `SpeechSynthesizer` inną `using` instrukcją. Przekaż `config` obiekt i `audioConfig` obiekt jako params. Następnie wykonywanie syntezy mowy i zapisywanie do `SpeakTextAsync()` pliku jest tak proste, jak uruchamianie z ciągiem tekstu.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
    using var synthesizer = new SpeechSynthesizer(config, audioConfig);
    await synthesizer.SpeakTextAsync("A simple test to write to a file.");
}
```

Uruchom program, a plik syntetyzowany `.wav` jest zapisywany w określonej lokalizacji. Jest to dobry przykład najbardziej podstawowego użycia, ale następnie spojrzeć na dostosowywanie danych wyjściowych i obsługi odpowiedzi wyjściowej jako strumień w pamięci do pracy z scenariuszy niestandardowych.

## <a name="synthesize-to-speaker-output"></a>Syntetyzowanie do wyjścia głośnika

W niektórych przypadkach można bezpośrednio wyprowadzić syntetyzowaną mowę bezpośrednio do głośnika. Aby to zrobić, po `AudioConfig` prostu pomiń `SpeechSynthesizer` param podczas tworzenia w powyższym przykładzie. Spowoduje to wyjście do bieżącego aktywnego urządzenia wyjściowego.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config);
    await synthesizer.SpeakTextAsync("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Uzyskaj wynik jako strumień w pamięci

W wielu scenariuszach w rozwoju aplikacji mowy prawdopodobnie potrzebujesz wynikowych danych audio jako strumienia w pamięci, a nie bezpośrednio do pliku. Pozwoli to na tworzenie zachowań niestandardowych, w tym:

* Abstrakcja wynikowej tablicy bajtów jako strumienia w zakresie wyszukiwania dla niestandardowych usług podrzędnych.
* Zintegruj wynik z innymi interfejsami API lub usługami.
* Modyfikuj dane `.wav` audio, zapisuj niestandardowe nagłówki itp.

To proste, aby to zmienić z poprzedniego przykładu. Najpierw usuń `AudioConfig` blok, ponieważ będzie zarządzać zachowanie danych wyjściowych ręcznie od tego punktu do zwiększenia kontroli. Następnie `null` przekazać `AudioConfig` w `SpeechSynthesizer` konstruktorze. 

> [!NOTE]
> Przechodząc `null` do `AudioConfig`, zamiast pomijając go jak w przykładzie wyjścia głośnika powyżej, nie będzie odtwarzać dźwięku domyślnie na bieżącym aktywnym urządzeniu wyjściowym.

Tym razem wynik jest zapisywany w zmiennej. [`SpeechSynthesisResult`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult?view=azure-dotnet) Właściwość `AudioData` zawiera `byte []` a danych wyjściowych. Można pracować z `byte []` tym ręcznie lub można [`AudioDataStream`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream?view=azure-dotnet) użyć klasy do zarządzania strumieniem w pamięci. W tym przykładzie `AudioDataStream.FromResult()` można użyć funkcji statycznej, aby uzyskać strumień z wyniku.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);
    
    var result = await synthesizer.SpeakTextAsync("Getting the response as an in-memory stream.");
    using var stream = AudioDataStream.FromResult(result);
}
```

W tym miejscu można zaimplementować dowolne zachowanie niestandardowe przy użyciu wynikowego `stream` obiektu.

## <a name="customize-audio-format"></a>Dostosowywanie formatu audio

W poniższej sekcji pokazano, jak dostosować atrybuty wyjścia audio, w tym:

* Typ pliku audio
* Częstotliwość próbkowania
* Głębia bitowa

Aby zmienić format dźwięku, `SetSpeechSynthesisOutputFormat()` należy `SpeechConfig` użyć funkcji obiektu. Ta funkcja oczekuje `enum` typu [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet), którego używasz do wyboru formatu wyjściowego. Zobacz dokumenty referencyjne, aby uzyskać [listę dostępnych formatów audio.](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet)

Istnieją różne opcje dla różnych typów plików w zależności od wymagań. Należy zauważyć, że z `Raw24Khz16BitMonoPcm` definicji formaty nieprzetworzone, takie jak nie zawierają nagłówków audio. Formaty nieprzetworzone należy używać tylko wtedy, gdy wiadomo, że implementacja niższego rzędu może dekodować nieprzetworzony strumień bitów lub jeśli planujesz ręczne tworzenie nagłówków na podstawie głębi bitowej, częstotliwości próbkowania, liczby kanałów itp.

W tym przykładzie należy określić format `Riff24Khz16BitMonoPcm` RIFF `SpeechSynthesisOutputFormat` o `SpeechConfig` wysokiej wierności, ustawiając na obiekcie. Podobnie jak w przykładzie w [`AudioDataStream`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream?view=azure-dotnet) poprzedniej sekcji, można użyć, aby uzyskać strumień w pamięci wynik, a następnie zapisać go do pliku.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config.SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    using var synthesizer = new SpeechSynthesizer(config, null);
    var result = await synthesizer.SpeakTextAsync("Customizing audio output format.");

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
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

Następnie należy zmienić żądanie syntezy mowy, aby odwołać się do pliku XML. Żądanie jest w większości takie samo, ale `SpeakTextAsync()` zamiast korzystać z funkcji, używasz `SpeakSsmlAsync()`. Ta funkcja oczekuje ciągu XML, więc najpierw należy załadować config `File.ReadAllText()`SSML jako ciąg przy użyciu . W tym miejscu obiekt wynik jest dokładnie taki sam jak poprzednie przykłady.

> [!NOTE]
> Jeśli używasz programu Visual Studio, config kompilacji prawdopodobnie nie znajdzie pliku XML domyślnie. Aby rozwiązać ten problem, kliknij prawym przyciskiem myszy plik XML i wybierz polecenie **Właściwości**. Zmień **akcję kompilacji** na *zawartość*i zmień **opcję Kopiuj do katalogu wyjściowego,** aby *zawsze kopiować*.

```csharp
public static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);
    
    var ssml = File.ReadAllText("./ssml.xml");
    var result = await synthesizer.SpeakSsmlAsync(ssml);

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
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
