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
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523528"
---
## <a name="create-a-speech-resource"></a>Tworzenie zasobu mowy

1. Zaloguj się do [Azure Portal](https://portal.azure.com)
1. Kliknij pozycję [Utwórz zasób **mowy** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)
1. Wprowadź wszystkie wymagane ustawienia:

    |Ustawienie|Wartość|
    |--|--|
    |Nazwa|Wymagana nazwa (2-64 znaków)|
    |Subskrypcja|Wybierz odpowiednią subskrypcję|
    |Lokalizacja|Wybierz dowolną lokalizację w pobliżu i dostępną|
    |Warstwa cenowa|`F0` — minimalna warstwa cenowa|
    |Grupa zasobów|Wybierz dostępną grupę zasobów|

1. Kliknij przycisk **Utwórz** i poczekaj na utworzenie zasobu. Po jego utworzeniu przejdź do strony zasobów
1. Zbierz skonfigurowane `endpoint` i klucz interfejsu API:

    |Karta zasób w portalu|Ustawienie|Wartość|
    |--|--|--|
    |**Omówienie**|Endpoint|Skopiuj punkt końcowy. Wygląda podobnie do `https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**Ponownie**|Klucz interfejsu API|Kopiuj 1 z dwóch kluczy. Jest to 32 ciąg znaków alfanumerycznych bez spacji ani kresek, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
