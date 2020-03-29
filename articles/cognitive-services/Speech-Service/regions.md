---
title: Regiony — usługa mowy
titleSuffix: Azure Cognitive Services
description: Lista dostępnych regionów i punktów końcowych dla usługi Mowa, w tym zamiany mowy na tekst, zamiany tekstu na mowę i tłumaczenia mowy.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220469"
---
# <a name="speech-service-supported-regions"></a>Obsługiwane regiony usługi mowy

Usługa Mowy umożliwia aplikacji konwertowanie dźwięku na tekst, wykonywanie tłumaczenia mowy i ukrytego tekstu na mowę. Usługa jest dostępna w wielu regionach z unikatowymi punktami końcowymi dla zestawów API speech i REST.

Portal mowy do wykonywania niestandardowych konfiguracji do środowiska mowy dla wszystkich regionów jest dostępny tutaj:https://speech.microsoft.com

W przypadku wywołań usługi mowy upewnij się, że wywołanie jest zgodne z regionem subskrypcji.

## <a name="speech-sdk"></a>Zestaw SDK rozpoznawania mowy

W [SDK mowy](speech-sdk.md)regiony są określone jako ciąg (na `SpeechConfig.FromSubscription` przykład jako parametr w SDK mowy dla języka C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Zamiana mowy na tekst, zamiana tekstu na mowę i tłumaczenie

Portal dostosowywania mowy jest dostępny tutaj:https://speech.microsoft.com

Usługa Mowy jest dostępna w tych regionach do **rozpoznawania mowy,** **zamiany tekstu na mowę**i **tłumaczenia:**

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

W przypadku użycia [SDK mowy](speech-sdk.md)regiony są określane przez **identyfikator regionu** `SpeechConfig.FromSubscription`(na przykład jako parametr do ). Upewnij się, że region jest zgodny z regionem subskrypcji.

### <a name="intent-recognition"></a>Rozpoznawanie intencji

Dostępne regiony **do rozpoznawania intencji** za pośrednictwem sdk mowy są następujące:

| Region globalny | Region           | Identyfikator regionu |
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

Jest to podzbiór regionów publikowania obsługiwanych przez [usługę rozumienie języka (LUIS).](/azure/cognitive-services/luis/luis-reference-regions)

### <a name="voice-assistants"></a>Asystenci głosowi

Zestaw [SDK mowy](speech-sdk.md) obsługuje funkcje **asystenta głosowego** w następujących regionach:

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

Usługa mowy udostępnia również punkty końcowe REST dla żądań zamiany mowy na tekst i zamiany tekstu na mowę.

### <a name="speech-to-text"></a>Zamiana mowy na tekst

Aby zapoznać się z dokumentacją referencyjną mowy na tekst, zobacz [Interfejs API REST speech-to-text](rest-speech-to-text.md).

Punkt końcowy interfejsu API REST ma ten format:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Zamień `<REGION_IDENTIFIER>` identyfikator odpowiadający regionowi subskrypcji z tej tabeli:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Parametr języka musi być dołączona do adresu URL, aby uniknąć otrzymania błędu HTTP 4xx. Na przykład język ustawiony na angielski amerykański przy użyciu `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`punktu końcowego zachodniego stanów USA to: .

### <a name="text-to-speech"></a>Zamiana tekstu na mowę

Aby zapoznać się z dokumentacją referencyjną zamiany tekstu na mowę, zobacz [Interfejs API REST od zamiany tekstu na mowę](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
