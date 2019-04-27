---
title: Metoda wyszukiwania słownika interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Metoda wyszukiwania słownika interfejsu API tekstu usługi Translator.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: b844ac4018ef768527ca17bd68ca53baaf5d9552
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605504"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Translator Text API 3.0: Wyszukiwanie w słowniku

Dostarcza alternatywnych tłumaczeń dla programów word i małą liczbą wyrażenia idiomatyczne. Każde tłumaczenie części mowy i ma listę wstecz tłumaczenia. Tłumaczenia wstecz umożliwić użytkownikowi zrozumieć tłumaczenie w kontekście. [Przykład słownika](./v3-0-dictionary-examples.md) operacja umożliwia dalsze do przechodzenia do szczegółów Zobacz przykład użycia każdej pary tłumaczenia.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` limit czasu żądania:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
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
    <td>*Wymagany parametr*.<br/>Określa język tekstu wejściowego. Język źródłowy musi być jednym z [obsługiwane języki](./v3-0-languages.md) objęte `dictionary` zakresu.</td>
  </tr>
  <tr>
    <td>na</td>
    <td>*Wymagany parametr*.<br/>Określa język tekstu wyjściowego. Język docelowy musi mieć jedną z [obsługiwane języki](./v3-0-languages.md) objęte `dictionary` zakresu.</td>
  </tr>
</table>

Nagłówki żądania obejmują:

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>Nagłówki uwierzytelniania</td>
    <td><em>Nagłówek żądania wymagane</em>.<br/>Zobacz <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostępne opcje uwierzytelniania</a>.</td>
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
    <td>X-ClientTraceId</td>
    <td>*Opcjonalnie*.<br/>Generowane przez klienta identyfikator GUID do unikatowego identyfikowania żądania. Możesz pominąć ten nagłówek, jeśli zawierają identyfikator śledzenia w ciągu zapytania za pomocą parametru zapytania o nazwie `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiekt JSON z właściwością ciągu o nazwie `Text`, która reprezentuje termin do wyszukiwania.

```json
[
    {"Text":"fly"}
]
```

Obowiązują następujące ograniczenia:

* Tablica może mieć co najwyżej 10 elementów.
* Wartość tekstowa elementu tablicy nie może przekraczać 100 znaków, łącznie ze spacjami.

## <a name="response-body"></a>Treść odpowiedzi

