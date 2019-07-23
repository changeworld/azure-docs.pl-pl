---
title: Tworzenie zasobu analiza tekstu Cognitive Services
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób analiza tekstu Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 6a9363472e1d5770232ca9a0b151fb309d9c4c75
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377420"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Tworzenie zasobu analiza tekstu Cognitive Services

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Utwórz zasób**, a następnie przejdź do pozycji **AI + Machine Learning** > **Analiza tekstu**.
   Lub przejdź do pozycji [utwórz analiza tekstu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Wprowadź wszystkie wymagane ustawienia:

    |Ustawienie|Wartość|
    |--|--|
    |Name (Nazwa)|Wprowadź nazwę (2-64 znaków).|
    |Subscription|Wybierz odpowiednią subskrypcję.|
    |Location|Wybierz lokalizację znajdującą się w pobliżu.|
    |Warstwa cenowa| Przejdź **do**warstwy cenowej standardowa.|
    |Resource group|Wybierz dostępną grupę zasobów.|

1. Wybierz pozycję **Utwórz**i poczekaj na utworzenie zasobu. Przeglądarka automatycznie przekierowuje do nowo utworzonej strony zasobów.
1. Zbierz skonfigurowany `endpoint` i klucz interfejsu API:

    |Karta zasób w portalu|Ustawienie|Wartość|
    |--|--|--|
    |**Omówienie**|Endpoint|Skopiuj punkt końcowy. Wygląda podobnie do `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`.|
    |**Ponownie**|Klucz interfejsu API|Skopiuj jeden z dwóch kluczy. Jest to 32-znakowy ciąg alfanumeryczny bez spacji ani kresek: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
