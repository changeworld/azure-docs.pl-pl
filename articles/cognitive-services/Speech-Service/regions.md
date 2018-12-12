---
title: Regionów — usługi mowy
titlesuffix: Azure Cognitive Services
description: Odwołanie dla regionów usługi mowy.
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mahilleb
ms.custom: seodec18
ms.openlocfilehash: d41213d72d40555d8dc5aeab76040fc556dae774
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091649"
---
# <a name="speech-service-supported-regions"></a>Usługa rozpoznawania mowy obsługiwane regiony

Usługa mowy umożliwia aplikacji konwertowanie dźwięku na tekst, wykonać tłumaczenie mowy i konwertowania tekstu na mowę. Usługa jest dostępna w wielu regionach za pomocą punktów końcowych unikatowy zestaw SDK rozpoznawania mowy i interfejsów API REST.

Należy upewnić się, że punkt końcowy, który odpowiada regionie dla Twojej subskrypcji.

## <a name="speech-sdk"></a>Zestaw SDK rozpoznawania mowy

W [zestawu SDK usługi mowy](speech-sdk.md), regiony są określane jako ciąg (na przykład, jako parametr do `SpeechConfig.FromSubscription` mowy zestawu SDK dla języka C#).

### <a name="speech-recognition-and-translation"></a>Rozpoznawanie mowy i tłumaczenia

Zestaw SDK rozpoznawania mowy jest dostępna w regionach w celu **rozpoznawania mowy** i **tłumaczenia**:

  Region | Parametr zestaw SDK rozpoznawania mowy | Portal dostosowania mowy
 ------|-------|--------
 Zachodnie stany USA | `westus` | https://westus.cris.ai
 Zachodnie stany USA 2 | `westus2` | https://westus2.cris.ai
 Wschodnie stany USA | `eastus` | https://eastus.cris.ai
 Wschodnie stany USA 2 | `eastus2` | https://eastus2.cris.ai
 Azja Wschodnia | `eastasia` | https://eastasia.cris.ai
 Azja Południowo-Wschodnia | `southeastasia` | https://southeastasia.cris.ai
 Europa Północna | `northeurope` | https://northeurope.cris.ai
 Europa Zachodnia | `westeurope` | https://westeurope.cris.ai


### <a name="intent-recognition"></a>Rozpoznawanie intencji

**Rozpoznawanie intencji** udziałów zestaw SDK rozpoznawania mowy regiony obsługują z użyciem usługi LUIS. Aby uzyskać pełną listę dostępnych regionów, zobacz [publikowania regionów i punkty końcowe — usługi LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions)

Dostępne regiony dla **rozpoznawanie intencji** za pośrednictwem mowy usługi SDK są wymienione na [strony region usługi Language Understanding](/azure/cognitive-services/luis/luis-reference-regions).

Dla każdego regionu publikowania na liście, użyj podanych **nazwa regionu interfejsu API**. Na przykład użyć `westus` regionu zachodnie stany USA.

## <a name="rest-apis"></a>Interfejsy API REST

Usługa rozpoznawania mowy udostępnia również punkty końcowe REST dla żądania zamiany mowy na tekst i zamiany tekstu na mowę.

### <a name="speech-to-text"></a>Zamiany mowy na tekst

Aby uzyskać dokumentację referencyjną mowy na tekst, zobacz [interfejsów API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Zamiana tekstu na mowę

Aby uzyskać dokumentację referencyjną zamiany tekstu na mowę, zobacz [interfejsów API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