Odpowiedź oznaczająca Powodzenie to tablica JSON z jeden wynik dla każdego ciągu w tabeli wejściowej. Obiekt wyniku zawiera następujące właściwości:

  * `normalizedSource`: Ciąg, zapewniając znormalizowana postać okresu źródła. Na przykład jeśli żądanie jest "JOHN", znormalizowana postać będzie "john". Zawartość tego pola staje się dane wejściowe [Przykłady wyszukiwania](./v3-0-dictionary-examples.md).
    
  * `displaySource`: Ciąg, dzięki czemu termin źródłowy w postaci najlepiej dostosowane do wyświetlenia przez użytkownika końcowego. Na przykład jeśli dane wejściowe są "JOHN", Wyświetl formularz zostanie naliczona zwykle pisownię nazwy: "John". 

  * `translations`: Lista tłumaczeń termin źródła. Każdy element listy jest obiektem z następującymi właściwościami:

    * `normalizedTarget`: Ciąg, dzięki czemu znormalizowana postać tego warunku w języku docelowym. Ta wartość będzie używana jako dane wejściowe [Przykłady wyszukiwania](./v3-0-dictionary-examples.md).

    * `displayTarget`: Ciąg, podając wyrażenie w języku docelowym oraz w formie najlepiej dostosowane do wyświetlenia przez użytkownika końcowego. Ogólnie rzecz biorąc, to będzie tylko różnią się od `normalizedTarget` pod względem wielkości liter. Na przykład stoi takie jak "Juan" będą mieć `normalizedTarget = "juan"` i `displayTarget = "Juan"`.

    * `posTag`: Ciąg skojarzenie tego terminu za pomocą tagów części mowy.

        | Nazwa tagu | Opis  |
        |----------|--------------|
        | ADJ      | Określeniem   |
        | ADV      | Parametrów      |
        | CONJ     | Spójniki |
        | DET      | Determiners  |
        | MODAL    | Zlecenia        |
        | RZECZOWNIK     | Rzeczowniki        |
        | PRZYGOTOWYWANIE     | Przyimkami |
        | PRON     | Zaimki     |
        | VERB     | Zlecenia        |
        | INNE    | Inne        |

        Jako notatka implementacji te znaczniki zostały określone przez części mowy znakowanie angielskim po stronie, a następnie wykonywanie najczęściej tagu dla każdej pary źródło i cel. Dlatego jeśli ludzie często tłumaczenie hiszpańskim słowem do różnych tagów części mowy w języku angielskim, znaczniki może pozostać problemy (względem hiszpańskim słowem).

    * `confidence`: Wartość z zakresu od 0.0 do 1.0, który reprezentuje "zaufania" (lub bardziej precyzyjnie, "prawdopodobieństwo w danych szkoleniowych") pary tej tłumaczeń. Suma wyniki zaufania dla jednego słowa źródła może lub nie może być suma, umożliwiającej 1.0. 

    * `prefixWord`: Ciąg, zapewniając programu word do wyświetlenia jako prefiksu tłumaczenia. Obecnie jest gendered determiner z rzeczowniki w językach, które mają gendered determiners. Na przykład prefiks hiszpańskim słowem "mosca" jest "la", ponieważ "mosca" żeński rzeczowników w języku hiszpańskim. Jest to tylko zależnych na tłumaczenia, a nie na "source". Jeśli nie ma żadnego prefiksu, będzie pusty ciąg.
    
    * `backTranslations`: Lista "tłumaczenia Wstecz" element docelowy. Na przykład źródło wyrazy, które element docelowy można przełożyć na. Listy może zawierać słowo źródła, którego zażądano (np. w przypadku, gdy trwa źródłowego programu word wyszukiwana jest "podnoszenia", a następnie gwarantuje, że "podnoszenia" będzie `backTranslations` listy). Jednak nie może być na pierwszym miejscu i często nie będzie. Każdy element obiektu `backTranslations` lista jest obiektem opisanym przez następujące właściwości:

        * `normalizedText`: Ciąg, zapewniając znormalizowana postać składnika źródłowego, który jest tłumaczeniem kopii obiektu docelowego. Ta wartość będzie używana jako dane wejściowe [Przykłady wyszukiwania](./v3-0-dictionary-examples.md).        

        * `displayText`: Ciąg, zapewniając termin źródłowego, który jest ponownie tłumaczenie obiektu docelowego w formie najlepszych dostosowane do wyświetlenia przez użytkownika końcowego.

        * `numExamples`: Liczba całkowita reprezentująca liczbę przykładów, które są dostępne dla tej pary tłumaczenia. Rzeczywiste przykłady musi zostać pobrany z oddzielnym wywołaniu [Przykłady wyszukiwania](./v3-0-dictionary-examples.md). Liczba jest przede wszystkim mająca na celu ułatwienie ekran w najlepsze środowisko użytkownika. Na przykład interfejs użytkownika może dodać hiperłącze do tłumaczenia Wstecz, jeśli wiele przykładów jest większa niż zero i Pokaż tłumaczenia wstecz jako zwykły tekst, jeśli istnieją żadnych przykładów. Należy pamiętać, że rzeczywista liczba przykłady zwracane przez wywołanie [Przykłady wyszukiwania](./v3-0-dictionary-examples.md) może być mniejsza niż `numExamples`, ponieważ filtrowania dodatkowych można stosować na bieżąco, aby usunąć przykłady "złe".
        
        * `frequencyCount`: Liczba całkowita reprezentująca częstotliwość Ta para tłumaczenia w danych. Głównym celem tego pola jest udostępniają interfejs użytkownika umożliwia sortowanie tłumaczenia Wstecz, więc terminy najczęściej są po raz pierwszy.

    > [!NOTE]
    > Jeśli termin jest tablicą w górę nie istnieje w słowniku, odpowiedź jest 200 (OK), ale `translations` lista jest pusta lista.

## <a name="examples"></a>Przykłady

W tym przykładzie pokazano, jak wyszukać alternatywnych tłumaczeń w języku hiszpańskim okresu angielskiej `fly` .

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

---

Treść odpowiedzi (skrót dla jasności) to:

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

Ten przykład pokazuje, co się stanie, gdy termin są wyszukiwane nie istnieje dla pary prawidłowe słownika.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

---

Ponieważ wyrażenie nie zostanie znaleziony w słowniku, treść odpowiedzi zawiera pustą `translations` listy.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
