---
title: Sposób użycia interfejsu API Finder anomalii języka Python - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy za pomocą wyszukiwania anomalii języka Python w usługach kognitywnych.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: c14916b0644edab613b298d6e71f8bbb9a6bb804
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348157"
---
# <a name="use-the-anomaly-finder-api-with-python"></a>Wyszukiwanie anomalii interfejsu API za pomocą języka Python

Ten artykuł zawiera informacje i przykłady kodu w celu szybkiego rozpocząć korzystanie z API Finder anomalii języka Python do wykonania zadania pobierania wyników anomalii czasu serii danych.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-python"></a>Pobieranie punktów anomalii z interfejsem API Finder anomalii używany język Python 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Przykład czasu danych w serii.

Przykład czasu punktów danych serii ma następującą składnię.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-python-example"></a>Analizowanie danych i uzyskać przykład Python punktów anomalii

Sprawdź, czy zainstalowano python3, następnie utwórz plik wykonywalny python o nazwie detect.py. W detect.py powinny zawierać poniższy kod. Przed wykonaniem kodu, pamiętaj, aby zastąpić `[YOUR_SUBSCRIPTION_KEY]` wartości z klucza ważnej subskrypcji.
Zastąp `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` z punktami danych.

```python
import requests
import json


def detect(url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(url, data=json.dumps(request_data), headers=headers)
    if response.status_code == 200:
        return json.loads(response.content.decode("utf-8"))
    else:
        print(response.status_code)
        raise Exception(response.text)


sample_data = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]"
endpont = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
subscription_key = "[YOUR_SUBSCRIPTION_KEY]"

result = detect(endpont, subscription_key, sample_data)
print(result)

```

### <a name="example-response"></a>Przykładowa odpowiedź

Odpowiedź oznaczająca Powodzenie jest zwracana w formacie JSON. Przykładowa odpowiedź ma następującą składnię.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Aplikacji Python](../tutorials/python-tutorial.md)
