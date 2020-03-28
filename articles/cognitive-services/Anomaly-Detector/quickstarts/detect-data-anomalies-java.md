---
title: 'Szybki start: wykrywanie anomalii w danych szeregów czasowych przy użyciu interfejsu API REST detektora anomalii i oprogramowania Java'
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
ms.openlocfilehash: 4e7cc969ff2c36415b8fd1cebafa1873f9efd38c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239051"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Szybki start: wykrywanie anomalii w danych szeregów czasowych przy użyciu interfejsu API REST detektora anomalii i oprogramowania Java

Użyj tego przewodnika Szybki start, aby rozpocząć korzystanie z dwóch trybów wykrywania interfejsu API detektora anomalii do wykrywania anomalii w danych szeregów czasowych. Ta aplikacja Java wysyła dwa żądania interfejsu API zawierające dane szeregów czasowych w formacie JSON i pobiera odpowiedzi.

| Żądanie interfejsu API                                        | Dane wyjściowe aplikacji                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Wykrywanie anomalii jako partii                        | Odpowiedź JSON zawierająca stan anomalii (i inne dane) dla każdego punktu danych w danych szeregów czasowych i pozycje wykrytych anomalii. |
| Wykrywanie stanu anomalii najnowszego punktu danych | Odpowiedź JSON zawierająca stan anomalii (i inne dane) dla najnowszego punktu danych w danych szeregów czasowych.                                                                                                                                         |

 Chociaż ta aplikacja jest napisana w języku Java, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania. Kod źródłowy tego przewodnika Szybki start można znaleźć w [serwisie GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java).

## <a name="prerequisites"></a>Wymagania wstępne

- [Zestaw&trade; Java Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) lub nowszy.
- Klucz i punkt końcowy detektora anomalii
- Importowanie tych bibliotek z repozytorium Maven
    - [JSON w](https://mvnrepository.com/artifact/org.json/json) pakiecie Java
    - Pakiet [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Plik JSON zawierający punkty danych szeregów czasowych. Przykładowe dane dla tego przewodnika Szybki start można znaleźć w [usłudze GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Tworzenie zasobu detektora anomalii

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. Utwórz nowy projekt java i zaimportuj następujące biblioteki.
    
    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Tworzenie zmiennych dla klucza subskrypcji i punktu końcowego. Poniżej znajdują się identyfikatory URI, których można użyć do wykrywania anomalii. Zostaną one dołączone do punktu końcowego usługi później, aby utworzyć adresy URL żądań interfejsu API.

    |Metoda wykrywania  |Identyfikator URI  |
    |---------|---------|
    |Wykrywanie partii    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Wykrywanie w najnowszym punkcie danych     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Tworzenie funkcji do wysyłania żądań

1. Utwórz nową `sendRequest()` funkcję o nazwie, która przyjmuje zmienne utworzone powyżej. Następnie wykonaj poniższe czynności.

2. Utwórz `CloseableHttpClient` obiekt, który może wysyłać żądania do interfejsu API. Wyślij żądanie do `HttpPost` obiektu żądania, łącząc punkt końcowy i adres URL detektora anomalii.

3. Użyj `setHeader()` funkcji żądania, aby `Content-Type` ustawić `application/json`nagłówek na , i `Ocp-Apim-Subscription-Key` dodać klucz subskrypcji do nagłówka.

4. Użyj `setEntity()` funkcji żądania do danych, które mają być wysyłane.

5. Użyj `execute()` funkcji klienta, aby wysłać żądanie i `CloseableHttpResponse` zapisać je w obiekcie.

6. Utwórz `HttpEntity` obiekt do przechowywania zawartości odpowiedzi. Pobierz zawartość `getEntity()`z . Jeśli odpowiedź nie jest pusta, zwróć ją.

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Wykrywanie anomalii jako partii

1. Utwórz metodę `detectAnomaliesBatch()` wywoływaną do wykrywania anomalii w danych jako partii. Wywołanie `sendRequest()` metody utworzonej powyżej z punktu końcowego, adres URL, klucz subskrypcji i json danych. Pobierz wynik i wydrukuj go na konsoli.

2. Jeśli odpowiedź `code` zawiera pole, wydrukuj kod błędu i komunikat o błędzie.

3. W przeciwnym razie znajdź pozycje anomalii w zestawie danych. `isAnomaly` Pole odpowiedzi zawiera wartość logiczną odnoszącą się do tego, czy dany punkt danych jest anomalią. Pobierz tablicy JSON i iteracji za jej `true` pośrednictwem, drukowanie indeksu dowolnych wartości. Wartości te odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

Utwórz metodę `detectAnomaliesLatest()` wywoływaną w celu wykrycia stanu anomalii ostatniego punktu danych w zestawie danych. Wywołanie `sendRequest()` metody utworzonej powyżej z punktu końcowego, adres URL, klucz subskrypcji i json danych. Pobierz wynik i wydrukuj go na konsoli.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Załaduj dane szeregów czasowych i wyślij żądanie

1. W głównej metodzie aplikacji przeczytaj w pliku JSON zawierający dane, które zostaną dodane do żądań.

2. Wywołanie dwóch funkcji wykrywania anomalii utworzonych powyżej.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Przykładowa odpowiedź

Pomyślna odpowiedź jest zwracana w formacie JSON. Kliknij poniższe łącza, aby wyświetlić odpowiedź JSON w usłudze GitHub:
* [Przykładowa odpowiedź wykrywania partii](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Przykładowa najnowsza odpowiedź wykrywania punktów](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
