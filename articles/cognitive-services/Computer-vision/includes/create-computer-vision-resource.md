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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499108"
---
## <a name="create-an-computer-vision-resource"></a>Tworzenie zasobu przetwarzanie obrazów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Kliknij pozycję [Utwórz zasób **Przetwarzanie obrazów** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) .
1. Wprowadź wszystkie wymagane ustawienia:

    |Ustawienie|Wartość|
    |--|--|
    |Nazwa|Wymagana nazwa (2-64 znaków)|
    |Subskrypcja|Wybierz odpowiednią subskrypcję|
    |Lokalizacja|Wybierz dowolną lokalizację w pobliżu i dostępną|
    |Warstwa cenowa|`F0` — minimalna warstwa cenowa|
    |Grupa zasobów|Wybierz dostępną grupę zasobów|

1. Kliknij przycisk **Utwórz** i poczekaj na utworzenie zasobu. Po jego utworzeniu przejdź do strony zasobów.
1. Zbierz skonfigurowane `{ENDPOINT_URI}` i `{API_KEY}`, zobacz [zbieranie wymaganych parametrów](../computer-vision-how-to-install-containers.md#gathering-required-parameters) w celu uzyskania szczegółowych informacji.
