---
title: Jak używać interfejsu API wyszukiwania anomalii za pomocą języka Ruby — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
description: Pobierz informacje oraz przykłady kodu w celu szybkiego Rozpocznij pracę przy użyciu języka Ruby i interfejs API wyszukiwania anomalii w usługach Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 6eb559f8971583afe9619fb41fe331bd3013bb69
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2018
ms.locfileid: "41987966"
---
# <a name="use-the-anomaly-finder-api-with-ruby"></a>Narzędzie do wyszukiwania anomalii interfejsu API za pomocą języka Ruby

Ten artykuł zawiera informacje i przykłady kodu, aby pomóc Ci szybko rozpocząć pracę, przy użyciu interfejsu API wyszukiwania anomalii za pomocą języka Ruby do wykonywania zadań pobierania wynik wykrywania anomalii w danych szeregów czasowych.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-ruby"></a>Pobieranie punktów anomalii przy użyciu interfejsu API wyszukiwania anomalii przy użyciu języka Ruby 
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Przykład danych szeregów czasowych
Przykład godziny punktów danych serii jest następująca,

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-ruby-example"></a>Analizowanie danych otrzymasz odpowiednie punkty anomalii przykład języka Ruby

Dostępne są następujące kroki w przykładzie.

1. Zainstaluj [klienta rest](https://github.com/rest-client/rest-client) , uruchamiając "rozwiązania gem klienta rest Zainstaluj".
2. Zapisz poniższego kodu, jako pliku RB.
3. Zastąp `[YOUR_SUBSCRIPTION_KEY]` wartość kluczem ważnej subskrypcji.
4. Zastąp `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` przy użyciu przykładu lub punktów danych.
5. Wykonywanie, a następnie sprawdź odpowiedzi.

```ruby
# https://github.com/rest-client/rest-client
require 'rest_client'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscription_key = '[YOUR_SUBSCRIPTION_KEY]';

endpoint = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

# Replace the request data with your real data.
requestData = '[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]';

header = {
    :content_type => 'application/json',
    :'Ocp-Apim-Subscription-Key' => subscription_key
}

response = RestClient::Request.execute(
    :url => endpoint,
    :method => :post,
    :verify_ssl => true,
    :payload => requestData,
    :header => header)

# You will see the response with anomaly results
puts response.body
```

### <a name="example-response"></a>Przykładowa odpowiedź

Pomyślnej odpowiedzi jest zwracany w formacie JSON. Przykładowa odpowiedź wygląda następująco:
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
