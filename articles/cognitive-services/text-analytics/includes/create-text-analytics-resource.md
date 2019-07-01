---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób usług cognitive services text analytics.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 022ffcd806d4d4f89f8a8cf256a541518ea12602
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455084"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Utwórz zasób Cognitive Services Text Analytics

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)
1. Wybierz **+ Utwórz zasób**, przejdź do **SI i uczenie maszynowe > analizy tekstu**, lub kliknij przycisk [Utwórz **analizy tekstu**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)
1. Wprowadź wszystkie wymagane ustawienia:

    |Ustawienie|Wartość|
    |--|--|
    |Name (Nazwa)|Nazwy żądanej (2 do 64 znaków)|
    |Subskrypcja|Wybierz odpowiednią subskrypcję.|
    |Location|Wybierz lokalizację w pobliżu|
    |Warstwa cenowa|`S` -ze standardowej warstwy cenowej|
    |Grupa zasobów|Wybierz grupę dostępnych zasobów|

1. Kliknij przycisk **Utwórz** i poczekaj na zasób, który ma zostać utworzony. Po jego utworzeniu przeglądarce automatycznie przekierowuje do strony nowo utworzonego zasobu
1. Zbierz skonfigurowane `endpoint` i klucz interfejsu API:

    |Karta zasobów w portalu|Ustawienie|Wartość|
    |--|--|--|
    |**Omówienie**|Endpoint|Kopiowanie punktu końcowego. Będzie on podobny do `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**klucze**|Klucz interfejsu API|Skopiuj 1 dwa klucze. Jest to 32 ciąg znaków alfanumerycznych, bez spacji lub kreski, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|