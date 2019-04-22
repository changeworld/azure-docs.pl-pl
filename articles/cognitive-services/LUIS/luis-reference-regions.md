---
title: Publikowanie regionów i punktów końcowych
titleSuffix: Azure Cognitive Services
description: 3 regiony tworzenia pakietów administracyjnych i ich portale obsługi wielu regionów publikowania. Region, w którym możesz opublikować aplikację usługi LUIS odnosi się do regionu lub lokalizacji w witrynie Azure portal podczas tworzenia klucza punktu końcowego usługi LUIS platformy Azure. Po opublikowaniu aplikacji LUIS automatycznie generuje adres URL punktu końcowego dla regionu skojarzonego z kluczem.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 20ea2eb632a6d685351178691cc3d0f58a567902
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58891416"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Tworzenie i publikowanie regionów i skojarzonych kluczy

Trzy regiony tworzenia pakietów administracyjnych są obsługiwane przez odpowiednie portali usługi LUIS. Aby opublikować więcej niż jeden region z aplikacją usługi LUIS, potrzebujesz co najmniej jeden klucz dla regionu. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Tworzenie usługi LUIS regionów
Istnieją trzy usługi LUIS tworzenia portali oparte na region. Należy tworzyć i publikować w tym samym regionie. 

|LUIS|Tworzenie regionu|Nazwa regionu platformy Azure|
|--|--|--|
|[www.Luis.AI][www.luis.ai]|Federalna<br>nie Europa<br>nie Australia| `westus`|
|[AU.Luis.AI][au.luis.ai]|Australia| `australiaeast`|
|[EU.Luis.AI][eu.luis.ai]|Europa|`westeurope`|

Regiony tworzenia pakietów administracyjnych z [sparowane regiony pracy awaryjnej](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publikowanie regionów i zasobów platformy Azure
Aplikacja została opublikowana do wszystkich regionów skojarzonych z zasobami usługi LUIS dodane w portalu usługi LUIS. Na przykład aplikacja utworzona na [www.luis.ai][www.luis.ai], jeśli utworzysz zasób usługi LUIS lub usługi cognitive Services w **westus** i [dodać go do aplikacji jako zasób ](luis-how-to-azure-subscription.md), aplikacja zostanie opublikowana w danym regionie. 

## <a name="public-apps"></a>Publicznych aplikacji
Opublikowaniu publicznych aplikacji we wszystkich regionach, aby użytkownik z kluczem zależności od regionu zasobu usługi LUIS dostęp do aplikacji w niezależnie od regionu jest skojarzony z kluczem ich zasobów.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Publikowanie regiony są powiązane z tworzenia regionów

Tworzenia aplikacji w regionie mogą być publikowane tylko dla odpowiedni region publikowania. Jeśli Twoja aplikacja jest obecnie w niewłaściwym regionie tworzenia pakietów administracyjnych, należy wyeksportować aplikację i zaimportuj go do poprawny region tworzenia pakietów administracyjnych w Twoim regionie publikowania. 

Usługa LUIS aplikacje utworzone na https://www.luis.ai można opublikować wszystkie punkty końcowe, z wyjątkiem [Europejskiej](#publishing-to-europe) i [Australii](#publishing-to-australia) regionów. 

## <a name="publishing-to-europe"></a>Publikowanie w Europie

Aby opublikować w regionach w Europie, aplikacje można tworzyć usługi LUIS w https://eu.luis.ai tylko. Jeśli spróbujesz opublikować gdziekolwiek w przeciwnym razie użycie klucza w regionie Europa, LUIS wyświetla komunikat ostrzegawczy. Zamiast tego należy użyć https://eu.luis.ai. Aplikacje usługi LUIS utworzono [ https://eu.luis.ai ] [ eu.luis.ai] nie są migrowane automatycznie do innych regionów. Wyeksportować, a następnie zaimportować aplikacji usługi LUIS, aby można było przeprowadzić jej migrację.

## <a name="europe-publishing-regions"></a>Europa Zachodnia publikowania

 Globalne regionu | Tworzenie interfejsu API region i tworzenia witryny sieci Web| Publikowanie i wykonywania zapytań regionu<br>`API region name`   |  Format adresu URL punktu końcowego   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[EU.Luis.AI][eu.luis.ai]| Francja Środkowa<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe)| `westeurope`<br>[EU.Luis.AI][eu.luis.ai]| Europa Północna<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[EU.Luis.AI][eu.luis.ai]| Europa Zachodnia<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[EU.Luis.AI][eu.luis.ai]| Południowe Zjednoczone Królestwo<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Publikowanie w Australii

