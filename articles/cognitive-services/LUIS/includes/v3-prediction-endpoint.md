---
title: Jak uzyskać punkt końcowy przewidywania V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 23e7f88b23ac7af7470183877eeb1be89e79779c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80287751"
---
1. W portalu usługi LUIS w sekcji **Zarządzanie** (menu w prawym górnym rogu) na stronie **Zasoby platformy Azure** (menu po lewej) na karcie Zasoby **przewidywania** skopiuj **przykładową kwerendę** u dołu strony.

    Wklej adres URL do nowej karty przeglądarki.

    Adres URL ma identyfikator aplikacji, klucz i nazwę gniazda. Adres URL punktu końcowego przewidywania V3 wygląda następująco:

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

