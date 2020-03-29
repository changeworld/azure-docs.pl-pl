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
ms.openlocfilehash: 3c42bf2b2acc2472741bd603ea9d653a314ecc40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73523528"
---
## <a name="create-a-speech-resource"></a>Tworzenie zasobu Mowy

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)
1. Kliknij [pozycję Utwórz **Speech** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) zasób Mowy
1. Wprowadź wszystkie wymagane ustawienia:

    |Ustawienie|Wartość|
    |--|--|
    |Nazwa|Żądana nazwa (2-64 znaki)|
    |Subskrypcja|Wybierz odpowiednią subskrypcję|
    |Lokalizacja|Wybierz dowolną lokalizację w pobliżu i do dyspozycji|
    |Warstwa cenowa|`F0`- minimalny poziom cenowy|
    |Grupa zasobów|Wybieranie dostępnej grupy zasobów|

1. Kliknij **przycisk Utwórz** i poczekaj na utworzenie zasobu. Po utworzeniu przejdź do strony zasobu
1. Zbieranie skonfigurowanych `endpoint` i klucz interfejsu API:

    |Karta Zasoby w portalu|Ustawienie|Wartość|
    |--|--|--|
    |**Przegląd**|Endpoint|Skopiuj punkt końcowy. Wygląda podobnie do`https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**Klucze**|Klucz interfejsu API|Skopiuj 1 z dwóch klawiszy. Jest to ciąg 32 znaków alfanumerycznej bez spacji lub kresek, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
