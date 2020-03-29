---
title: Metoda tłumaczenia interfejsu API tekstu tłumacza translatora
titleSuffix: Azure Cognitive Services
description: Zrozumienie parametrów, nagłówków i komunikatów treści dla metody tłumaczenia tekstu usługi Azure Cognitive Services Translator dla tłumaczenia tekstu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: 1821623fbe2a22234af649934ac06e72897a19cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052392"
---
# <a name="translator-text-api-30-translate"></a>Interfejs API tekstu tłumacza 3.0: Tłumaczenie

Tłumaczy tekst.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` prośbę na:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przekazywane na ciąg zapytania są:

### <a name="required-parameters"></a>Wymagane parametry

<table width="100%">
  <th width="20%">Parametr kwerendy</th>
  <th>Opis</th>
  <tr>
    <td>api-version</td>
    <td><em>Wymagany parametr</em>.<br/>Wersja interfejsu API żądana przez klienta. Wartość musi <code>3.0</code>być .</td>
  </tr>
  <tr>
    <td>na</td>
    <td><em>Wymagany parametr</em>.<br/>Określa język tekstu wyjściowego. Język docelowy musi być jednym z <a href="./v3-0-languages.md">obsługiwanych języków zawartych</a> w <code>translation</code> zakresie. Na przykład <code>to=de</code> użyj do tłumaczenia na język niemiecki.<br/>Jest możliwe, aby przetłumaczyć na wiele języków jednocześnie, powtarzając parametr w ciągu zapytania. Na przykład <code>to=de&to=it</code> użyj do tłumaczenia na niemiecki i włoski.</td>
  </tr>
</table>

### <a name="optional-parameters"></a>Parametry opcjonalne

<table width="100%">
  <th width="20%">Parametr kwerendy</th>
  <th>Opis</th>
  <tr>
    <td>Z</td>
    <td><em>Parametr opcjonalny</em>.<br/>Określa język tekstu wejściowego. Dowiedz się, które języki są dostępne do tłumaczenia, <code>translation</code> <a href="./v3-0-languages.md">wyszukując obsługiwane języki</a> przy użyciu zakresu. Jeśli <code>from</code> parametr nie jest określony, automatyczne wykrywanie języka jest stosowane do określenia języka źródłowego. <br/><br/>Należy użyć <code>from</code> parametru, a nie autodetekcji podczas korzystania z funkcji <a href="https://docs.microsoft.com/azure/cognitive-services/translator/dynamic-dictionary">słownika dynamicznego.</a></td>
  </tr>  
  <tr>
    <td>textType</td>
    <td><em>Parametr opcjonalny</em>.<br/>Określa, czy tłumaczony tekst jest tekstem zwykłym, czy tekstem HTML. Każdy kod HTML musi być dobrze ukształtowanym, kompletnym elementem. Możliwe wartości <code>plain</code> to: (domyślnie) lub <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Parametr opcjonalny</em>.<br/>Ciąg określający kategorię (domenę) tłumaczenia. Ten parametr jest używany do uzyskania tłumaczeń z dostosowanego systemu zbudowanego za pomocą <a href="../customization.md">translatora niestandardowego</a>. Dodaj identyfikator kategorii ze szczegółów <a href="https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">projektu usługi</a> Translator do tego parametru, aby użyć wdrożonego systemu dostosowanego. Wartość domyślna <code>general</code>to: .</td>
  </tr>
  <tr>
    <td>wulgaryzmyAkcja</td>
    <td><em>Parametr opcjonalny</em>.<br/>Określa sposób traktowania wulgaryzmów w tłumaczeniach. Możliwe wartości <code>NoAction</code> to: <code>Marked</code> (domyślnie) lub <code>Deleted</code>. Aby zrozumieć sposoby leczenia wulgaryzmów, zobacz <a href="#handle-profanity">Obsługiwania się wulgaryzmami</a>.</td>
  </tr>
  <tr>
    <td>wulgaryzmyMarker</td>
    <td><em>Parametr opcjonalny</em>.<br/>Określa, w jaki sposób wulgaryzmy powinny być oznaczane w tłumaczeniach. Możliwe wartości <code>Asterisk</code> to: (domyślnie) lub <code>Tag</code>. Aby zrozumieć sposoby leczenia wulgaryzmów, zobacz <a href="#handle-profanity">Obsługiwania się wulgaryzmami</a>.</td>
  </tr>
  <tr>
    <td>uwzględnijwzdość</td>
    <td><em>Parametr opcjonalny</em>.<br/>Określa, czy rzutowanie wyrównania ma być uwzględniane od tekstu źródłowego do przetłumaczonego tekstu. Możliwe wartości <code>true</code> to: lub <code>false</code> (domyślnie). </td>
  </tr>
  <tr>
    <td>zawieraSentenceLength</td>
    <td><em>Parametr opcjonalny</em>.<br/>Określa, czy należy uwzględnić granice zdania dla tekstu wejściowego i przetłumaczonego tekstu. Możliwe wartości <code>true</code> to: lub <code>false</code> (domyślnie).</td>
  </tr>
  <tr>
    <td>sugerowaneOd</td>
    <td><em>Parametr opcjonalny</em>.<br/>Określa język rezerwowy, jeśli nie można zidentyfikować języka tekstu wejściowego. Automatyczne wykrywanie języka jest <code>from</code> stosowane po pominięciu parametru. Jeśli wykrywanie <code>suggestedFrom</code> nie powiedzie się, język zostanie przyjęty.</td>
  </tr>
  <tr>
    <td>fromScript (odScript)</td>
    <td><em>Parametr opcjonalny</em>.<br/>Określa skrypt tekstu wejściowego.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>Parametr opcjonalny</em>.<br/>Określa skrypt przetłumaczonego tekstu.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>Parametr opcjonalny</em>.<br/>Określa, że usługa może wrócić do systemu ogólnego, gdy system niestandardowy nie istnieje. Możliwe wartości <code>true</code> to: (domyślnie) lub <code>false</code>.<br/><br/><code>allowFallback=false</code>określa, że tłumaczenie powinno używać wyłącznie <code>category</code> systemów przeszkolonych dla określonych przez żądanie. Jeśli tłumaczenie języka X na język Y wymaga tworzenia łańcucha przez język przestawny E, wszystkie systemy w łańcuchu (X->E i E->Y) muszą być niestandardowe i mają tę samą kategorię. Jeśli nie zostanie znaleziony żaden system z określoną kategorią, żądanie zwróci kod stanu 400. <code>allowFallback=true</code>określa, że usługa może wrócić do systemu ogólnego, gdy system niestandardowy nie istnieje.
</td>
  </tr>
</table> 

Nagłówki żądań obejmują:

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>Nagłówki uwierzytelniania</td>
    <td><em>Wymagany nagłówek żądania</em>.<br/>Zobacz <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostępne opcje uwierzytelniania</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td><em>Wymagany nagłówek żądania</em>.<br/>Określa typ zawartości ładunku.<br/> Zaakceptowaną wartością jest <code>application/json; charset=UTF-8</code>.</td>
  </tr>
  <tr>
    <td>Długość zawartości</td>
    <td><em>Wymagany nagłówek żądania</em>.<br/>Długość treści żądania.</td>
  </tr>
  <tr>
    <td>Identyfikator X-ClientTraceId</td>
    <td><em>Opcjonalnie</em>.<br/>Identyfikator GUID wygenerowany przez klienta, aby jednoznacznie zidentyfikować żądanie. Ten nagłówek można pominąć, jeśli identyfikator śledzenia zostanie uwzględniony w <code>ClientTraceId</code>ciągu zapytania przy użyciu parametru kwerendy o nazwie .</td>
  </tr>
</table> 

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiektem JSON z właściwością string o nazwie `Text`, która reprezentuje ciąg do przetłumaczenia.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Obowiązują następujące ograniczenia:

* Tablica może mieć co najwyżej 100 elementów.
* Cały tekst zawarty w żądaniu nie może przekraczać 5000 znaków, w tym spacji.

## <a name="response-body"></a>Treść odpowiedzi

Pomyślna odpowiedź jest tablicą JSON z jednym wynikiem dla każdego ciągu w tablicy wejściowej. Obiekt wynikowy zawiera następujące właściwości:

  * `detectedLanguage`: Obiekt opisujący wykryty język za pomocą następujących właściwości:

      * `language`: Ciąg reprezentujący kod wykrytego języka.

      * `score`: Wartość pływaka wskazująca zaufanie do wyniku. Wynik jest od zera do jednego, a niski wynik wskazuje na niską pewność siebie.

    Właściwość `detectedLanguage` jest obecny tylko w obiekcie wynik, gdy wymagane jest automatyczne wykrywanie języka.

  * `translations`: Tablica wyników tłumaczenia. Rozmiar tablicy odpowiada liczbie języków docelowych określonych `to` za pośrednictwem parametru zapytania. Każdy element w tablicy zawiera:

    * `to`: Ciąg reprezentujący kod języka docelowego.

    * `text`: Ciąg dający przetłumaczony tekst.

    * `transliteration`: Obiekt dający przetłumaczony tekst w `toScript` skrypcie określonym przez parametr.

      * `script`: Ciąg określający skrypt docelowy.   

      * `text`: Ciąg dający przetłumaczony tekst w skrypcie docelowym.

    Obiekt `transliteration` nie jest uwzględniony, jeśli transliteracja nie ma miejsca.

    * `alignment`: Obiekt o nazwie `proj`pojedynczego ciągu , który mapuje tekst wejściowy na przetłumaczony tekst. Informacje o wyrównaniu są `includeAlignment` dostarczane `true`tylko wtedy, gdy parametr request jest . Wyrównanie jest zwracane jako wartość `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`ciągu w następującym formacie: .  Dwukropek oddziela indeks początkowy i końcowy, kreska oddziela języki, a spacja oddziela wyrazy. Jedno słowo może być wyrównane z wartością zero, jeden lub wiele wyrazów w drugim języku, a wyrównane wyrazy mogą być niesłacalne. Gdy nie są dostępne żadne informacje o wyrównaniu, element wyrównania będzie pusty. Zobacz [Uzyskiwanie informacji o wyrównaniu](#obtain-alignment-information) dla przykładu i ograniczeń.

    * `sentLen`: Obiekt zwracający granice zdań w tekstach wejściowych i wyjściowych.

      * `srcSentLen`: Tablica całkowita reprezentująca długości zdań w tekście wejściowym. Długość tablicy jest liczbą zdań, a wartości są długością każdego zdania.

      * `transSentLen`: Tablica całkowita reprezentująca długości zdań w przetłumaczonym tekście. Długość tablicy jest liczbą zdań, a wartości są długością każdego zdania.

    Granice zdań są uwzględniane `includeSentenceLength` `true`tylko wtedy, gdy parametr request jest .

  * `sourceText`: Obiekt o nazwie `text`pojedynczego ciągu , który daje tekst wejściowy w domyślnym skrypcie języka źródłowego. `sourceText`właściwość jest obecny tylko wtedy, gdy dane wejściowe są wyrażone w skrypcie, który nie jest zwykłym skryptem dla języka. Na przykład, jeśli dane wejściowe były arabskie napisane w łacinie skryptu, a następnie `sourceText.text` będzie ten sam tekst arabski przekonwertowany na skrypt arabski.

Przykład odpowiedzi JSON znajdują się w sekcji [przykłady.](#examples)

## <a name="response-headers"></a>Nagłówki odpowiedzi

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
    <tr>
    <td>X-RequestId (ida żądania X)</td>
    <td>Wartość generowana przez usługę w celu zidentyfikowania żądania. Jest on używany do rozwiązywania problemów.</td>
  </tr>
  <tr>
    <td>System X-MT</td>
    <td>Określa typ systemu, który był używany do tłumaczenia dla każdego języka "do" żądanego do tłumaczenia. Wartość jest oddzieloną przecinkami listą ciągów. Każdy ciąg wskazuje typ:<br/><ul><li>Niestandardowe — żądanie zawiera system niestandardowy i co najmniej jeden system niestandardowy został użyty podczas tłumaczenia.</li><li>Team - Wszystkie inne prośby</li></td>
  </tr>
</table> 

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
    <td>Brakuje jednego z parametrów zapytania lub jest on nieprawidłowy. Popraw parametry żądania przed ponowieniem próby.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Nie można uwierzytelnić żądania. Sprawdź, czy poświadczenia są określone i prawidłowe.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Żądanie nie jest autoryzowane. Sprawdź komunikat o błędzie szczegółów. Często oznacza to, że wszystkie bezpłatne tłumaczenia dostarczane z subskrypcją próbną zostały wykorzystane.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>Nie można spełnić żądania, ponieważ brakuje zasobu. Sprawdź komunikat o błędzie szczegółów. Podczas korzystania <code>category</code>z niestandardowego często wskazuje to, że niestandardowy system tłumaczenia nie jest jeszcze dostępny do obsługi żądań. Wniosek należy ponowić po upływie okresu oczekiwania (np. 1 minuta).</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Serwer odrzucił żądanie, ponieważ klient przekroczył limity żądań.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Wystąpił nieoczekiwany błąd. Jeśli błąd będzie się powtarzał, zgłoś go z: datą i godziną awarii, identyfikatorem żądania z nagłówka <code>X-RequestId</code>odpowiedzi i identyfikatorem klienta z nagłówka <code>X-ClientTraceId</code>żądania .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Serwer tymczasowo niedostępny. Ponów próbę żądania. Jeśli błąd będzie się powtarzał, zgłoś go z: datą i godziną awarii, identyfikatorem żądania z nagłówka <code>X-RequestId</code>odpowiedzi i identyfikatorem klienta z nagłówka <code>X-ClientTraceId</code>żądania .</td>
  </tr>
</table> 

Jeśli wystąpi błąd, żądanie zwróci również odpowiedź na błąd JSON. Kod błędu to 6-cyfrowy numer łączący 3-cyfrowy kod stanu HTTP, po którym następuje 3-cyfrowy numer w celu dalszej kategoryzowania błędu. Typowe kody błędów można znaleźć na [stronie odwołania do interfejsu API tekstu tłumacza translatora](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Przykłady

### <a name="translate-a-single-input"></a>Tłumaczenie pojedynczego wejścia

W tym przykładzie pokazano, jak przetłumaczyć pojedyncze zdanie z angielskiego na chiński uproszczony.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Organ odpowiedzi jest:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

Tablica `translations` zawiera jeden element, który zapewnia tłumaczenie pojedynczego fragmentu tekstu w danych wejściowych.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Tłumaczenie pojedynczego wejścia za pomocą automatycznego wykrywania języka

W tym przykładzie pokazano, jak przetłumaczyć pojedyncze zdanie z angielskiego na chiński uproszczony. Żądanie nie określa języka wprowadzania. Zamiast tego jest używane automatyczne wykrywanie języka źródłowego.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Organ odpowiedzi jest:

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
Odpowiedź jest podobna do odpowiedzi z poprzedniego przykładu. Ponieważ zażądano automatycznego wykrywania języka, odpowiedź zawiera również informacje o języku wykrytym dla tekstu wejściowego. 

### <a name="translate-with-transliteration"></a>Tłumacz z transliteracją

Rozszerzmy poprzedni przykład, dodając transliterację. Poniższa prośba prosi o tłumaczenie na język chiński napisany alfabetem łacińskim.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Organ odpowiedzi jest:

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

Wynik tłumaczenia zawiera `transliteration` teraz właściwość, która daje przetłumaczony tekst przy użyciu znaków łacińskich.

### <a name="translate-multiple-pieces-of-text"></a>Tłumaczenie wielu fragmentów tekstu

Tłumaczenie wielu ciągów naraz jest po prostu kwestią określania tablicy ciągów w treści żądania.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

Organ odpowiedzi jest:

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

W tym przykładzie pokazano, jak przetłumaczyć te same dane wejściowe na kilka języków w jednym żądaniu.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Organ odpowiedzi jest:

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

### <a name="handle-profanity"></a>Uchwyt wulgaryzmów

Zwykle usługa Translator zachowuje wulgaryzmy, które są obecne w źródle w tłumaczeniu. Stopień wulgaryzmów i kontekst, który sprawia, że słowa wulgarne różnią się między kulturami, a w rezultacie stopień wulgaryzmów w języku docelowym może być wzmocniony lub zmniejszony.

Jeśli chcesz uniknąć wulgaryzmów w tłumaczeniu, niezależnie od obecności wulgaryzmów w tekście źródłowym, możesz użyć opcji filtrowania wulgaryzmów. Ta opcja pozwala wybrać, czy wulgaryzmy mają być usuwane, czy chcesz oznaczyć wulgaryzmy odpowiednimi tagami (co daje możliwość dodania własnego przetwarzania końcowego), czy nie chcesz podejmować żadnych działań. Akceptowane wartości `ProfanityAction` są `Deleted`i `Marked` `NoAction` (domyślnie).

<table width="100%">
  <th width="20%">WulgaryzmyAkcja</th>
  <th>Akcja</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Jest to zachowanie domyślne. Wulgaryzmy będą przechodzić ze źródła do celu.<br/><br/>
    <strong>Przykładowe źródło (japońskie):</strong>jap.<br/>
    <strong>Przykładowe tłumaczenie (angielski)</strong>: Jest jackass.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Wulgarne słowa zostaną usunięte z wyjścia bez wymiany.<br/><br/>
    <strong>Przykładowe źródło (japońskie):</strong>jap.<br/>
    <strong>Przykładowe tłumaczenie (angielski)</strong>: On jest.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Wulgarne słowa są zastępowane znacznikiem na wyjściu. Znacznik zależy od <code>ProfanityMarker</code> parametru.<br/><br/>
Dla <code>ProfanityMarker=Asterisk</code>, wulgarne słowa <code>***</code>są zastępowane:<br/>
    <strong>Przykładowe źródło (japońskie):</strong>jap.<br/>
    <strong>Przykładowe tłumaczenie (angielski)</strong> \* \* \*: On jest .<br/><br/>
Dla <code>ProfanityMarker=Tag</code>, wulgarne słowa są otoczone &lt;znacznikami&gt; &lt;XML wulgaryzmów i / wulgaryzmów:&gt;<br/>
    <strong>Przykładowe źródło (japońskie):</strong>jap.<br/>
    <strong>Przykładowe tłumaczenie (angielski)</strong> &lt;: Jest&gt;wulgaryzmami jackass / wulgaryzmów&lt;&gt;.
  </tr>
</table> 

Przykład:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```
To polecenie zwraca następującą treść:

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

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```

To ostatnie żądanie zwraca:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Tłumaczenie zawartości za pomocą znaczników i decydowania o tym, co jest tłumaczone

Często tłumaczy się zawartość zawierającą znaczniki, takie jak zawartość ze strony HTML lub zawartość z dokumentu XML. Dołącz parametr `textType=html` zapytania podczas tłumaczenia zawartości za pomocą znaczników. Ponadto czasami warto wykluczyć określone treści z tłumaczenia. Za pomocą tego `class=notranslate` atrybutu można określić zawartość, która powinna pozostać w oryginalnym języku. W poniższym przykładzie zawartość `div` wewnątrz pierwszego elementu nie zostanie przetłumaczona, `div` podczas gdy zawartość w drugim elemencie zostanie przetłumaczona.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Oto przykładowe żądanie do zilustrowania.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

Odpowiedź brzmi:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Uzyskiwanie informacji o wyrównaniu

Aby otrzymać informacje `includeAlignment=true` o wyrównaniu, należy określić ciąg zapytania.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation.'}]"
```

Odpowiedź brzmi:

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

Informacje o wyrównaniu zaczynają się od `0:2-0:1`, co oznacza, że pierwsze trzy znaki w tekście źródłowym (`The`) są mapowane na pierwsze dwa znaki w przetłumaczonym tekście (`La`).

#### <a name="limitations"></a>Ograniczenia
Uzyskiwanie informacji o wyrównaniu jest funkcją eksperymentalną, którą włączyliśmy do tworzenia prototypów badań i doświadczeń z potencjalnymi mapowaniami fraz. Możemy przestać wspierać to w przyszłości. Oto niektóre z istotnych ograniczeń, w których wyrównanie nie jest obsługiwane:

* Wyrównanie nie jest dostępne dla tekstu w formacie HTML, tj.
* Wyrównanie jest zwracane tylko dla podzbioru par języków:
  - z angielskiego na jakikolwiek inny język;
  - z dowolnego innego języka na angielski, z wyjątkiem języka chińskiego uproszczonego, chińskiego tradycyjnego oraz łotewskiego na angielski;
  - z japońskiego na koreański lub z koreańskiego na japoński.
* Nie otrzymasz wyrównania, jeśli zdanie jest tłumaczeniem w puszkach. Przykładem tłumaczenia w puszkach jest "To jest test", "Kocham cię" i inne zdania o wysokiej częstotliwości.
* Wyrównanie nie jest dostępne po zastosowaniu któregokolwiek z podejść, aby zapobiec tłumaczeniu, jak opisano [tutaj](../prevent-translation.md)

