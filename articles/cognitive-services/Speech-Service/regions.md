---
title: Regiony usługi mowy
titlesuffix: Azure Cognitive Services
description: Odwołanie dla regionów usługi mowy.
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mahilleb
ms.openlocfilehash: 088e581da7511797a0f39959d867c6298262462a
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242334"
---
# <a name="regions-of-the-speech-service"></a>Regiony usługi mowy

Usługa rozpoznawania mowy jest dostępna w różnych regionach.
Po utworzeniu subskrypcji można wybrać dostępny region, w zależności od potrzeb.

Korzystając z subskrypcją, masz konto dla wybranego regionu.

## <a name="rest-api"></a>Interfejs API REST

Użyj interfejsu API REST, aby wybrać poprawne punkty końcowe specyficzne dla regionu.
Zobacz [interfejsów API REST](rest-apis.md) Aby uzyskać szczegółowe informacje.

## <a name="speech-sdk"></a>Zestaw SDK rozpoznawania mowy

W [zestawu SDK usługi mowy](speech-sdk.md), regiony są określane jako ciąg (na przykład, jako parametr do `SpeechConfig.FromSubscription` mowy zestawu SDK dla języka C#).

### <a name="regions-for-speech-recognition-and-translation"></a>Regiony usługi rozpoznawania mowy i tłumaczenia

W poniższej tabeli wymieniono dostępne regiony dla **rozpoznawania mowy** i **tłumaczenia**.

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


### <a name="regions-for-intent-recognition"></a>Regiony dla rozpoznawanie intencji

Dostępne regiony dla **rozpoznawanie intencji** za pośrednictwem mowy usługi SDK są wymienione na [strony region usługi Language Understanding](/azure/cognitive-services/luis/luis-reference-regions).
Dla każdego regionu publikowania na liście z odpowiadającym mu parametrem zestaw SDK rozpoznawania mowy w regionie jest określana jako pierwszą część nazwy domeny, punktu końcowego.
Na przykład użyć `westus` do określenia publikowania regionu zachodnie stany USA.
