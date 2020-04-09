---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2647c604bebc2885f5c8516eb997792b65751e88
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876096"
---
## <a name="create-an-computer-vision-resource"></a>Tworzenie zasobu przetwarzania wizyjnego

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).
1. Kliknij pozycję Utwórz zasób [ **przetwarzania.** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
1. Wprowadź wszystkie wymagane ustawienia:

    |Ustawienie|Wartość|
    |--|--|
    |Nazwa|Żądana nazwa (2-64 znaki)|
    |Subskrypcja|Wybierz odpowiednią subskrypcję|
    |Lokalizacja|Wybierz dowolną lokalizację w pobliżu i do dyspozycji|
    |Warstwa cenowa|`F0`- minimalny poziom cenowy|
    |Grupa zasobów|Wybieranie dostępnej grupy zasobów|

1. Kliknij **przycisk Utwórz** i poczekaj na utworzenie zasobu. Po jego utworzeniu przejdź do strony zasobu.
1. Aby uzyskać `{ENDPOINT_URI}` `{API_KEY}`szczegółowe informacje, zbierz skonfigurowane i , zobacz [zbieranie wymaganych parametrów.](../computer-vision-how-to-install-containers.md#gathering-required-parameters)
