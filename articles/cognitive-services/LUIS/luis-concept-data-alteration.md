---
title: Zmiana danych — LUIS
description: Dowiedz się, jak można zmienić danych przed prognozy w Language Understanding (LUIS)
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 5547724a6333d248a7ba4e9aeecaaa8f331feb7d
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77148270"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Instrukcja ALTER wypowiedź danych przed lub w trakcie prognoz
Usługa LUIS udostępnia metody do manipulowania wypowiedź przed lub podczas prognozowania. Obejmują one [naprawianie błędów](luis-tutorial-bing-spellcheck.md)i rozwiązywanie problemów ze strefą czasową dla prekompilowanego [datetimeV2](luis-reference-prebuilt-datetimev2.md).

## <a name="correct-spelling-errors-in-utterance"></a>W poprawianiu błędów pisowni w polu wypowiedź


### <a name="v3-runtime"></a>Środowisko uruchomieniowe v3

Przed wysłaniem wypowiedź do LUIS należy wstępnie przetworzyć tekst w celu wprowadzania poprawek pisowni. Użyj przykładu wyrażenia długości z poprawną pisownią, aby upewnić się, że pobrano poprawne przewidywania.

Użyj [Sprawdzanie pisowni Bing](../bing-spell-check/overview.md) , aby poprawić tekst przed wysłaniem go do Luis.

### <a name="prior-to-v3-runtime"></a>Przed wprowadzeniem do wersji 3 środowiska uruchomieniowego

LUIS [Sprawdzanie pisowni Bing używa interfejsu API wersji 7](../Bing-Spell-Check/overview.md) do poprawiania błędów pisowni w wypowiedź. Usługa LUIS musi mieć klucz skojarzony z tą usługą. Utwórz klucz, a następnie Dodaj klucz jako parametr QueryString w [punkcie końcowym](https://go.microsoft.com/fwlink/?linkid=2092356).

Punkt końcowy wymaga dwóch parametrów dla korekty pisowni pracę:

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

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Zmień strefę czasową datetimeV2 wstępnie utworzone jednostki
Gdy aplikacja LUIS używa wstępnie skompilowanej jednostki [datetimeV2](luis-reference-prebuilt-datetimev2.md) , w odpowiedzi predykcyjnej można zwrócić wartość typu DateTime. Strefa czasowa żądania jest używany do określenia poprawną datę i godzinę do zwrócenia. Jeśli żądanie pochodzi z robota lub innej aplikacji scentralizowane przed przejściem do usługi LUIS, popraw strefy czasowej, których używa usługi LUIS.

### <a name="endpoint-querystring-parameter"></a>Parametr querystring punktu końcowego
Strefa czasowa jest korygowana przez dodanie strefy czasowej użytkownika do [punktu końcowego](https://go.microsoft.com/fwlink/?linkid=2092356) przy użyciu `timezoneOffset` PARAM. Wartość `timezoneOffset` powinna być liczbą dodatnią lub ujemną w minutach, aby zmienić czas.

|Param|Wartość|
|--|--|
|`timezoneOffset`|liczba dodatnia lub ujemna, w ciągu kilku minut|

### <a name="daylight-savings-example"></a>Przykład oszczędność czasu letniego
Jeśli potrzebujesz dostosowanej wstępnie skompilowanej datetimeV2 w celu dostosowania do czasu letniego, należy użyć parametru `timezoneOffset` QueryString z wartością +/-w minutach dla kwerendy [punktu końcowego](https://go.microsoft.com/fwlink/?linkid=2092356) .

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

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>Kod C# określa poprawnej wartości timezoneOffset
Poniższy C# kod używa metody [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) klasy [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) w celu określenia poprawnej `timezoneOffset` w oparciu o czas systemowy:

```csharp
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
