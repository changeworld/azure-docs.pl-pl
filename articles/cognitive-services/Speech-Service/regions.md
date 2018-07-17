---
title: Regiony usługi mowy | Dokumentacja firmy Microsoft
description: Odwołanie dla regionów usługi mowy.
services: cognitive-services
author: mahilleb-msft
manager: wolmfa61
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 11360d163fdba057d373d091d46903cde7789a8b
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071423"
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

W poniższej tabeli wymieniono dostępne regiony dla **rozpoznawania mowy** i **tłumaczenia**:

Region| Wartość parametru regionu w zestawie SDK rozpoznawania mowy
-|-
Zachodnie stany USA| `westus`
Azja Wschodnia| `eastasia`
Europa Północna| `northeurope`

Dostępne regiony dla **rozpoznawanie intencji** za pośrednictwem mowy usługi SDK są wymienione w [strony region usługi Language Understanding](/azure/cognitive-services/luis/luis-reference-regions).
Dla każdego regionu publikowania na liście z odpowiadającym mu parametrem zestaw SDK rozpoznawania mowy w regionie jest określana jako pierwszą część nazwy domeny, punktu końcowego.
Na przykład użyć `westus` do określenia publikowania regionu zachodnie stany USA.
