---
title: Jak używać interfejsu API wyszukiwania anomalii z językiem PHP — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
description: Pobierz informacje oraz przykłady kodu w celu szybkiego Rozpocznij pracę przy użyciu narzędzie do wyszukiwania anomalii za pomocą języka PHP w usługach Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: e5262de518e6cdf5a131fa9a84a3aa8ce9ce9236
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213688"
---
# <a name="use-the-anomaly-finder-api-with-php"></a>Narzędzie do wyszukiwania anomalii interfejsu API za pomocą języka PHP

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Ten artykuł zawiera informacje i przykłady kodu, aby pomóc Ci szybko rozpocząć pracę, przy użyciu interfejsu API wyszukiwania anomalii za pomocą języka PHP w celu uzyskania wyniku anomalii w danych szeregów czasowych.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-php"></a>Pobieranie punktów anomalii przy użyciu interfejsu API wyszukiwania anomalii przy użyciu języka PHP
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Przykład danych szeregów czasowych
Przykład danych szeregów czasowych to w następujący sposób.
[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-php-example"></a>Analizowanie danych otrzymasz odpowiednie punkty anomalii przykład PHP
1. Zastąp wartość `[YOUR_SUBSCRIPTION_KEY]` prawidłowym kluczem subskrypcji.
2. Zastąp `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` przy użyciu przykładu lub punktów danych.
3. Wykonywanie, a następnie sprawdź odpowiedzi.

```PHP
<?php
# This sample uses the Apache HTTP client from HTTP components (http://hc.apache.org/httpcomponents-client-ga/)
require_once 'HTTP/Request2.php';

$request = new HTTP_Request2('https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection');
$url = $request->getUrl();

$requestData = '[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]';

$headers = array(
    # Request headers
    'Content-Type' => 'application/json',
    # NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '[YOUR_SUBSCRIPTION_KEY]',
);

$request->setHeader($headers);

$request->setMethod(HTTP_Request2::METHOD_POST);

# Request body
$request->setBody($requestData);

try
{
    $response = $request->send();
    echo $response->getBody();
}
catch (HttpException $ex)
{
    echo $ex;
}
?>
```

### <a name="example-response"></a>Przykładowa odpowiedź

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON. Przykładowa odpowiedź wygląda następująco:
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
