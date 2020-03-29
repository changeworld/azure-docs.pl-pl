---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: b40f1833f08074cb0a8d45fe3afc6bac7cbac7f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73523486"
---
## <a name="create-an-anomaly-detector-resource"></a>Tworzenie zasobu detektora anomalii

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)
1. Kliknij [pozycję Utwórz zasób **Detektor anomalii** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)
1. Wprowadź wszystkie wymagane ustawienia:

    |Ustawienie|Wartość|
    |--|--|
    |Nazwa|Żądana nazwa (2-64 znaki)|
    |Subskrypcja|Wybierz odpowiednią subskrypcję|
    |Lokalizacja|Wybierz dowolną lokalizację w pobliżu i do dyspozycji|
    |Warstwa cenowa|`F0`- minimalny poziom cenowy|
    |Grupa zasobów|Wybieranie dostępnej grupy zasobów|
    |Pole wyboru potwierdzenia podglądu (wymagane)|Niezależnie od tego, czy zapoznałeś się z informacją o **podglądzie,** czy nie|

1. Kliknij **przycisk Utwórz** i poczekaj na utworzenie zasobu. Po utworzeniu przejdź do strony zasobu
1. Zbieranie skonfigurowanych `endpoint` i klucz interfejsu API:

    |Karta Zasoby w portalu|Ustawienie|Wartość|
    |--|--|--|
    |**Przegląd**|Endpoint|Skopiuj punkt końcowy. Wygląda podobnie do`https://westus2.api.cognitive.microsoft.com/`|
    |**Klucze**|Klucz interfejsu API|Skopiuj 1 z dwóch klawiszy. Jest to ciąg 32 znaków alfanumerycznej bez spacji lub kresek, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|



