---
title: Język znaczników synteza mowy (SSML) — usługi mowy
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
ms.openlocfilehash: 8285a76f8cd07863874f9c8e8eebe96f1cb968dd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604818"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Język znaczników syntezy mowy (SSML)

Mowy syntezy Markup Language (SSML) to język znaczników oparty na formacie XML, które umożliwiają deweloperom określenie sposobu wprowadzania tekstu jest konwertowana na syntezatora mowy przy użyciu usługi zamiany tekstu na mowę. W porównaniu do zwykłego tekstu, SSML umożliwia deweloperom dostosować wysokość wymowa, wypowiedzi szybkości, woluminów i nie tylko zamiany tekstu na mowę danych wyjściowych. Normalne znaki interpunkcyjne, takie jak przytrzymanie po upływie lub za pomocą poprawne maksimów, gdy dowolne zdanie kończy się znakiem zapytania są realizowane automatycznie.

Implementacja usługi mowy SSML opiera się na World Wide Web Consortium [mowy synteza znaczników języka w wersji 1.0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Liczba znaków chińskich, japońskiej i koreańskiej jako dwa znaki na potrzeby rozliczeń. Aby uzyskać więcej informacji, zobacz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standardowe, neuronowych i niestandardowe głosów

Wybierać standardowych i neuronowych głosów lub utworzyć niestandardowe głosu unikatowe dla produktu lub marki. ponad 75 standardowa głosy są dostępne w ponad 45 języków i ustawień regionalnych i 5 neuronowych głosy są dostępne w 4 języków i ustawień regionalnych. Aby uzyskać pełną listę obsługiwanych języków, ustawień regionalnych i głosów (neuronowych i standardowa), zobacz [języki](language-support.md).

Aby dowiedzieć się więcej na temat standardu neuronowej i głosów niestandardowych, zobacz [Przegląd zamiany tekstu na mowę](text-to-speech.md).

## <a name="supported-ssml-elements"></a>Obsługiwane elementy SSML

Każdy dokument SSML jest tworzony za pomocą elementów SSML (lub tagów). Te elementy są używane do dostosowania pomysłu, prosody, woluminów i. W poniższych sekcjach opisano sposób użycia każdego elementu, a gdy element jest wymagane lub opcjonalne.  

> [!IMPORTANT]
> Nie zapomnij o użycie podwójnego cudzysłowu wokół wartości atrybutów. Standardy dla formatu XML z dobrze sformułowany i prawidłowy wymaga wartości atrybutów do być ujęta w znaki podwójnego cudzysłowu. Na przykład `<prosody volume="90">` jest dobrze sformułowany i prawidłowy element, ale `<prosody volume=90>` nie jest. SSML nie może rozpoznać wartości atrybutów, które nie znajdują się w cudzysłowy.

## <a name="create-an-ssml-document"></a>Utwórz dokument SSML

`speak` jest elementem głównym i jest **wymagane** dla wszystkich dokumentów SSML. `speak` Element zawiera ważne informacje, takie jak wersja, języka i definicji słownictwa znaczników.

**Składnia**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| version | Wskazuje wersję specyfikacji SSML używaną do interpretacji parametru znaczników dokumentu. Bieżąca wersja to 1.0. | Wymagane |
| xml:lang | Określa język dokumentu głównego. Wartość może zawierać kod, małe litery, dwuliterowych język (na przykład **en**), lub kod języka i kraju/regionu wielkie litery (na przykład **en US**). | Wymagane |
| xmlns | Określa identyfikator URI dokumentu, który definiuje znaczników słownictwa dokumentu SSML (typy elementu i nazwach atrybutów). Bieżący identyfikator URI jest https://www.w3.org/2001/10/synthesis. | Wymagane |

## <a name="choose-a-voice-for-text-to-speech"></a>Wybieranie głosowe zamiany tekstu na mowę

`voice` Element jest wymagany. Służy do określania głosowej, która jest używana do zamiany tekstu na mowę.

**Składnia**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| name | Identyfikuje głosu używany do zamiany tekstu na mowę w danych wyjściowych. Aby uzyskać pełną listę obsługiwanych głosów, zobacz [języki](language-support.md#text-to-speech). | Wymagane |

**Przykład**

> [!NOTE]
> W tym przykładzie użyto `en-US-Jessa24kRUS` głosu. Aby uzyskać pełną listę obsługiwanych głosów, zobacz [języki](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Użyj wielu głosów

W ramach `speak` elementu, można określić wiele głosów zamiany tekstu na mowę w danych wyjściowych. Tych głosów może być w różnych językach. Dla każdego połączenia głosowe, tekst musi być ujęte w `voice` elementu.

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| name | Identyfikuje głosu używany do zamiany tekstu na mowę w danych wyjściowych. Aby uzyskać pełną listę obsługiwanych głosów, zobacz [języki](language-support.md#text-to-speech). | Wymagane |

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

## <a name="adjust-speaking-styles"></a>Dostosuj style wypowiedzi

> [!IMPORTANT]
> Ta funkcja działa tylko z neuronowych głosów.

Domyślnie usługa zamiany tekstu na mowę syntetyzuje tekstu przy użyciu neutralne stylu wypowiedzi dla standardowych i neuronowych głosów. Głosy neuronowych umożliwia dostosowanie stylu wypowiedzi wyrażenia cheerfulness wprowadzenia empatii i opinii przy użyciu `<mstts:express-as>` elementu. Ten element jest opcjonalny unikatowe dla usług przetwarzania mowy platformy Azure.

Obecnie wypowiedzi dostosowania stylu są obsługiwane w przypadku tych głosów neuronowej:
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

Zmiany są stosowane na poziomie pojedynczych zdań i zależą od style głosowej. Jeśli styl nie jest obsługiwany, usługa zwraca mowy w domyślnym neutralne wymowy.

**Składnia**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| type | Określa styl wypowiedzi. Obecnie wymowy są określonych głosu. | Wymagane, jeśli jest to dostosowanie stylu wypowiedzi neuronowych głosu. Jeśli przy użyciu `mstts:express-as`, a następnie typ musi być podana. Jeśli nie podano nieprawidłową wartość, ten element zostanie zignorowany. |

Ta tabela służy do określenia wypowiedzi style, które są obsługiwane w przypadku każdego neuronowych głosu.

| Połączenia głosowe | Typ | Opis |
|-------|------|-------------|
| `en-US-JessaNeural` | type=`cheerful` | Wyraża rozpoznawania emocji, pozytywny i wszystkiego |
| | type=`empathy` | Wyraża poczucie opiekowanie i opis |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Określa sygnał formalne podobne do transmisji wiadomości |
| | type=`sentiment` | Wywołuje touching wiadomości lub wątku |

**Przykład**

Ten fragment SSML ilustruje sposób, w jaki `<mstts:express-as>` element służy do zmiany stylu wypowiedzi do `cheerful`.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Dodawanie lub usuwanie pause/break

Użyj `break` elementów, aby wstawić wstrzymuje (lub podziały) między wyrazami lub uniemożliwić wstrzymuje automatycznie dodany przez usługę zamiany tekstu na mowę.

> [!NOTE]
> Użyj tego elementu, aby zastąpić domyślne zachowanie zamiany tekstu na mowę (TTS) dla wyrazu lub frazy, jeśli nienaturalnym brzmi syntezatora mowy dla tego wyrazu lub frazy. Ustaw `strength` do `none` zapobiegające podział akcent, który jest wstawiany automatycznie przez usługę tekstu na mowę.

**Składnia**

```xml
<break strength="string" />
<break time="string" />
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| siła | Zawiera wartość względna czasu trwania wstrzymania, przy użyciu jednej z następujących wartości:<ul><li>Brak</li><li>x-weak</li><li>słabe</li><li>Średni (domyślnie)</li><li>Silne</li><li>x-strong</li></ul> | Optional (Opcjonalność) |
| time | Określa bezwzględny czas trwania wstrzymania w ciągu kilku sekund i milisekund. Prawidłowe wartości należą do nich 2s do 500 | Optional (Opcjonalność) |

| siła | Opis |
|----------|-------------|
| Brak, czy podano żadnej wartości | 0 ms |
| x-weak | 250 ms |
| słabe | 500 ms |
| Średni | 750 ms |
| Silne | 1000 ms |
| x-strong | 1250 ms |


**Przykład**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Określ akapitów i zdań

`p` i `s` elementy są używane w celu oznaczenia akapitów i zdania, odpowiednio. W przypadku braku tych elementów zamiany tekstu na mowę usługi automatycznie określa strukturę dokumentu SSML.

`p` Element może zawierać tekst i następujące elementy: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `sub`, `mstts:express-as`, i `s`.

`s` Element może zawierać tekst i następujące elementy: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `mstts:express-as`, i `sub`.

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

## <a name="use-phonemes-to-improve-pronunciation"></a>Umożliwia poprawę Wymowa fonemów

`ph` Element służy do wymowa fonetyczna w dokumentach SSML. `ph` Może zawierać tylko tekst, nie inne elementy. Zawsze podawać mowy zrozumiałą dla użytkownika jako rezerwowa metoda.

Fonetyczny alfabety składają się z telefonów, które składają się z litery, cyfry lub znaki, czasami w połączeniu. Każdy Telefon w tym artykule opisano unikatowy dźwięk mowy. Jest to w przeciwieństwie do alfabetu łacińskiego, których wszystkie litery może reprezentować wielu mówionego dźwięku. Należy wziąć pod uwagę różne wymowy literę "c" słowa "jajek" i "zaprzestanie" lub inną wymowy kombinacji literę "th" w wyrazy "rzecz" oraz "".

**Składnia**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| alfabetu | Określa alfabet fonetyczny do użycia podczas Syntetyzujące Wymowa ciągu w `ph` atrybutu. Ciąg określający alfabetu musi być określona w małe litery. Poniżej przedstawiono możliwe alfabetów, które można określić.<ul><li>archiwum IPA &ndash; alfabet fonetyczny International</li><li>nieokreślone &ndash; Speech API telefon ustawiony</li><li>UPS &ndash; uniwersalnym ustawić telefonu</li></ul>Alfabetu dotyczy tylko phoneme w elemencie. Aby uzyskać więcej informacji, zobacz [odwołania alfabet fonetyczny](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx). | Optional (Opcjonalność) |
| pH | Ciąg zawierający telefonów, które określają Wymowa wyraz `phoneme` elementu. Jeśli określony ciąg zawiera nierozpoznany telefonów, zamiany tekstu na mowę usługi (TTS) odrzuca cały dokument SSML i tworzy Brak mowie określona w dokumencie. | Wymagany, jeśli za pomocą fonemów. |

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

## <a name="adjust-prosody"></a>Dostosuj prosody

`prosody` Element jest używany do określania zmiany pomysłu, countour, zakres, szybkość, czas trwania i wolumin na potrzeby zamiany tekstu na mowę danych wyjściowych. `prosody` Element może zawierać tekst i następujące elementy: `audio`, `break`, `p`, `phoneme`, `prosody`, `say-as`, `sub`, i `s`.

Ponieważ wartości atrybutów akcent mogą się różnić w różnych, rozpoznawania mowy interpretuje przypisane wartości jako sugestia co powinna być rzeczywiste wartości akcent wybranego głosu. Zamiany tekstu na mowę usługa ogranicza lub zastępuje wartości, które nie są obsługiwane. Przykładowe wartości nieobsługiwanego są wysokość 1 MHz lub ilości 120.

**Składnia**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| Wysokość | Wskazuje pomysłu punktu odniesienia dla tekstu. Mogą wyrazić pomysłu jako:<ul><li>Wartość bezwzględna wyrażona jako liczba następuje "Hz" (Hz). Na przykład, 600Hz.</li><li>Wartość względna wyrażona jako liczba poprzedzony przez "+" lub "-" i następuje "Hz" lub "st", który określa ilość można zmienić wysokość. Na przykład: + 80 Hz lub - 2st. "st" oznacza, że jednostka zmian jest semitone, czyli połowę sygnał (jest to krok w połowie) na standardowy skali diatonic.</li><li>Stała wartość:<ul><li>x-low</li><li>Niska</li><li>Średni</li><li>Wysoka</li><li>x-high</li><li>default</li></ul></li></ul>. | Optional (Opcjonalność) |
| ROZKŁAD | Rozkład nie jest obsługiwane w przypadku neuronowych głosów. Rozkład reprezentuje zmiany w wysokość mowy jako tablica obiektów docelowych w określonym czasie pozycjach w danych wyjściowych mowy. Każdego obiektu docelowego jest definiowany przez zestawy par parametru. Na przykład: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Pierwsza wartość w każdym zestawie parametrów określa lokalizację zmiany pomysłu, jako wartość procentowa czasu trwania tekstu. Druga wartość określa ilość, aby podnieść lub obniżyć pomysłu, za pomocą względnej wartości lub wartości wyliczenia gęstość (zobacz `pitch`). | Optional (Opcjonalność) |
| Zakres  | Wartość, która przedstawia szereg wysokość tekstu. Może wyrazić `range` przy użyciu tej samej wartości bezwzględne, względne wartości lub wartości wyliczenia używane do opisywania `pitch`. | Optional (Opcjonalność) |
| Kurs  | Wskazuje częstotliwość wypowiedzi tekstu. Może wyrazić `rate` jako:<ul><li>Wartość względna wyrażone jako liczba, która działa jako mnożnik domyślnych. Na przykład, wartość *1* skutkuje nie zmieni się częstotliwość. Wartość *.5* skutkuje halving stawki. Wartość *3* skutkuje tripling stawki.</li><li>Stała wartość:<ul><li>x-slow</li><li>Powolne</li><li>Średni</li><li>Szybka</li><li>x-fast</li><li>default</li></ul></li></ul> | Optional (Opcjonalność) |
| duration  | Czas, który powinien upłynąć podczas mowy usługa syntezy (TTS) odczytuje tekst w ciągu kilku sekund i milisekund. Na przykład *2s* lub *1800ms*. | Optional (Opcjonalność) |
| wolumin  | Wskazuje poziom głośności wypowiedzi głosu. Mogą wyrazić woluminu jako:<ul><li>Wartość bezwzględna wyrażone jako liczba z zakresu od 0,0 do 100,0 z *najcichsze* do *najgłośniejszymi*. Na przykład, 75. Wartość domyślna to 100,0.</li><li>Wartość względna wyrażona jako liczba poprzedzony przez "+" lub "-", który określa ilość, aby zmienić wolumin. Na przykład 10 lub-5.5.</li><li>Stała wartość:<ul><li>dyskretnej</li><li>x-soft</li><li>nietrwałe</li><li>Średni</li><li>Zezwalaj</li><li>x-loud</li><li>default</li></ul></li></ul> | Optional (Opcjonalność) |

### <a name="change-speaking-rate"></a>Zmień częstotliwość wypowiedzi

Wypowiedzi współczynnik można zastosować do standardowego głosów na poziomie zdania lub word. Wypowiedzi współczynnik mogą być stosowane tylko do neuronowych głosów na poziomie pojedynczych zdań.

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

Można zastosować zmian woluminu do standardowego głosów na poziomie zdania lub word. Zmiany głośności mogą być stosowane tylko do neuronowych głosów na poziomie pojedynczych zdań.

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

Można zastosować zmian pomysłu do standardowego głosów na poziomie zdania lub word. Gęstość zmiany mogą być stosowane tylko do neuronowych głosów na poziomie pojedynczych zdań.

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
> Gęstość konturu zmiany nie są obsługiwane przy użyciu głosów neuronowych.

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

## <a name="next-steps"></a>Kolejne kroki

* [Obsługa języków: głosów, ustawień regionalnych, języków](language-support.md)
