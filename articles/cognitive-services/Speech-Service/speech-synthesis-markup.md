---
title: Speech Markup Language (SSML) — usługa mowy
titleSuffix: Azure Cognitive Services
description: W celu kontrolowania Wymowa i prosody w zamiany tekstu na mowę, przy użyciu Markup Language synteza mowy.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 89b8b5f8c574de033fabf6861e24fc7d2b31e171
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855100"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Język znaczników syntezy mowy (SSML)

Język SSML (Speech Syntezing Language) to język znaczników oparty na języku XML, który umożliwia deweloperom określenie sposobu konwersji tekstu wejściowego na funkcję syntezy mowy przy użyciu usługi zamiany tekstu na mowę. W porównaniu do zwykłego tekstu, SSML umożliwia deweloperom precyzyjne dostosowanie wartości, wymowy, natężenia głosu, ilości i większej liczby danych wyjściowych zamiany tekstu na mowę. Normalne znaki interpunkcyjne, takie jak Wstrzymywanie po upływie okresu lub używanie poprawnych intonation po zakończeniu zdania z znakiem zapytania, są automatycznie obsługiwane.

Implementacja usługi Speech Services SSML opiera się na wykorzystaniu [języka znaczników funkcji rozpoznawania mowy organizacja World Wide Web Consortium w wersji 1,0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Znaki w języku chińskim, japońskim i koreańskim są liczone jako dwa znaki do rozliczania. Aby uzyskać więcej informacji, zobacz [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standardowe, neuronowych i niestandardowe głosy

Wybieraj spośród standardowych i neuronowychych głosów lub twórz własny niestandardowy głos unikatowy dla danego produktu lub marki. 75 standardowych głosów jest dostępnych w więcej niż 45 językach i ustawieniach regionalnych oraz 5 neuronowych głosów jest dostępnych w 4 językach i ustawieniach regionalnych. Aby zapoznać się z pełną listą obsługiwanych języków, ustawień regionalnych i głosów (neuronowych i standard), zobacz temat [Obsługa języków](language-support.md).

Aby dowiedzieć się więcej na temat standardowych, neuronowych i niestandardowych głosów, zobacz [Omówienie zamiany tekstu na mowę](text-to-speech.md).

## <a name="supported-ssml-elements"></a>Obsługiwane elementy SSML

Każdy dokument SSML jest tworzony z elementami SSML (lub tagami). Te elementy służą do dostosowywania wysokości, Prosody, objętości i innych. W poniższych sekcjach szczegółowo opisano, jak każdy element jest używany oraz kiedy element jest wymagany lub opcjonalny.  

> [!IMPORTANT]
> Nie zapomnij użyć podwójnych cudzysłowów wokół wartości atrybutów. Standardy dla poprawnie sformułowanych, prawidłowy kod XML wymaga, aby wartości atrybutów były ujęte w podwójny cudzysłów. Na przykład, `<prosody volume="90">` jest poprawnie sformułowanym, prawidłowym elementem, ale `<prosody volume=90>` nie jest. SSML nie rozpoznaje wartości atrybutów, które nie znajdują się w cudzysłowie.

## <a name="create-an-ssml-document"></a>Tworzenie dokumentu SSML

`speak`jest elementem głównym i jest **wymagany** dla wszystkich dokumentów SSML. `speak` Element zawiera ważne informacje, takie jak wersja, język i definicja słownictwa znaczników.

**Składnia**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| version | Wskazuje wersję specyfikacji SSML użytej do interpretacji znacznika dokumentu. Bieżąca wersja to 1,0. | Wymagane |
| xml:lang | Określa język dokumentu głównego. Wartość może zawierać małe litery, dwuliterowy kod języka (na przykład **EN**) lub kod języka oraz wielkie kraje/region (na przykład **en-us**). | Wymagane |
| xmlns | Określa identyfikator URI dokumentu, który definiuje słownictwo znaczników (typy elementów i nazwy atrybutów) dokumentu SSML. Bieżący identyfikator URI to https://www.w3.org/2001/10/synthesis. | Wymagane |

## <a name="choose-a-voice-for-text-to-speech"></a>Wybieranie głosu dla zamiany tekstu na mowę

`voice` Element jest wymagany. Służy do określania głosu używanego do zamiany tekstu na mowę.

**Składnia**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| name | Identyfikuje głos używany do wyprowadzania tekstu na mowę. Aby uzyskać pełną listę obsługiwanych głosów, zobacz temat [Obsługa języków](language-support.md#text-to-speech). | Wymagane |

**Przykład**

> [!NOTE]
> Ten przykład używa `en-US-Jessa24kRUS` głosu. Aby uzyskać pełną listę obsługiwanych głosów, zobacz temat [Obsługa języków](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Użyj wielu głosów

W obrębie `speak` elementu można określić wiele głosów dla danych wyjściowych zamiany tekstu na mowę. Głosy te mogą znajdować się w różnych językach. Dla każdego głosu tekst musi być opakowany w `voice` element.

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| name | Identyfikuje głos używany do wyprowadzania tekstu na mowę. Aby uzyskać pełną listę obsługiwanych głosów, zobacz temat [Obsługa języków](language-support.md#text-to-speech). | Wymagane |

**Przykład**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice  name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Dopasuj style mówienia

> [!IMPORTANT]
> Ta funkcja będzie działała tylko z głosymi neuronowych.

Domyślnie usługa zamiany tekstu na mowę służy do syntezowania tekstu przy użyciu neutralnego stylu mowy dla głosów standardowych i neuronowych. Za pomocą głosów neuronowych można dostosować styl mówiący do wyrazów cheerfulness, empatię lub tonacji przy użyciu `<mstts:express-as>` elementu. Jest to opcjonalny element unikatowy dla usług Azure Speech Services.

Obecnie w przypadku tych głosów neuronowych są obsługiwane zmiany stylu mowy:
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

Zmiany są stosowane na poziomie zdania i stylu różnią się w zależności od głosu. Jeśli styl nie jest obsługiwany, usługa zwróci mowę w domyślnym stylu mowy neutralnej.

**Składnia**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| — typ | Określa styl wymawiania. Obecnie style głosu są specyficzne dla głosu. | Wymagane, jeśli ustawienie stylu wymawiania dla głosu neuronowych. Jeśli używasz `mstts:express-as`, należy podać typ. Jeśli podano nieprawidłową wartość, ten element zostanie zignorowany. |

Użyj tej tabeli, aby określić, które style wymawiające są obsługiwane dla każdego głosu neuronowych.

| Głos | Type | Opis |
|-------|------|-------------|
| `en-US-JessaNeural` | type=`cheerful` | Wyraża rozpoznawania emocji o wartości dodatniej i szczęśliwej |
| | type=`empathy` | Wyraża świadomość Caring i zrozumienie |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Wyraża formalny sygnał podobny do emisji wiadomości |
| | type=`sentiment` | Przekazuje komunikat dotykowy lub historię |

**Przykład**

Ten fragment kodu SSML ilustruje, `<mstts:express-as>` w jaki sposób element jest używany do zmiany stylu `cheerful`wymawiania na.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Dodawanie lub usuwanie przerwy/wstrzymania

`break` Użyj elementu, aby wstawić pauzy (lub przerwy) między wyrazami lub zapobiec automatycznemu dodawaniu przez usługę zamiany tekstu na mowę.

> [!NOTE]
> Użyj tego elementu, aby przesłonić domyślne zachowanie zamiany tekstu na mowę (TTS) dla wyrazu lub frazy, jeśli wydawane przez nich słowo lub fraza jest odtwarzane jako nienaturalne. Ustaw `strength` , `none` aby nie granicę prozodyczną przerwania, które jest automatycznie wstawiane przez usługę zamiany tekstu na mowę.

**Składnia**

```xml
<break strength="string" />
<break time="string" />
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| naprężeni | Określa względny czas trwania pauzy przy użyciu jednej z następujących wartości:<ul><li>brak</li><li>x — słabe</li><li>słabe</li><li>Średni (domyślnie)</li><li>silne</li><li>x — Strong</li></ul> | Optional |
| time | Określa bezwzględny czas przerwy w sekundach lub milisekund. Przykłady prawidłowych wartości to 2S i 500 | Optional |

| Naprężeni | Opis |
|----------|-------------|
| Brak lub nie podano wartości | 0 MS |
| x — słabe | 250 MS |
| słabe | 500 ms |
| średnie | 750 ms |
| silne | 1000 MS |
| x — Strong | 1250 MS |


**Przykład**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Określ akapity i zdania

`p`elementy `s` i są używane do określenia odpowiednio akapitów i zdań. W przypadku braku tych elementów usługa zamiany tekstu na mowę automatycznie określa strukturę dokumentu SSML.

`prosody` `break` `phoneme` `audio` Elementmoże`say-as`zawierać tekst i następujące elementy: ,,`mstts:express-as`,,,,, i `s`. `sub` `p`

`phoneme` `break` `prosody` Elementmoże`mstts:express-as`zawierać tekst i następujące elementy: `audio`, ,`say-as`,,, i `sub`. `s`

**Składnia**

```XML
<p></p>
<s></s>
```

**Przykład**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Aby ulepszyć wymowę, użyj fonemów

`ph` Element jest używany do wymowy fonetycznej w dokumentach SSML. `ph` Element może zawierać tylko tekst, nie inne elementy. Zawsze dostarczaj mowę do odczytu przez człowieka jako rezerwowe.

Alfabety fonetyczne składają się z telefonów, które składają się z liter, cyfr lub znaków, czasami w połączeniu. Każdy telefon opisuje unikatowy dźwięk mowy. Jest to w przeciwieństwie do alfabetu łacińskiego, gdzie jakakolwiek litera może reprezentować wiele dźwięków wypowiadanych. Rozważ różne wymowy literę "c" w słowach "Candy" i "zaprzestanie" albo różne wymowy kombinacji liter "TH" w słowach "rzecz" i "te".

**Składnia**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| alfabetu | Określa alfabet fonetyczny do użycia podczas syntezowania wymowy ciągu w `ph` atrybucie. Ciąg określający alfabet musi być określony małymi literami. Poniżej przedstawiono możliwe litery, które można określić.<ul><li>IPA &ndash; Międzynarodowy alfabet fonetyczny</li><li>zestaw &ndash; Speech API SAPI</li><li>Uniwersalny &ndash; zestaw telefonów UPS</li></ul>Litera ma zastosowanie tylko do fonem w elemencie. Aby uzyskać więcej informacji, zobacz [odwołanie](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx)do alfabetu fonetycznego. | Optional |
| pH | Ciąg zawierający telefony, które określają wymowę wyrazu w `phoneme` elemencie. Jeśli określony ciąg zawiera nierozpoznane telefony, usługa zamiany tekstu na mowę (TTS) odrzuci cały dokument SSML i nie wygeneruje żadnych danych wyjściowych mowy określonych w dokumencie. | Wymagany, jeśli jest używany fonemów. |

**Przykłady**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="adjust-prosody"></a>Dostosuj Prosody

`prosody` Element służy do określania zmian wysokości, countour, zakresu, szybkości, czasu trwania i woluminu dla danych wyjściowych zamiany tekstu na mowę. `phoneme` `break` `p` `audio` Elementmoże`prosody`zawierać tekst i następujące elementy: ,,`sub`,,,,, i `s`. `say-as` `prosody`

Ponieważ wartości atrybutów granicę prozodyczną mogą się różnić w różnych zakresach, aparat rozpoznawania mowy interpretuje przypisane wartości jako sugestię rzeczywistych wartości granicę prozodyczną wybranego głosu. Usługa zamiany tekstu na mowę lub zastępuje wartości, które nie są obsługiwane. Przykłady nieobsługiwanych wartości to wysokość 1 MHz lub objętość 120.

**Składnia**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| tonu | Wskazuje gęstość linii bazowej dla tekstu. Możesz wyrazić gęstość jako:<ul><li>Wartość bezwzględna wyrażona jako liczba, po której następuje "Hz" (Hz). Na przykład 600Hz.</li><li>Wartość względna, wyrażona jako liczba poprzedzona znakiem "+" lub "-", po której następuje "Hz" lub "St", która określa ilość, aby zmienić gęstość. Na przykład: + 80Hz lub-2st. "St" wskazuje, że jednostka zmiany to semitone, czyli połowę tonu (pół kroku) w standardowej skali Diatonic.</li><li>Stała wartość:<ul><li>x — niska</li><li>niska</li><li>średnie</li><li>wysokowydajn</li><li>x — wysoka</li><li>default</li></ul></li></ul>. | Optional |
| wybranym | Rozkład nie jest obsługiwany w przypadku głosów neuronowych. Rozkład reprezentuje zmiany w wysokości dla zawartości mowy jako tablicę elementów docelowych w określonych miejscach w danych wyjściowych mowy. Każdy element docelowy jest definiowany przez zestawy par parametrów. Na przykład: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Pierwsza wartość w każdym zestawie parametrów określa lokalizację zmiany w postaci procentu czasu trwania tekstu. Druga wartość określa wielkość, aby podnieść lub obniżyć gęstość, przy użyciu wartości względnej lub wartości wyliczenia dla skoku (zobacz `pitch`). | Optional |
| zakresu  | Wartość, która reprezentuje zakres skoku dla tekstu. Można wyrazić `range` przy użyciu tych samych wartości bezwzględnych, wartości względnych lub wartości wyliczenia `pitch`, które są używane do opisywania. | Optional |
| transmisji  | Wskazuje stawkę głosu tekstu. Można wyrazić `rate` jako:<ul><li>Wartość względna wyrażona jako liczba, która działa jako mnożnik wartości domyślnej. Na przykład wartość *1* powoduje brak zmian w szybkości. Wartość *.5* skutkuje halving szybkością. Wartość *3* powoduje przekroczenie stawki.</li><li>Stała wartość:<ul><li>x-slow</li><li>opóźnienie</li><li>średnie</li><li>Fast</li><li>x — Fast</li><li>default</li></ul></li></ul> | Optional |
| duration  | Okres, który powinien upłynąć, gdy usługa synteza mowy (TTS) odczytuje tekst w sekundach lub milisekundach. Na przykład *2S* lub *1800ms*. | Optional |
| wolumin  | Wskazuje poziom głośności głosu. Wolumin można wyrazić jako:<ul><li>Wartość bezwzględna wyrażona jako liczba z zakresu od 0,0 do 100,0, od najcisza do *głośnie*. Na przykład 75. Wartość domyślna to 100,0.</li><li>Wartość względna wyrażona jako liczba poprzedzona znakiem "+" lub "-", która określa wielkość zmiany woluminu. Na przykład + 10 lub-5,5.</li><li>Stała wartość:<ul><li>automatycznie</li><li>x-soft</li><li>rezerwacje</li><li>średnie</li><li>głośn</li><li>x-głośne</li><li>default</li></ul></li></ul> | Optional |

### <a name="change-speaking-rate"></a>Zmień częstotliwość wypowiedzi

Stawkę głosu można zastosować do głosów standardowych na poziomie wyrazu lub zdania. Stawkę głosu można stosować tylko do głosów neuronowych na poziomie zdania.

**Przykład**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Zmiany woluminu

Zmiany woluminów mogą być stosowane do głosów standardowych na poziomie wyrazu lub zdania. Zmiany woluminów mogą być stosowane tylko do głosów neuronowych na poziomie zdania.

**Przykład**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Zmień wysokość

Zmiany wysokości mogą być stosowane do głosów standardowych na poziomie wyrazu lub zdania. Zmiany szerokości mogą być stosowane tylko do głosów neuronowych na poziomie zdania.

**Przykład**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Zmiana wysokości konturu

> [!IMPORTANT]
> Zmiany konturu skoku nie są obsługiwane przez głosy neuronowych.

**Przykład**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Dodaj zarejestrowane audio

`audio`to opcjonalny element, który umożliwia wstawianie audio MP3 do dokumentu SSML. Treść elementu audio może zawierać zwykły tekst lub SSML znaczników, które są wymawiane, jeśli plik dźwiękowy jest niedostępny lub nieosiągalny. `p` `s` `audio` `break` `sub`Ponadto element może zawierać tekst i następujące elementy: `phoneme` ,,`say-as`, ,,,,i.`prosody` `audio`

Wszystkie audio zawarte w dokumencie SSML muszą spełniać następujące wymagania:

* MP3 musi być hostowany w punkcie końcowym HTTPS dostępnym z Internetu. Wymagany jest protokół HTTPS, a domena hostującym plik MP3 musi mieć prawidłowy zaufany certyfikat SSL.
* Plik MP3 musi być prawidłowym plikiem MP3 (MPEG v2).
* Szybkość transmisji bitów musi wynosić 48 KB/s.
* Częstotliwość próbkowania musi wynosić 16000 Hz.
* Łączny całkowity czas dla wszystkich plików tekstowych i dźwiękowych w pojedynczej odpowiedzi nie może przekroczyć 90 (90) sekund.
* MP3 nie może zawierać żadnych informacji poufnych ani innych.

**Składnia**

```xml
<audio src="string"/></audio>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| SRC | Określa lokalizację/adres URL pliku audio. | Wymagany w przypadku używania elementu audio w dokumencie SSML. |

**Przykład**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <p>
        <audio src="https://contoso.com/opinionprompt.wav"/>
        Thanks for offering your opinion. Please begin speaking after the beep.
        <audio src="https://contoso.com/beep.wav">
        Could not play the beep, please voice your opinion now. </audio>
    </p>
</speak>
```

## <a name="add-background-audio"></a>Dodaj dźwięk w tle

`mstts:backgroundaudio` Element umożliwia dodanie dźwięku w tle do dokumentów SSML (lub mieszanie pliku dźwiękowego z funkcją zamiany tekstu na mowę). Za `mstts:backgroundaudio` pomocą programu możesz zapętlać plik audio w tle, stopniowo zanikać na początku tekstu na mowę i stopniowo przechodzić na koniec zamiany tekstu na mowę.

Jeśli podany dźwięk w tle jest krótszy niż tekst-do-mowę lub zanikanie, spowoduje to zapętlenie. Jeśli jest dłuższa niż Zamiana tekstu na mowę, zostanie zatrzymana po zakończeniu zanikania.

Dozwolony jest tylko jeden plik audio w tle dla dokumentu SSML. Można jednak przeplatać `audio` Tagi `voice` w obrębie elementu, aby dodać dodatkowe audio do dokumentu SSML.

**Składnia**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| SRC | Określa lokalizację/adres URL pliku dźwiękowego w tle. | Wymagany w przypadku używania dźwięku w tle w dokumencie SSML. |
| wolumin | Określa wolumin pliku dźwiękowego w tle. **Akceptowane wartości**: `0` do `100` włącznie. Wartość domyślna to `1`. | Optional |
| Rozjaśnianie | Określa czas zanikania dźwięku w tle w milisekundach. Wartość domyślna to `0`, która jest równoważna brak zanikania w. **Akceptowane wartości**: `0` do `10000` włącznie.  | Optional |
| fadeout | Określa czas zanikania dźwięku w tle w milisekundach. Wartość domyślna to `0`, która jest równoważna brak zanikania. **Akceptowane wartości**: `0` do `10000` włącznie.  | Optional |

**Przykład**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Następne kroki

* [Obsługa języków: głosy, zmienne lokalne, Języki](language-support.md)
