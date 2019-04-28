---
title: Jak używać interfejsu API wyszukiwania anomalii za pomocą programu cURL — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
description: Pobierz informacje ułatwią Ci szybko rozpocząć pracę, przy użyciu programu cURL i interfejsu API wyszukiwania anomalii w usługach Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 7a4996c83d57b34fcfcff43650b21359acb4e65e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094905"
---
# <a name="use-the-anomaly-finder-api-with-curl"></a>Interfejs API wyszukiwania anomalii za pomocą programu cURL

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Ten artykuł zawiera informacje i przykłady kodu, aby pomóc Ci szybko rozpocząć pracę, przy użyciu interfejs API wyszukiwania anomalii za pomocą programu cURL w celu uzyskania wyniku anomalii w danych szeregów czasowych.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-finder-api-using-curl"></a>Pobieranie punktów anomalii przy użyciu interfejsu API wyszukiwania anomalii przy użyciu programu cURL 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Przykład danych szeregów czasowych

Przykład godziny punktów danych serii wyglądają następująco.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-curl-example"></a>Analizowanie danych otrzymasz odpowiednie punkty anomalii przykład programu cURL

Dostępne są następujące kroki w przykładzie.

1. Zastąp wartość `[YOUR_SUBSCRIPTION_KEY]` prawidłowym kluczem subskrypcji.
2. Zastąp `[YOUR_REGION]` Aby użyć lokalizacji, gdzie uzyskać klucze subskrypcji.
3. Zastąp `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` przy użyciu przykładu lub punktów danych.
4. Wykonywanie, a następnie sprawdź odpowiedzi.

```cURL

curl -v -X POST "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: [YOUR_SUBSCRIPTION_KEY]"
--data-ascii "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]" 

```

### <a name="example-response"></a>Przykładowa odpowiedź
Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON. Przykładowa odpowiedź jest następująca: [!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
