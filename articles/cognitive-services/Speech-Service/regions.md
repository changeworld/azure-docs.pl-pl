---
title: Regiony usługi mowy
description: Odwołanie dla regionów usługi mowy.
services: cognitive-services
author: mahilleb-msft
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 1cb00035dc8f1cdeabd1beb22ca69f47bf4bd89e
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379574"
---
# <a name="regions-of-the-speech-service"></a>Regiony usługi mowy

Usługa rozpoznawania mowy jest dostępna w różnych regionach.
Po utworzeniu subskrypcji można wybrać dostępny region, w zależności od potrzeb.

Korzystając z subskrypcji musisz uwzględnić region wybrany przez Ciebie.

## <a name="rest-api"></a>Interfejs API REST

Za pomocą interfejsu API REST, wybierz punkty końcowe bezpośrednio specyficzne dla regionu.
Zobacz [interfejsów API REST](rest-apis.md) Aby uzyskać szczegółowe informacje.

## <a name="speech-sdk"></a>Zestaw SDK rozpoznawania mowy

W [zestaw SDK rozpoznawania mowy](speech-sdk.md), regiony są określane jako ciąg (na przykład, jako parametr do [SpeechFactory.FromSubscription](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechfactory.fromsubscription) mowy zestawu SDK dla języka C#).

### <a name="regions-for-speech-recognition-and-translation"></a>Regiony usługi rozpoznawania mowy i tłumaczenia

W poniższej tabeli wymieniono dostępne regiony dla **rozpoznawania mowy** i **tłumaczenia**:

Region| Wartość parametru regionu w zestawie SDK rozpoznawania mowy| Portal
-|-
Zachodnie stany USA| `westus`| https://westus.cris.ai
Zachodnie stany USA 2| `westus2`| https://westus2.cris.ai
Wschodnie stany USA| `eastus`| https://eastus.cris.ai
Wschodnie stany USA 2| `eastus2`| https://eastus2.cris.ai
Azja Wschodnia| `eastasia`| https://eastasia.cris.ai
Azja Południowo-Wschodnia| `southeastasia`| https://southeastasia.cris.ai
Europa Północna| `northeurope`| https://northeurope.cris.ai
Europa Zachodnia|  `westeurope`| https://westeurope.cris.ai

### <a name="regions-for-intent-recognition"></a>Regiony dla rozpoznawanie intencji

Dostępne regiony dla **rozpoznawanie intencji** za pośrednictwem mowy usługi SDK są wymienione w [strony region usługi Language Understanding](/azure/cognitive-services/luis/luis-reference-regions).
Dla każdego regionu publikowania na liście z odpowiadającym mu parametrem zestaw SDK rozpoznawania mowy w regionie jest określana jako pierwszą część nazwy domeny, punktu końcowego.
Na przykład użyć `westus` do określenia publikowania regionu zachodnie stany USA.
