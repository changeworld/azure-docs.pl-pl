---
title: Aby migracja interfejsu API w wersji 2 w wersji 1
titleSuffix: Azure Cognitive Services
description: Punkt końcowy w wersji 1 i tworzenia interfejsy API interpretacji języka są przestarzałe. Użyj tego przewodnika, aby zrozumieć, jak przeprowadzić migrację do endpoint w wersji 2 i Tworzenie interfejsów API.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 9eb73db6f641d3a5f5bb82901bd12ea291eada58
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60196035"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>Interfejsu API v1 na v2 Przewodnik migracji dla aplikacji usługi LUIS
Wersja 1 [punktu końcowego](https://aka.ms/v1-endpoint-api-docs) i [tworzenia](https://aka.ms/v1-authoring-api-docs) interfejsy API są przestarzałe. Użyj tego przewodnika, aby zrozumieć, jak przeprowadzić migrację do wersji 2 [punktu końcowego](https://aka.ms/luis-endpoint-apis) i [tworzenia](https://aka.ms/luis-authoring-apis) interfejsów API. 

## <a name="new-azure-regions"></a>Nowe regiony platformy Azure
Usługa LUIS ma nowy [regionów](https://aka.ms/LUIS-regions) podany dla interfejsów API usługi LUIS. Usługa LUIS zapewnia innego portalu dla regionu grup. Aplikacja musi zostać utworzona w tym samym regionie, które chcesz zbadać. Aplikacje nie są automatycznie migrowane regionów. Możesz wyeksportować aplikację z jednego regionu, a następnie importowania do innej, aby była dostępna w nowym regionie.

## <a name="authoring-route-changes"></a>Tworzenie zmiany trasy
Trasy interfejsu API tworzenia zmieniła się z pomocą **programu** tras za pomocą **interfejsu api** trasy.


| version | trasa |
|--|--|
|1|/Luis/V1.0/**programu**/apps|
|2|/Luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Zmiany trasy punktu końcowego
Punkt końcowy interfejsu API ma nowe parametry ciągu zapytania, a także różnych odpowiedzi. Jeśli flaga pełne ma wartość true, wszystkie opcje, niezależnie od tego, wynik, są zwracane w tablicy o nazwie intencji oprócz topScoringIntent.

| version | Pobierz trasę |
|--|--|
|1|/Luis/V1/Application? ID = {appId} & q = {pytania}|
|2|/ luis/v2.0/apps/{appId}?q={q} [& timezoneOffset] [-verbose] [& Sprawdzanie pisowni] [& przemieszczania] [& bing pisowni wyboru subscription-key] [& dziennika]|


odpowiedź sukcesu punkt końcowy 1:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

odpowiedź sukcesu punktu końcowego v2:
```json
{
  "query": "forward to frank 30 dollars through HSBC",
  "topScoringIntent": {
    "intent": "give",
    "score": 0.3964121
  },
  "entities": [
    {
      "entity": "30",
      "type": "builtin.number",
      "startIndex": 17,
      "endIndex": 18,
      "resolution": {
        "value": "30"
      }
    },
    {
      "entity": "frank",
      "type": "frank",
      "startIndex": 11,
      "endIndex": 15,
      "score": 0.935219169
    },
    {
      "entity": "30 dollars",
      "type": "builtin.currency",
      "startIndex": 17,
      "endIndex": 26,
      "resolution": {
        "unit": "Dollar",
        "value": "30"
      }
    },
    {
      "entity": "hsbc",
      "type": "Bank",
      "startIndex": 36,
      "endIndex": 39,
      "resolution": {
        "values": [
          "BankeName"
        ]
      }
    }
  ]
}
```

## <a name="key-management-no-longer-in-api"></a>Zarządzanie kluczami nie jest już w interfejsie API
Klucz punktu końcowego subskrypcji interfejsy API są przestarzałe zwracanie 410 GONE.

| version | trasa |
|--|--|
|1|/Luis/V1.0/prog/Subscriptions|
|1|/ luis/v1.0/prog/subscriptions/{subscriptionKey}|

Azure [klucze punktu końcowego](luis-how-to-azure-subscription.md) są generowane w witrynie Azure portal. Przypisany klawisz aplikacją usługi LUIS w **[Publikuj](luis-how-to-azure-subscription.md)** strony. Nie trzeba znać rzeczywistej wartości klucza. Usługa LUIS używa nazwy subskrypcji, aby przypisania. 

## <a name="new-versioning-route"></a>Nową trasę przechowywania wersji
V2 model jest teraz zawarty w [wersji](luis-how-to-manage-versions.md). Nazwa wersji jest 10 znaków w trasie. Domyślna wersja to "0.1".

| version | trasa |
|--|--|
|1|/Luis/V1.0/**programu**/apps/ {appId} / jednostek|
|2|/Luis/**api**/v2.0/apps/{appId}/**wersji**/ {versionId} / jednostek|

## <a name="metadata-renamed"></a>Zmieniono nazwę metadanych
Kilka interfejsów API, które zwracają metadanych usługi LUIS mają nowe nazwy.

| Nazwa trasy V1 | Nazwa trasy w wersji 2 |
|--|--|
|PersonalAssistantApps |Asystenci|
|applicationcultures|kultur|
|applicationdomains|domeny|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>"Przykładowy" zmieniona na "Zaproponuj"
Usługa LUIS sugeruje wypowiedzi z istniejących [wypowiedzi punktu końcowego](luis-how-to-review-endpoint-utterances.md) , może pomóc w lepszym modelu. W poprzedniej wersji, ta nosiła nazwę **przykładowe**. W nowej wersji nazwa została zmieniona z przykładu, aby **zasugerować**. Jest to nazywane **[Przejrzyj wypowiedzi punktu końcowego](luis-how-to-review-endpoint-utterances.md)** w witrynie internetowej usługi LUIS.

| version | trasa |
|--|--|
|1|/Luis/V1.0/**programu**/entities/ /apps/ {appId} {entityId} /**próbki**|
|1|/Luis/V1.0/**programu**/intents/ /apps/ {appId} {intentId} /**próbki**|
|2|/Luis/**api**/v2.0/apps/{appId}/**wersji**/ /entities/ {versionId} {entityId} /**zasugerować**|
|2|/Luis/**api**/v2.0/apps/{appId}/**wersji**/ /intents/ {versionId} {intentId} /**zasugerować**|


## <a name="create-app-from-prebuilt-domains"></a>Tworzenie aplikacji na podstawie ze wstępnie utworzonych domen
[Ze wstępnie utworzonych domen](luis-how-to-use-prebuilt-domains.md) zapewnić model domeny wstępnie zdefiniowane. Ze wstępnie utworzonych domen umożliwiają szybkie tworzenie aplikacji usługi LUIS dla typowych domeny. Ten interfejs API umożliwia tworzenie nowej aplikacji na podstawie wstępnie domeny. Odpowiedź jest nowy identyfikator aplikacji.

|trasy w wersji 2|zlecenia|
|--|--|
|/Luis/API/v2.0/Apps/customprebuiltdomains  |GET, post|
|/ luis/api/v2.0/apps/customprebuiltdomains/{culture}  |Pobierz|

## <a name="importing-1x-app-into-2x"></a>Importowanie aplikacji 1.x do 2.x
1.x wyeksportowany plik JSON aplikacji ma kilka obszarów, które należy zmienić przed zaimportowaniem do [LUIS] [ LUIS] w wersji 2.0. 

### <a name="prebuilt-entities"></a>Wstępnie utworzonych jednostek 
[Ze wstępnie utworzonych jednostek](luis-prebuilt-entities.md) uległy zmianie. Upewnij się, że używasz wersji 2 ze wstępnie utworzonych jednostek. W tym za pomocą [datetimeV2](luis-reference-prebuilt-datetimev2.md), zamiast daty/godziny. 

### <a name="actions"></a>Akcje
Właściwość akcji nie jest już prawidłowy. Powinna być pusta 

### <a name="labeled-utterances"></a>Labeled wypowiedzi
V1 dozwolone etykietami wypowiedzi na zawiera spacje na początku lub końcu wyrazu lub frazy. Usunięte spacje. 

## <a name="common-reasons-for-http-response-status-codes"></a>Typowe przyczyny kodów stanu odpowiedzi HTTP
Zobacz [kodów odpowiedzi interfejsu API usługi LUIS](luis-reference-response-codes.md).

## <a name="next-steps"></a>Kolejne kroki

Korzystanie z dokumentacji interfejsu API w wersji 2, aby zaktualizować istniejące REST wywołania usługi LUIS [punktu końcowego](https://aka.ms/luis-endpoint-apis) i [tworzenia](https://aka.ms/luis-authoring-apis) interfejsów API. 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
