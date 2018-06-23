---
title: Tekst Microsoft Translator interfejsu API Translate — metoda | Dokumentacja firmy Microsoft
titleSuffix: Cognitive Services
description: Użyj metody tłumaczenie Microsoft Translator tekst interfejsu API.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: d8d5e1e2fac747fa733f1d92c08008b7eac2a1bc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349704"
---
# <a name="text-api-30-translate"></a>Tekst interfejsu API 3.0: tłumaczenia

Wykonuje translację tekstu.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` żądanie:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Żądania są parametry przekazywane w ciągu kwerendy:

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>wersja interfejsu API</td>
    <td>*Wymagany parametr*.<br/>Wersja interfejsu API żądanego przez klienta. Wartość musi być `3.0`.</td>
  </tr>
  <tr>
    <td>z</td>
    <td>*Opcjonalny parametr*.<br/>Określa język tekstu wejściowego. Znajdź języki są dostępne do tłumaczenia z wyszukując [obsługiwanych języków](.\v3-0-languages.md) przy użyciu `translation` zakresu. Jeśli `from` parametr nie zostanie określony, automatyczne wykrywanie języka jest stosowane w celu określenia języka źródłowego.</td>
  </tr>
  <tr>
    <td>na</td>
    <td>*Wymagany parametr*.<br/>Określa język tekstu wyjściowego. Język docelowy musi mieć jedną z [obsługiwanych języków](.\v3-0-languages.md) objęte `translation` zakresu. Na przykład użyć `to=de` do tłumaczenia na język niemiecki.<br/>Istnieje możliwość tłumaczenie jednocześnie do wielu języków, powtarzając parametr w ciągu zapytania. Na przykład użyć `to=de&to=it` do tłumaczenia na język niemiecki i włoski.</td>
  </tr>
  <tr>
    <td>textType</td>
    <td>*Opcjonalny parametr*.<br/>Określa, czy tekst tłumaczonym jest zwykły tekst lub tekstu w formacie HTML. Kod HTML musi być elementem pełną, poprawnie sformułowany. Możliwe wartości to: `plain` (ustawienie domyślne) lub `html`.</td>
  </tr>
  <tr>
    <td>category</td>
    <td>*Opcjonalny parametr*.<br/>Ciąg określający kategorii (domena) tłumaczenia. Ten parametr jest używany do pobierania tłumaczenia z dostosowany system skompilowanej za pomocą [Translator niestandardowe](../customization.md). Wartość domyślna to: `general`.</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td>*Opcjonalny parametr*.<br/>Określa, jak profanities powinny być traktowane w translacji. Możliwe wartości to: `NoAction` (ustawienie domyślne), `Marked` lub `Deleted`. Aby poznać sposoby Traktuj niestosownych wyrażeń, zobacz [Obsługa niestosownych wyrażeń](#handle-profanity).</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td>*Opcjonalny parametr*.<br/>Określa, jak profanities powinna być oznaczona jako w translacji. Możliwe wartości to: `Asterisk` (ustawienie domyślne) lub `Tag`. Aby poznać sposoby Traktuj niestosownych wyrażeń, zobacz [Obsługa niestosownych wyrażeń](#handle-profanity).</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td>*Opcjonalny parametr*.<br/>Określa, czy dołączać projekcji wyrównania tekstu źródłowego do tłumaczenia. Możliwe wartości to: `true` lub `false` (ustawienie domyślne). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td>*Opcjonalny parametr*.<br/>Określa, czy dołączać zdanie granice dla tekstu wejściowego i przetłumaczony tekst. Możliwe wartości to: `true` lub `false` (ustawienie domyślne).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td>*Opcjonalny parametr*.<br/>Określa język bazowy, jeśli nie można zidentyfikować język wprowadzania tekstu. Automatyczne wykrywanie języka jest stosowany podczas `from` parametr zostanie pominięty. W przypadku niepowodzenia wykrywania `suggestedFrom` przyjmuje języka.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Opcjonalny parametr*.<br/>Określa skrypt wejściowego tekstu.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Opcjonalny parametr*.<br/>Określa skrypt przetłumaczony tekst.</td>
  </tr>
</table> 

Nagłówki żądania obejmują:

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>_Jeden autoryzacji_<br/>_header_</td>
    <td>*Nagłówek żądania wymagane*.<br/>Zobacz [dostępne opcje dla uwierzytelniania](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Nagłówek żądania wymagane*.<br/>Określa typ zawartości ładunku. Możliwe wartości to: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length.</td>
    <td>*Nagłówek żądania wymagane*.<br/>Długość treści żądania.</td>
  </tr>
  <tr>
    <td>X ClientTraceId</td>
    <td>*Opcjonalnie*.<br/>Generowane przez klientów identyfikator GUID, aby jednoznacznie zidentyfikować żądania. Jeśli dołączysz identyfikator śledzenia w ciągu zapytania za pomocą parametru zapytania o nazwie można pominąć ten nagłówek `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablica JSON. Każdy element tablicy jest obiektem JSON z właściwości ciągu o nazwie `Text`, reprezentuje ciąg do tłumaczenia.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Mają zastosowanie następujące ograniczenia:

* Tablica może mieć co najwyżej 25 elementów.
* Cały tekst w żądaniu uwzględniono nie może przekraczać 5000 znaków, łącznie ze spacjami.

## <a name="response-body"></a>Treść odpowiedzi

Odpowiedź oznaczająca Powodzenie to tablica JSON o jeden wynik dla każdego ciągu w tablicy wejściowej. Obiekt wyniku zawiera następujące właściwości:

  * `detectedLanguage`: Obiekt opisujący języka wykryte za pomocą następujących właściwości:

      * `language`: Ciąg reprezentujący kod języka wykryte.

      * `score`: Float wartość wskazującą zaufania w wyniku. Wynik jest od zera do jednego i niski wynik wskazuje niski zaufania.

    `detectedLanguage` Właściwości jest obecna tylko w obiekcie wynikowym zleconą automatycznego wykrywania języka.

  * `translations`: Tablica tłumaczenie. Rozmiar tablicy jest zgodna z liczbą języków docelowej określonej za pomocą `to` parametr zapytania. Każdy element tablicy obejmuje:

    * `to`: Ciąg reprezentujący kod języka w języku docelowym.

    * `text`: Ciąg, podając przetłumaczony tekst.

    * `transliteration`: Obiekt nadanie przetłumaczony tekst w skrypcie określonego przez `toScript` parametru.

      * `script`: Ciąg określający skrypt docelowej.   

      * `text`: Ciąg przedstawiający przetłumaczony tekst skryptu docelowej.

    `transliteration` Obiektu nie jest dołączana w przypadku transliterację nie ma miejsca.

    * `alignment`: Obiekt o właściwości jednego ciągu o nazwie `proj`, zmapowanym wprowadź tekst do tłumaczenia. Informacje wyrównania jest podawana tylko wtedy, gdy parametr żądania `includeAlignment` jest `true`. Wyrównanie jest zwracana jako wartość ciągu o następującym formacie: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  Dwukropkiem oddziela start językach oddziela Indeks końcowy, kreska i spacją słowa. O jedno słowo w mogą być wyrównane z zero, co najmniej kilka słów w innym języku, a wyrównany wyrażenie może być nieciągłych. Jeśli wyrównanie informacje są niedostępne, element wyrównania jest pusta. Zobacz [uzyskać informacji o wyrównanie](#obtain-alignment-information) przykład i ograniczenia.

    * `sentLen`: Obiekt zwracanie granice zdanie w tekstach wejściowych i wyjściowych.

      * `srcSentLen`: Liczba całkowita tablica reprezentujący długości zdań w wejściowego tekstu. Długość tablicy jest liczbę zdań i wartości są długość każdego zdania.

      * `transSentLen`: Liczba całkowita tablica reprezentujący długości zdań w przetłumaczony tekst. Długość tablicy jest liczbę zdań i wartości są długość każdego zdania.

    Zdania granice są tylko włączone, gdy parametr żądania `includeSentenceLength` jest `true`.

  * `sourceText`: Obiekt o właściwości jednego ciągu o nazwie `text`, co daje wejściowego tekstu w skrypcie domyślnego języka źródłowego. `sourceText` Właściwość jest tylko wtedy, gdy dane wejściowe jest wyrażona w skrypcie, który nie jest zwykle skryptu języka. Na przykład, jeśli dane wejściowe były zapisywane w skrypcie alfabetu łacińskiego, następnie arabski `sourceText.text` zostanie tego samego tekstu Arabic konwertowany do Emiraty skryptu.

Przykład JSON odpowiedzi znajdują się w [przykłady](#examples) sekcji.

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

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
    <td>Jeden z parametrów zapytania jest lub jest ona nieprawidłowa. Popraw parametry żądania przed ponowną próbą.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Nie można uwierzytelnić żądania. Sprawdź, czy poświadczenia są określonego i prawidłowe.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Żądanie nie jest autoryzowane. Sprawdź szczegóły komunikatu o błędzie. Często oznacza zużyte wszystkie tłumaczenia wolnego dostarczane z subskrypcji wersji próbnej.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Obiekt wywołujący wysyła zbyt wiele żądań.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Wystąpił nieoczekiwany błąd. Jeśli błąd będzie się powtarzać, zgłoś go przy użyciu: Data i godzina awarii, identyfikator żądania z nagłówka odpowiedzi `X-RequestId`, a identyfikator klienta z nagłówka żądania `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Serwer jest tymczasowo niedostępny. Ponów żądanie. Jeśli błąd będzie się powtarzać, zgłoś go przy użyciu: Data i godzina awarii, identyfikator żądania z nagłówka odpowiedzi `X-RequestId`, a identyfikator klienta z nagłówka żądania `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Przykłady

### <a name="translate-a-single-input"></a>Tłumaczenie pojedynczego dane wejściowe

W tym przykładzie przedstawiono sposób tłumaczenie zdania jednego z języka angielskiego na język chiński uproszczony.

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

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

`translations` Tablica zawiera jeden element, który zawiera tłumaczenia jeden fragment tekstu w danych wejściowych.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Tłumaczenie pojedynczego danych wejściowych z automatycznego wykrywania języka

W tym przykładzie przedstawiono sposób tłumaczenie zdania jednego z języka angielskiego na język chiński uproszczony. W żądaniu nie określono języka. Zamiast niego jest używana automatycznego wykrywania języka źródłowego.

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

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
Odpowiedź jest podobny do odpowiedzi z poprzedniego przykładu. Ponieważ zażądano automatycznego wykrywania języka, odpowiedzi zawiera także informacje o języku wykryta wejściowego tekstu. 

### <a name="translate-with-transliteration"></a>Tłumaczenie z transliterację

Ta funkcja pozwala rozszerzyć poprzedni przykład, dodając transliterację. Następujące żądania pyta o Tłumaczenie chińskie zapisywane w skrypcie alfabetu łacińskiego.

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

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

Wynik tłumaczenia zawiera teraz `transliteration` właściwość, która zapewnia tłumaczenia znaków alfabetu łacińskiego.

### <a name="translate-multiple-pieces-of-text"></a>Tłumaczenie wielu fragmentów tekstu

Jednocześnie tłumaczenia wielu ciągów jest po prostu określenie tablica ciągów w treści żądania.

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

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

### <a name="translate-to-multiple-languages"></a>Przetłumacz na wiele języków

W tym przykładzie pokazano, jak można przetłumaczyć tej samej dane wejściowe dla wielu języków w jednym żądaniu.

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

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

Zazwyczaj usługa translatora zachowują niestosownych wyrażeń, który znajduje się w źródle w tłumaczenia. Stopień niestosownych wyrażeń i kontekst sprawia, że słowa profanujących różnią się od kultury, a w związku z tym stopień niestosownych wyrażeń w języku docelowym może być rozszerzone lub zmniejszenie.

Jeśli chcesz uniknąć pobierania niestosownych wyrażeń w tłumaczeniu, niezależnie od obecności niestosownych wyrażeń w tekst źródłowy, można użyć niestosownych wyrażeń, opcji filtrowania. Opcję można wybrać, czy użytkownik chce zobaczyć niestosownych wyrażeń usunięta, czy chcesz oznaczyć profanities z odpowiednich tagów (dzięki któremu można dodać własne przetwarzania końcowego) lub ma nie podjęto żadnej akcji. Zaakceptowane wartości `ProfanityAction` są `Deleted`, `Marked` i `NoAction` (ustawienie domyślne).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Akcja</th>
  <tr>
    <td>`NoAction`</td>
    <td>Jest to zachowanie domyślne. Niestosownych wyrażeń przechodzą ze źródła do docelowego.<br/><br/>
    **Przykład źródła (wersja japońska)**: 彼はジャッカスです。<br/>
    **Przykład tłumaczenia (angielski)**: jest jackass.
    </td>
  </tr>
  <tr>
    <td>`Deleted`</td>
    <td>Wyrazy profanujących zostaną usunięte z danych wyjściowych bez zastępowania.<br/><br/>
    **Przykład źródła (wersja japońska)**: 彼はジャッカスです。<br/>
    **Przykład tłumaczenia (angielski)**: jest.
    </td>
  </tr>
  <tr>
    <td>`Marked`</td>
    <td>Profanujących wyrazy przez znacznika w danych wyjściowych. Zależy od znacznika `ProfanityMarker` parametru.<br/><br/>
Aby uzyskać `ProfanityMarker=Asterisk`, profanujących wyrazy z `***`:<br/>
    **Przykład źródła (wersja japońska)**: 彼はジャッカスです。<br/>
    **Przykład tłumaczenia (angielski)**: jest on \* \* \*.<br/><br/>
Aby uzyskać `ProfanityMarker=Tag`, profanujących wyrazy są ujęte w tagi XML &lt;niestosownych wyrażeń&gt; i &lt;/profanity&gt;:<br/>
    **Przykład źródła (wersja japońska)**: 彼はジャッカスです。<br/>
    **Przykład tłumaczenia (angielski)**: jest on &lt;niestosownych wyrażeń&gt;jackass&lt;/profanity&gt;.
  </tr>
</table> 

Na przykład:

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

To polecenie zwróci:

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

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

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

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Przetłumacz zawartości za pomocą znacznika i zdecydować, co jest translacja

Są często tłumaczenie zawartości, który zawiera kod znaczników, np. zawartość ze strony HTML lub zawartości z dokumentu XML. Parametr zapytania `textType=html` podczas tłumaczenia zawartości przy użyciu tagów. Ponadto czasami jest przydatne do wykluczenia z określoną zawartością z tłumaczenia. Można użyć atrybutu `class=notranslate` do określenia zawartości, które powinny pozostać w oryginalnej wersji językowej. W poniższym przykładzie zawartości wewnątrz pierwszy `div` element zostanie nie można przetłumaczyć podczas zawartości w ciągu sekundy `div` element zostanie zamieniona.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Oto przykładowe żądanie w celu zilustrowania.

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

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

Aby odbierać informacje wyrównania, określ `includeAlignment=true` na ciąg zapytania.

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

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

Rozpoczyna się od informacji wyrównanie `0:2-0:1`, co oznacza, że pierwsze trzy znaki w tekście źródła (`The`) mapy do dwóch pierwszych znaków w tekście przetłumaczonego (`La`).

Należy uwzględnić następujące ograniczenia:

* Wyrównanie jest zwracany wyłącznie dla podzbioru kierunki:
  - z języka angielskiego na inny język;
  - z dowolnego innego języka na język angielski, chiński uproszczony, chiński tradycyjny i łotewski język angielski;
  - z japoński, koreański i koreański dla języka japońskiego.
* W przypadku zdanie zwięzłych tłumaczenia nie będą otrzymywać wyrównania. Przykład zwięzłych tłumaczenia jest "To jest test", "Świetnie musisz" i innych zdania o dużej częstotliwości.

### <a name="obtain-sentence-boundaries"></a>Uzyskaj zdanie granicami

Aby otrzymywać informacje o długości zdanie w tekst źródłowy i tłumaczenia, określ `includeSentenceLength=true` na ciąg zapytania.

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

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

Jeśli znasz już tłumaczenia, które chcesz zastosować do wyraz lub frazę, możesz podać go jako kod znaczników w żądaniu. Dynamiczny słownik jest tylko bezpieczne dla rzeczowniki złożone, takich jak nazwy i nazwy produktu.

Kod znaczników do dostarczania używa następującej składni.

``` 
<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>
```

Rozważmy na przykład angielskiej wersji językowej zdania "wpisu słownika jest wordomatic programu word." Aby zachować wyraz _wordomatic_ w tłumaczeniu, Wyślij żądanie:

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

Ta funkcja działa tak samo z `textType=text` lub `textType=html`. Funkcja powinny być używane rzadko. Odpowiednie i znacznie sposób dostosowywania tłumaczenia polega na użyciu translatora niestandardowe. Translator niestandardowych powoduje, że pełne wykorzystanie kontekstu i statystyczne prawdopodobieństwa. Jeśli masz lub akceptowalny Utwórz dane szkoleniowe, pokazujący pracodawcę lub frazę w kontekście, możesz uzyskać znacznie lepszych wyników. [Dowiedz się więcej o Translator niestandardowe](../customization.md).
 





