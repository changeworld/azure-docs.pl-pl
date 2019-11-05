---
title: Jak uzyskać punkt końcowy przewidywania wersji v3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bc6e43faca47e360daa8214e6b9f6e9df4a2f130
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495453"
---
1. W portalu LUIS, w sekcji Zarządzanie (menu w prawym górnym rogu), na stronie klucze i punkty końcowe (menu po lewej) wybierz adres URL punktu końcowego w dolnej części strony.

    Ta akcja powoduje otwarcie karty przeglądarki z adresem URL punktu końcowego na pasku adresu.

    Adres URL zawiera identyfikator aplikacji, klucz i nazwę gniazda. Adres URL punktu końcowego przewidywania v3 wygląda następująco:

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=UTTERANCE-TEXT.`

