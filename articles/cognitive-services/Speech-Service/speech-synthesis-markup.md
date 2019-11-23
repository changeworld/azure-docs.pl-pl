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
ms.openlocfilehash: 3791b2d60b84299fc3b646f7e6585002078b607f
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350169"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Język znaczników syntezy mowy (SSML)

Język SSML (Speech Syntezing Language) to język znaczników oparty na języku XML, który umożliwia deweloperom określenie sposobu konwersji tekstu wejściowego na funkcję syntezy mowy przy użyciu usługi zamiany tekstu na mowę. W porównaniu do zwykłego tekstu, SSML umożliwia deweloperom precyzyjne dostosowanie wartości, wymowy, natężenia głosu, ilości i większej liczby danych wyjściowych zamiany tekstu na mowę. Normalne znaki interpunkcyjne, takie jak Wstrzymywanie po upływie okresu lub używanie poprawnych intonation po zakończeniu zdania z znakiem zapytania, są automatycznie obsługiwane.

Implementacja usługi Speech Services SSML opiera się na wykorzystaniu [języka znaczników funkcji rozpoznawania mowy organizacja World Wide Web Consortium w wersji 1,0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Znaki w języku chińskim, japońskim i koreańskim są liczone jako dwa znaki do rozliczania. Aby uzyskać więcej informacji, zobacz [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standardowe, neuronowych i niestandardowe głosy

Wybieraj spośród standardowych i neuronowychych głosów lub twórz własny niestandardowy głos unikatowy dla danego produktu lub marki. 75 standardowych głosów jest dostępnych w więcej niż 45 językach i ustawieniach regionalnych oraz 5 neuronowych głosów jest dostępnych w 4 językach i ustawieniach regionalnych. Aby zapoznać się z pełną listą obsługiwanych języków, ustawień regionalnych i głosów (neuronowych i standard), zobacz temat [Obsługa języków](language-support.md).

Aby dowiedzieć się więcej na temat standardowych, neuronowych i niestandardowych głosów, zobacz [Omówienie zamiany tekstu na mowę](text-to-speech.md).

## <a name="special-characters"></a>Znaki specjalne

Korzystając z SSML do konwertowania zamiany tekstu na syntezę, należy pamiętać, że podobnie jak w przypadku formatu XML, znaki specjalne, takie jak cudzysłowy, apostrofy i nawiasy muszą zostać zmienione. Aby uzyskać więcej informacji, zobacz [XML (XML) 1,0: Dodatek D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Obsługiwane elementy SSML

Każdy dokument SSML jest tworzony z elementami SSML (lub tagami). Te elementy służą do dostosowywania wysokości, Prosody, objętości i innych. W poniższych sekcjach szczegółowo opisano, jak każdy element jest używany oraz kiedy element jest wymagany lub opcjonalny.  

> [!IMPORTANT]
> Nie zapomnij użyć podwójnych cudzysłowów wokół wartości atrybutów. Standardy dla poprawnie sformułowanych, prawidłowy kod XML wymaga, aby wartości atrybutów były ujęte w podwójny cudzysłów. Na przykład `<prosody volume="90">` jest poprawnie sformułowanym, prawidłowym elementem, ale `<prosody volume=90>` nie jest. SSML nie rozpoznaje wartości atrybutów, które nie znajdują się w cudzysłowie.

## <a name="create-an-ssml-document"></a>Tworzenie dokumentu SSML

`speak` jest elementem głównym i jest **wymagany** dla wszystkich dokumentów SSML. Element `speak` zawiera ważne informacje, takie jak wersja, język i definicja słownictwa znaczników.

**Składnia**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| version | Wskazuje wersję specyfikacji SSML użytej do interpretacji znacznika dokumentu. Bieżąca wersja to 1,0. | Wymagane |
| xml:lang | Określa język dokumentu głównego. Wartość może zawierać małe litery, dwuliterowy kod języka (na przykład **EN**) lub kod języka oraz wielkie kraje/region (na przykład **en-us**). | Wymagane |
| xmlns | Określa identyfikator URI dokumentu, który definiuje słownictwo znaczników (typy elementów i nazwy atrybutów) dokumentu SSML. Bieżący identyfikator URI jest https://www.w3.org/2001/10/synthesis. | Wymagane |

## <a name="choose-a-voice-for-text-to-speech"></a>Wybieranie głosu dla zamiany tekstu na mowę

Element `voice` jest wymagany. Służy do określania głosu używanego do zamiany tekstu na mowę.

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

W ramach elementu `speak` można określić wiele głosów dla danych wyjściowych zamiany tekstu na mowę. Głosy te mogą znajdować się w różnych językach. Dla każdego głosu tekst musi być opakowany w `voice` elementu.

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

Domyślnie usługa zamiany tekstu na mowę służy do syntezowania tekstu przy użyciu neutralnego stylu mowy dla głosów standardowych i neuronowych. Za pomocą głosów neuronowych można dostosować styl mówiący do wyrazów cheerfulness, empatię lub tonacji przy użyciu elementu `<mstts:express-as>`. Jest to opcjonalny element unikatowy dla usług Azure Speech Services.

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
| type | Określa styl wymawiania. Obecnie style głosu są specyficzne dla głosu. | Wymagane, jeśli ustawienie stylu wymawiania dla głosu neuronowych. W przypadku używania `mstts:express-as`należy podać typ. Jeśli podano nieprawidłową wartość, ten element zostanie zignorowany. |

Użyj tej tabeli, aby określić, które style wymawiające są obsługiwane dla każdego głosu neuronowych.

| Połączenia głosowe | Typ | Opis |
|-------|------|-------------|
| `en-US-JessaNeural` | type=`cheerful` | Wyraża rozpoznawania emocji o wartości dodatniej i szczęśliwej |
| | type=`empathy` | Wyraża świadomość Caring i zrozumienie |
| | type=`chat` | Czytaj swobodny, swobodny sygnał |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Wyraża formalny sygnał podobny do emisji wiadomości |
| | type=`sentiment` | Przekazuje komunikat dotykowy lub historię |

**Przykład**

Ten fragment kodu SSML ilustruje, w jaki sposób element `<mstts:express-as>` jest używany do zmiany stylu mówienia na `cheerful`.

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

Użyj `break` elementu, aby wstawić pauzy (lub przerwy) między wyrazami lub zapobiec automatycznemu dodawaniu przez usługę zamiany tekstu na mowę.

> [!NOTE]
> Użyj tego elementu, aby przesłonić domyślne zachowanie zamiany tekstu na mowę (TTS) dla wyrazu lub frazy, jeśli wydawane przez nich słowo lub fraza jest odtwarzane jako nienaturalne. Ustaw `strength` na `none`, aby zapobiec przerwie granicę prozodyczną, która jest automatycznie wstawiana przez usługę zamiany tekstu na mowę.

**Składnia**

```xml
<break strength="string" />
<break time="string" />
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| naprężeni | Określa względny czas trwania pauzy przy użyciu jednej z następujących wartości:<ul><li>brak</li><li>x — słabe</li><li>lekko</li><li>Średni (domyślnie)</li><li>najwyższy</li><li>x — Strong</li></ul> | Optional (Opcjonalność) |
| time | Określa bezwzględny czas przerwy w sekundach lub milisekund. Przykłady prawidłowych wartości to 2S i 500 | Optional (Opcjonalność) |

| Naprężeni | Opis |
|----------|-------------|
| Brak lub nie podano wartości | 0 MS |
| x — słabe | 250 MS |
| lekko | 500 ms |
| Średniookresow | 750 ms |
| najwyższy | 1000 MS |
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

elementy `p` i `s` są używane do oznaczania akapitów i zdań odpowiednio. W przypadku braku tych elementów usługa zamiany tekstu na mowę automatycznie określa strukturę dokumentu SSML.

Element `p` może zawierać tekst i następujące elementy: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `sub`, `mstts:express-as`i `s`.

Element `s` może zawierać tekst i następujące elementy: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `mstts:express-as`i `sub`.

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

Element `ph` jest używany do wymowy fonetycznej w dokumentach SSML. Element `ph` może zawierać tylko tekst, nie ma innych elementów. Zawsze dostarczaj mowę do odczytu przez człowieka jako rezerwowe.

Alfabety fonetyczne składają się z telefonów, które składają się z liter, cyfr lub znaków, czasami w połączeniu. Każdy telefon opisuje unikatowy dźwięk mowy. Jest to w przeciwieństwie do alfabetu łacińskiego, gdzie jakakolwiek litera może reprezentować wiele dźwięków wypowiadanych. Rozważ różne wymowy literę "c" w słowach "Candy" i "zaprzestanie" albo różne wymowy kombinacji liter "TH" w słowach "rzecz" i "te".

**Składnia**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| alfabetu | Określa alfabet fonetyczny, który ma być używany podczas syntezowania wymowy ciągu w atrybucie `ph`. Ciąg określający alfabet musi być określony małymi literami. Poniżej przedstawiono możliwe litery, które można określić.<ul><li>IPA &ndash; Międzynarodowy alfabet fonetyczny</li><li>&ndash; SAPI Speech API zestaw telefonu</li><li>Uniwersalny zestaw telefonów &ndash; UPS</li></ul>Litera ma zastosowanie tylko do fonem w elemencie. Aby uzyskać więcej informacji, zobacz [odwołanie do alfabetu fonetycznego](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx). | Optional (Opcjonalność) |
| pH | Ciąg zawierający telefony, które określają wymowę wyrazu w `phoneme` elementu. Jeśli określony ciąg zawiera nierozpoznane telefony, usługa zamiany tekstu na mowę (TTS) odrzuci cały dokument SSML i nie wygeneruje żadnych danych wyjściowych mowy określonych w dokumencie. | Wymagany, jeśli jest używany fonemów. |

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

Element `prosody` służy do określania zmian wysokości, countour, zakresu, szybkości, czasu trwania i woluminu dla danych wyjściowych zamiany tekstu na mowę. Element `prosody` może zawierać tekst i następujące elementy: `audio`, `break`, `p`, `phoneme`, `prosody`, `say-as`, `sub`i `s`.

Ponieważ wartości atrybutów granicę prozodyczną mogą się różnić w różnych zakresach, aparat rozpoznawania mowy interpretuje przypisane wartości jako sugestię rzeczywistych wartości granicę prozodyczną wybranego głosu. Usługa zamiany tekstu na mowę lub zastępuje wartości, które nie są obsługiwane. Przykłady nieobsługiwanych wartości to wysokość 1 MHz lub objętość 120.

**Składnia**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| tonu | Wskazuje gęstość linii bazowej dla tekstu. Możesz wyrazić gęstość jako:<ul><li>Wartość bezwzględna wyrażona jako liczba, po której następuje "Hz" (Hz). Na przykład 600Hz.</li><li>Wartość względna, wyrażona jako liczba poprzedzona znakiem "+" lub "-", po której następuje "Hz" lub "St", która określa ilość, aby zmienić gęstość. Na przykład: + 80Hz lub-2st. "St" wskazuje, że jednostka zmiany to semitone, czyli połowę tonu (pół kroku) w standardowej skali Diatonic.</li><li>Stała wartość:<ul><li>x — niska</li><li>małą</li><li>Średniookresow</li><li>wysokowydajn</li><li>x — wysoka</li><li>default</li></ul></li></ul>. | Optional (Opcjonalność) |
| wybranym | Rozkład nie jest obsługiwany w przypadku głosów neuronowych. Rozkład reprezentuje zmiany w wysokości dla zawartości mowy jako tablicę elementów docelowych w określonych miejscach w danych wyjściowych mowy. Każdy element docelowy jest definiowany przez zestawy par parametrów. Na przykład: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Pierwsza wartość w każdym zestawie parametrów określa lokalizację zmiany w postaci procentu czasu trwania tekstu. Druga wartość określa wielkość, aby podnieść lub obniżyć gęstość, przy użyciu wartości względnej lub wartości wyliczenia dla skoku (zobacz `pitch`). | Optional (Opcjonalność) |
| zakresu  | Wartość, która reprezentuje zakres skoku dla tekstu. Możesz wyrazić `range` przy użyciu tych samych wartości bezwzględnych, wartości względnych lub wartości wyliczenia używanych do opisywania `pitch`. | Optional (Opcjonalność) |
| transmisji  | Wskazuje stawkę głosu tekstu. Możesz wyrazić `rate` jako:<ul><li>Wartość względna wyrażona jako liczba, która działa jako mnożnik wartości domyślnej. Na przykład wartość *1* powoduje brak zmian w szybkości. Wartość *.5* skutkuje halving szybkością. Wartość *3* powoduje przekroczenie stawki.</li><li>Stała wartość:<ul><li>x-slow</li><li>Opóźnienie</li><li>Średniookresow</li><li>Fast</li><li>x — Fast</li><li>default</li></ul></li></ul> | Optional (Opcjonalność) |
| duration  | Okres, który powinien upłynąć, gdy usługa synteza mowy (TTS) odczytuje tekst w sekundach lub milisekundach. Na przykład *2S* lub *1800ms*. | Optional (Opcjonalność) |
| wolumin  | Wskazuje poziom głośności głosu. Wolumin można wyrazić jako:<ul><li>Wartość bezwzględna wyrażona jako liczba z zakresu od 0,0 do 100,0, od *najcisza* do *głośnie*. Na przykład 75. Wartość domyślna to 100,0.</li><li>Wartość względna wyrażona jako liczba poprzedzona znakiem "+" lub "-", która określa wielkość zmiany woluminu. Na przykład + 10 lub-5,5.</li><li>Stała wartość:<ul><li>automatycznie</li><li>x-soft</li><li>rezerwacje</li><li>Średniookresow</li><li>głośn</li><li>x-głośne</li><li>default</li></ul></li></ul> | Optional (Opcjonalność) |

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
## <a name="say-as-element"></a>"Powiedz" jako element  

`say-as` jest opcjonalny element, który wskazuje typ zawartości (na przykład liczbę lub datę) tekstu elementu. Zapewnia to wskazówkę dotyczącą aparatu syntezy mowy dotyczącą sposobu wymawiania tekstu. 

**Składnia**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| Interpretuj jako | Wskazuje typ zawartości tekstu elementu. Aby zapoznać się z listą typów, zobacz poniższą tabelę. | Wymagane |
| format | Zawiera dodatkowe informacje na temat precyzyjnego formatowania tekstu elementu dla typów zawartości, które mogą mieć niejednoznaczne formaty. SSML definiuje formaty dla typów zawartości, które ich używają (patrz tabela poniżej). | Optional (Opcjonalność) |
| formant | Wskazuje poziom szczegółowości, który ma być wypowiadany. Na przykład ten atrybut może zażądać, aby aparat syntezy mowy wymawiał znaki interpunkcyjne. Nie zdefiniowano żadnych standardowych wartości dla `detail`. | Optional (Opcjonalność) |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Poniżej przedstawiono obsługiwane typy zawartości dla atrybutów `interpret-as` i `format`. Uwzględnij atrybut `format` tylko wtedy, gdy `interpret-as` jest ustawiona na datę i godzinę.

| Interpretuj jako | format | Błędne |
|--------------|--------|----------------|
| Ulica | | Tekst jest wymawiany jako adres. Aparat syntezy mowy mówi:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Podobnie jak w przypadku 150th sąd północno-wschodnia (Waszyngton). |
| Kardynalność, numer | | Tekst jest wymawiany jako numer kardynalny. Aparat syntezy mowy mówi:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Jak "Istnieją trzy alternatywy". |
| znaki, pisownia | | Tekst jest wymawiany jako pojedyncze litery (wypisane). Aparat syntezy mowy mówi:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Jako "T E S T". |
| date  | DMY, MDR, YMD, YDM, ym, my, MD, DM, d, m, y | Tekst jest wymawiany jako Data. Atrybut `format` określa format daty (*d = Day, m = month i y = Year*). Aparat syntezy mowy mówi:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Jako "dzisiaj jest Nineteenth październik 2016." |
| cyfry, number_digit | | Tekst jest wymawiany jako sekwencja pojedynczych cyfr. Aparat syntezy mowy mówi:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Jako "1 2 3 4 5 6 7 8 9". |
| część | | Tekst jest wymawiany jako liczba ułamkowa. Aparat syntezy mowy mówi:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Jako "trzy osiem cala". |
| ordinal  | | Tekst jest wymawiany jako numer porządkowy. Aparat syntezy mowy mówi:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Jako "Wybierz trzecią opcję". |
| Central  | | Tekst jest wymawiany jako numer telefonu. Atrybut `format` może zawierać cyfry, które reprezentują kod kraju. Na przykład "1" dla Stany Zjednoczone lub "39" dla Włoch. Aparat syntezy mowy może korzystać z tych informacji w celu podzielenia wymowy numeru telefonu. Numer telefonu może zawierać również kod kraju, a jeśli tak, ma pierwszeństwo przed kodem kraju w `format`. Aparat syntezy mowy mówi:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Jako "mój numer jest kodem obszaru 8 8 8 5 5 5 1 2 1 2." |
| time | hms12, hms24 | Tekst jest wymawiany jako czas. Atrybut `format` określa, czy czas jest określony przy użyciu zegara 12-godzinnego (hms12), czy 24-godzinnego zegara (hms24). Użyj dwukropka do oddzielenia liczb reprezentujących godziny, minuty i sekundy. Poniżej przedstawiono prawidłowe przykłady czasu: 12:35, 1:14:32, 08:15 i 02:50:45. Aparat syntezy mowy mówi:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Jako "pozostała część" uczenia w czterech A M ". |

**Użycie**

Element `say-as` może zawierać tylko tekst.

**Przykład**

Aparat syntezy mowy mówi Poniższy przykład jako "pierwsze żądanie było w jednym pokoju od Nineteenth października 20 10 z wczesnym nadejściem o 12 35 P M".
 
```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
    <p>
    Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
    on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
    </p>
</speak>
```


## <a name="add-recorded-audio"></a>Dodaj zarejestrowane audio

`audio` to opcjonalny element, który umożliwia wstawianie audio MP3 do dokumentu SSML. Treść elementu audio może zawierać zwykły tekst lub SSML znaczników, które są wymawiane, jeśli plik dźwiękowy jest niedostępny lub nieosiągalny. Ponadto element `audio` może zawierać tekst i następujące elementy: `audio`, `break`, `p`, `s`, `phoneme`, `prosody`, `say-as`i `sub`.

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

Element `mstts:backgroundaudio` umożliwia dodanie dźwięku w tle do dokumentów SSML (lub zamieszanie pliku dźwiękowego z funkcją zamiany tekstu na mowę). Za pomocą `mstts:backgroundaudio` można zapętlać plik audio w tle, zanikać na początku tekstu na mowę i stopniowo przechodzić na koniec zamiany tekstu na mowę.

Jeśli podany dźwięk w tle jest krótszy niż tekst-do-mowę lub zanikanie, spowoduje to zapętlenie. Jeśli jest dłuższa niż Zamiana tekstu na mowę, zostanie zatrzymana po zakończeniu zanikania.

Dozwolony jest tylko jeden plik audio w tle dla dokumentu SSML. Można jednak przeplatać `audio` Tagi w obrębie elementu `voice`, aby dodać dodatkowe audio do dokumentu SSML.

**Składnia**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| SRC | Określa lokalizację/adres URL pliku dźwiękowego w tle. | Wymagany w przypadku używania dźwięku w tle w dokumencie SSML. |
| wolumin | Określa wolumin pliku dźwiękowego w tle. **Akceptowane wartości**: `0` do `100` włącznie. Wartość domyślna to `1`. | Optional (Opcjonalność) |
| Rozjaśnianie | Określa czas zanikania dźwięku w tle w milisekundach. Wartość domyślna to `0`, która jest równoważna brak zanikania w. **Akceptowane wartości**: `0` do `10000` włącznie.  | Optional (Opcjonalność) |
| fadeout | Określa czas zanikania dźwięku w tle w milisekundach. Wartość domyślna to `0`, która jest równoważna brak zanikania. **Akceptowane wartości**: `0` do `10000` włącznie.  | Optional (Opcjonalność) |

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
