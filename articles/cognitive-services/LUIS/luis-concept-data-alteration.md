---
title: Zmiana danych — LUIS
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak można zmienić danych przed prognozy w Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: a199821c4db7fd8131ec54700b8c999dfe604a6e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74222020"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Instrukcja ALTER wypowiedź danych przed lub w trakcie prognoz
Usługa LUIS udostępnia metody do manipulowania wypowiedź przed lub podczas prognozowania. Obejmują one [naprawianie błędów](luis-tutorial-bing-spellcheck.md)i rozwiązywanie problemów ze strefą czasową dla prekompilowanego [datetimeV2](luis-reference-prebuilt-datetimev2.md). 

## <a name="correct-spelling-errors-in-utterance"></a>W poprawianiu błędów pisowni w polu wypowiedź

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


LUIS [Sprawdzanie pisowni Bing używa interfejsu API wersji 7](../Bing-Spell-Check/overview.md) do poprawiania błędów pisowni w wypowiedź. Usługa LUIS musi mieć klucz skojarzony z tą usługą. Utwórz klucz, a następnie Dodaj klucz jako parametr QueryString w [punkcie końcowym](https://go.microsoft.com/fwlink/?linkid=2092356). 

<!--
You can also correct spelling errors in the **Test** panel by [entering the key](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key is kept as a session variable in the browser for the Test panel. Add the key to the Test panel in each browser session you want spelling corrected. 

Usage of the key in the test panel and at the endpoint count toward the [key usage](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) quota. LUIS implements Bing Spell Check limits for text length. 

-->

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
