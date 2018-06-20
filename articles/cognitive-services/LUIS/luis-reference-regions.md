---
title: Regiony opis (LUIS) języka | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera listę regionów LUIS dla witryny sieci Web, subskrypcji platformy Azure i regiony world LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 86a20770178707f72cf2991ca08b6b98eaeaf0cf
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36237114"
---
# <a name="regions-and-keys"></a>Regiony i kluczy

Region, w którym jest publikowanie aplikacji LUIS odnosi się do regionu lub lokalizacji w portalu Azure po klucz punktu końcowego Azure LUIS zostanie utworzony. Gdy użytkownik [publikowanie aplikacji](./PublishApp.md), LUIS automatycznie generuje adres URL punktu końcowego dla regionu skojarzonego z kluczem. Aby opublikować aplikację LUIS więcej niż jeden region, potrzebujesz co najmniej jeden klucz dla regionu. 

## <a name="luis-website"></a>LUIS witryny sieci Web
Istnieją trzy LUIS witryn sieci Web, na podstawie regionu. Należy tworzyć i publikować w tym samym regionie. 

|LUIS|Region|
|--|--|
|[www.Luis.AI][www.luis.ai]|Federalna<br>nie Europy<br>nie Australia|
|[AU.Luis.AI][au.luis.ai]|Australia|
|[EU.Luis.AI][eu.luis.ai]|Europa|


## <a name="publishing-regions"></a>Regiony publikowania

Aplikacje LUIS utworzony na https://www.luis.ai mogą być publikowane do wszystkich punktów końcowych z wyjątkiem [Europejskiej](#publishing-to-europe) i [Australian](#publishing-to-australia) regionów. 

Tworzenia aplikacji regionu mogą być publikowane tylko do odpowiedniej publikowania regionu. Jeśli aplikacja jest obecnie nieprawidłowy region tworzenia pakietów administracyjnych, eksportowanie aplikacji i zaimportuj go do tworzenia poprawny region w Twoim regionie publikowania. 

 Region globalne | Tworzenie regionu | Publikowanie i badania regionu   |   LUIS witryny sieci Web | Format adresu URL punktu końcowego   |
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

Aby opublikować europejskich regionach, Utwórz LUIS aplikacje https://eu.luis.ai tylko. Jeśli spróbujesz opublikować gdziekolwiek else przy użyciu klucza w regionie Europy, LUIS wyświetla komunikat ostrzegawczy. Zamiast tego należy użyć https://eu.luis.ai. Aplikacje LUIS utworzonego w dniu [ https://eu.luis.ai ] [ eu.luis.ai] nie automatycznie zostanie przeprowadzona migracja do innych regionów. Wyeksportować, a następnie zaimportować LUIS aplikacji, aby przeprowadzić migrację.

## <a name="publishing-to-australia"></a>Publikowanie w Australii

Aby opublikować Australian regionach, Utwórz LUIS aplikacji na https://au.luis.ai tylko. Jeśli spróbujesz opublikować gdziekolwiek else przy użyciu klucza w regionie Australian LUIS wyświetla komunikat ostrzegawczy. Zamiast tego należy użyć https://au.luis.ai. Aplikacje LUIS utworzonego w dniu [ https://au.luis.ai ] [ au.luis.ai] nie automatycznie zostanie przeprowadzona migracja do innych regionów. Wyeksportować, a następnie zaimportować LUIS aplikacji, aby przeprowadzić migrację.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Odwołanie do jednostki wbudowane](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai