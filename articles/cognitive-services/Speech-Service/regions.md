---
title: Regionów — usługi mowy
titlesuffix: Azure Cognitive Services
description: Odwołanie dla regionów usługi mowy.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 90fa56bb0e2044d24533c35a051d3f4b644e05b5
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58349641"
---
# <a name="speech-service-supported-regions"></a>Usługa rozpoznawania mowy obsługiwane regiony

Usługa mowy umożliwia aplikacji konwertowanie dźwięku na tekst, wykonać tłumaczenie mowy i konwertowania tekstu na mowę. Usługa jest dostępna w wielu regionach za pomocą punktów końcowych unikatowy zestaw SDK rozpoznawania mowy i interfejsów API REST.

Należy upewnić się, że punkt końcowy, który odpowiada regionie dla Twojej subskrypcji.

## <a name="speech-sdk"></a>Zestaw SDK rozpoznawania mowy

W [zestaw SDK rozpoznawania mowy](speech-sdk.md), regiony są określane jako ciąg (na przykład, jako parametr do `SpeechConfig.FromSubscription` mowy zestawu SDK dla języka C#).

### <a name="speech-recognition-and-translation"></a>Rozpoznawanie mowy i tłumaczenia

Zestaw SDK rozpoznawania mowy jest dostępna w regionach w celu **rozpoznawania mowy** i **tłumaczenia**:

  Region | Parametr zestaw SDK rozpoznawania mowy | Portal dostosowania mowy
 ------|-------|--------
 Zachodnie stany USA | `westus` | https://westus.cris.ai
 Zachodnie stany USA 2 | `westus2` | https://westus2.cris.ai
 Wschodnie stany USA | `eastus` | https://eastus.cris.ai
 Wschodnie stany USA 2 | `eastus2` | https://eastus2.cris.ai
 Środkowe stany USA | 'centralus' | https://centralus.cris.ai
 Środkowo-północne stany USA | 'northcentralus' | https://northcentralus.cris.ai
 Środkowo-południowe stany USA | 'southcentralus' | https://southcentralus.cris.ai
 Indie Środkowe | "centralindia" | https://centralindia.cris.ai
 Azja Wschodnia | `eastasia` | https://eastasia.cris.ai
 Azja Południowo-Wschodnia | `southeastasia` | https://southeastasia.cris.ai
 Japonia Wschodnia | `japaneast` | https://japaneast.cris.ai
 Korea Środkowa | `koreacentral` | https://koreacentral.cris.ai
 Australia Wschodnia | `australiaeast` | https://australiaeast.cris.ai
 Kanada Środkowa | `canadacentral` | https://canadacentral.cris.ai
 Europa Północna | `northeurope` | https://northeurope.cris.ai
 Europa Zachodnia | `westeurope` | https://westeurope.cris.ai
 Południowe Zjednoczone Królestwo | `uksouth` | https://uksouth.cris.ai
 Francja Środkowa | `francecentral` | https://francecentral.cris.ai

### <a name="intent-recognition"></a>Rozpoznawanie intencji

Dostępne regiony dla **rozpoznawanie intencji** za pośrednictwem zestawu SDK mowy są następujące:

 Globalne regionu | Region | Parametr zestaw SDK rozpoznawania mowy
 ------|-------|--------
 Azja | Azja Wschodnia | `eastasia`
 Azja | Azja Południowo-Wschodnia | `southeastasia`
 Australia | Australia Wschodnia | `australiaeast`
 Europa | Europa Północna | `northeurope`
 Europa | Europa Zachodnia | `westeurope`
 Ameryka Północna | Wschodnie stany USA | `eastus`
 Ameryka Północna | Wschodnie stany USA 2 | `eastus2`
 Ameryka Północna | Środkowo-południowe stany USA | `southcentralus`
 Ameryka Północna | Środkowo-zachodnie stany USA | `westcentralus`
 Ameryka Północna | Zachodnie stany USA | `westus`
 Ameryka Północna | Zachodnie stany USA 2 | `westus2`
 Ameryka Południowa | Brazylia Południowa | `brazilsouth`

To jest podzbiorem publikowania regiony obsługiwane przez [Language Understanding Intelligent service (LUIS)](/azure/cognitive-services/luis/luis-reference-regions).

## <a name="rest-apis"></a>Interfejsy API REST

Usługa rozpoznawania mowy udostępnia również punkty końcowe REST dla żądania zamiany mowy na tekst i zamiany tekstu na mowę.

### <a name="speech-to-text"></a>Zamiany mowy na tekst

Aby uzyskać dokumentację referencyjną mowy na tekst, zobacz [interfejsów API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Zamiana tekstu na mowę

Aby uzyskać dokumentację referencyjną zamiany tekstu na mowę, zobacz [interfejsów API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
