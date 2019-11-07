---
title: Publikowanie regionów & punkty końcowe — LUIS
titleSuffix: Azure Cognitive Services
description: Region określony w Azure Portal jest taki sam, gdzie zostanie opublikowana aplikacja LUIS, a dla tego samego regionu zostanie wygenerowany adres URL punktu końcowego.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: diberry
ms.openlocfilehash: fe4436afe56e631ae308b70ae35b1fb4a7c32e11
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669189"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Tworzenie i publikowanie regionów oraz skojarzonych kluczy

Trzy regiony tworzenia są obsługiwane przez odpowiednie portale LUIS. Aby opublikować aplikację LUIS w więcej niż jednym regionie, musisz mieć co najmniej jeden klucz na region. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUISe regiony tworzenia
Istnieją trzy portale tworzenia LUIS na podstawie regionu. Musisz tworzyć i publikować w tym samym regionie. 

|LUIS|Region tworzenia|Nazwa regionu platformy Azure|
|--|--|--|
|[www.luis.ai][www.luis.ai]|Federalna<br>nie Europa<br>nie Australia| `westus`|
|[au.luis.ai][au.luis.ai]|Australia| `australiaeast`|
|[eu.luis.ai][eu.luis.ai]|Europa|`westeurope`|

Regiony tworzenia mają [sparowane regiony trybu failover](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publikowanie regionów i zasobów platformy Azure
Aplikacja zostanie opublikowana we wszystkich regionach skojarzonych z zasobami LUIS dodanymi w portalu LUIS. Na przykład w przypadku aplikacji utworzonej w witrynie [www.Luis.AI][www.luis.ai], jeśli utworzysz zasób usługi Luis lub poznawczej w formie **zachodniej** i [dodasz go do aplikacji jako zasób](luis-how-to-azure-subscription.md), aplikacja zostanie opublikowana w tym regionie. 

## <a name="public-apps"></a>Aplikacje publiczne
Publiczna aplikacja jest publikowana we wszystkich regionach, dzięki czemu użytkownik z kluczem zasobów LUIS opartym na regionie może uzyskać dostęp do aplikacji w dowolnym regionie, który jest skojarzony z ich kluczem zasobów.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Regiony publikowania są powiązane z regionami tworzenia

Aplikację region tworzenia można opublikować tylko w odpowiednim regionie publikacji. Jeśli Twoja aplikacja znajduje się w niewłaściwym regionie tworzenia, wyeksportuj aplikację i zaimportuj ją do poprawnego regionu tworzenia w regionie publikacji. 

Aplikacje LUIS utworzone w https://www.luis.ai mogą być publikowane we wszystkich punktach końcowych z wyjątkiem regionów [Europy](#publishing-to-europe) i [Australii](#publishing-to-australia) . 

## <a name="publishing-to-europe"></a>Publikowanie w Europie

Aby opublikować w regionach europejskich, możesz tworzyć aplikacje LUIS tylko w https://eu.luis.ai. Jeśli spróbujesz opublikować gdziekolwiek w innym miejscu przy użyciu klucza w regionie Europa, LUIS wyświetla komunikat ostrzegawczy. Zamiast tego należy użyć https://eu.luis.ai. Aplikacje LUIS utworzone w [https://eu.luis.ai][eu.luis.ai] nie są automatycznie migrowane do innych regionów. Aby przeprowadzić migrację, wyeksportuj i zaimportuj aplikację LUIS.

## <a name="europe-publishing-regions"></a>Regiony publikowania w Europie

 Region globalny | Tworzenie regionu interfejsu API & tworzenia witryny sieci Web| Publikowanie & regionie przeszukiwania<br>`API region name`   |  Format adresu URL punktu końcowego   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Francja Środkowa<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa Północna<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa Zachodnia<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Południowe Zjednoczone Królestwo<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Publikowanie w Australii

Aby publikować w regionach australijskich, możesz tworzyć aplikacje LUIS tylko w https://au.luis.ai. Jeśli spróbujesz opublikować gdziekolwiek w innym miejscu przy użyciu klucza w regionie australijski, LUIS wyświetla komunikat ostrzegawczy. Zamiast tego należy użyć https://au.luis.ai. Aplikacje LUIS utworzone w [https://au.luis.ai][au.luis.ai] nie są automatycznie migrowane do innych regionów. Aby przeprowadzić migrację, wyeksportuj i zaimportuj aplikację LUIS.

## <a name="australia-publishing-regions"></a>Regiony publikacji Australii

 Region globalny | Tworzenie regionu interfejsu API & tworzenia witryny sieci Web| Publikowanie & regionie przeszukiwania<br>`API region name`   |  Format adresu URL punktu końcowego   |
|-----|------|------|------|
| [Australia](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Australia Wschodnia<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Publikowanie w innych regionach

Aby publikować w innych regionach, możesz tworzyć aplikacje LUIS tylko w [https://www.luis.ai](https://www.luis.ai) . 

## <a name="other-publishing-regions"></a>Inne regiony publikowania

 Region globalny | Tworzenie regionu interfejsu API & tworzenia witryny sieci Web| Publikowanie & regionie przeszukiwania<br>`API region name`   |  Format adresu URL punktu końcowego   |
|-----|------|------|------|
| Afryka | `westus`<br>[www.luis.ai][www.luis.ai]| Północna Republika Południowej Afryki<br>`southafricanorth` |  https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Indie Środkowe<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Azja Wschodnia<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Japonia Wschodnia<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Japonia Zachodnia<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Korea Środkowa<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Azja Południowo-Wschodnia<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Kanada Środkowa<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Środkowe stany USA<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Wschodnie stany USA<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ameryka Północna | `westus`<br>[www.luis.ai][www.luis.ai] | Wschodnie stany USA 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ameryka Północna | `westus`<br>[www.luis.ai][www.luis.ai] | Środkowo-północne stany USA<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Ameryka Północna | `westus`<br>[www.luis.ai][www.luis.ai] | Środkowo-południowe stany USA<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Środkowo-zachodnie stany USA<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ameryka Północna | `westus`<br>[www.luis.ai][www.luis.ai] | Zachodnie stany USA<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Zachodnie stany USA 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Ameryka Południowa | `westus`<br>[www.luis.ai][www.luis.ai] | Brazylia Południowa<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Punkty końcowe

LUIS ma obecnie 2 punkty końcowe: jeden dla tworzenia i jeden na potrzeby analizy prognozowania zapytań.

|Przeznaczenie|Adres URL|
|--|--|
|Tworzenie|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Analiza tekstu (prognoza zapytania)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

W poniższej tabeli objaśniono parametry oznaczone nawiasami klamrowymi `{}`w poprzedniej tabeli.

|Parametr|Przeznaczenie|
|--|--|
|region|Tworzenie i publikowanie w regionie platformy Azure mają różne regiony|
|appID|Identyfikator aplikacji LUIS używany w marszrucie adresu URL i znaleziony na pulpicie nawigacyjnym aplikacji|
|pytania|wypowiedź tekst wysłany z aplikacji klienckiej, na przykład Chat bot|

## <a name="failover-regions"></a>Regiony trybu failover

Każdy region ma region pomocniczy do przełączenia w tryb failover. Europa działa w trybie failover w Europie i Australia przechodzi w tryb failover w Australii.

Regiony tworzenia mają [sparowane regiony trybu failover](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Odwołania do wstępnie utworzonych jednostek](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
