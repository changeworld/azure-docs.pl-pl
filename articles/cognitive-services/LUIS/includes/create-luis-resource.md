---
title: Tworzenie zasobu usługi LUIS
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: a765ac27936da9da5a2f41464c17491e3561f44b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73465927"
---
## <a name="create-a-luis-resource"></a>Tworzenie zasobu usługi LUIS

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)
1. Kliknij [pozycję Utwórz **opis języka** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Wprowadź wszystkie wymagane ustawienia:

    |Ustawienie|Wartość|
    |--|--|
    |Nazwa|Żądana nazwa (2-64 znaki)|
    |Subskrypcja|Wybierz odpowiednią subskrypcję|
    |Lokalizacja|Wybierz dowolną lokalizację w pobliżu i do dyspozycji|
    |Warstwa cenowa|`F0`- minimalny poziom cenowy|
    |Grupa zasobów|Wybieranie dostępnej grupy zasobów|

1. Kliknij **przycisk Utwórz** i poczekaj na utworzenie zasobu. Po jego utworzeniu przejdź do strony zasobu.
1. Zbieranie skonfigurowanych `endpoint` i klucza INTERFEJSU API, zobacz [zbieranie wymaganych parametrów](#gathering-required-parameters).

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]
