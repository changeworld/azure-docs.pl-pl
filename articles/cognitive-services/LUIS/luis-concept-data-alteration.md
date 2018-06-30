---
title: Pojęciami zmiany danych w LUIS - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak danych można zmienić przed prognoz opis języka (LUIS)
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/26/2018
ms.author: v-geberr
ms.openlocfilehash: ab9b9fe5c34c334fd65971f9040c8e43467aa242
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112154"
---
# <a name="data-alterations"></a>Zmiany danych
LUIS udostępnia metod manipulowania utterance przed lub podczas prognozowania. 

## <a name="correct-spelling-errors-in-utterance"></a>Poprawianie błędów pisowni w utterance
Używa LUIS [7 interfejsu API sprawdzania pisowni usługi Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) do poprawiania błędów pisowni w utterance. LUIS musi mieć klucz skojarzony z tą usługą. Utwórz klucz, a następnie dodaj klucz jako parametr querystring na [punktu końcowego](https://aka.ms/luis-endpoint-apis). 

Można również Popraw pisownię w **testu** panelu przez [wprowadzania klucza](interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Klucz jest przechowywany jako zmiennej sesji w przeglądarce testu panelu. Dodaj klucz do panelu testu w każdej sesji przeglądarki ma pisowni rozwiązany. 

Użycie klucza w panelu testu i liczbę punktu końcowego kierunku [użycie klucza](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) przydziału. LUIS implementuje sprawdzania pisowni usługi Bing limity długość tekstu. 

Punkt końcowy wymagane są dwa parametry dla pisowni pracę:

|Param|Wartość|
|--|--|
|`spellCheck`|wartość logiczna|
|`bing-spell-check-subscription-key`|[7 interfejsu API sprawdzania pisowni usługi Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) klucz punktu końcowego|

Gdy [7 interfejsu API sprawdzania pisowni usługi Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) wykrywa błąd, oryginalny utterance i poprawiony utterance są zwracane wraz z prognoz z punktu końcowego.

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
 
## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Zmień strefę czasową jednostki datetimeV2 wbudowane
Gdy aplikacja LUIS korzysta z jednostki datetimeV2 wbudowane, wartości daty i godziny może być zwracany w odpowiedzi prognozowania. Strefa czasowa żądania jest używany do określenia poprawną datę i godzinę do zwrócenia. Jeśli żądanie pochodzi z robotów lub innej aplikacji scentralizowane przed przejściem do LUIS, popraw strefy czasowej, który używa LUIS. 

### <a name="endpoint-querystring-parameter"></a>Parametr querystring punktu końcowego
Strefa czasowa jest usuwana, dodając strefie czasowej użytkownika do [punktu końcowego](https://aka.ms/luis-endpoint-apis) przy użyciu `timezoneOffset` param. Wartość `timezoneOffset` powinna być liczbą dodatnią lub ujemną, w minutach, aby zmienić czas.  

|Param|Wartość|
|--|--|
|`timezoneOffset`|liczbę dodatnią lub ujemną w minutach|

### <a name="daylight-savings-example"></a>Przykład oszczędność czasu letniego
Jeśli potrzebujesz zwrócony datetimeV2 wbudowane, aby dopasować do czasu letniego, należy użyć `timezoneOffset` parametr querystring o +/-wartość w minutach, [punktu końcowego](https://aka.ms/luis-endpoint-apis) zapytania.

Dodaj 60 minut: 

https://{region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appId}?q=Turn świateł na? **wartości timezoneOffset = 60**& pełne = {boolean} & Sprawdzanie pisowni = {boolean} & przemieszczania = {boolean} & bing pisowni wyboru subskrypcji — klucz dziennika & = {ciąg} = {boolean}

Usuń 60 minut: 

https://{region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appId}?q=Turn świateł na? **wartości timezoneOffset = – 60**& pełne = {boolean} & Sprawdzanie pisowni = {boolean} & przemieszczania = {boolean} & bing pisowni wyboru subskrypcji — klucz dziennika & = {ciąg} = {boolean}

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>Kod w języku C# określa poprawną wartość wartości timezoneOffset
W poniższym kodzie C# użyto [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo?view=netframework-4.7.1) klasy [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid?view=netframework-4.7.1#examples) metodę, aby określić poprawny `timezoneOffset` na podstawie czasu systemu:

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

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Poprawianie błędów pisowni z tego samouczka](luis-tutorial-bing-spellcheck.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions