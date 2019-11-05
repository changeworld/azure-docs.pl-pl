---
title: Regiony — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dokumentacja dotycząca regionów usługi mowy.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 469dab093ed7a62171d232695af3258cc874b5f3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481316"
---
# <a name="speech-service-supported-regions"></a>Obsługiwane regiony usługi Speech

Usługa mowy umożliwia aplikacji Konwertowanie dźwięku na tekst, wykonywanie tłumaczeń mowy i przekonwertować tekstu na mowę. Usługa jest dostępna w wielu regionach z unikatowymi punktami końcowymi zestawu Speech SDK i interfejsów API REST.

Upewnij się, że używasz punktu końcowego, który jest zgodny z regionem subskrypcji.

## <a name="speech-sdk"></a>Zestaw SDK usługi Mowa

W [zestawie mowy SDK](speech-sdk.md)regiony są określane jako ciąg (na przykład jako parametr do `SpeechConfig.FromSubscription` w zestawie mowy SDK dla C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Zamiana mowy na tekst, zamiana tekstu na mowę i tłumaczenie

Zestaw Speech SDK jest dostępny w tych regionach na potrzeby **rozpoznawania mowy**, zamiany **tekstu na mowę**i **tłumaczenia**:

  Region | Parametr zestawu Speech SDK | Portal dostosowania mowy
 ------|-------|--------
 Zachodnie stany USA | `westus` | https://westus.cris.ai
 Zachodnie stany USA 2 | `westus2` | https://westus2.cris.ai
 Wschodnie stany USA | `eastus` | https://eastus.cris.ai
 Wschodnie stany USA 2 | `eastus2` | https://eastus2.cris.ai
 Środkowe stany USA | `centralus` | https://centralus.cris.ai
 Północno-środkowe stany USA | `northcentralus` | https://northcentralus.cris.ai
 Środkowo-południowe stany USA | `southcentralus` | https://southcentralus.cris.ai
 Indie Środkowe | `centralindia` | https://centralindia.cris.ai
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

Dostępne regiony do **rozpoznawania intencji** za pośrednictwem zestawu Speech SDK są następujące:

 Region globalny | Region | Parametr zestawu Speech SDK
 ------|-------|--------
 Azja | Azja Wschodnia | `eastasia`
 Azja | Azja Południowo-Wschodnia | `southeastasia`
 Australia | Australia Wschodnia | `australiaeast`
 Europa | Europa Północna | `northeurope`
 Europa | Europa Zachodnia | `westeurope`
 Ameryka Północna | Wschodnie stany USA | `eastus`
 Ameryka Północna | Wschodnie stany USA 2 | `eastus2`
 Ameryka Północna | Środkowo-południowe stany USA | `southcentralus`
 Ameryka Północna | Zachodnio-środkowe stany USA | `westcentralus`
 Ameryka Północna | Zachodnie stany USA | `westus`
 Ameryka Północna | Zachodnie stany USA 2 | `westus2`
 Ameryka Południowa | Brazylia Południowa | `brazilsouth`

Jest to podzestaw regionów publikowania obsługiwanych przez [usługę Language Understanding (Luis)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-assistants"></a>Asystenci głosu

[Zestaw Speech SDK](speech-sdk.md) obsługuje funkcje **asystenta głosowego** w następujących regionach:

Region | Parametr zestawu Speech SDK
-------|---------------------
Zachodnie stany USA | `westus`
Zachodnie stany USA 2 | `westus2`
Wschodnie stany USA | `eastus`
Wschodnie stany USA 2 | `eastus2`
Europa Zachodnia | `westeurope`
Europa Północna | `northeurope`
Azja Południowo-Wschodnia | `southeastasia`

## <a name="rest-apis"></a>Interfejsy API REST

Usługa mowy udostępnia również punkty końcowe REST dla żądań zamiany mowy na tekst i zamiany tekstu na mowę.

### <a name="speech-to-text"></a>Zamiana mowy na tekst

Aby zapoznać się z dokumentacją dotyczącą zamiany mowy na tekst, zobacz [interfejs API REST zamiany mowy na tekst](rest-speech-to-text.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Zamiana tekstu na mowę

Aby zapoznać się z dokumentacją dotyczącą zamiany tekstu na mowę, zobacz [interfejs API REST zamiany tekstu na mowę](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]