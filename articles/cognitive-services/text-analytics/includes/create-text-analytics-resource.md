---
title: Tworzenie zasobu analiza tekstu Cognitive Services
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób analiza tekstu Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383454"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Tworzenie zasobu analiza tekstu Cognitive Services

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Utwórz zasób**, a następnie przejdź do pozycji **AI + Machine Learning** > **Analiza tekstu**.
   Lub przejdź do pozycji [utwórz analiza tekstu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Wprowadź wszystkie wymagane ustawienia:

    |Ustawienie|Wartość|
    |--|--|
    |Nazwa|Wprowadź nazwę (2-64 znaków).|
    |Subskrypcja|Wybierz odpowiednią subskrypcję.|
    |Lokalizacja|Wybierz lokalizację znajdującą się w pobliżu.|
    |Warstwa cenowa| Przejdź **do**warstwy cenowej standardowa.|
    |Grupa zasobów|Wybierz dostępną grupę zasobów.|

1. Wybierz pozycję **Utwórz**i poczekaj na utworzenie zasobu. Przeglądarka automatycznie przekierowuje do nowo utworzonej strony zasobów.
1. Zbierz skonfigurowany `endpoint` i klucz interfejsu API:

    |Karta zasób w portalu|Ustawienie|Wartość|
    |--|--|--|
    |**Omówienie**|Endpoint|Skopiuj punkt końcowy. Wydaje się wyglądać podobnie do `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`.|
    |**Ponownie**|Klucz interfejsu API|Skopiuj jeden z dwóch kluczy. Jest to 32-znakowy ciąg alfanumeryczny bez spacji ani kresek: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