Aby opublikować australijskich regionach, aplikacje można tworzyć usługi LUIS w https://au.luis.ai tylko. Jeśli spróbujesz opublikować gdziekolwiek w przeciwnym razie użycie klucza w regionie Australii LUIS wyświetla komunikat ostrzegawczy. Zamiast tego należy użyć https://au.luis.ai. Aplikacje usługi LUIS utworzono [ https://au.luis.ai ] [ au.luis.ai] nie są migrowane automatycznie do innych regionów. Wyeksportować, a następnie zaimportować aplikacji usługi LUIS, aby można było przeprowadzić jej migrację.

## <a name="australia-publishing-regions"></a>Publikowanie regionów w Australii

 Globalne regionu | Tworzenie interfejsu API region i tworzenia witryny sieci Web| Publikowanie i wykonywania zapytań regionu<br>`API region name`   |  Format adresu URL punktu końcowego   |
|-----|------|------|------|
| [Australia](#publishing-to-australia) | `australiaeast`<br>[AU.Luis.AI][au.luis.ai]| Australia Wschodnia<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Publikowanie w innych regionach

Aby opublikować w innych regionach, aplikacje można tworzyć usługi LUIS w [ https://www.luis.ai ](https://www.luis.ai) tylko. 

## <a name="other-publishing-regions"></a>Inne regiony publikowania

 Globalne regionu | Tworzenie interfejsu API region i tworzenia witryny sieci Web| Publikowanie i wykonywania zapytań regionu<br>`API region name`   |  Format adresu URL punktu końcowego   |
|-----|------|------|------|
| Azja | `westus`<br>[www.Luis.AI][www.luis.ai]| Indie Środkowe<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Azja | `westus`<br>[www.Luis.AI][www.luis.ai]| Azja Wschodnia<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Azja | `westus`<br>[www.Luis.AI][www.luis.ai]| Japonia Wschodnia<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Azja | `westus`<br>[www.Luis.AI][www.luis.ai]| Japonia Zachodnia<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Azja | `westus`<br>[www.Luis.AI][www.luis.ai]| Korea Środkowa<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Azja | `westus`<br>[www.Luis.AI][www.luis.ai]| Azja Południowo-Wschodnia<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ameryka Północna |`westus`<br>[www.Luis.AI][www.luis.ai] | Kanada Środkowa<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ameryka Północna |`westus`<br>[www.Luis.AI][www.luis.ai] | Środkowe stany USA<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ameryka Północna |`westus`<br>[www.Luis.AI][www.luis.ai] | Wschodnie stany USA<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ameryka Północna | `westus`<br>[www.Luis.AI][www.luis.ai] | Wschodnie stany USA 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ameryka Północna | `westus`<br>[www.Luis.AI][www.luis.ai] | Środkowo-północne stany USA<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Ameryka Północna | `westus`<br>[www.Luis.AI][www.luis.ai] | Środkowo-południowe stany USA<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Ameryka Północna |`westus`<br>[www.Luis.AI][www.luis.ai] | Środkowo-zachodnie stany USA<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ameryka Północna | `westus`<br>[www.Luis.AI][www.luis.ai] | Zachodnie stany USA<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Ameryka Północna |`westus`<br>[www.Luis.AI][www.luis.ai] | Zachodnie stany USA 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Ameryka Południowa | `westus`<br>[www.Luis.AI][www.luis.ai] | Brazylia Południowa<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Punkty końcowe

Usługa LUIS obecnie ma 2 punkty końcowe: jeden do tworzenia i jeden dla zapytania prediction analizy.

|Przeznaczenie|Adres URL|
|--|--|
|Tworzenie|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Analiza tekstu (prognozowane zapytania)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

W poniższej tabeli opisano parametry, oznaczony za pomocą nawiasów klamrowych `{}`, w powyższej tabeli.

|Parametr|Przeznaczenie|
|--|--|
|region|Region platformy Azure — tworzenie i publikowanie mają różnych regionach|
|appID|Identyfikator aplikacji usługi LUIS używane w trasy adresu URL i znalezione na pulpit nawigacyjny aplikacji|
|q|tekstowe wypowiedź wysyłane z aplikacji klienckiej, takich jak czatbot|

## <a name="failover-regions"></a>Regiony trybu failover

W każdym regionie istnieje do trybu failover do regionu pomocniczego. Europa kończy się niepowodzeniem, tryb failover w Europie i Australii w trybie Failover w Australii.

Regiony tworzenia pakietów administracyjnych z [sparowane regiony pracy awaryjnej](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja ze wstępnie utworzonych jednostek](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
