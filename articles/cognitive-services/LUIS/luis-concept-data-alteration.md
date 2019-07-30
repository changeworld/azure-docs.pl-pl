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
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 198ce98808c8a62a839d154c365518c9e8263056
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619897"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Instrukcja ALTER wypowiedź danych przed lub w trakcie prognoz
Usługa LUIS udostępnia metody do manipulowania wypowiedź przed lub podczas prognozowania. Obejmują one [naprawianie błędów](luis-tutorial-bing-spellcheck.md)i rozwiązywanie problemów ze strefą czasową dla prekompilowanego [datetimeV2](luis-reference-prebuilt-datetimev2.md). 

## <a name="correct-spelling-errors-in-utterance"></a>W poprawianiu błędów pisowni w polu wypowiedź
Używa usługi LUIS [Bing pisowni Sprawdź interfejsu API w wersji 7](../Bing-Spell-Check/overview.md) poprawiać błędy pisowni w wypowiedź. Usługa LUIS musi mieć klucz skojarzony z tą usługą. Utwórz klucz, a następnie dodaj klucz jako parametr querystring na [punktu końcowego](https://go.microsoft.com/fwlink/?linkid=2092356). 

Możesz również poprawić błędy pisowni w **testu** panelu przez [wprowadzenie klucza](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Klucz jest przechowywana jako zmienną sesji przeglądarki panelu testu. Dodaj klucz do panelu testu w każdej sesji przeglądarki, chcesz, aby pisownia rozwiązany. 

Użycie klucza w panelu testu i na punkt końcowy wliczane [użycie klucza](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) limitu przydziału. Usługa LUIS implementuje sprawdzania pisowni Bing limity długości tekstu. 

Punkt końcowy wymaga dwóch parametrów dla korekty pisowni pracę:

|Param|Wartość|
|--|--|
|`spellCheck`|wartość logiczna|
|`bing-spell-check-subscription-key`|[7 interfejsu API sprawdzanie pisowni Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) klucza punktu końcowego|

Gdy [Bing pisowni Sprawdź interfejsu API w wersji 7](https://azure.microsoft.com/services/cognitive-services/spell-check/) wykrywa błąd, oryginalnym wypowiedź i poprawione wypowiedź są zwracane z punktu końcowego oraz prognozy.

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
 
### <a name="list-of-allowed-words"></a>Lista dozwolonych wyrazów
Interfejs API sprawdzania pisowni Bing używany w programie LUIS nie obsługuje listy wyrazów (nazywanych również dozwolonych) słów do ignorowania podczas zmian sprawdzania pisowni. Jeśli musisz zezwolić na listę wyrazów lub akronimów, przetwórz wypowiedź w aplikacji klienckiej przed wysłaniem wypowiedź do LUIS w celu przewidywania założeń.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Zmień strefę czasową datetimeV2 wstępnie utworzone jednostki
Gdy aplikacja LUIS używa wstępnie skompilowanej jednostki [datetimeV2](luis-reference-prebuilt-datetimev2.md) , w odpowiedzi predykcyjnej można zwrócić wartość typu DateTime. Strefa czasowa żądania jest używany do określenia poprawną datę i godzinę do zwrócenia. Jeśli żądanie pochodzi z robota lub innej aplikacji scentralizowane przed przejściem do usługi LUIS, popraw strefy czasowej, których używa usługi LUIS. 

### <a name="endpoint-querystring-parameter"></a>Parametr querystring punktu końcowego
Strefa czasowa jest usuwana przez dodanie użytkownika strefa czasowa stanowiąca kryterium [punktu końcowego](https://go.microsoft.com/fwlink/?linkid=2092356) przy użyciu `timezoneOffset` param. Wartość `timezoneOffset` powinna być liczbą dodatnią lub ujemną, w ciągu kilku minut, aby zmienić czas.  

|Param|Wartość|
|--|--|
|`timezoneOffset`|liczba dodatnia lub ujemna, w ciągu kilku minut|

### <a name="daylight-savings-example"></a>Przykład oszczędność czasu letniego
Jeśli potrzebujesz zwrócone datetimeV2 wstępnie utworzone, aby dostosować czas letni, należy użyć `timezoneOffset` parametr querystring z +/-wartości w ciągu kilku minut do [punktu końcowego](https://go.microsoft.com/fwlink/?linkid=2092356) zapytania.

Dodaj 60 minut: 

https://{region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appId}?q=Turn światła na? **timezoneOffset = 60**& pełne = {atrybut typu wartość logiczna} & Sprawdzanie pisowni = {atrybut typu wartość logiczna} & przemieszczania = {atrybut typu wartość logiczna} & bing pisowni wyboru subscription-key dziennika & = {ciąg} = {atrybut typu wartość logiczna}

Usuń 60 minut: 

https://{region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appId}?q=Turn światła na? **timezoneOffset =-60**& pełne = {atrybut typu wartość logiczna} & Sprawdzanie pisowni = {atrybut typu wartość logiczna} & przemieszczania = {atrybut typu wartość logiczna} & bing pisowni wyboru subscription-key dziennika & = {ciąg} = {atrybut typu wartość logiczna}

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>Kod C# określa poprawnej wartości timezoneOffset
Następujący kod C# używa [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) klasy [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) metodę, aby określić poprawny `timezoneOffset` na podstawie czasu systemu:

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
> [Poprawianie błędów pisowni za pomocą tego samouczka](luis-tutorial-bing-spellcheck.md)
