---
title: Metoda wyszukiwania słownika interfejsu API tekst Microsoft Translator | Dokumentacja firmy Microsoft
description: Metoda Microsoft Translator tekst interfejsu API słownika wyszukiwania.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 31435fcfca61517bfc72d534e911a1dcadbee52b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349268"
---
# <a name="text-api-30-dictionary-lookup"></a>Tekst interfejsu API 3.0: Słownika wyszukiwania

Udostępnia alternatywne tłumaczenia dla programów word i niewielką liczbę idiomatyczne fraz. Każde tłumaczenie ma części mowy i listę tłumaczeń Wstecz. Tłumaczenie wstecz umożliwić użytkownikowi zrozumieć tłumaczenia w kontekście. [Przykład słownika](.\v3-0-dictionary-examples.md) operacji umożliwia przejdź do Zobacz przykład używa każdej pary tłumaczenia dalszych szczegółów.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` żądanie:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
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
    <td>*Wymagany parametr*.<br/>Określa język tekstu wejściowego. Język źródłowy musi być jedną z [obsługiwanych języków](.\v3-0-languages.md) objęte `dictionary` zakresu.</td>
  </tr>
  <tr>
    <td>na</td>
    <td>*Wymagany parametr*.<br/>Określa język tekstu wyjściowego. Język docelowy musi mieć jedną z [obsługiwanych języków](.\v3-0-languages.md) objęte `dictionary` zakresu.</td>
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

Treść żądania jest tablica JSON. Każdy element tablicy jest obiektem JSON z właściwości ciągu o nazwie `Text`, który reprezentuje terminu wyszukiwania.

```json
[
    {"Text":"fly"}
]
```

Mają zastosowanie następujące ograniczenia:

* Tablica może mieć maksymalnie 10 elementów.
* Wartość tekstowego elementu tablicy nie może przekraczać 100 znaków, łącznie ze spacjami.

## <a name="response-body"></a>Treść odpowiedzi

Odpowiedź oznaczająca Powodzenie to tablica JSON o jeden wynik dla każdego ciągu w tablicy wejściowej. Obiekt wyniku zawiera następujące właściwości:

  * `normalizedSource`: Ciąg, podając znormalizowana postać warunku źródła. Na przykład jeśli żądanie jest "Jan", znormalizowana postać będzie "Jan". Zawartość tego pola staje się dane wejściowe [Przykłady wyszukiwania](.\v3-0-dictionary-examples.md).
    
  * `displaySource`: Ciąg nadanie termin źródłowy w postaci najlepiej nadaje się do wyświetlania przez użytkownika końcowego. Na przykład, jeśli dane wejściowe "Jan", Wyświetl formularz będzie odzwierciedlać zwykle pisownię nazwy: "Jan". 

  * `translations`: Lista tłumaczeń terminu źródła. Każdy element listy jest obiektem z następującymi właściwościami:

    * `normalizedTarget`: Ciąg, podając znormalizowana postać tego warunku w języku docelowym. Ta wartość będzie używana jako dane wejściowe [Przykłady wyszukiwania](.\v3-0-dictionary-examples.md).

    * `displayTarget`: Ciąg nadawaniu termin w języku docelowym oraz w formie najlepiej nadaje się do wyświetlania przez użytkownika końcowego. Ogólnie rzecz biorąc, to zostanie różnią się tylko z `normalizedTarget` pod względem wielkości liter. Na przykład rzeczownikiem poprawnej takich jak "Juan" będzie mieć `normalizedTarget = "juan"` i `displayTarget = "Juan"`.

    * `posTag`: Ciąg skojarzenie tego terminu przy użyciu tagu części z mowy.

        | Nazwa tagu | Opis  |
        |----------|--------------|
        | PR      | Określeniem   |
        | ADV      | Parametrów      |
        | CONJ     | Spójników |
        | DET      | Determiners  |
        | MODALNE    | Zlecenia        |
        | RZECZOWNIK     | Rzeczowniki        |
        | PRZEDPRODUKCYJNYM     | Przyimki |
        | PRON     | Zaimki     |
        | ZLECENIE     | Zlecenia        |
        | INNE    | Inne        |

        Jako notatek wykonania te znaczniki zostały określone przez część elementu mowy znakowanie angielskiej wersji językowej po stronie, a następnie biorąc najczęstsze tagu dla każdej pary źródłowy i docelowy. Więc jeśli osób tłumaczenie często hiszpańskim programu word do znacznika różnych części programu mowy w języku angielskim, tagi może nastąpić przejście jest nieprawidłowy (względem hiszpańskim programu word).

    * `confidence`: Wartość z zakresu od 0,0 do 1,0, który reprezentuje "zaufania" (lub bardziej dokładnie, "prawdopodobieństwo w danych szkoleniowych") tej pary tłumaczenia. Suma wyniki zaufania dla źródła o jedno słowo może lub nie może Suma, umożliwiającej 1.0. 

    * `prefixWord`Nadanie ciąg programu word, który będzie wyświetlany jako prefiksu tłumaczenia. Obecnie jest gendered determiner z rzeczowniki w językach, które mają gendered determiners. Na przykład prefiks hiszpańskim słowo "mosca" jest "la", ponieważ "mosca" jest żeński rzeczownik w języku hiszpańskim. Jest to tylko zależnych na tłumaczenia, a nie w źródle. Jeśli nie ma żadnego prefiksu, będzie pusty ciąg.
    
    * `backTranslations`: Lista "back tłumaczeń" elementu docelowego. Na przykład źródła słowa, które element docelowy może przełożyć na. Lista jest gwarantowana zawierać słowo źródła, którego zażądano (np. Jeśli trwa word źródła wyszukiwana jest "udać", a następnie będzie gwarancji, że "udać" będzie `backTranslations` listy). Jednak nie jest gwarantowana się na pierwszym miejscu i często nie będzie. Każdy element `backTranslations` listy jest obiektem opisanego przez następujących właściwościach:

        * `normalizedText`: Ciąg, podając znormalizowana postać warunku źródła będący wstecz translację elementu docelowego. Ta wartość będzie używana jako dane wejściowe [Przykłady wyszukiwania](.\v3-0-dictionary-examples.md).        

        * `displayText`: Ciąg nadanie terminu źródłowego, który jest tłumaczenie wstecz obiektu docelowego w postaci najlepsze nadaje się do wyświetlania przez użytkownika końcowego.

        * `numExamples`: Całkowitą reprezentującą liczbę przykłady, które są dostępne dla tej pary tłumaczenia. Rzeczywiste przykłady musi zostać pobrany z wymaga oddzielnego wywołania do [Przykłady wyszukiwania](.\v3-0-dictionary-examples.md). Liczba jest przeważnie mająca na celu ułatwienie wyświetlania w UX. Na przykład interfejsu użytkownika mogą Dodawanie hiperłącza do tłumaczenia zwrotnego, jeśli wiele przykładów jest większa niż zero i Pokaż tłumaczenia wstecz jako zwykły tekst, jeśli istnieją nie przykłady. Należy pamiętać, że rzeczywista liczba przykłady zwrócony przez wywołanie do [Przykłady wyszukiwania](.\v3-0-dictionary-examples.md) może być mniejsza niż `numExamples`, ponieważ filtrowania dodatkowe mogą być stosowane na bieżąco, aby usunąć przykłady "zły".
        
        * `frequencyCount`: Liczba całkowita reprezentująca częstotliwość tej pary tłumaczenia w danych. Głównym celem tego pola jest zapewnienie interfejs użytkownika umożliwia sortowanie tłumaczeń Wstecz, terminy najczęściej są po raz pierwszy.

    > [!NOTE]
    > Jeśli termin jest tablicą up nie istnieje w słowniku, odpowiedź jest 200 (OK), ale `translations` lista jest pusta lista.

## <a name="examples"></a>Przykłady

W tym przykładzie przedstawiono sposób wyszukiwania alternatywne tłumaczenia w języku hiszpańskim angielski termin `fly` .

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

---

Treść odpowiedzi (skrót dla jasności) jest:

```
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

Ten przykład przedstawia, co się stanie, gdy są wyszukiwane termin nie istnieje dla pary prawidłowy słownik.

# <a name="curltabcurl"></a>[Narzędzie curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

---

Ponieważ termin nie zostanie znaleziony w słowniku, treść odpowiedzi zawiera pustą `translations` listy.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
