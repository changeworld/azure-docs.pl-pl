---
title: Utwórz zasób Cognitive Services Text Analytics
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób usługi Cognitive Services Text Analytics.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 7e8b4480911f00afa8524ef4b81d697bb8ee5bcc
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877448"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Utwórz zasób Cognitive Services Text Analytics

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz **Utwórz zasób**, a następnie przejdź do **SI i uczenie maszynowe** > **analizy tekstu**.
   Lub przejdź do [tworzenia analizy tekstu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Wprowadź wszystkie wymagane ustawienia:

    |Ustawienie|Wartość|
    |--|--|
    |Name (Nazwa)|Wprowadź nazwę (2 do 64 znaków)|
    |Subscription|Wybierz odpowiednią subskrypcję|
    |Location|Wybierz lokalizację w pobliżu|
    |Warstwa cenowa| Wprowadź **S**, ze standardowej warstwy cenowej|
    |Resource group|Wybierz grupę dostępnych zasobów|

1. Wybierz **Utwórz** i poczekaj na zasób, który ma zostać utworzony. Przeglądarka automatycznie przekierowuje do strony nowo utworzonego zasobu.
1. Zbieraj skonfigurowanych `endpoint` i klucz interfejsu API:

    |Karta zasobów w portalu|Ustawienie|Value|
    |--|--|--|
    |**Omówienie**|Endpoint|Kopiowanie punktu końcowego. Wygląda na to, podobnie jak `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**klucze**|Klucz interfejsu API|Skopiuj jeden z dwóch kluczy. Jest to ciąg alfanumeryczny 32-znakowy, bez spacji lub kreski: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
