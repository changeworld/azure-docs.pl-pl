---
title: Metoda wyszukiwania słownika interfejs API tłumaczenia tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Użyj metody Lookup słownika interfejs API tłumaczenia tekstu w usłudze Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: 0bbdba343888bc91521245d0c9a0e4eaa87c5538
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932005"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Interfejs API tłumaczenia tekstu w usłudze Translator 3,0: Wyszukiwanie w słowniku

Oferuje alternatywne tłumaczenia wyrazu i niewielką liczbę fraz idiomatyczne. Każde tłumaczenie ma część mowy i listę zwrotów. Ponowne tłumaczenia umożliwiają użytkownikowi zrozumienie tłumaczenia w kontekście. Przykładowa operacja [słownika](./v3-0-dictionary-examples.md) umożliwia dalsze przechodzenie do szczegółów, aby zobaczyć przykład użycia poszczególnych par tłumaczeń.

## <a name="request-url"></a>Adres URL żądania

`POST` Wyślij żądanie do:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przesłane na ciągu zapytania są następujące:

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>api-version</td>
    <td>*Wymagany parametr*.<br/>Wersja interfejsu API żądana przez klienta. Wartość musi być `3.0`.</td>
  </tr>
  <tr>
    <td>from</td>
    <td>*Wymagany parametr*.<br/>Określa język tekstu wejściowego. Język źródłowy musi być jednym z [obsługiwanych języków](./v3-0-languages.md) zawartych w `dictionary` zakresie.</td>
  </tr>
  <tr>
    <td>to</td>
    <td>*Wymagany parametr*.<br/>Określa język tekstu wyjściowego. Język docelowy musi być jednym z [obsługiwanych języków](./v3-0-languages.md) zawartych w `dictionary` zakresie.</td>
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
    <td>*Wymagany nagłówek żądania*.<br/>Określa typ zawartości ładunku. Możliwe wartości to: `application/json`.</td>
  </tr>
  <tr>
    <td>Długość zawartości</td>
    <td>*Wymagany nagłówek żądania*.<br/>Długość treści żądania.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Opcjonalnie*.<br/>Wygenerowany przez klienta identyfikator GUID służący do unikatowej identyfikacji żądania. Możesz pominąć ten nagłówek, jeśli dołączysz identyfikator śledzenia w ciągu zapytania przy użyciu parametru zapytania o nazwie `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiektem JSON z właściwością ciągu o `Text`nazwie, która reprezentuje termin do wyszukania.

```json
[
    {"Text":"fly"}
]
```

Obowiązują następujące ograniczenia:

* Tablica może zawierać co najwyżej 10 elementów.
* Wartość tekstowa elementu tablicy nie może być dłuższa niż 100 znaków, w tym spacje.

## <a name="response-body"></a>Treść odpowiedzi

Pomyślna odpowiedź to tablica JSON z jednym wynikiem dla każdego ciągu w tablicy wejściowej. Obiekt wynikowy zawiera następujące właściwości:

  * `normalizedSource`: Ciąg przedstawiający znormalizowaną formę okresu źródłowego. Na przykład, jeśli żądanie nosi wartość "Jan", znormalizowana forma będzie "Jan". Zawartość tego pola zmieni się na dane wejściowe [przykładów wyszukiwania](./v3-0-dictionary-examples.md).
    
  * `displaySource`: Ciąg dający termin źródłowy w formularzu najlepiej dopasowany do wyświetlania przez użytkownika końcowego. Na przykład, jeśli dane wejściowe to "Jan", formularz wyświetlania będzie odzwierciedlał zwykłą pisownię nazwy: "Jan". 

  * `translations`: Lista tłumaczeń dla terminu źródłowego. Każdy element listy jest obiektem o następujących właściwościach:

    * `normalizedTarget`: Ciąg przedstawiający znormalizowaną formę tego terminu w języku docelowym. Ta wartość powinna być używana jako dane wejściowe [przykładów wyszukiwania](./v3-0-dictionary-examples.md).

    * `displayTarget`: Ciąg przedstawiający termin w języku docelowym i w postaci najlepiej dopasowanej do wyświetlania przez użytkownika końcowego. Ogólnie rzecz biorąc, będzie ona różnić się `normalizedTarget` tylko od wielkości liter. Na przykład, prawidłowy rzeczownik, taki jak "Juan", `normalizedTarget = "juan"` będzie `displayTarget = "Juan"`miał i.

    * `posTag`: Ciąg kojarzenia tego terminu z tagiem części mowy.

        | Nazwa tagu | Opis  |
        |----------|--------------|
        | ADJ      | Przymiotniki   |
        | ADV      | Parametrów      |
        | CONJ     | Spójniki |
        | DET      | Określenia  |
        | MODAL    | Słowa        |
        | RZECZOWNIK     | Rzeczowniki        |
        | PRODUKCYJN     | Przyimkami |
        | PRON     | Zaimki     |
        | CZASOWNIK     | Słowa        |
        | RÓŻNYCH    | Inne        |

        Jako uwagi dotyczące implementacji te Tagi zostały określone przez znakowanie części mowy po stronie angielskiej, a następnie pobierając najbardziej częste znaczniki dla każdej pary Source/Target. Dlatego jeśli ludzie często przekładają wyrazu hiszpańskiego na inny tag części mowy w języku angielskim, Tagi mogą kończyć się nieprawidłowym (w odniesieniu do wyrazu hiszpańskiego).

    * `confidence`: Wartość z zakresu od 0,0 do 1,0, która reprezentuje "pewność" (lub prawdopodobnie dokładniej "prawdopodobieństwo w danych szkoleniowych") tej pary tłumaczeń. Suma wyników pewności dla jednego słowa źródłowego może lub nie może być sumą do 1,0. 

    * `prefixWord`: Ciąg przedstawiający słowo, które ma być wyświetlane jako prefiks tłumaczenia. Obecnie jest to jednopłciowe określenie rzeczowników w językach, które mają jednopłciowe określenia. Na przykład prefiks słowa "Mosca" w języku hiszpańskim to "La", ponieważ "Mosca" jest rzeczownikiem Feminine w języku hiszpańskim. Jest to zależne od tłumaczenia, a nie źródła. Jeśli nie ma prefiksu, będzie to pusty ciąg.
    
    * `backTranslations`: Lista "retranslations" obiektu docelowego. Na przykład słowa źródłowe, do których można przetłumaczyć element docelowy. Lista jest gwarantowana, aby zawierała żądany wyraz źródłowy (na przykład, jeśli szukane słowo źródłowe jest "przylot", wówczas zostanie zagwarantowane, że "przylot" będzie na `backTranslations` liście). Nie ma jednak gwarancji, że jest w pierwszej pozycji i często nie będzie. Każdy element `backTranslations` listy jest obiektem opisanym przez następujące właściwości:

        * `normalizedText`: Ciąg przedstawiający znormalizowaną formę terminu źródłowego, który jest tłumaczeniem zwrotnym dla elementu docelowego. Ta wartość powinna być używana jako dane wejściowe [przykładów wyszukiwania](./v3-0-dictionary-examples.md).        

        * `displayText`: Ciąg dający termin źródłowy, który jest tłumaczeniem zwrotnym elementu docelowego w postaci najlepiej dopasowanej do wyświetlania przez użytkownika końcowego.

        * `numExamples`: Liczba całkowita reprezentująca liczbę przykładów dostępnych dla tej pary tłumaczeń. Rzeczywiste przykłady muszą zostać pobrane z oddzielnym wywołaniem [przykładów wyszukiwania](./v3-0-dictionary-examples.md). Ta liczba jest przeznaczona głównie do ułatwienia wyświetlania w interfejsie użytkownika. Na przykład interfejs użytkownika może dodać hiperlink do tłumaczenia zwrotnego, jeśli liczba przykładów jest większa od zera i w przypadku braku przykładów pokazać tłumaczenie zwrotne jako zwykły tekst. Należy zauważyć, że rzeczywista liczba przykładów zwracanych przez wywołanie do [przykładów odnośnika](./v3-0-dictionary-examples.md) może być `numExamples`mniejsza niż, ponieważ na bieżąco można zastosować dodatkowe filtrowanie, aby usunąć przykłady "złe".
        
        * `frequencyCount`: Liczba całkowita reprezentująca częstotliwość tej pary tłumaczeń w danych. Głównym celem tego pola jest zapewnienie interfejsu użytkownika przy użyciu metody do sortowania wstecz-tłumaczenia, tak aby najczęstsze terminy były pierwsze.

    > [!NOTE]
    > Jeśli wyszukiwany okres nie istnieje w słowniku, odpowiedź jest 200 (ok), ale `translations` lista jest pusta.

## <a name="examples"></a>Przykłady

Ten przykład pokazuje, jak wyszukiwać alternatywne tłumaczenia w hiszpańskim okresie `fly` w języku angielskim.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

Treść odpowiedzi (skróci do przejrzystości) to:

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

Ten przykład pokazuje, co się stanie, gdy wyszukiwany termin nie istnieje dla poprawnej pary słownika.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

Ponieważ termin nie został znaleziony w słowniku, treść odpowiedzi zawiera pustą `translations` listę.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
