---
title: Tworzenie zasobu analizy tekstu usług Cognitive Services
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób analizy tekstu usług Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6cd653909e26dc5e0484ca289a1d2ab47e20457f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876446"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Tworzenie zasobu analizy tekstu usług Cognitive Services

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz **pozycję Utwórz zasób**, a następnie przejdź do **aplikacji AI + Analiza** > tekstu uczenia**maszynowego**.
   Możesz też przejść do tworzenia [analizy tekstu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Wprowadź wszystkie wymagane ustawienia:

    |Ustawienie|Wartość|
    |--|--|
    |Nazwa|Wprowadź nazwę (2-64 znaki).|
    |Subskrypcja|Wybierz odpowiednią subskrypcję.|
    |Lokalizacja|Wybierz pobliską lokalizację.|
    |Warstwa cenowa| Wprowadź **S**, standardową warstwę cenową.|
    |Grupa zasobów|Wybierz dostępną grupę zasobów.|

1. Wybierz **pozycję Utwórz**i poczekaj na utworzenie zasobu. Przeglądarka automatycznie przekierowuje do nowo utworzonej strony zasobów.
1. Zbieranie skonfigurowanego `endpoint` i klucza interfejsu API:

    |Karta Zasoby w portalu|Ustawienie|Wartość|
    |--|--|--|
    |**Omówienie**|Endpoint|Skopiuj punkt końcowy. Wydaje się `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`podobny do .|
    |**Klucze**|Klucz interfejsu API|Skopiuj jeden z dwóch kluczy. Jest to 32-znakowy ciąg alfanumeryczny bez spacji i `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` kresek: <>.|
