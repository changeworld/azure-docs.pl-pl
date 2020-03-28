---
title: 'Szybki start: wykrywanie anomalii jako partii przy użyciu interfejsu API REST detektora anomalii i języka Python'
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API detektora anomalii, aby wykryć nieprawidłowości w serii danych jako wsadowe lub przesyłane strumieniowo dane w tym przewodniku Szybki start.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 684aba561dc50b64dd7cc564cff8e55229ce1429
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239045"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Szybki start: wykrywanie anomalii w danych szeregów czasowych przy użyciu interfejsu API REST detektora anomalii i języka Python

Użyj tego przewodnika Szybki start, aby rozpocząć korzystanie z dwóch trybów wykrywania interfejsu API detektora anomalii do wykrywania anomalii w danych szeregów czasowych. Ta aplikacja języka Python wysyła dwa żądania interfejsu API zawierające dane szeregów czasowych w formacie JSON i pobiera odpowiedzi.

| Żądanie interfejsu API                                        | Dane wyjściowe aplikacji                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Wykrywanie anomalii jako partii                        | Odpowiedź JSON zawierająca stan anomalii (i inne dane) dla każdego punktu danych w danych szeregów czasowych i pozycje wykrytych anomalii. |
| Wykrywanie stanu anomalii najnowszego punktu danych | Odpowiedź JSON zawierająca stan anomalii (i inne dane) dla najnowszego punktu danych w danych szeregów czasowych.                                                                                                                                         |

 Chociaż ta aplikacja jest napisana w języku Python, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania. Kod źródłowy tego przewodnika Szybki start można znaleźć w [serwisie GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/python-detect-anomalies.py).

## <a name="prerequisites"></a>Wymagania wstępne

- [Python 2.x lub 3.x](https://www.python.org/downloads/)
- Klucz i punkt końcowy detektora anomalii
- [Biblioteka Żądań](https://pypi.org/project/requests/) dla pythona

- Plik JSON zawierający punkty danych szeregów czasowych. Przykładowe dane dla tego przewodnika Szybki start można znaleźć w [usłudze GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Tworzenie zasobu detektora anomalii

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]


## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. Utwórz nowy plik języka Python i dodaj następujące importy.

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. Tworzenie zmiennych dla klucza subskrypcji i punktu końcowego. Poniżej znajdują się identyfikatory URI, których można użyć do wykrywania anomalii. Zostaną one dołączone do punktu końcowego usługi później, aby utworzyć adresy URL żądań interfejsu API.

    |Metoda wykrywania  |Identyfikator URI  |
    |---------|---------|
    |Wykrywanie partii    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Wykrywanie w najnowszym punkcie danych     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. Odczyt w pliku danych JSON, otwierając `json.load()`go i używając pliku .

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>Tworzenie funkcji do wysyłania żądań

1. Utwórz nową `send_request()` funkcję o nazwie, która przyjmuje zmienne utworzone powyżej. Następnie wykonaj poniższe czynności.

2. Utwórz słownik dla nagłówków żądań. Ustaw `Content-Type` na `application/json`, i dodaj klucz `Ocp-Apim-Subscription-Key` subskrypcji do nagłówka.

3. Wyślij żądanie `requests.post()`za pomocą pliku . Połącz adres URL punktu końcowego i wykrywania anomalii dla pełnego adresu URL żądania i dołącz nagłówki i dane żądania json. A następnie zwróć odpowiedź.

    [!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Wykrywanie anomalii jako partii

1. Utwórz metodę `detect_batch()` wywoływaną do wykrywania anomalii w danych jako partii. Wywołanie `send_request()` metody utworzonej powyżej z punktu końcowego, adres URL, klucz subskrypcji i json danych.

2. Wywołaj `json.dumps()` wynik, aby go sformatować i wydrukować na konsoli.

3. Jeśli odpowiedź `code` zawiera pole, wydrukuj kod błędu i komunikat o błędzie.

4. W przeciwnym razie znajdź pozycje anomalii w zestawie danych. `isAnomaly` Pole odpowiedzi zawiera wartość logiczną odnoszącą się do tego, czy dany punkt danych jest anomalią. Iteruje przez listę i wydrukuj `True` indeks dowolnych wartości. Wartości te odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

    [!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

1. Utwórz metodę `detect_latest()` wywoływaną w celu ustalenia, czy najnowszy punkt danych w szeregach czasowych jest anomalią. Wywołanie `send_request()` powyższej metody z punktu końcowego, adres URL, klucz subskrypcji i json danych. 

2. Wywołaj `json.dumps()` wynik, aby go sformatować i wydrukować na konsoli.

    [!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="send-the-request"></a>Wysyłanie żądania

Wywołanie metod wykrywania anomalii utworzonych powyżej.

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>Przykładowa odpowiedź

Pomyślna odpowiedź jest zwracana w formacie JSON. Kliknij poniższe łącza, aby wyświetlić odpowiedź JSON w usłudze GitHub:
* [Przykładowa odpowiedź wykrywania partii](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Przykładowa najnowsza odpowiedź wykrywania punktów](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
