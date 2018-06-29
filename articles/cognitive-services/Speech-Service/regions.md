---
title: Regiony usługi mowy | Dokumentacja firmy Microsoft
description: Dokumentacja dotycząca regiony usługi mowy.
services: cognitive-services
author: mahilleb-msft
manager: wolmfa61
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 1eb3768f5a5c5a27a45dde3f62f862f36fa3e8ac
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098502"
---
# <a name="regions-of-the-speech-service"></a>Regiony usługi mowy

Usługa rozpoznawania mowy jest dostępna w różnych regionach.
Po utworzeniu subskrypcji można wybrać dostępny region, w zależności od potrzeb.

Podczas korzystania z subskrypcji masz konto dla tego obszaru przez Ciebie.

## <a name="rest-api"></a>Interfejs API REST

Przy użyciu interfejsu API REST, wybierz punkty końcowe prawo określonego regionu.
Zobacz [interfejsów API REST](rest-apis.md) szczegółowe informacje.



## <a name="speech-sdk"></a>Zestaw SDK rozpoznawania mowy

W [mowy SDK](speech-sdk.md), regiony są określone jako ciąg (na przykład jako parametr [SpeechFactory.FromSubscription](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechfactory.fromsubscription) mowy zestawu SDK dla języka C#).

W poniższej tabeli przedstawiono dostępne regiony rozpoznawania mowy i translację:

Region| Wartość parametru regionu w zestawie SDK mowy
-|-
Zachodnie stany USA| `westus`
Azja Wschodnia| `eastasia`
Europa Północna| `northeurope`

Dostępne regiony konwersji uznania za pośrednictwem usługi SDK mowy są wymienione w [strony region usługi języka opis](/azure/cognitive-services/luis/luis-reference-regions).
Dla każdego regionu publikowania na liście odpowiadającego mu parametru region mowy SDK jest określana jako pierwsza część nazwy punktu końcowego.
Na przykład użyć `westus` do określenia publikowania regionu zachodnie stany USA.
