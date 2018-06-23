---
title: Jak używać interfejsu API Finder anomalii z Ruby - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy przy użyciu Ruby i interfejsu API Finder anomalii w usługach kognitywnych.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: ca4754514ba5012f7e9e28981d0869d174561fb3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348168"
---
# <a name="use-the-anomaly-finder-api-with-ruby"></a>Wyszukiwanie anomalii interfejsu API za pomocą Ruby

Ten artykuł zawiera informacje i przykłady kodu w celu szybkiego rozpocząć korzystanie z API Finder anomalii z Ruby zadania pobierania wynik wykrywania anomalii czasu serii danych.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-ruby"></a>Pobieranie punktów anomalii z interfejsem API Finder anomalii przy użyciu Ruby 
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Przykład czasu danych w serii.
Przykład czasu punktów danych serii jest następująca, [!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-ruby-example"></a>Analizowanie danych i uzyskać punkty anomalii przykład Ruby

Dostępne są następujące kroki w przykładzie.

1. Zainstaluj [klienta rest](https://github.com/rest-client/rest-client) uruchamiając "gem klient rest instalacji".
2. Zapisz poniżej kod jako plik .rb.
3. Zastąp `[YOUR_SUBSCRIPTION_KEY]` wartości z klucza ważnej subskrypcji.
4. Zastąp `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` przykładzie lub punktami danych.
5. Wykonaj i sprawdź odpowiedzi.

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

Odpowiedź oznaczająca Powodzenie jest zwracana w formacie JSON. Przykładowa odpowiedź ma następującą składnię.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