### <a name="obtain-sentence-boundaries"></a>Uzyskiwanie granic zdań

Aby otrzymywać informacje o długości zdania w tekście `includeSentenceLength=true` źródłowym i przetłumaczonym tekście, określ ciąg zapytania.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

Odpowiedź brzmi:

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

### <a name="translate-with-dynamic-dictionary"></a>Tłumaczenie ze słownikiem dynamicznym

Jeśli znasz już tłumaczenie, które chcesz zastosować do wyrazu lub frazy, możesz podać je jako znaczniki w żądaniu. Słownik dynamiczny jest bezpieczny tylko dla rzeczowników złożonych, takich jak nazwy własne i nazwy produktów.

Znaczników do dostarczenia używa następującej składni.

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Rozważmy na przykład zdanie w języku angielskim "Słowo wordomatic jest wpisem słownika." Aby zachować słowo _wordomatic_ w tłumaczeniu, wyślij żądanie:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Rezultatem jest:

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Ta funkcja działa w `textType=text` ten `textType=html`sam sposób z programem . Funkcja ta powinna być używana oszczędnie. Odpowiedni i znacznie lepszy sposób dostosowywania tłumaczenia jest za pomocą translatora niestandardowego. Custom Translator w pełni wykorzystuje kontekst i prawdopodobieństwa statystyczne. Jeśli masz lub możesz sobie pozwolić na tworzenie danych szkoleniowych, które pokazują twoją pracę lub frazę w kontekście, otrzymujesz znacznie lepsze wyniki. [Dowiedz się więcej o tłumaczu niestandardowym](../customization.md).
