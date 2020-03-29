---
title: Migracja interfejsu API w wersji 1 do 2
titleSuffix: Azure Cognitive Services
description: Punkt końcowy w wersji 1 i tworzenie interfejsów API rozumienia języka są przestarzałe. Ten przewodnik służy do do zrozumienia, jak przeprowadzić migrację do punktu końcowego w wersji 2 i tworzyć interfejsy API.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 2f67bf0951ef8928297c71e8fc9f924cf05c63f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68932687"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>Przewodnik po migracji interfejsu API w wersji 1 do wersji 2 dla aplikacji usługi LUIS
[Punkt końcowy](https://aka.ms/v1-endpoint-api-docs) w wersji 1 i [interfejsy](https://aka.ms/v1-authoring-api-docs) API tworzenia są przestarzałe. Ten przewodnik służy do do zrozumienia, jak przeprowadzić migrację do [punktu końcowego](https://go.microsoft.com/fwlink/?linkid=2092356) w wersji 2 i [tworzyć interfejsy](https://go.microsoft.com/fwlink/?linkid=2092087) API. 

## <a name="new-azure-regions"></a>Nowe regiony platformy Azure
Usługa LUIS ma nowe [regiony przewidziane](https://aka.ms/LUIS-regions) dla interfejsów API usługi LUIS. Usługa LUIS udostępnia inny portal dla grup regionów. Aplikacja musi być autorem w tym samym regionie, który oczekuje się kwerendy. Aplikacje nie migrują automatycznie regionów. Eksportujesz aplikację z jednego regionu, a następnie importuj do innego, aby była dostępna w nowym regionie.

## <a name="authoring-route-changes"></a>Tworzenie zmian trasy
Trasa interfejsu API tworzenia zmieniona z przy użyciu trasy **prog** do korzystania z trasy **interfejsu api.**


| version | trasa |
|--|--|
|1|/luis/v1.0/**prog**/apps|
|2|/luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Zmiany trasy punktu końcowego
Interfejs API punktu końcowego ma nowe parametry ciągu kwerendy, a także inną odpowiedź. Jeśli flaga pełne jest true, wszystkie intencje, niezależnie od wyniku, są zwracane w tablicy o nazwie intencji, oprócz topScoringIntent.

| version | Trasa GET |
|--|--|
|1|/luis/v1/application?ID={appId}&q={q}|
|2|/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]|


odpowiedź na sukces punktu końcowego v1:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

odpowiedź na sukces punktu końcowego v2:
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
Interfejsy API klucza końcowego subskrypcji są przestarzałe, zwracając 410 GONE.

| version | trasa |
|--|--|
|1|/luis/v1.0/prog/subscriptions|
|1|/luis/v1.0/prog/subscriptions/{subscriptionKey}|

[Klucze punktu końcowego](luis-how-to-azure-subscription.md) platformy Azure są generowane w witrynie Azure portal. Klucz można przypisać do aplikacji usługi LUIS na stronie **[Publikowania.](luis-how-to-azure-subscription.md)** Nie trzeba znać rzeczywistej wartości klucza. Usługa LUIS używa nazwy subskrypcji do dokonania przypisania. 

## <a name="new-versioning-route"></a>Nowa trasa przechowywania wersji
Model v2 jest teraz zawarty w [wersji](luis-how-to-manage-versions.md). Nazwa wersji to 10 znaków w trasie. Domyślną wersją jest "0.1".

| version | trasa |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities|
|2|/luis/ api /v2.0/apps/{appId}/ versions /{versionId}/entities /luis/ api /v2.0/apps/{appId}/ versions /{versionId}/entities /luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/entities /luis|

## <a name="metadata-renamed"></a>Nazwa metadanych
Kilka interfejsów API zwracających metadane usługi LUIS ma nowe nazwy.

| nazwa trasy w wersji 1 | nazwa trasy v2 |
|--|--|
|PersonalAssistantApps |Asystentów|
|kultury zastosowań|kultury|
|applicationdoains|Domen|
|scenariusze aplikacji|scenariusze użytkowania|


## <a name="sample-renamed-to-suggest"></a>"Przykład" zmieniono nazwę na "sugerować"
Usługa LUIS sugeruje wypowiedzi z istniejących [wypowiedzi punktu końcowego,](luis-how-to-review-endpoint-utterances.md) które mogą poprawić model. W poprzedniej wersji nazwano to **przykładem**. W nowej wersji nazwa jest zmieniana z przykładowego na **sugerowany**. Jest to tak zwane **[Przejrzyj wypowiedzi punktów końcowych](luis-how-to-review-endpoint-utterances.md)** w witrynie sieci Web usługi LUIS.

| version | trasa |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities/{entityId}/**przykład**|
|1|/luis/v1.0/**prog**/apps/{appId}/intents/{intentId}/**przykład**|
|2|/luis/ api /v2.0/apps/{appId}/ versions /{versionId}/entities/{entityId}/ suggest /luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/entities/{entityId}/**suggest /**|
|2|/luis/ api /v2.0/apps/{appId}/ versions /{versionId}/intents/{intentId}/ suggest /luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/intents/{intentId}/ suggest /{versionId}/intents/{intentId}/**suggest /{**|


## <a name="create-app-from-prebuilt-domains"></a>Tworzenie aplikacji z wstępnie utworzonych domen
[Wstępnie utworzone domeny](luis-how-to-use-prebuilt-domains.md) zapewniają wstępnie zdefiniowany model domeny. Wstępnie utworzone domeny umożliwiają szybkie tworzenie aplikacji usługi LUIS dla typowych domen. Ten interfejs API umożliwia utworzenie nowej aplikacji na podstawie wstępnie utworzonej domeny. Odpowiedź jest nowy appID.

|trasa v2|Czasownik|
|--|--|
|/luis/api/v2.0/apps/customprebuiltdoains  |dostać, pisać|
|/luis/api/v2.0/apps/customprebuiltdoains/{culture}  |get|

## <a name="importing-1x-app-into-2x"></a>Importowanie aplikacji 1.x do 2.x
Eksportowana aplikacja 1.x JSON ma pewne obszary, które należy zmienić przed zaimportowaniem do [usługi LUIS][LUIS] 2.0. 

### <a name="prebuilt-entities"></a>Wstępnie utworzone jednostki 
[Wstępnie utworzone jednostki uległy](luis-prebuilt-entities.md) zmianie. Upewnij się, że używasz wstępnie utworzonych jednostek V2. Obejmuje to użycie [datetimeV2](luis-reference-prebuilt-datetimev2.md)zamiast datetime. 

### <a name="actions"></a>Akcje
Właściwość akcji nie jest już prawidłowa. Powinien to być pusty 

### <a name="labeled-utterances"></a>Wypowiedzi z etykietą
Wersja 1 dozwolone wypowiedzi oznaczone zawierać spacje na początku lub na końcu wyrazu lub frazy. Usunięto spacje. 

## <a name="common-reasons-for-http-response-status-codes"></a>Typowe przyczyny kodów stanu odpowiedzi HTTP
Zobacz [Kody odpowiedzi interfejsu API usługi LUIS](luis-reference-response-codes.md).

## <a name="next-steps"></a>Następne kroki

Użyj dokumentacji interfejsu API w wersji 2, aby zaktualizować istniejące wywołania REST do [punktu końcowego](https://go.microsoft.com/fwlink/?linkid=2092356) usługi LUIS i [tworzyć](https://go.microsoft.com/fwlink/?linkid=2092087) interfejsy API. 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
