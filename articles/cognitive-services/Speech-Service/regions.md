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
ms.openlocfilehash: 409ce8b904997f2ab75f70b2138ec5b1e70a0e69
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816653"
---
# <a name="speech-service-supported-regions"></a>Obsługiwane regiony usługi Speech

Usługa mowy umożliwia aplikacji konwertowanie dźwięku na tekst, wykonać tłumaczenie mowy i konwertowania tekstu na mowę. Usługa jest dostępna w wielu regionach za pomocą punktów końcowych unikatowy zestaw SDK rozpoznawania mowy i interfejsów API REST.

Należy upewnić się, że punkt końcowy, który odpowiada regionie dla Twojej subskrypcji.

## <a name="speech-sdk"></a>Zestaw SDK usługi Mowa

W [zestawie mowy SDK](speech-sdk.md)regiony są określane jako ciąg (na przykład jako parametr do `SpeechConfig.FromSubscription` w zestawie mowy SDK dla C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Zamiana mowy na tekst, zamiana tekstu na mowę i tłumaczenie

Zestaw Speech SDK jest dostępny w tych regionach na potrzeby **rozpoznawania mowy**, zamiany **tekstu na mowę**i **tłumaczenia**:

| Region           | Parametr zestaw SDK rozpoznawania mowy | Portal dostosowania mowy    |
| ---------------- | -------------------- | ------------------------------ |
| Zachodnie stany USA          | `westus`             | https://westus.cris.ai         |
| Zachodnie stany USA 2        | `westus2`            | https://westus2.cris.ai        |
| Wschodnie stany USA          | `eastus`             | https://eastus.cris.ai         |
| Wschodnie stany USA 2        | `eastus2`            | https://eastus2.cris.ai        |
| Środkowe stany USA       | `centralus`          | https://centralus.cris.ai      |
| Północno-środkowe stany USA | `northcentralus`     | https://northcentralus.cris.ai |
| Południowo-środkowe stany USA | `southcentralus`     | https://southcentralus.cris.ai |
| Indie Środkowe    | `centralindia`       | https://centralindia.cris.ai   |
| Azja Wschodnia        | `eastasia`           | https://eastasia.cris.ai       |
| Azja Południowo-Wschodnia   | `southeastasia`      | https://southeastasia.cris.ai  |
| Japonia Wschodnia       | `japaneast`          | https://japaneast.cris.ai      |
| Korea Środkowa    | `koreacentral`       | https://koreacentral.cris.ai   |
| Australia Wschodnia   | `australiaeast`      | https://australiaeast.cris.ai  |
| Kanada Środkowa   | `canadacentral`      | https://canadacentral.cris.ai  |
| Europa Północna     | `northeurope`        | https://northeurope.cris.ai    |
| Europa Zachodnia      | `westeurope`         | https://westeurope.cris.ai     |
| Południowe Zjednoczone Królestwo         | `uksouth`            | https://uksouth.cris.ai        |
| Francja Środkowa   | `francecentral`      | https://francecentral.cris.ai  |

### <a name="intent-recognition"></a>Rozpoznawanie intencji

Dostępne regiony do **rozpoznawania intencji** za pośrednictwem zestawu Speech SDK są następujące:

| Globalne regionu | Region           | Parametr zestaw SDK rozpoznawania mowy |
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

| Region         | Parametr zestaw SDK rozpoznawania mowy |
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

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Zamiana tekstu na mowę

Aby zapoznać się z dokumentacją dotyczącą zamiany tekstu na mowę, zobacz [interfejs API REST zamiany tekstu na mowę](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
