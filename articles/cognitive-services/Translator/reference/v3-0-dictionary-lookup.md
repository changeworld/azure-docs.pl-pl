---
title: Metoda wyszukiwania słownika interfejs API tłumaczenia tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Metoda wyszukiwania słownika oferuje alternatywne tłumaczenia wyrazu i niewielką liczbę fraz idiomatyczne.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: bd725d41f75bdfb1048b5bee7e8224679dbece4c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837270"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Interfejs API tłumaczenia tekstu w usłudze Translator 3,0: wyszukiwanie słownika

Oferuje alternatywne tłumaczenia wyrazu i niewielką liczbę fraz idiomatyczne. Każde tłumaczenie ma część mowy i listę zwrotów. Ponowne tłumaczenia umożliwiają użytkownikowi zrozumienie tłumaczenia w kontekście. [Przykładowa operacja słownika](./v3-0-dictionary-examples.md) umożliwia dalsze przechodzenie do szczegółów, aby zobaczyć przykład użycia poszczególnych par tłumaczeń.

## <a name="request-url"></a>Adres URL żądania

Wyślij żądanie `POST` do:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przesłane na ciągu zapytania są następujące:

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>wersja interfejsu API</td>
    <td>*Wymagany parametr*.<br/>Wersja interfejsu API żądana przez klienta. Wartość musi być `3.0`.</td>
  </tr>
  <tr>
    <td>wniosek</td>
    <td>*Wymagany parametr*.<br/>Określa język tekstu wejściowego. Język źródłowy musi być jednym z [obsługiwanych języków](./v3-0-languages.md) znajdujących się w zakresie `dictionary`.</td>
  </tr>
  <tr>
    <td>na</td>
    <td>*Wymagany parametr*.<br/>Określa język tekstu wyjściowego. Język docelowy musi być jednym z [obsługiwanych języków](./v3-0-languages.md) uwzględnionych w zakresie `dictionary`.</td>
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

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiektem JSON z właściwością ciągu o nazwie `Text`, która reprezentuje termin do wyszukania.

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

  * `normalizedSource`: ciąg dający znormalizowaną formę okresu źródłowego. Na przykład, jeśli żądanie nosi wartość "Jan", znormalizowana forma będzie "Jan". Zawartość tego pola zmieni się na dane wejściowe [przykładów wyszukiwania](./v3-0-dictionary-examples.md).
    
  * `displaySource`: ciąg dający termin źródłowy w postaci najlepiej dopasowanej do wyświetlania przez użytkownika końcowego. Na przykład, jeśli dane wejściowe to "Jan", formularz wyświetlania będzie odzwierciedlał zwykły pisownię nazwy: "Jan". 

  * `translations`: Lista tłumaczeń dla terminu źródłowego. Każdy element listy jest obiektem o następujących właściwościach:

    * `normalizedTarget`: ciąg dający znormalizowaną formę tego terminu w języku docelowym. Ta wartość powinna być używana jako dane wejściowe [przykładów wyszukiwania](./v3-0-dictionary-examples.md).

    * `displayTarget`: ciąg określający termin w języku docelowym i w postaci najlepiej dopasowanej do wyświetlania przez użytkownika końcowego. Ogólnie rzecz biorąc, będzie ona różnić się tylko od `normalizedTarget` w przypadku wielkich liter. Na przykład, prawidłowy rzeczownik, taki jak "Juan", będzie miał `normalizedTarget = "juan"` i `displayTarget = "Juan"`.

    * `posTag`: ciąg kojarzenia tego terminu z tagiem części mowy.

        | Nazwa tagu | Opis  |
        |----------|--------------|
        | KOREKT      | Przymiotniki   |
        | Zaawansowana      | Parametrów      |
        | CONJ     | Spójniki |
        | DET      | Określenia  |
        | MODAL    | Słowa        |
        | RZECZOWNIK     | Rzeczowniki        |
        | PRODUKCYJN     | Przyimkami |
        | PRON     | Zaimki     |
        | CZASOWNIK     | Słowa        |
        | RÓŻNYCH    | Inne        |

        Jako uwagi dotyczące implementacji te Tagi zostały określone przez znakowanie części mowy po stronie angielskiej, a następnie pobierając najbardziej częste znaczniki dla każdej pary Source/Target. Dlatego jeśli ludzie często przekładają wyrazu hiszpańskiego na inny tag części mowy w języku angielskim, Tagi mogą kończyć się nieprawidłowym (w odniesieniu do wyrazu hiszpańskiego).

    * `confidence`: wartość z zakresu od 0,0 do 1,0, która reprezentuje "pewność" (lub prawdopodobnie dokładniej "prawdopodobieństwo w danych szkoleniowych") tej pary tłumaczeń. Suma wyników pewności dla jednego słowa źródłowego może lub nie może być sumą do 1,0. 

    * `prefixWord`: ciąg nadający wyraz do wyświetlenia jako prefiks tłumaczenia. Obecnie jest to jednopłciowe określenie rzeczowników w językach, które mają jednopłciowe określenia. Na przykład prefiks słowa "Mosca" w języku hiszpańskim to "La", ponieważ "Mosca" jest rzeczownikiem Feminine w języku hiszpańskim. Jest to zależne od tłumaczenia, a nie źródła. Jeśli nie ma prefiksu, będzie to pusty ciąg.
    
    * `backTranslations`: Lista "retranslations" dla elementu docelowego. Na przykład słowa źródłowe, do których można przetłumaczyć element docelowy. Lista jest gwarantowana, aby zawierała żądane słowo źródłowe (np. Jeśli szukane słowo źródłowe jest "przylot", wówczas jest gwarantowane, że "przylot" będzie znajdować się na liście `backTranslations`). Nie ma jednak gwarancji, że jest w pierwszej pozycji i często nie będzie. Każdy element listy `backTranslations` jest obiektem opisanym przez następujące właściwości:

        * `normalizedText`: ciąg reprezentujący znormalizowaną formę terminu źródłowego, który jest tłumaczeniem zwrotnym dla elementu docelowego. Ta wartość powinna być używana jako dane wejściowe [przykładów wyszukiwania](./v3-0-dictionary-examples.md).        

        * `displayText`: ciąg dający termin źródłowy, który jest tłumaczeniem zwrotnym elementu docelowego w postaci najlepiej dopasowanej do wyświetlania przez użytkownika końcowego.

        * `numExamples`: liczba całkowita reprezentująca liczbę przykładów dostępnych dla tej pary tłumaczeń. Rzeczywiste przykłady muszą zostać pobrane z oddzielnym wywołaniem [przykładów wyszukiwania](./v3-0-dictionary-examples.md). Ta liczba jest przeznaczona głównie do ułatwienia wyświetlania w interfejsie użytkownika. Na przykład interfejs użytkownika może dodać hiperlink do tłumaczenia zwrotnego, jeśli liczba przykładów jest większa od zera i w przypadku braku przykładów pokazać tłumaczenie zwrotne jako zwykły tekst. Należy zauważyć, że rzeczywista liczba przykładów zwracanych przez wywołanie [przykłady wyszukiwania](./v3-0-dictionary-examples.md) może być mniejsza niż `numExamples`, ponieważ na bieżąco można zastosować dodatkowe filtrowanie, aby usunąć przykłady "złe".
        
        * `frequencyCount`: liczba całkowita reprezentująca częstotliwość tej pary tłumaczeń w danych. Głównym celem tego pola jest zapewnienie interfejsu użytkownika przy użyciu metody do sortowania wstecz-tłumaczenia, tak aby najczęstsze terminy były pierwsze.

    > [!NOTE]
    > Jeśli wyszukiwany okres nie istnieje w słowniku, odpowiedź jest 200 (OK), ale lista `translations` jest pustą listą.

## <a name="examples"></a>Przykłady

Ten przykład pokazuje, jak wyszukiwać alternatywne tłumaczenia w hiszpańskim `fly` terminów w języku angielskim.

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

Ponieważ w słowniku nie znaleziono tego terminu, treść odpowiedzi zawiera pustą listę `translations`.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
