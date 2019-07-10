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
ms.openlocfilehash: 3886777b283af35e84683480a59097584b537fea
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711583"
---
## <a name="create-an-face-resource"></a>Utwórz zasób usługi rozpoznawania twarzy

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)
1. Kliknij przycisk [Utwórz **twarzy** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace) zasobów
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
    |**Omówienie**|Endpoint|Kopiowanie punktu końcowego. Będzie on podobny do `https://face.cognitiveservices.azure.com/face/v1.0`|
    |**klucze**|Klucz interfejsu API|Skopiuj 1 dwa klucze. Jest to 32 ciąg znaków alfanumerycznych, bez spacji lub kreski, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
