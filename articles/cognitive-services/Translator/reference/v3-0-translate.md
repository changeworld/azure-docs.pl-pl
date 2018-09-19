---
title: Interfejs API tekstu usługi Translator Translate — metoda
titleSuffix: Azure Cognitive Services
description: Metoda Translator tekstu interfejsu API tłumaczenia.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 1841730a39d29c5fe1f3451b7614818e924b339f
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128761"
---
# <a name="translator-text-api-30-translate"></a>API 3.0 tekstu usługi Translator: tłumaczenie

Tłumaczy tekst.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` limit czasu żądania:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Żądania, parametry przekazane w ciągu zapytania są:

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>wersja interfejsu API</td>
    <td>*Wymagany parametr*.<br/>Wersja interfejsu API zażądane przez klienta. Wartość musi być `3.0`.</td>
  </tr>
  <tr>
    <td>z</td>
    <td>*Opcjonalny parametr*.<br/>Określa język tekstu wejściowego. Dowiedz się, jakie języki są dostępne do tłumaczenia z przez wyszukanie [obsługiwane języki](.\v3-0-languages.md) przy użyciu `translation` zakresu. Jeśli `from` parametr nie zostanie określony, funkcja automatycznego wykrywania języka jest stosowane w celu określenia języka źródłowego.</td>
  </tr>
  <tr>
    <td>na</td>
    <td>*Wymagany parametr*.<br/>Określa język tekstu wyjściowego. Język docelowy musi mieć jedną z [obsługiwane języki](.\v3-0-languages.md) objęte `translation` zakresu. Na przykład użyć `to=de` do tłumaczenia na język niemiecki.<br/>Istnieje możliwość translacji jednocześnie do wielu języków, powtarzając parametr ciągu zapytania. Na przykład użyć `to=de&to=it` do translacji niemieckim i włoskim.</td>
  </tr>
  <tr>
    <td>textType</td>
    <td>*Opcjonalny parametr*.<br/>Określa, czy tekst tłumaczony jest zwykły tekst lub tekstu w formacie HTML. Kod HTML musi być elementem pełną, poprawnie sformułowany. Możliwe wartości to: `plain` (ustawienie domyślne) lub `html`.</td>
  </tr>
  <tr>
    <td>category</td>
    <td>*Opcjonalny parametr*.<br/>Ciąg określający kategoria tłumaczenia (domena). Ten parametr umożliwia uzyskiwanie tłumaczenia niestandardowych utworzonych za pomocą systemu [niestandardowe w usłudze Translator](../customization.md). Wartość domyślna to: `general`.</td>
  </tr>
  <tr>
    <td>ProfanityAction</td>
    <td>*Opcjonalny parametr*.<br/>Określa, jak profanities powinny być traktowane w translacji. Możliwe wartości to: `NoAction` (ustawienie domyślne), `Marked` lub `Deleted`. Aby poznać sposoby traktować wulgaryzmów, zobacz [obsługi wulgaryzmów](#handle-profanity).</td>
  </tr>
  <tr>
    <td>ProfanityMarker</td>
    <td>*Opcjonalny parametr*.<br/>Określa, jak profanities powinien być oznaczony w translacji. Możliwe wartości to: `Asterisk` (ustawienie domyślne) lub `Tag`. Aby poznać sposoby traktować wulgaryzmów, zobacz [obsługi wulgaryzmów](#handle-profanity).</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td>*Opcjonalny parametr*.<br/>Określa, czy mają zostać dołączone wyrównanie rzutowania z tekst źródłowy do przetłumaczonego tekstu. Możliwe wartości to: `true` lub `false` (ustawienie domyślne). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td>*Opcjonalny parametr*.<br/>Określa, czy dołączać granice zdanie do tekstu wejściowego i przetłumaczonego tekstu. Możliwe wartości to: `true` lub `false` (ustawienie domyślne).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td>*Opcjonalny parametr*.<br/>Określa język bazowy, jeśli nie można zidentyfikować język tekstu wejściowego. Automatyczne wykrywanie języka jest stosowany podczas `from` parametr zostanie pominięty. Jeśli wykrywanie nie powiedzie się, `suggestedFrom` języka zostanie przyjęta wartość.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Opcjonalny parametr*.<br/>Określa skrypt wejściowego tekstu.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Opcjonalny parametr*.<br/>Określa skrypt przetłumaczonego tekstu.</td>
  </tr>
</table> 

Nagłówki żądania obejmują:

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>_Jeden autoryzacji_<br/>_header_</td>
    <td>*Nagłówek żądania wymagane*.<br/>Zobacz [dostępne opcje uwierzytelniania](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Nagłówek żądania wymagane*.<br/>Określa typ zawartości ładunku. Możliwe wartości to: `application/json`.</td>
  </tr>
  <tr>
    <td>Długość zawartości</td>
    <td>*Nagłówek żądania wymagane*.<br/>Długość treści żądania.</td>
  </tr>
  <tr>
    <td>X ClientTraceId</td>
    <td>*Opcjonalnie*.<br/>Generowane przez klienta identyfikator GUID do unikatowego identyfikowania żądania. Możesz pominąć ten nagłówek, jeśli zawierają identyfikator śledzenia w ciągu zapytania za pomocą parametru zapytania o nazwie `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiekt JSON z właściwością ciągu o nazwie `Text`, która reprezentuje ciągu do tłumaczenia.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Obowiązują następujące ograniczenia:

