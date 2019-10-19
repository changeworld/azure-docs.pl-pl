---
title: 'Szybki Start: wykrywanie anomalii w danych szeregów czasowych przy użyciu interfejsu API REST usługi wykrywania anomalii iC#'
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API wykrywania anomalii w celu wykrycia nieprawidłowości w seriach danych jako partii lub przesyłania strumieniowego danych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: aahi
ms.openlocfilehash: 222fb5d37065bc40e9c96a9ff3487a7ea8ad0570
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554776"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Szybki Start: wykrywanie anomalii w danych szeregów czasowych przy użyciu interfejsu API REST usługi wykrywania anomalii iC# 

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć korzystanie z dwóch trybów wykrywania interfejsu API wykrywania anomalii w celu wykrycia anomalii w danych szeregów czasowych. Ta C# aplikacja wysyła dwa żądania interfejsu API zawierające dane szeregów czasowych w formacie JSON i pobiera odpowiedzi.

| Żądanie interfejsu API                                        | Dane wyjściowe aplikacji                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Wykryj anomalie jako partię                        | Odpowiedź JSON zawierająca stan anomalii (i inne dane) dla każdego punktu danych w danych szeregów czasowych oraz pozycje wykryte anomalie. |
| Wykrywanie stanu anomalii najnowszego punktu danych | Odpowiedź JSON zawierająca stan anomalii (i inne dane) dla najnowszego punktu danych w danych szeregów czasowych.                                                                                                                                         |

 Chociaż ta aplikacja jest napisana w języku C#, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

## <a name="prerequisites"></a>Wymagania wstępne

- Dowolna wersja programu [Visual Studio 2017 lub nowszego](https://visualstudio.microsoft.com/downloads/),

- Struktura [Json.NET](https://www.newtonsoft.com/json) dostępna jako pakiet NuGet. Aby zainstalować plik Newtonsoft. JSON jako pakiet NuGet w programie Visual Studio:
    
    1. Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**.
    2. Wybierz pozycję **Zarządzaj pakietami NuGet**.
    3. Wyszukaj plik *Newtonsoft. JSON* i zainstaluj pakiet.

- Jeśli używasz systemu Linux/MacOS, ta aplikacja może być uruchamiana przy użyciu narzędzia [mono](https://www.mono-project.com/).

- Plik JSON zawierający punkty danych szeregów czasowych. Przykładowe dane dla tego przewodnika Szybki Start można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Tworzenie zasobu wykrywania anomalii

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. W programie Visual Studio Utwórz nowe rozwiązanie konsoli i Dodaj następujące pakiety. 

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Utwórz zmienne dla klucza subskrypcji i punktu końcowego. Poniżej znajdują się identyfikatory URI, których można użyć do wykrywania anomalii. Zostaną one dołączone do punktu końcowego usługi później w celu utworzenia adresów URL żądań interfejsu API.

    |Metoda wykrywania  |ADRESU  |
    |---------|---------|
    |Wykrywanie partii    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Wykrywanie najnowszego punktu danych     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Utwórz funkcję do wysyłania żądań

1. Utwórz nową funkcję asynchroniczną o nazwie `Request`, która przyjmuje zmienne utworzone powyżej.

2. Ustaw informacje o protokole i nagłówku zabezpieczeń klienta przy użyciu obiektu `HttpClient`. Pamiętaj, aby dodać klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key`. Następnie Utwórz obiekt `StringContent` dla żądania.

3. Wyślij żądanie przy użyciu `PostAsync()`, a następnie Zwróć odpowiedź.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Wykryj anomalie jako partię

1. Utwórz nową funkcję o nazwie `detectAnomaliesBatch()`. Utwórz żądanie i wyślij je, wywołując funkcję `Request()` z punktem końcowym, kluczem subskrypcji, adresem URL na potrzeby wykrywania anomalii w usłudze Batch oraz danymi szeregów czasowych.

2. Deserializacji obiektu JSON i Zapisz go w konsoli.

3. Jeśli odpowiedź zawiera pole `code`, Wydrukuj kod błędu i komunikat o błędzie. 

4. W przeciwnym razie Znajdź pozycje anomalii w zestawie danych. Pole `isAnomaly` odpowiedzi zawiera tablicę wartości logicznych, z których każdy wskazuje, czy punkt danych jest anomalią. Przekonwertuj ten element na tablicę ciągów z funkcją `ToObject<bool[]>()` obiektu Response. Wykonaj iterację tablicy i wydrukuj indeks dowolnych wartości `true`. Te wartości odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

1. Utwórz nową funkcję o nazwie `detectAnomaliesLatest()`. Utwórz żądanie i wyślij je, wywołując funkcję `Request()` z punktem końcowym, kluczem subskrypcji, adresem URL dla ostatniego wykrywania anomalii w punkcie i danymi szeregów czasowych.

2. Deserializacji obiektu JSON i Zapisz go w konsoli.

[!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Załaduj dane szeregów czasowych i Wyślij żądanie

1. W metodzie Main aplikacji Załaduj dane szeregów czasowych JSON z `File.ReadAllText()`. 

2. Wywołaj utworzone powyżej funkcje wykrywania anomalii. Użyj `System.Console.ReadKey()`, aby zachować otwarte okno konsoli po uruchomieniu aplikacji.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Przykładowa odpowiedź

Pomyślna odpowiedź jest zwracana w formacie JSON. Kliknij poniższe linki, aby wyświetlić odpowiedź JSON w serwisie GitHub:
* [Przykładowa reakcja wykrywania partii](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Przykład ostatniej odpowiedzi wykrywania punktu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Wykrywanie anomalii przesyłania strumieniowego za pomocą Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* Co to jest [interfejs API wykrywania anomalii?](../overview.md)
* [Najlepsze rozwiązania](../concepts/anomaly-detection-best-practices.md) w przypadku korzystania z interfejsu API wykrywania anomalii.
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
