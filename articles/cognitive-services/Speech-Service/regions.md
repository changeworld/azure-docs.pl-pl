---
title: Regiony — usługa mowy
titleSuffix: Azure Cognitive Services
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
ms.openlocfilehash: c0414277b4851891911908ba4f42e92abedc86e4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553192"
---
# <a name="speech-service-supported-regions"></a>Usługa rozpoznawania mowy obsługiwane regiony

Usługa mowy umożliwia aplikacji konwertowanie dźwięku na tekst, wykonać tłumaczenie mowy i konwertowania tekstu na mowę. Usługa jest dostępna w wielu regionach za pomocą punktów końcowych unikatowy zestaw SDK rozpoznawania mowy i interfejsów API REST.

Należy upewnić się, że punkt końcowy, który odpowiada regionie dla Twojej subskrypcji.

## <a name="speech-sdk"></a>Zestaw SDK rozpoznawania mowy

W [zestawie mowy SDK](speech-sdk.md)regiony są określone jako ciąg (na przykład jako parametr `SpeechConfig.FromSubscription` w zestawie mowy SDK dla C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Zamiana mowy na tekst, zamiana tekstu na mowę i tłumaczenie

Zestaw Speech SDK jest dostępny w tych regionach na potrzeby **rozpoznawania mowy**, zamiany **tekstu na mowę**i **tłumaczenia**:

  Region | Parametr zestaw SDK rozpoznawania mowy | Portal dostosowania mowy
 ------|-------|--------
 Zachodnie stany USA | `westus` | https://westus.cris.ai
 Zachodnie stany USA 2 | `westus2` | https://westus2.cris.ai
 East US | `eastus` | https://eastus.cris.ai
 Wschodnie stany USA 2 | `eastus2` | https://eastus2.cris.ai
 Środkowe stany USA | `centralus` | https://centralus.cris.ai
 Środkowo-północne stany USA | `northcentralus` | https://northcentralus.cris.ai
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

 Globalne regionu | Region | Parametr zestaw SDK rozpoznawania mowy
 ------|-------|--------
 Azja | Azja Wschodnia | `eastasia`
 Azja | Azja Południowo-Wschodnia | `southeastasia`
 Australia | Australia Wschodnia | `australiaeast`
 Europa | Europa Północna | `northeurope`
 Europa | Europa Zachodnia | `westeurope`
 Ameryka Północna | East US | `eastus`
 Ameryka Północna | Wschodnie stany USA 2 | `eastus2`
 Ameryka Północna | Środkowo-południowe stany USA | `southcentralus`
 Ameryka Północna | Środkowo-zachodnie stany USA | `westcentralus`
 Ameryka Północna | Zachodnie stany USA | `westus`
 Ameryka Północna | Zachodnie stany USA 2 | `westus2`
 Ameryka Południowa | Brazylia Południowa | `brazilsouth`

Jest to podzestaw regionów publikowania obsługiwanych przez [usługę Language Understanding (Luis)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-first-virtual-assistants"></a>Wirtualne Asystenci z dźwiękiem

[Zestaw Speech SDK](speech-sdk.md) obsługuje funkcje **głosowego asystenta wirtualnego** w następujących regionach:

Region | Parametr zestaw SDK rozpoznawania mowy
-------|---------------------
Zachodnie stany USA | `westus`
Zachodnie stany USA 2 | `westus2`
East US | `eastus`
Wschodnie stany USA 2 | `eastus2`
Europa Zachodnia | `westeurope`
Europa Północna | `northeurope`
Azja Południowo-Wschodnia | `southeastasia`

## <a name="rest-apis"></a>Interfejsy API REST

Usługa rozpoznawania mowy udostępnia również punkty końcowe REST dla żądania zamiany mowy na tekst i zamiany tekstu na mowę.

### <a name="speech-to-text"></a>Zamiany mowy na tekst

Aby zapoznać się z dokumentacją dotyczącą zamiany mowy na tekst, zobacz [interfejs API REST zamiany mowy na tekst](rest-speech-to-text.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Zamiana tekstu na mowę

Aby zapoznać się z dokumentacją dotyczącą zamiany tekstu na mowę, zobacz [interfejs API REST zamiany tekstu na mowę](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]