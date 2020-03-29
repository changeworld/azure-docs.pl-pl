---
title: Metoda wyszukiwania słownika interfejsu API tłumacza
titleSuffix: Azure Cognitive Services
description: Metoda wyszukiwania słownika zawiera alternatywne tłumaczenia wyrazu i niewielką liczbę fraz idiomatycznych.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: bd27827441082698bb4e0b43e7dd22d5b7e66539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76548955"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Interfejs API tekstu tłumacza 3.0: wyszukiwanie słownika

Zawiera alternatywne tłumaczenia wyrazu i niewielką liczbę fraz idiomatycznych. Każde tłumaczenie ma część mowy i listę tłumaczeń wstecznych. Tłumaczenia wsteczne umożliwiają użytkownikowi zrozumienie tłumaczenia w kontekście. [Słownik przykład](./v3-0-dictionary-examples.md) operacji umożliwia dalsze przechodzenie do szczegółów, aby zobaczyć przykładowe zastosowania każdej pary tłumaczenia.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` prośbę na:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przekazywane na ciąg zapytania są:

| Parametr kwerendy  | Opis |
| ------ | ----------- |
| api-version <img width=200/>   | **Wymagany parametr**.<br/>Wersja interfejsu API żądana przez klienta. Wartość musi być`3.0` |
| Z | **Wymagany parametr**.<br/>Określa język tekstu wejściowego. Język źródłowy musi być jednym z [obsługiwanych języków zawartych](./v3-0-languages.md) w `dictionary` zakresie. |
| na   | **Wymagany parametr**.<br/>Określa język tekstu wyjściowego. Język docelowy musi być jednym z [obsługiwanych języków zawartych](v3-0-languages.md) w `dictionary` zakresie. |


Nagłówki żądań obejmują:

| Nagłówki  | Opis |
| ------ | ----------- |
| Nagłówki uwierzytelniania <img width=200/>  | **Wymagany nagłówek żądania**.<br/>Zobacz <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostępne opcje uwierzytelniania</a>. |
| Content-Type | **Wymagany nagłówek żądania**.<br/>Określa typ zawartości ładunku. Możliwe wartości `application/json`to: . |
| Długość zawartości   | **Wymagany nagłówek żądania**.<br/>Długość treści żądania. |
| Identyfikator X-ClientTraceId   | **Opcjonalnie**.<br/>Identyfikator GUID wygenerowany przez klienta, aby jednoznacznie zidentyfikować żądanie. Ten nagłówek można pominąć, jeśli identyfikator śledzenia zostanie uwzględniony w `ClientTraceId`ciągu zapytania przy użyciu parametru kwerendy o nazwie . |

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiektem JSON z właściwością string o nazwie `Text`, która reprezentuje termin do wyszukiwania.

```json
[
    {"Text":"fly"}
]
```

Obowiązują następujące ograniczenia:

* Tablica może mieć co najwyżej 10 elementów.
* Wartość tekstowa elementu tablicy nie może przekraczać 100 znaków, w tym spacji.

## <a name="response-body"></a>Treść odpowiedzi

Pomyślna odpowiedź jest tablicą JSON z jednym wynikiem dla każdego ciągu w tablicy wejściowej. Obiekt wynikowy zawiera następujące właściwości:

  * `normalizedSource`: Ciąg dający znormalizowaną formę terminu źródłowego. Na przykład, jeśli żądanie jest "JOHN", znormalizowane formularz będzie "john". Zawartość tego pola staje się dane wejściowe do [przykładów wyszukiwania](./v3-0-dictionary-examples.md).
    
  * `displaySource`: Ciąg dający termin źródłowy w formie najlepiej dostosowanej do wyświetlania użytkownika końcowego. Na przykład, jeśli dane wejściowe to "JOHN", formularz wyświetlania będzie odzwierciedlał zwykłą pisownię nazwy: "Jan". 

  * `translations`: Lista tłumaczeń dla terminu źródłowego. Każdy element listy jest obiektem o następujących właściwościach:

    * `normalizedTarget`: Ciąg dający znormalizowaną formę tego terminu w języku docelowym. Ta wartość powinna być używana jako dane wejściowe do [przykładów wyszukiwania](./v3-0-dictionary-examples.md).

    * `displayTarget`: Ciąg dający termin w języku docelowym i w formie najlepiej dostosowanej do wyświetlania użytkownika końcowego. Ogólnie rzecz biorąc, będzie `normalizedTarget` to się różnić tylko pod względem kapitalizacji. Na przykład, właściwy rzeczownik jak "Juan" będzie miał `normalizedTarget = "juan"` i `displayTarget = "Juan"`.

    * `posTag`: Ciąg kojarzący ten termin z tagiem części mowy.

        | Nazwa tagu | Opis  |
        |----------|--------------|
        | Adj      | Przymiotniki   |
        | Adv      | Przysłówki      |
        | Conj (conj)     | Spójniki |
        | Det      | Determinery  |
        | Modalne    | zlecenia        |
        | Rzeczownik     | Rzeczowniki        |
        | Prep     | Przyimki |
        | Pron     | Zaimki     |
        | Czasownik     | zlecenia        |
        | Innych    | Inne        |

        Jako notatkę wdrożeniową, tagi te zostały określone przez część mowy tagowanie strony angielskiej, a następnie biorąc najczęściej tag dla każdej pary źródła/docelowego. Jeśli więc ludzie często tłumaczą hiszpańskie słowo na inny tag części mowy w języku angielskim, tagi mogą skończyć się błędem (w odniesieniu do hiszpańskiego słowa).

    * `confidence`: Wartość między 0,0 a 1,0, która reprezentuje "zaufanie" (a może dokładniej "prawdopodobieństwo w danych szkoleniowych") tej pary tłumaczeń. Suma punktów zaufania dla jednego słowa źródłowego może, ale nie musi, wynosić 1,0. 

    * `prefixWord`: Ciąg dający wyraz do wyświetlenia jako prefiks tłumaczenia. Obecnie jest to determiner płci rzeczowników, w językach, które mają określania płci. Na przykład przedrostek hiszpańskiego słowa "mosca" to "la", ponieważ "mosca" jest rzeczownikiem rodzaju żeńskiego w języku hiszpańskim. Jest to zależne tylko od tłumaczenia, a nie od źródła. Jeśli nie ma prefiksu, będzie to pusty ciąg.
    
    * `backTranslations`: Lista "tłumaczeń wstecznych" celu. Na przykład słowa źródłowe, które obiekt docelowy może przetłumaczyć. Lista jest gwarantowana, aby zawierać słowo źródłowe, który został poproszony (np. jeśli słowo źródłowe jest wyszukane jest `backTranslations` "fly", to jest gwarantowane, że "fly" będzie na liście). Jednak nie jest gwarantowane, aby być na pierwszej pozycji, a często nie będzie. Każdy element `backTranslations` listy jest obiektem opisanym przez następujące właściwości:

        * `normalizedText`: Ciąg dający znormalizowaną formę terminu źródłowego, który jest wstecznym tłumaczeniem obiektu docelowego. Ta wartość powinna być używana jako dane wejściowe do [przykładów wyszukiwania](./v3-0-dictionary-examples.md).        

        * `displayText`: Ciąg dający termin źródłowy, który jest wstecznym tłumaczeniem obiektu docelowego w formie najlepiej dostosowanej do wyświetlania użytkownika końcowego.

        * `numExamples`: Liczba całkowita reprezentująca liczbę przykładów dostępnych dla tej pary tłumaczeń. Rzeczywiste przykłady muszą być pobierane z osobnym wywołaniem do [przykładów wyszukiwania](./v3-0-dictionary-examples.md). Numer ten jest przeznaczony głównie do ułatwienia wyświetlania w UX. Na przykład interfejs użytkownika może dodać hiperłącze do tłumaczenia wstecznego, jeśli liczba przykładów jest większa niż zero i pokazać tłumaczenie wsteczne jako zwykły tekst, jeśli nie ma żadnych przykładów. Należy zauważyć, że rzeczywista liczba przykładów zwracanych przez wywołanie do [przykładów wyszukiwania](./v3-0-dictionary-examples.md) może być mniejsza niż `numExamples`, ponieważ dodatkowe filtrowanie może być stosowane na bieżąco, aby usunąć "złe" przykłady.
        
        * `frequencyCount`: Liczba całkowita reprezentująca częstotliwość tej pary tłumaczeń w danych. Głównym celem tego pola jest zapewnienie interfejsu użytkownika ze środków do sortowania tłumaczeń wstecznych, więc najczęściej terminy są pierwsze.

    > [!NOTE]
    > Jeśli termin, który jest wyszukany nie istnieje w słowniku, odpowiedź jest `translations` 200 (OK), ale lista jest pusta lista.

## <a name="examples"></a>Przykłady

W tym przykładzie pokazano, jak wyszukać `fly` alternatywne tłumaczenia w języku hiszpańskim terminu angielskiego .

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

Organ odpowiedzi (w skrócie dla jasności) jest:

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

W tym przykładzie pokazano, co się dzieje, gdy termin wyszukany nie istnieje dla prawidłowej pary słownika.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

Ponieważ termin nie znajduje się w słowniku, treść `translations` odpowiedzi zawiera pustą listę.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
