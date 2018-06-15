---
title: Interfejs API migracji przewodnik od v1 v2 | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się do migracji z interfejsem API najnowszej konfiguracji.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/01/2018
ms.author: v-geberr
ms.openlocfilehash: 07c26753ed7198a2d98f4b7d5afb4cc7845fbcbe
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349812"
---
# <a name="api-v2-migration-guide"></a>Przewodnik migracji interfejsu API w wersji 2
Wersja 1 [punktu końcowego](https://aka.ms/v1-endpoint-api-docs) i [tworzenia](https://aka.ms/v1-authoring-api-docs) zostaną wycofane interfejsów API. Użyj tego przewodnika, aby zrozumieć, jak przeprowadzić migrację do wersji 2 [punktu końcowego](https://aka.ms/luis-endpoint-apis) i [tworzenia](https://aka.ms/luis-authoring-apis) interfejsów API. 

## <a name="new-azure-regions"></a>Nowe regiony platformy Azure
LUIS ma new [regionów](https://aka.ms/LUIS-regions) podany dla interfejsów API LUIS. LUIS zawiera inną witrynę sieci Web dla regionu grup. Aplikacja musi być utworzone w tym samym regionie, które chcesz zbadać. Aplikacje nie są automatycznie migrowane regionów. Możesz wyeksportować aplikację jeden region, a następnie importowanie do innego dla powinna być dostępna w regionie nowe.

## <a name="authoring-route-changes"></a>Zmiany trasy źródłowej
Trasa tworzenia interfejsu API zmieniła się z pomocą **programu** trasy przy użyciu **interfejsu api** trasy.


| wersja | trasa |
|--|--|
|1|/Luis/V1.0/**programu**/apps|
|2|/Luis/**interfejsu api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Zmiany trasy punktu końcowego
Punkt końcowy interfejsu API ma nowe parametry querystring, a także różne odpowiedzi. Jeśli flaga pełne ma wartość true, wszystkie opcje, niezależnie od tego, wynik, są zwracane w tablicy o nazwie intencje oprócz topScoringIntent.

| wersja | Pobierz trasy |
|--|--|
|1|/Luis/V1/Application? ID = {appId} & q = {q}|
|2|/ luis/v2.0/apps/{appId}?q={q} [& wartości timezoneOffset] [-verbose] [& Sprawdzanie pisowni] [& tymczasowej] [& bing pisowni wyboru subskrypcji — klucz] [& dziennika]|


odpowiedź sukcesu punkt końcowy 1:
```JSON
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

odpowiedź sukcesu punktu końcowego v2:
```JSON
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
Klucz subskrypcji interfejsów API są przestarzałe, zwracając 410 GONE.

| wersja | trasa |
|--|--|
|1|/Luis/V1.0/prog/Subscriptions|
|1|/ luis/v1.0/prog/subscriptions/{subscriptionKey}|

Azure [klucze subskrypcji](luis-how-to-azure-subscription.md) są generowane w portalu Azure. Przypisz klucz do aplikacji LUIS na **[publikowania](manage-keys.md)** strony. Nie trzeba znać rzeczywistej wartości klucza. LUIS używa Nazwa subskrypcji, aby przypisania. 

## <a name="new-versioning-route"></a>Nową trasę kontroli wersji
V2 model jest teraz zawarta w [wersji](luis-how-to-manage-versions.md). Nazwa wersji jest 10 znaków w trasie. Wersja domyślna to "0,1".

| wersja | trasa |
|--|--|
|1|/Luis/V1.0/**programu**/apps/ {appId} / jednostek|
|2|/Luis/**interfejsu api**/v2.0/apps/{appId}/**wersji**/ {versionId} / jednostek|

## <a name="metadata-renamed"></a>Metadane zmieniona
Kilka interfejsów API, które zwracają metadanych LUIS mają nowej nazwy.

| Nazwa trasy V1 | Nazwa trasy v2 |
|--|--|
|PersonalAssistantApps |Asystenci|
|applicationcultures|kultur|
|applicationdomains|domeny|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>"Sample" zmieniona na "Sugeruj"
LUIS sugeruje zniesławiających z istniejących [zniesławiających punktu końcowego](label-suggested-utterances.md) które może poprawić modelu. W poprzedniej wersji, to był o nazwie **próbki**. W nowej wersji, nazwa została zmieniona z próby **Sugeruj**. Ta metoda jest wywoływana **[Przejrzyj zniesławiających punktu końcowego](https://docs.microsoft.com/azure/cognitive-services/LUIS/label-suggested-utterances)** w LUIS witryny sieci Web.

| wersja | trasa |
|--|--|
|1|/Luis/V1.0/**programu**/entities/ /apps/ {appId} {entityId} /**próbki**|
|1|/Luis/V1.0/**programu**/intents/ /apps/ {appId} {intentId} /**próbki**|
|2|/Luis/**interfejsu api**/v2.0/apps/{appId}/**wersji**/ /entities/ {versionId} {entityId} /**Sugeruj**|
|2|/Luis/**interfejsu api**/v2.0/apps/{appId}/**wersji**/ /intents/ {versionId} {intentId} /**Sugeruj**|


## <a name="create-app-from-prebuilt-domains"></a>Tworzenie aplikacji z domen wbudowane
[Wbudowane domen](luis-how-to-use-prebuilt-domains.md) Podaj modelu domeny wstępnie zdefiniowane. Wbudowane domeny pozwala szybko utworzyć aplikację LUIS dla typowych domen. Ten interfejs API umożliwia tworzenie nowej aplikacji na podstawie wbudowane domeny. Odpowiedź jest nowy identyfikator aplikacji.

|trasy v2|Zlecenie|
|--|--|
|/Luis/API/v2.0/Apps/customprebuiltdomains  |GET, post|
|/ luis/api/v2.0/apps/customprebuiltdomains/{culture}  |Pobierz|

## <a name="importing-1x-app-into-2x"></a>Importowanie aplikacji 1.x 2.x
Wyeksportowane 1.x JSON aplikacji ma pewne kwestie, które należy zmienić przed zaimportowaniem do [LUIS] [ LUIS] 2.0. 

### <a name="prebuilt-entities"></a>Wbudowane jednostek 
[Wbudowane jednostek](Pre-builtEntities.md) zostały zmienione. Upewnij się, że używasz V2 wbudowane jednostek. Obejmuje to przy użyciu [datetimeV2](pre-builtentities.md?#use-a-prebuilt-datetimev2-entity), zamiast daty/godziny. 

### <a name="actions"></a>Akcje
Właściwości działania nie jest już prawidłowy. Powinna być pusta 

### <a name="labeled-utterances"></a>Zniesławiających etykietą
V1 dozwolone etykietą zniesławiających uwzględnić spacje na początku lub na końcu wyraz lub frazę. Usunięte spacje. 

## <a name="common-reasons-for-http-response-status-codes"></a>Najczęstsze przyczyny kody stanu odpowiedzi HTTP
Zobacz [kody odpowiedzi interfejsu API LUIS](luis-reference-response-codes.md).

## <a name="next-steps"></a>Kolejne kroki

Użyj wywołań w dokumentacji interfejsu API w wersji 2 do zaktualizowania istniejącej REST LIUS [punktu końcowego](https://aka.ms/luis-endpoint-apis) i [tworzenia](https://aka.ms/luis-authoring-apis) interfejsów API. 

[LUIS]: luis-reference-regions.md