---
title: Jak używać interfejsu API wyszukiwania anomalii za pomocą programu cURL — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
description: Pobierz informacje ułatwią Ci szybko rozpocząć pracę, przy użyciu programu cURL i interfejsu API wyszukiwania anomalii w usługach Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 31049e24687192b1ea1030a7180299f57bc76771
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48246334"
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
