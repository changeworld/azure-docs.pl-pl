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
ms.openlocfilehash: c026c636030b397da612b3c1409b80f0ff970f57
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284748"
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
