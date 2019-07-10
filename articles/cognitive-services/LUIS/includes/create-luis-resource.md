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
ms.openlocfilehash: 52d8e1355558b197b193a50c7cde571799541268
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711836"
---
## <a name="create-a-luis-resource"></a>Utwórz zasób usługi LUIS

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)
1. Kliknij przycisk [tworzenie **Language Understanding**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUIS)
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
    |**Omówienie**|Endpoint|Kopiowanie punktu końcowego. Będzie on podobny do `https://luis.cognitiveservices.azure.com/luis/v2.0`|
    |**klucze**|Klucz interfejsu API|Skopiuj 1 dwa klucze. Jest to 32 ciąg znaków alfanumerycznych, bez spacji lub kreski, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
