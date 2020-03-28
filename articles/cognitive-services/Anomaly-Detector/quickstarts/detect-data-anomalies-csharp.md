---
title: 'Szybki start: wykrywanie anomalii w danych szeregów czasowych przy użyciu interfejsu API REST detektora anomalii i C #'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API detektora anomalii do wykrywania nieprawidłowości w serii danych jako partii lub przesyłania strumieniowego danych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: b53fecad3655048a7b9d799134926b2730b16dae
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239108"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Szybki start: wykrywanie anomalii w danych szeregów czasowych przy użyciu interfejsu API REST detektora anomalii i C # 

Użyj tego przewodnika Szybki start, aby rozpocząć korzystanie z dwóch trybów wykrywania interfejsu API detektora anomalii do wykrywania anomalii w danych szeregów czasowych. Ta aplikacja języka C# wysyła dwa żądania interfejsu API zawierające dane szeregów czasowych w formacie JSON i pobiera odpowiedzi.

| Żądanie interfejsu API                                        | Dane wyjściowe aplikacji                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Wykrywanie anomalii jako partii                        | Odpowiedź JSON zawierająca stan anomalii (i inne dane) dla każdego punktu danych w danych szeregów czasowych i pozycje wykrytych anomalii. |
| Wykrywanie stanu anomalii najnowszego punktu danych | Odpowiedź JSON zawierająca stan anomalii (i inne dane) dla najnowszego punktu danych w danych szeregów czasowych.                                        |

 Chociaż ta aplikacja jest napisana w języku C#, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania. Kod źródłowy tego przewodnika Szybki start można znaleźć w [serwisie GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs).

## <a name="prerequisites"></a>Wymagania wstępne

- Dowolna wersja [programu Visual Studio 2017 lub nowsza](https://visualstudio.microsoft.com/downloads/),
- Klucz i punkt końcowy detektora anomalii
- Struktura [Json.NET](https://www.newtonsoft.com/json) dostępna jako pakiet NuGet. Aby zainstalować newtonsoft.Json jako pakiet NuGet w programie Visual Studio:
    
    1. Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań**.
    2. Wybierz pozycję **Zarządzaj pakietami NuGet**.
    3. Wyszukaj *plik Newtonsoft.Json* i zainstaluj pakiet.

- Jeśli używasz Linuksa /MacOS, ta aplikacja może być uruchamiana za pomocą [Mono](https://www.mono-project.com/).

- Plik JSON zawierający punkty danych szeregów czasowych. Przykładowe dane dla tego przewodnika Szybki start można znaleźć w [usłudze GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Tworzenie zasobu detektora anomalii

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. W programie Visual Studio utwórz nowe rozwiązanie konsoli i dodaj następujące pakiety. 

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Tworzenie zmiennych dla klucza subskrypcji i punktu końcowego. Poniżej znajdują się identyfikatory URI, których można użyć do wykrywania anomalii. Zostaną one dołączone do punktu końcowego usługi później, aby utworzyć adresy URL żądań interfejsu API.

    | Metoda wykrywania                   | Identyfikator URI                                              |
    |------------------------------------|--------------------------------------------------|
    | Wykrywanie partii                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | Wykrywanie w najnowszym punkcie danych | `/anomalydetector/v1.0/timeseries/last/detect`   |
        
    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Tworzenie funkcji do wysyłania żądań

1. Utwórz nową funkcję asynchronizową o nazwie, `Request` która przyjmuje zmienne utworzone powyżej.

2. Ustaw protokół zabezpieczeń klienta i informacje `HttpClient` nagłówka przy użyciu obiektu. Pamiętaj, aby dodać klucz `Ocp-Apim-Subscription-Key` subskrypcji do nagłówka. Następnie utwórz `StringContent` obiekt dla żądania.

3. Wyślij żądanie `PostAsync()`za pomocą , a następnie zwróć odpowiedź.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Wykrywanie anomalii jako partii

1. Utwórz nową `detectAnomaliesBatch()`funkcję o nazwie . Skonstruuj żądanie i `Request()` wyślij go, wywołując funkcję z punktem końcowym, kluczem subskrypcji, adresem URL wykrywania anomalii wsadowych i danymi szeregów czasowych.

2. Deserialize obiektu JSON i zapisać go do konsoli.

3. Jeśli odpowiedź `code` zawiera pole, wydrukuj kod błędu i komunikat o błędzie. 

4. W przeciwnym razie znajdź pozycje anomalii w zestawie danych. `isAnomaly` Pole odpowiedzi zawiera tablicę wartości logicznych, z których każda wskazuje, czy punkt danych jest anomalią. Przekonwertuj to na tablicę `ToObject<bool[]>()` ciągów z funkcją obiektu odpowiedzi. Iteracji za pośrednictwem tablicy i `true` wydrukować indeks wszystkich wartości. Wartości te odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

1. Utwórz nową `detectAnomaliesLatest()`funkcję o nazwie . Skonstruuj żądanie i `Request()` wyślij go, wywołując funkcję z punktem końcowym, kluczem subskrypcji, adresem URL dla najnowszego wykrywania anomalii punktów i danymi szeregów czasowych.

2. Deserialize obiektu JSON i zapisać go do konsoli.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]
 
## <a name="load-your-time-series-data-and-send-the-request"></a>Załaduj dane szeregów czasowych i wyślij żądanie

1. W głównej metodzie aplikacji załaduj dane `File.ReadAllText()`szeregów czasowych JSON za pomocą pliku . 

2. Wywołanie funkcji wykrywania anomalii utworzonych powyżej. Służy `System.Console.ReadKey()` do utrzymywanie otwartego okna konsoli po uruchomieniu aplikacji.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Przykładowa odpowiedź

Pomyślna odpowiedź jest zwracana w formacie JSON. Kliknij poniższe łącza, aby wyświetlić odpowiedź JSON w usłudze GitHub:
* [Przykładowa odpowiedź wykrywania partii](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Przykładowa najnowsza odpowiedź wykrywania punktów](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
