---
title: Zmiana danych — LUIS
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak można zmienić dane przed przewidywaniami w Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: diberry
ms.openlocfilehash: 734389c92ede88d336df60a1a79a738d2abcfa92
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703177"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Zmień wypowiedź danych przed lub podczas przewidywania
LUIS zapewnia sposoby manipulowania wypowiedź przed lub podczas przewidywania. Obejmują one [naprawianie błędów](luis-tutorial-bing-spellcheck.md)i rozwiązywanie problemów ze strefą czasową dla prekompilowanego [datetimeV2](luis-reference-prebuilt-datetimev2.md). 

## <a name="correct-spelling-errors-in-utterance"></a>Poprawianie błędów pisowni w wypowiedź
LUIS [Sprawdzanie pisowni Bing używa interfejsu API wersji 7](../Bing-Spell-Check/overview.md) do poprawiania błędów pisowni w wypowiedź. LUIS wymaga klucza skojarzonego z tą usługą. Utwórz klucz, a następnie Dodaj klucz jako parametr QueryString w [punkcie końcowym](https://go.microsoft.com/fwlink/?linkid=2092356). 

Możesz również poprawić błędy pisowni w panelu **test** , [wprowadzając klucz](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Klucz jest przechowywany jako zmienna sesji w przeglądarce dla panelu test. Dodaj klucz do panelu test w każdej sesji przeglądarki, dla którego ma zostać poprawiona pisownia. 

Użycie klucza w panelu testowania i w punkcie końcowym w kierunku przydziału [użycia klucza](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) . LUIS implementuje limity sprawdzanie pisowni Bing dla długości tekstu. 

Punkt końcowy wymaga dwóch parametrów do działania poprawek pisowni:

|Param|Wartość|
|--|--|
|`spellCheck`|wartość logiczna|
|`bing-spell-check-subscription-key`|Klucz punktu końcowego [wersji 7 interfejsu API sprawdzanie pisowni Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/)|

Gdy [Sprawdzanie pisowni Bing API wersji 7](https://azure.microsoft.com/services/cognitive-services/spell-check/) wykrywa błąd, oryginalny wypowiedź i poprawione wypowiedź są zwracane wraz z przewidywaniami z punktu końcowego.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)
 
```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * * 

### <a name="list-of-allowed-words"></a>Lista dozwolonych wyrazów
Interfejs API sprawdzania pisowni Bing używany w programie LUIS nie obsługuje listy wyrazów ignorowanych podczas sprawdzania pisowni. Jeśli musisz zezwolić na listę wyrazów lub akronimów, przetwórz wypowiedź w aplikacji klienckiej przed wysłaniem wypowiedź do LUIS w celu przewidywania założeń.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Zmień strefę czasową wstępnie skompilowanej jednostki datetimeV2
Gdy aplikacja LUIS używa wstępnie skompilowanej jednostki [datetimeV2](luis-reference-prebuilt-datetimev2.md) , w odpowiedzi predykcyjnej można zwrócić wartość typu DateTime. Strefa czasowa żądania służy do określenia poprawnej wartości DateTime do zwrócenia. Jeśli żądanie pochodzi z bot lub innej scentralizowanej aplikacji przed uzyskaniem LUIS, należy poprawić użycie LUIS strefy czasowej. 

### <a name="endpoint-querystring-parameter"></a>Parametr QueryString punktu końcowego
Strefa czasowa jest korygowana przez dodanie strefy czasowej użytkownika do [punktu końcowego](https://go.microsoft.com/fwlink/?linkid=2092356) przy użyciu parametru `timezoneOffset`. Wartość `timezoneOffset` powinna być liczbą dodatnią lub ujemną w minutach, aby zmienić czas.  

|Param|Wartość|
|--|--|
|`timezoneOffset`|liczba dodatnia lub ujemna, w minutach|

### <a name="daylight-savings-example"></a>Przykład oszczędności czasu letniego
Jeśli potrzebujesz dostosowanej wstępnie skompilowanej datetimeV2 do dostosowania czasu letniego, należy użyć parametru QueryString `timezoneOffset` z wartością +/-w minutach dla kwerendy [punktu końcowego](https://go.microsoft.com/fwlink/?linkid=2092356) .

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[Żądanie punktu końcowego przewidywania wersji 2](#tab/V2)

Dodaj 60 minut: 

https://{region}. API. poznawcze. Microsoft. com/Luis/v 2.0/Apps/{appId}? q = włączaj światła? **timezoneOffset = 60**& verbose = {boolean} & sprawdzania pisowni = {boolean} & przejściowe = {boolean} & Bing-pisownia-Check-Subscription-Key = {string} & log = {Boolean}

Usuń 60 minut: 

https://{region}. API. poznawcze. Microsoft. com/Luis/v 2.0/Apps/{appId}? q = włączaj światła? **timezoneOffset =-60**& verbose = {boolean} & sprawdzanie = {boolean} & przejściowe = {boolean} & Bing-pisownia-Check-Subscription-Key = {string} & log = {Boolean}

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[Żądanie punktu końcowego przewidywania v3](#tab/V3)

Dodaj 60 minut:

https://{region}. API. poznawcze. Microsoft. com/Luis/v 3.0 — wersja zapoznawcza/aplikacje/{appId}/gniazda/produkcja/przewidywanie? zapytanie = Włącz kontrolki? **timezoneOffset = 60**& sprawdzania pisowni = {boolean} & Bing-pisownia-Check-Subscription-Key = {string} & log = {Boolean}

Usuń 60 minut: 

https://{region}. API. poznawcze. Microsoft. com/Luis/v 3.0 — wersja zapoznawcza/aplikacje/{appId}/gniazda/produkcja/przewidywanie? zapytanie = Włącz kontrolki? **timezoneOffset =-60**& sprawdzania pisowni = {boolean} & Bing-pisownia-Check-Subscription-Key = {string} & log = {Boolean}

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

* * * 

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C#kod określa poprawną wartość timezoneOffset
Poniższy C# kod używa metody [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) klasy [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) w celu określenia poprawnej `timezoneOffset` w oparciu o czas systemowy:

```CSharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Popraw błędy pisowni w tym samouczku](luis-tutorial-bing-spellcheck.md)
