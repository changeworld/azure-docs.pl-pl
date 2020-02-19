---
title: Regiony — usługa mowy
titleSuffix: Azure Cognitive Services
description: Lista dostępnych regionów i punktów końcowych usługi mowy, w tym zamiany mowy na tekst, zamiany tekstu na mowę i Tłumaczenie mowy.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 560575ca7f51218e472abecb4319f4a3db69b1ff
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77431166"
---
# <a name="speech-service-supported-regions"></a>Obsługiwane regiony usługi Speech

Usługa mowy umożliwia aplikacji konwertowanie dźwięku na tekst, wykonać tłumaczenie mowy i konwertowania tekstu na mowę. Usługa jest dostępna w wielu regionach za pomocą punktów końcowych unikatowy zestaw SDK rozpoznawania mowy i interfejsów API REST.

Portal mowy do wykonywania niestandardowych konfiguracji w środowisku mowy dla wszystkich regionów jest dostępny tutaj: https://speech.microsoft.com

W przypadku wywołań usługi mowy upewnij się, że wywołanie jest zgodne z regionem subskrypcji.

## <a name="speech-sdk"></a>Zestaw SDK rozpoznawania mowy

W [zestawie mowy SDK](speech-sdk.md)regiony są określane jako ciąg (na przykład jako parametr do `SpeechConfig.FromSubscription` w zestawie mowy SDK dla C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Zamiana mowy na tekst, zamiana tekstu na mowę i tłumaczenie

Portal dostosowania mowy jest dostępny tutaj: https://speech.microsoft.com

Usługa Speech jest dostępna w tych regionach na potrzeby **rozpoznawania mowy**, **zamiany tekstu na mowę**i **tłumaczenia**:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Jeśli używasz [zestawu Speech SDK](speech-sdk.md), regiony są określane przez **Identyfikator regionu** (na przykład jako parametr do `SpeechConfig.FromSubscription`). Upewnij się, że region jest zgodny z regionem subskrypcji.

### <a name="intent-recognition"></a>Rozpoznawanie intencji

Dostępne regiony do **rozpoznawania intencji** za pośrednictwem zestawu Speech SDK są następujące:

| Globalne regionu | Region           | Identyfikator regionu |
| ------------- | ---------------- | -------------------- |
| Azja          | Azja Wschodnia        | `eastasia`           |
| Azja          | Azja Południowo-Wschodnia   | `southeastasia`      |
| Australia     | Australia Wschodnia   | `australiaeast`      |
| Europa        | Europa Północna     | `northeurope`        |
| Europa        | Europa Zachodnia      | `westeurope`         |
| Ameryka Północna | Wschodnie stany USA          | `eastus`             |
| Ameryka Północna | Wschodnie stany USA 2        | `eastus2`            |
| Ameryka Północna | Południowo-środkowe stany USA | `southcentralus`     |
| Ameryka Północna | Zachodnio-środkowe stany USA  | `westcentralus`      |
| Ameryka Północna | Zachodnie stany USA          | `westus`             |
| Ameryka Północna | Zachodnie stany USA 2        | `westus2`            |
| Ameryka Południowa | Brazylia Południowa     | `brazilsouth`        |

Jest to podzestaw regionów publikowania obsługiwanych przez [usługę Language Understanding (Luis)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-assistants"></a>Asystenci głosowi

[Zestaw Speech SDK](speech-sdk.md) obsługuje funkcje **asystenta głosowego** w następujących regionach:

| Region         | Identyfikator regionu |
| -------------- | -------------------- |
| Zachodnie stany USA        | `westus`             |
| Zachodnie stany USA 2      | `westus2`            |
| Wschodnie stany USA        | `eastus`             |
| Wschodnie stany USA 2      | `eastus2`            |
| Europa Zachodnia    | `westeurope`         |
| Europa Północna   | `northeurope`        |
| Azja Południowo-Wschodnia | `southeastasia`      |

## <a name="rest-apis"></a>Interfejsy API REST

Usługa rozpoznawania mowy udostępnia również punkty końcowe REST dla żądania zamiany mowy na tekst i zamiany tekstu na mowę.

### <a name="speech-to-text"></a>Zamiana mowy na tekst

Aby zapoznać się z dokumentacją dotyczącą zamiany mowy na tekst, zobacz [interfejs API REST zamiany mowy na tekst](rest-speech-to-text.md).

Punkt końcowy interfejsu API REST ma następujący format:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Zastąp `<REGION_IDENTIFIER>` identyfikatorem odpowiadającym regionowi subskrypcji z tej tabeli:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Parametr Language musi być dołączony do adresu URL, aby uniknąć otrzymania błędu HTTP 4xx. Na przykład język ustawiony na angielski (Zachodnie stany USA) to: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

### <a name="text-to-speech"></a>Zamiana tekstu na mowę

Aby zapoznać się z dokumentacją dotyczącą zamiany tekstu na mowę, zobacz [interfejs API REST zamiany tekstu na mowę](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
