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
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711825"
---
## <a name="create-a-speech-resource"></a>Utwórz zasób mowy

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)
1. Kliknij przycisk [Utwórz **mowy** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) zasobów
1. Wprowadź wszystkie wymagane ustawienia:

    |Ustawienie|Wartość|
    |--|--|
    |Name (Nazwa)|Nazwy żądanej (2 do 64 znaków)|
    |Subscription|Wybierz odpowiednią subskrypcję.|
    |Location|Wybierz dowolną lokalizację w pobliżu i dostępne|
    |Warstwa cenowa|`F0` -minimalnej warstwy cenowej|
    |Grupa zasobów|Wybierz grupę dostępnych zasobów|

1. Kliknij przycisk **Utwórz** i poczekaj na zasób, który ma zostać utworzony. Po jego utworzeniu, przejdź do strony zasobu
1. Zbierz skonfigurowane `endpoint` i klucz interfejsu API:

    |Karta zasobów w portalu|Ustawienie|Wartość|
    |--|--|--|
    |**Omówienie**|Endpoint|Kopiowanie punktu końcowego. Będzie on podobny do `https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**klucze**|Klucz interfejsu API|Skopiuj 1 dwa klucze. Jest to 32 ciąg znaków alfanumerycznych, bez spacji lub kreski, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
