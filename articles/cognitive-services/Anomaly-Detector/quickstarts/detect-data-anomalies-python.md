---
title: 'Szybki start: Wykrywaj anomalie jako partię przy użyciu interfejsu API REST usługi wykrywania anomalii i języka Python'
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API wykrywania anomalii w celu wykrycia nieprawidłowości w seriach danych jako partii lub przesyłania strumieniowego danych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: f40f1b94b3e7c2732fd8bed0bc6e503277b533c3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565819"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Szybki start: Wykrywaj anomalie w danych szeregów czasowych przy użyciu interfejsu API REST usługi wykrywania anomalii i języka Python

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć korzystanie z dwóch trybów wykrywania interfejsu API wykrywania anomalii w celu wykrycia anomalii w danych szeregów czasowych. Ta aplikacja w języku Python wysyła dwa żądania interfejsu API zawierające dane szeregów czasowych w formacie JSON i pobiera odpowiedzi.

| Żądanie interfejsu API                                        | Dane wyjściowe aplikacji                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Wykryj anomalie jako partię                        | Odpowiedź JSON zawierająca stan anomalii (i inne dane) dla każdego punktu danych w danych szeregów czasowych oraz pozycje wykryte anomalie. |
| Wykrywanie stanu anomalii najnowszego punktu danych | Odpowiedź JSON zawierająca stan anomalii (i inne dane) dla najnowszego punktu danych w danych szeregów czasowych.                                                                                                                                         |

 Chociaż ta aplikacja jest napisana w języku Python, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

## <a name="prerequisites"></a>Wymagania wstępne

- [Środowisko Python 2.x lub 3.x](https://www.python.org/downloads/)

- [Biblioteka żądań](http://docs.python-requests.org) dla języka Python

- Plik JSON zawierający punkty danych szeregów czasowych. Przykładowe dane dla tego przewodnika Szybki Start można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. W ulubionym edytorze tekstu lub w środowisku IDE Utwórz nowy plik w języku Python. Dodaj następujące Importy.

    ```python
    import requests
    import json
    ```

2. Utwórz zmienne dla klucza subskrypcji i punktu końcowego. Poniżej znajdują się identyfikatory URI, których można użyć do wykrywania anomalii. Zostaną one dołączone do punktu końcowego usługi później w celu utworzenia adresów URL żądań interfejsu API.

    |Metoda wykrywania  |Identyfikator URI  |
    |---------|---------|
    |Wykrywanie partii    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Wykrywanie najnowszego punktu danych     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. Przeczytaj plik danych JSON, otwierając go i używając `json.load()`.

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>Utwórz funkcję do wysyłania żądań

1. Utwórz nową funkcję o nazwie `send_request()` , która przyjmuje zmienne utworzone powyżej. Następnie wykonaj poniższe czynności.

2. Utwórz słownik dla nagłówków żądania. Ustaw wartość `application/json` `Ocp-Apim-Subscription-Key` na, a następnie Dodaj swój klucz subskrypcji do nagłówka. `Content-Type`

3. Wyślij żądanie przy użyciu `requests.post()`. Połącz swój punkt końcowy i adres URL wykrywania anomalii dla adresu URL pełnego żądania oraz Uwzględnij nagłówki i dane żądania JSON. A następnie Zwróć odpowiedź.

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json',
               'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(
        endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>Wykryj anomalie jako partię

1. Utwórz metodę o nazwie `detect_batch()` , aby wykrywać anomalie w danych jako partię. Wywołaj `send_request()` metodę utworzoną powyżej przy użyciu swojego punktu końcowego, adresu URL, klucza subskrypcji i danych JSON.

2. Zadzwoń `json.dumps()` na wynik, aby sformatować go i wydrukować w konsoli programu.

3. Jeśli odpowiedź zawiera `code` pole, Wydrukuj kod błędu i komunikat o błędzie.

4. W przeciwnym razie Znajdź pozycje anomalii w zestawie danych. `isAnomaly` Pole odpowiedzi zawiera wartość logiczną odnoszącą się do tego, czy dany punkt danych jest anomalią. Wykonaj iterację na liście i wydrukuj indeks wszelkich `True` wartości. Te wartości odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))

    if result.get('code') != None:
        print("Detection failed. ErrorCode:{}, ErrorMessage:{}".format(
            result['code'], result['message']))
    else:
        # Find and display the positions of anomalies in the data set
        anomalies = result["isAnomaly"]
        print("Anomalies detected in the following data positions:")
        for x in range(len(anomalies)):
            if anomalies[x] == True:
                print(x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

1. Utwórz metodę o nazwie `detect_latest()` , aby określić, czy najnowszy punkt danych w szeregu czasowym jest anomalią. Wywołaj `send_request()` powyższą metodę za pomocą punktu końcowego, adresu URL, klucza subskrypcji i danych JSON. 

2. Zadzwoń `json.dumps()` na wynik, aby sformatować go i wydrukować w konsoli programu.

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Załaduj dane szeregów czasowych i Wyślij żądanie

1. Załaduj dane szeregów czasowych JSON, otwierając procedurę obsługi plików `json.load()` i korzystając z niej. Następnie wywołaj metody wykrywania anomalii utworzone powyżej.

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>Przykładowa odpowiedź

Pomyślna odpowiedź jest zwracana w formacie JSON. Kliknij poniższe linki, aby wyświetlić odpowiedź JSON w serwisie GitHub:
* [Przykładowa reakcja wykrywania partii](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Przykład ostatniej odpowiedzi wykrywania punktu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
