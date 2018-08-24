---
title: Language Understanding (LUIS) regionów i punkty końcowe — platforma Azure — Cognitive Service LUIS | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Region, w którym możesz opublikować aplikację usługi LUIS odnosi się do regionu lub lokalizacji w witrynie Azure portal podczas tworzenia klucza punktu końcowego usługi LUIS platformy Azure. Po opublikowaniu aplikacji LUIS automatycznie generuje adres URL punktu końcowego dla regionu skojarzonego z kluczem. Aby opublikować więcej niż jeden region z aplikacją usługi LUIS, potrzebujesz co najmniej jeden klucz dla regionu.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 7812ce37cb22c8774c785f5f645b8fef90b02a3e
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42815507"
---
# <a name="regions-and-keys"></a>Regiony i klucze

Region, w którym możesz opublikować aplikację usługi LUIS odnosi się do regionu lub lokalizacji w witrynie Azure portal podczas tworzenia klucza punktu końcowego usługi LUIS platformy Azure. Gdy użytkownik [publikowanie aplikacji](./luis-how-to-publish-app.md), LUIS automatycznie generuje adres URL punktu końcowego dla regionu skojarzonego z kluczem. Aby opublikować więcej niż jeden region z aplikacją usługi LUIS, potrzebujesz co najmniej jeden klucz dla regionu. 

## <a name="luis-website"></a>Witryny sieci Web usługi LUIS
Istnieją trzy usługi LUIS witryn sieci Web, na podstawie regionu. Należy tworzyć i publikować w tym samym regionie. 

|LUIS|Region|
|--|--|
|[www.Luis.AI][www.luis.ai]|Federalna<br>nie Europa<br>nie Australia|
|[AU.Luis.AI][au.luis.ai]|Australia|
|[EU.Luis.AI][eu.luis.ai]|Europa|


## <a name="publishing-regions"></a>Publikowanie regionów

Usługa LUIS aplikacje utworzone na https://www.luis.ai można opublikować wszystkie punkty końcowe, z wyjątkiem [Europejskiej](#publishing-to-europe) i [Australii](#publishing-to-australia) regionów. 

Tworzenia aplikacji w regionie mogą być publikowane tylko dla odpowiedni region publikowania. Jeśli Twoja aplikacja jest obecnie w niewłaściwym regionie tworzenia pakietów administracyjnych, należy wyeksportować aplikację i zaimportuj go do poprawny region tworzenia pakietów administracyjnych w Twoim regionie publikowania. 

 Globalne regionu | Tworzenie regionu | Publikowanie i wykonywania zapytań regionu   |   Witryny sieci Web usługi LUIS | Format adresu URL punktu końcowego   |
|-----|------|------|------|------|
| Azja | Zachodnie stany USA| Azja Wschodnia     | [www.Luis.AI][www.luis.ai] |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Azja | Zachodnie stany USA| Azja Południowo-Wschodnia     | [www.Luis.AI][www.luis.ai] |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Australia](#publishing-to-australia) | Australia Wschodnia| Australia Wschodnia     |   [AU.Luis.AI][au.luis.ai] | https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Europa](#publishing-to-europe)| Europa Zachodnia| Europa Północna     | [EU.Luis.AI][eu.luis.ai]|  https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Europa](#publishing-to-europe) | Europa Zachodnia| Europa Zachodnia     | [EU.Luis.AI][eu.luis.ai]|  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Ameryka Północna | Zachodnie stany USA | Wschodnie stany USA      |[www.Luis.AI][www.luis.ai] |   https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ameryka Północna | Zachodnie stany USA | Wschodnie stany USA 2     | [www.Luis.AI][www.luis.ai] |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ameryka Północna | Zachodnie stany USA | Środkowo-południowe stany USA     | [www.Luis.AI][www.luis.ai] |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Ameryka Północna | Zachodnie stany USA | Środkowo-zachodnie stany USA     |[www.Luis.AI][www.luis.ai] |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ameryka Północna | Zachodnie stany USA | Zachodnie stany USA |  [www.Luis.AI][www.luis.ai] | https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Ameryka Północna | Zachodnie stany USA | Zachodnie stany USA 2    | [www.Luis.AI][www.luis.ai] |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Ameryka Południowa | Zachodnie stany USA | Brazylia Południowa     | [www.Luis.AI][www.luis.ai] |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-europe"></a>Publikowanie w Europie

Aby opublikować w regionach w Europie, aplikacje można tworzyć usługi LUIS w https://eu.luis.ai tylko. Jeśli spróbujesz opublikować gdziekolwiek w przeciwnym razie użycie klucza w regionie Europa, LUIS wyświetla komunikat ostrzegawczy. Zamiast tego należy użyć https://eu.luis.ai. Aplikacje usługi LUIS utworzono [ https://eu.luis.ai ] [ eu.luis.ai] nie są migrowane automatycznie do innych regionów. Wyeksportować, a następnie zaimportować aplikacji usługi LUIS, aby można było przeprowadzić jej migrację.

## <a name="publishing-to-australia"></a>Publikowanie w Australii

Aby opublikować australijskich regionach, aplikacje można tworzyć usługi LUIS w https://au.luis.ai tylko. Jeśli spróbujesz opublikować gdziekolwiek w przeciwnym razie użycie klucza w regionie Australii LUIS wyświetla komunikat ostrzegawczy. Zamiast tego należy użyć https://au.luis.ai. Aplikacje usługi LUIS utworzono [ https://au.luis.ai ] [ au.luis.ai] nie są migrowane automatycznie do innych regionów. Wyeksportować, a następnie zaimportować aplikacji usługi LUIS, aby można było przeprowadzić jej migrację.

## <a name="endpoints"></a>Punkty końcowe

Usługa LUIS obecnie ma 2 punkty końcowe: jeden do tworzenia i jeden dla analizy tekstu.

|Przeznaczenie|Adres URL|
|--|--|
|Tworzenie|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Analiza tekstu (prognozowane zapytania)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

W poniższej tabeli opisano parametry, oznaczony za pomocą nawiasów klamrowych `{}`, w powyższej tabeli.

|Parametr|Przeznaczenie|
|--|--|
|region|Region platformy Azure — tworzenie i publikowanie mają różnych regionach|
|appID|Identyfikator aplikacji usługi LUIS używane w trasy adresu URL i znalezione na pulpit nawigacyjny aplikacji|
|pytania i odpowiedzi|tekstowe wypowiedź wysyłane z aplikacji klienckiej, takich jak czatbot|


## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja ze wstępnie utworzonych jednostek](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai