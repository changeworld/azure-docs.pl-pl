---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: ecb172aec37fdab21bed8373ac7484dc52a54829
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72822017"
---
## <a name="create-a-luis-resource"></a>Tworzenie zasobu LUIS

1. Zaloguj się do [Azure Portal](https://portal.azure.com)
1. Kliknij przycisk [utwórz **Language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
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
    |**Omówienie**|Punkt końcowy|Skopiuj punkt końcowy. Wygląda podobnie do `https://luis.cognitiveservices.azure.com/luis/v2.0`|
    |**Ponownie**|Klucz interfejsu API|Kopiuj 1 z dwóch kluczy. Jest to 32 ciąg znaków alfanumerycznych bez spacji ani kresek, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
