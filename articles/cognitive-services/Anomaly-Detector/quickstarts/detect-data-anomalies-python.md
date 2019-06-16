---
title: 'Szybki start: Wykrywaj anomalie jako zadania wsadowego za pomocą interfejsu API REST wykrywanie anomalii i języka Python | Dokumentacja firmy Microsoft'
description: Wykrywanie nieprawidłowości w serii danych, jako partii albo na strumieniu danych za pomocą interfejsu API wykrywanie anomalii.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: cd37638b9bac2a1b48f0ef0abb7b18da440049f0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080740"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Szybki start: Wykrywanie anomalii w danych szeregów czasowych przy użyciu interfejsu API REST wykrywanie anomalii i Python

Użyj tego przewodnika Szybki Start, aby rozpocząć korzystanie z dwóch trybów wykrywania API wykrywanie anomalii do wykrycia anomalii w danych szeregów czasowych. Ta aplikacja języka Python wysyła dwa żądania interfejsu API, zawierająca dane szeregów czasowych w formacie JSON, a następnie pobiera odpowiedzi.

| Żądanie interfejsu API                                        | Dane wyjściowe aplikacji                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Wykrywaj anomalie jako zadania wsadowego                        | Odpowiedź JSON zawierający stan anomalii (i inne dane) dla każdego punktu danych w danych szeregów czasowych i pozycji wszelkie wykryte anomalie. |
| Wykrywanie anomalii stan najnowszego punktu danych | Odpowiedź JSON zawierający stan anomalii (i inne dane) najnowszy punkt danych w danych szeregów czasowych.                                                                                                                                         |

 Chociaż ta aplikacja jest napisana w języku Python, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

## <a name="prerequisites"></a>Wymagania wstępne

- [Środowisko Python 2.x lub 3.x](https://www.python.org/downloads/)

- [Biblioteki żądań](http://docs.python-requests.org) dla języka python

- Wskazuje JSON pliku zawierającego dane szeregów czasowych. Przykładowe dane dla tego przewodnika Szybki Start można znaleźć na [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. W ulubionym edytorze tekstów lub IDE Utwórz nowy plik w języku python. Dodaj następujące instrukcje importu.

    ```python
    import requests
    import json
    ```

2. Utwórz zmienne dla swój klucz subskrypcji i punktu końcowego usługi. Poniżej przedstawiono identyfikatory URI, można użyć do wykrywania anomalii. Te będą dołączane do punktu końcowego usługi później, aby utworzyć interfejs API adresów URL żądania.

    |Metoda wykrywania  |Identyfikator URI  |
    |---------|---------|
    |Wykrywanie usługi Batch    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Wykrywanie na najnowszy punkt danych     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. Przeczytaj w pliku danych JSON, otwierając go i przy użyciu `json.load()`.

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>Tworzenie funkcji na potrzeby wysyłania żądań

1. Utwórz nową funkcję o nazwie `send_request()` przyjmującej zmienne utworzone powyżej. Następnie wykonaj poniższe czynności.

2. Utwórz słownik na potrzeby nagłówki żądania. Ustaw `Content-Type` do `application/json`i Dodaj swój klucz subskrypcji, aby `Ocp-Apim-Subscription-Key` nagłówka.

3. Wyślij żądanie, używając `requests.post()`. Łączenie punktu końcowego i adres URL wykrywania anomalii pełny adres URL żądania i zawierać nagłówki i dane żądania json. A następnie zwraca odpowiedź.

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>Wykrywaj anomalie jako zadania wsadowego

1. Utwórz metodę o nazwie `detect_batch()` wykrycia anomalii w danych jako zadania wsadowego. Wywołaj `send_request()` metoda utworzoną za pomocą punktu końcowego, adres url, klucz subskrypcji i danych json.

2. Wywołaj `json.dumps()` na wynik, aby sformatować je i drukować do konsoli.

3. Jeśli odpowiedź zawiera `code` pola, drukowania, kod błędu oraz komunikat o błędzie.

4. W przeciwnym razie należy znaleźć pozycje anomalie w zestawie danych. Odpowiedź `isAnomaly` pole zawiera wartość typu boolean odnoszących się do tego, czy punkt danych to anomalii. Iteracyjne przeglądanie listy i drukowanie indeksu dowolnego `True` wartości. Te wartości odpowiadają indeks punktów danych nietypowe, jeśli którekolwiek.

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))

    if result.get('code') != None:
        print("Detection failed. ErrorCode:{}, ErrorMessage:{}".format(result['code'], result['message']))
    else:
        # Find and display the positions of anomalies in the data set
        anomalies = result["isAnomaly"]
        print("Anomalies detected in the following data positions:")
        for x in range(len(anomalies)):
            if anomalies[x] == True:
                print (x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie anomalii stan najnowszego punktu danych

1. Utwórz metodę o nazwie `detect_latest()` do ustalenia, czy najnowszego punktu danych w serii czasu jest anomalii. Wywołaj `send_request()` metody powyżej z punktu końcowego, adres url, klucz subskrypcji i danych json. 

2. Wywołaj `json.dumps()` na wynik, aby sformatować je i drukować do konsoli.

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Ładowanie danych szeregów czasowych i wysłać żądanie

1. Ładowanie danych szeregów czasowych JSON otwierania obsługi plików i używania `json.load()` na nim. Następnie wywołaj anomalii metody wykrywania utworzonego powyżej.

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>Przykładowa odpowiedź

Odpowiedź oznaczająca Powodzenie są zwracane w formacie JSON. Kliknij poniższe łącza, aby wyświetlić odpowiedź w formacie JSON w usłudze GitHub:
* [Przykładowa odpowiedź wykrywania usługi batch](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Przykład najnowszy punkt wykrywania odpowiedzi](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
