---
title: Zmiana danych — usługa LUIS
description: Dowiedz się, jak można zmieniać dane przed przewidywaniem w zrozumieni języku (LUIS)
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: b3b36351a64a4e1a0bd13d5785a4e0609a80901d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292047"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Zmienianie danych wypowiedź przed lub w trakcie przewidywania
Usługa LUIS udostępnia sposoby manipulowania wypowiedź przed lub w trakcie przewidywania. Należą do nich [naprawianie pisowni](luis-tutorial-bing-spellcheck.md)i rozwiązywanie problemów ze strefą czasową dla wstępnie utworzonego [pliku datetimeV2.](luis-reference-prebuilt-datetimev2.md)

## <a name="correct-spelling-errors-in-utterance"></a>Poprawianie błędów pisowni w wypowiedzi


### <a name="v3-runtime"></a>Środowisko wykonawcze V3

Przedprocesowy tekst poprawek pisowni przed wysłaniem wypowiedź do usługi LUIS. Użyj wypowiedzi przykład z poprawnej pisowni, aby upewnić się, że masz poprawne prognoz.

Sprawdzanie [pisowni bing,](../bing-spell-check/overview.md) aby poprawić tekst przed wysłaniem go do usługi LUIS.

### <a name="prior-to-v3-runtime"></a>Przed uruchomieniem v3

Usługa LUIS używa [interfejsu API sprawdzania pisowni Bing w wersji 7](../Bing-Spell-Check/overview.md) do poprawiania błędów pisowni w wypowiedź. Usługa LUIS potrzebuje klucza skojarzonego z tą usługą. Utwórz klucz, a następnie dodaj klucz jako parametr querystring w [punkcie końcowym](https://go.microsoft.com/fwlink/?linkid=2092356).

Punkt końcowy wymaga dwóch params dla poprawek pisowni do pracy:

|Param|Wartość|
|--|--|
|`spellCheck`|wartość logiczna|
|`bing-spell-check-subscription-key`|Klucz punktu [końcowego interfejsu API sprawdzania pisowni bing](https://azure.microsoft.com/services/cognitive-services/spell-check/)|

Gdy [interfejs API sprawdzania pisowni Bing V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) wykryje błąd, oryginalny wypowiedź i skorygowany wypowiedź są zwracane wraz z prognoz z punktu końcowego.

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania V3](#tab/V3)

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

### <a name="list-of-allowed-words"></a>Lista dozwolonych słów
Interfejs API sprawdzania pisowni Bing używany w usłudze LUIS nie obsługuje listy słów do zignorowania podczas sprawdzania pisowni zmian. Jeśli chcesz zezwolić na listę słów lub akronimów, przetwórz wypowiedź w aplikacji klienckiej przed wysłaniem wypowiedź do usługi LUIS do przewidywania intencji.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Zmienianie strefy czasowej wstępnie utworzonej encji datetimeV2
Gdy aplikacja usługi LUIS używa wstępnie utworzonej jednostki [datetimeV2,](luis-reference-prebuilt-datetimev2.md) wartość datetime mogą być zwracane w odpowiedzi przewidywania. Strefa czasowa żądania służy do określenia poprawnej daty do zwrócenia. Jeśli żądanie pochodzi z bota lub innej scentralizowanej aplikacji przed dotarciem do usługi LUIS, należy poprawić używane usługi LUIS strefy czasowej.

### <a name="endpoint-querystring-parameter"></a>Parametr ciągowania zapytania punktu końcowego
Strefa czasowa jest korygowana przez dodanie strefy czasowej `timezoneOffset` użytkownika do [punktu końcowego](https://go.microsoft.com/fwlink/?linkid=2092356) przy użyciu param. Wartość `timezoneOffset` powinna być dodatnią lub ujemną liczbą w minutach, aby zmienić czas.

|Param|Wartość|
|--|--|
|`timezoneOffset`|dodatnia lub ujemna liczba, w minutach|

### <a name="daylight-savings-example"></a>Przykład czasu letniego
Jeśli potrzebujesz zwróconego wstępnie utworzonego datetimeV2, aby dostosować `timezoneOffset` czas letni, należy użyć querystring parametr z wartością +/- w minutach dla kwerendy [punktu końcowego.](https://go.microsoft.com/fwlink/?linkid=2092356)

#### <a name="v2-prediction-endpoint-request"></a>[Żądanie punktu końcowego przewidywania wersji 2](#tab/V2)

Dodaj 60 minut:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Usuń 60 minut:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v3-prediction-endpoint-request"></a>[Żądanie punktu końcowego przewidywania wersji 3](#tab/V3)

Dodaj 60 minut:

`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Usuń 60 minut:

`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=-60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

* * *

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>Kod C# określa prawidłową wartość timezoneOffset
Następujący kod języka C# używa metody [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) klasy `timezoneOffset` [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) w celu określenia poprawnej na podstawie czasu systemowego:

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
> [Poprawianie błędów pisowni w tym samouczku](luis-tutorial-bing-spellcheck.md)
