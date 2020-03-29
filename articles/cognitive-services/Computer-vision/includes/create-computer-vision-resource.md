---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2019
ms.author: dapine
ms.openlocfilehash: 2cb2cfbdfbac5d496f109d85977b41a050766ab0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499108"
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