* Tablica może mieć maksymalnie 25 elementów.
* Cały tekst zawarty w żądaniu nie może przekraczać 5000 znaków, łącznie ze spacjami.

## <a name="response-body"></a>Treść odpowiedzi

Odpowiedź oznaczająca Powodzenie to tablica JSON z jeden wynik dla każdego ciągu w tabeli wejściowej. Obiekt wyniku zawiera następujące właściwości:

  * `detectedLanguage`: Obiekt opisujący wykryty język za pomocą następujących właściwości:

      * `language`: Ciąg reprezentującą kod wykryty język.

      * `score`: Float wartość wskazująca pewność dotyczącą wyników. Wynik jest między zero a, oraz niskim wynikiem wskazuje niski zaufania.

    `detectedLanguage` Właściwość jest obecny w obiekcie wynikowym tylko, gdy automatyczne wykrywanie języka.

  * `translations`: Tablica wyników tłumaczenia. Rozmiar tablicy jest zgodna z liczbą języków docelowych określonych przez `to` parametr zapytania. Każdy element w tablicy zawiera:

    * `to`: Ciąg reprezentujący kod języka w języku docelowym.

    * `text`: Ciąg, dzięki czemu przetłumaczonego tekstu.

    * `transliteration`: Obiekt, zapewniając zawierające przetłumaczony tekst w skrypcie, określony przez `toScript` parametru.

      * `script`: Ciąg określający skrypt docelowego.   

      * `text`: Ciąg, zapewniając zawierające przetłumaczony tekst w skrypcie docelowego.

    `transliteration` Obiektu nie jest dołączona, jeśli transliterację, nie odbywa się.

    * `alignment`: Obiekt o jeden ciąg właściwość o nazwie `proj`, mapująca wprowadzanie tekstu do tłumaczenia. Informacje o wyrównania jest podawana tylko wtedy, gdy parametr żądania `includeAlignment` jest `true`. Wyrównanie jest zwracana jako wartość ciągu w następującym formacie: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  Dwukropek oddziela start końcowego indeksu, kreska oddziela językach i spacja oddziela słowa. O jeden wyraz może być dostosowanie zero, jeden lub wiele słów w innym języku, a wyrównany wyrażenie może być nieciągłe. Po udostępnieniu żadnych informacji wyrównanie elementu wyrównania jest pusta. Zobacz [uzyskać informacji o wyrównanie](#obtain-alignment-information) przykładem i ograniczenia.

    * `sentLen`: Obiekt zwracanie granice zdanie w tekstach wejściowych i wyjściowych.

      * `srcSentLen`: Tablicę liczb całkowitych reprezentujących długości zdań w tekście wejściowym. Długość tablicy jest liczba zdania, a wartości są długość każdego zdania.

      * `transSentLen`: Tablicę liczb całkowitych reprezentujących długości zdań w przetłumaczonego tekstu. Długość tablicy jest liczba zdania, a wartości są długość każdego zdania.

    Granice zdania są tylko włączone, gdy parametr żądania `includeSentenceLength` jest `true`.

  * `sourceText`: Obiekt o jeden ciąg właściwość o nazwie `text`, co daje tekst wejściowy w skrypcie domyślnego języka źródłowego. `sourceText` Właściwość jest tylko wtedy, gdy dane wejściowe jest wyrażona w skrypcie, który nie jest zwykle skryptu języka. Na przykład, jeśli dane wejściowe zostały arabski napisana łacińskim, następnie `sourceText.text` zostanie ten sam tekst arabski konwertowany do Emiraty skryptu.

Przykład odpowiedzi JSON znajdują się w [przykłady](#examples) sekcji.

## <a name="response-status-codes"></a>Kody stanów odpowiedzi

Poniżej przedstawiono możliwe kody stanu HTTP, które zwraca żądanie. 

<table width="100%">
  <th width="20%">Kod stanu</th>
  <th>Opis</th>
  <tr>
    <td>200</td>
    <td>Powodzenie.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>To jeden z parametrów zapytania, lub jest on nieprawidłowy. Popraw parametry żądania przed ponowieniem próby.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Nie można uwierzytelnić żądania. Sprawdź, czy poświadczenia są określonego i prawidłowy.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Żądanie nie jest autoryzowany. Sprawdź szczegóły komunikatu o błędzie. Często oznacza to, zużyte wszystkie bezpłatne tłumaczenia dostarczane z subskrypcji wersji próbnej.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Obiekt wywołujący wysyła zbyt wiele żądań.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Wystąpił nieoczekiwany błąd. Jeśli błąd będzie się powtarzać, zgłoś go: Data i godzina awarii, identyfikator żądania z nagłówka odpowiedzi `X-RequestId`oraz identyfikator klienta z nagłówka żądania `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Serwer jest tymczasowo niedostępny. Ponów żądanie. Jeśli błąd będzie się powtarzać, zgłoś go: Data i godzina awarii, identyfikator żądania z nagłówka odpowiedzi `X-RequestId`oraz identyfikator klienta z nagłówka żądania `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Przykłady

### <a name="translate-a-single-input"></a>Tłumaczenie pojedynczego danych wejściowych

Ten przykład przedstawia, jak przekształcać jednym zdaniu z angielskiego na język chiński uproszczony.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Treść odpowiedzi jest:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

`translations` Tablica zawiera jeden element, który zawiera tłumaczenie pojedynczy fragment tekstu w danych wejściowych.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Tłumaczenie pojedynczego dane wejściowe z automatycznego wykrywania języka

Ten przykład przedstawia, jak przekształcać jednym zdaniu z angielskiego na język chiński uproszczony. W żądaniu nie określono języka. Automatyczne wykrywanie języka źródłowego jest używana zamiast tego.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Treść odpowiedzi jest:

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
Odpowiedź jest podobna do odpowiedzi z poprzedniego przykładu. Ponieważ zażądano automatycznego wykrywania języka, odpowiedzi zawiera także informacje o języku wykryto tekstu wejściowego. 

### <a name="translate-with-transliteration"></a>Tłumaczenie z transliterację

Teraz można rozszerzyć poprzedni przykład, dodając transliterację. Następujące żądanie prosi o Tłumaczenie chińskie zapisywane w skrypcie alfabetu łacińskiego.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Treść odpowiedzi jest:

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"text":"nǐ hǎo , nǐ jiào shén me míng zì ？","script":"Latn"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

Wynik tłumaczenia zawiera teraz `transliteration` właściwość, która zapewnia przetłumaczony tekst przy użyciu znaków alfabetu łacińskiego.

### <a name="translate-multiple-pieces-of-text"></a>Tłumaczenie wiele fragmentów tekstu

Tłumaczenie jednocześnie wielu ciągów jest po prostu kwestią określania tablicę ciągów w treści żądania.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

---

Treść odpowiedzi jest:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Tłumaczenie na wiele języków

Ten przykład przedstawia, jak przekształcać to samo wejście do kilku języków w jednym żądaniu.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Treść odpowiedzi jest:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>Obsługa niestosownych wyrażeń

Zwykle usługa Translator zachowa wulgaryzmów, który znajduje się w źródle w tłumaczeniu. Stopień wulgaryzmów i kontekst, który sprawia, że wyrazy obsceniczne różnią się od kultury, a w wyniku stopień wulgaryzmów w języku docelowym może być większy lub mniejsze.

Jeśli chcesz uniknąć wulgaryzmów w tłumaczeniu, niezależnie od obecności wulgaryzmów w tekście źródłowym, można użyć wulgaryzmów opcję filtrowania. Opcja można wybrać, czy chcesz zobaczyć wulgaryzmów usunięte, czy chcesz oznaczyć profanities przy użyciu odpowiednich tagów (dzięki któremu można dodać własne przetwarzanie końcowe) lub chcesz, aby nie podjęto żadnej akcji. Akceptowane wartości `ProfanityAction` są `Deleted`, `Marked` i `NoAction` (ustawienie domyślne).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Akcja</th>
  <tr>
    <td>`NoAction`</td>
    <td>To zachowanie domyślne. Wulgaryzmów zostaną spełnione ze źródła do docelowego.<br/><br/>
    **Przykład źródłowej (japoński)**: 彼はジャッカスです。<br/>
    **Przykład tłumaczenie (język angielski)**: jest on jackass.
    </td>
  </tr>
  <tr>
    <td>`Deleted`</td>
    <td>Wyrazy obsceniczne zostaną usunięte z danych wyjściowych bez zastępowania.<br/><br/>
    **Przykład źródłowej (japoński)**: 彼はジャッカスです。<br/>
    **Przykład tłumaczenie (język angielski)**: jest on.
    </td>
  </tr>
  <tr>
    <td>`Marked`</td>
    <td>Obsceniczne wyrazów są zastępowane przez znacznika w danych wyjściowych. Zależy od znacznika `ProfanityMarker` parametru.<br/><br/>
Aby uzyskać `ProfanityMarker=Asterisk`, obsceniczne wyrazów są zastępowane `***`:<br/>
    **Przykład źródłowej (japoński)**: 彼はジャッカスです。<br/>
    **Przykład tłumaczenie (język angielski)**: jest on \* \* \*.<br/><br/>
Aby uzyskać `ProfanityMarker=Tag`, obsceniczne wyrazy są ujęte w znaczników XML &lt;wulgaryzmów&gt; i &lt;/profanity&gt;:<br/>
    **Przykład źródłowej (japoński)**: 彼はジャッカスです。<br/>
    **Przykład tłumaczenie (język angielski)**: jest on &lt;wulgaryzmów&gt;jackass&lt;/profanity&gt;.
  </tr>
</table> 

Na przykład:

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

Spowoduje to zwrócenie:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Porównaj z:

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

Zwraca tego ostatniego żądania:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Tłumacz zawartości przy użyciu znaczników i zdecydować, co jest tłumaczony

Jest to często tłumaczone zawartość, która zawiera kod znaczników, np. zawartość ze strony HTML lub zawartości z dokumentu XML. Parametr zapytania `textType=html` podczas tłumaczenia zawartości przy użyciu tagów. Ponadto warto czasami spod określonej zawartości tłumaczenia. Można użyć atrybutu `class=notranslate` do określania zawartości, które powinny pozostać w jego oryginalnym języku. W poniższym przykładzie zawartości w pierwszym `div` element będzie nie można przetłumaczyć podczas zawartości w drugim `div` element, który zostanie zamienione.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Oto przykładowe żądanie, aby zilustrować.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

---

Odpowiedź jest:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Uzyskaj informacje wyrównania

Aby otrzymywać informacje wyrównanie, należy określić `includeAlignment=true` w ciągu zapytania.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation.'}]"
```

---

Odpowiedź jest:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

Informacje o wyrównanie zaczyna się od `0:2-0:1`, co oznacza, że pierwsze trzy znaki w tekście źródłowym (`The`) mapę, aby dwa pierwsze znaki w przetłumaczonego tekstu (`La`).

Należy zauważyć następujące ograniczenia:

* Wyrównanie jest zwracany tylko wtedy dla podzbioru kierunki:
  - z języka angielskiego na dowolnym innym języku;
  - z dowolnego innego języka na język angielski, chiński uproszczony, chiński tradycyjny i łotewski do języka angielskiego;
  - w języku japońskim z koreański, japoński lub koreański.
* Nie otrzymasz wyrównanie, jeśli zdanie jest konserwowane tłumaczenia. Przykład zwięzłych tłumaczenia jest "To jest test", "Kocham Cię" i inne zdania wysokiej częstotliwości.

### <a name="obtain-sentence-boundaries"></a>Uzyskaj granice zdania

Aby otrzymywać informacje o długości zdanie w tekście źródłowym i przetłumaczonego tekstu, należy określić `includeSentenceLength=true` w ciągu zapytania.

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

---

Odpowiedź jest:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>Tłumaczenie z dynamiczny słownik

Jeśli znasz już tłumaczeń, które mają zostać zastosowane do wyrazu lub frazy, możesz podać go jako kod znaczników w żądaniu. Dynamiczny słownik jest tylko bezpieczne dla rzeczowniki złożone, np. prawidłowe nazwy i nazwy produktu.

Znaczniki umożliwiają określanie wartości używa następującej składni.

``` 
<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>
```

Na przykład należy wziąć pod uwagę zdanie w angielskim "word wordomatic jest wpis w słowniku." Aby zachować wyraz _wordomatic_ w tłumaczeniu, Wyślij żądanie:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Wynik jest:

```
[
    {
        "translations":[
            {"text":"Das Wort "wordomatic" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Ta funkcja działa tak samo, przy użyciu `textType=text` lub `textType=html`. Funkcja powinny być używane rzadko. Odpowiednie i znacznie lepiej sposób dostosowywania tłumaczenia jest przy użyciu niestandardowych w usłudze Translator. Niestandardowe w usłudze Translator sprawia, że pełne wykorzystanie kontekstu i prawdopodobieństwa statystycznych. Jeśli masz lub pozwolić sobie utworzyć dane szkoleniowe, które z miejsca pracy lub frazę w kontekście otrzymasz znacznie lepsze wyniki. [Dowiedz się więcej o niestandardowych w usłudze Translator](../customization.md).
 





