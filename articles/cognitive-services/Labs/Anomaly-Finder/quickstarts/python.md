---
title: Jak używać interfejsu API wyszukiwania anomalii przy użyciu języka Python — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
description: Pobierz informacje oraz przykłady kodu w celu szybkiego Rozpocznij pracę przy użyciu narzędzie do wyszukiwania anomalii przy użyciu języka Python w usługach Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 3d4411153f2c5c342b1640865f05b6bf4c4b0f61
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55212617"
---
# <a name="use-the-anomaly-finder-api-with-python"></a>Narzędzie do wyszukiwania anomalii interfejsu API za pomocą języka Python

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Ten artykuł zawiera informacje i przykłady kodu, aby pomóc Ci szybko rozpocząć pracę, przy użyciu interfejsu API wyszukiwania anomalii przy użyciu języka Python do wykonania zadania pobierania wyniku anomalii w danych szeregów czasowych.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-python"></a>Pobieranie punktów anomalii przy użyciu interfejsu API wyszukiwania anomalii przy użyciu języka Python 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Przykład danych szeregów czasowych

Przykład godziny punktów danych serii wyglądają następująco.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-python-example"></a>Analizowanie danych i pobrać przykład Python punktów anomalii

Upewnij się, możesz zainstalowano środowiska python3 jako, a następnie utworzyć plik wykonywalny języka python o nazwie detect.py. W detect.py powinny zawierać poniższy kod. Przed wykonaniem kodu, pamiętaj, aby zastąpić `[YOUR_SUBSCRIPTION_KEY]` wartość kluczem ważnej subskrypcji.
Zastąp `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` z punktów danych.

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

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON. Przykładowa odpowiedź wygląda następująco:
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Aplikacja w języku Python](../tutorials/python-tutorial.md)
