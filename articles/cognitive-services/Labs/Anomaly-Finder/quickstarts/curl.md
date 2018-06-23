---
title: Jak używać interfejsu API Finder anomalii z cURL - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
description: Pobierz informacje w celu szybkiego Rozpoczynanie pracy przy użyciu cURL i interfejsu API Finder anomalii w usługach kognitywnych.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 3c1d791b8c0478715b4ffa93cd7dfa43f9be4586
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348161"
---
# <a name="use-the-anomaly-finder-api-with-curl"></a>Za pomocą interfejsu API Finder anomalii z cURL

Ten artykuł zawiera informacje i przykłady kodu w celu szybkiego rozpocząć korzystanie z API Finder anomalii z cURL do wykonania zadania pobierania wyników anomalii czasu serii danych.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-finder-api-using-curl"></a>Pobieranie punktów anomalii przy użyciu interfejsu API Finder anomalii przy użyciu programu cURL 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Przykład czasu danych w serii.

Przykład czasu punktów danych serii ma następującą składnię.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-curl-example"></a>Analizowanie danych i uzyskać punkty anomalii przykład cURL

Dostępne są następujące kroki w przykładzie.

1. Zastąp `[YOUR_SUBSCRIPTION_KEY]` wartości z klucza ważnej subskrypcji.
2. Zastąp `[YOUR_REGION]` Aby użyć lokalizacji, w którym uzyskać klucze subskrypcji.
3. Zastąp `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` przykładzie lub punktami danych.
4. Wykonaj i sprawdź odpowiedzi.

```cURL

curl -v -X POST "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: [YOUR_SUBSCRIPTION_KEY]"
--data-ascii "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]" 

```

### <a name="example-response"></a>Przykładowa odpowiedź
Odpowiedź oznaczająca Powodzenie jest zwracana w formacie JSON. Przykład odpowiedzi jest następujący: [!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
