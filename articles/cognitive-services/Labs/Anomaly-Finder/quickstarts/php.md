---
title: Sposób użycia interfejsu API Finder anomalii za pomocą języka PHP — kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy przy użyciu anomalii wyszukiwania za pomocą języka PHP w usługach kognitywnych.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: f81c99b77f931b5b259633fa8fcd0bf3e358e281
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348169"
---
# <a name="use-the-anomaly-finder-api-with-php"></a>Wyszukiwanie anomalii interfejsu API za pomocą języka PHP

Ten artykuł zawiera informacje i przykłady kodu w celu szybkiego rozpocząć korzystanie z API Finder anomalii za pomocą języka PHP do wykonania zadania pobierania wyników anomalii czasu serii danych.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-php"></a>Pobieranie punktów anomalii z interfejsem API Finder anomalii używany język PHP
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Przykład czasu danych w serii.
Przykładowe dane serii czasu ma następującą składnię.
[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-php-example"></a>Analizowanie danych i uzyskać punkty anomalii przykład PHP
1. Zastąp `[YOUR_SUBSCRIPTION_KEY]` wartości z klucza ważnej subskrypcji.
2. Zastąp `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` przykładzie lub punktami danych.
3. Wykonaj i sprawdź odpowiedzi.

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

Odpowiedź oznaczająca Powodzenie jest zwracana w formacie JSON. Przykładowa odpowiedź ma następującą składnię.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
