---
title: 'Szybki Start: wykrywanie anomalii danych za pomocą biblioteki klienta wykrywania anomalii dla języka Python'
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API wykrywania anomalii w celu wykrycia nieprawidłowości w seriach danych jako partii lub przesyłania strumieniowego danych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: 084ef1b81f7db5bd4a2d371e0c322211c0f0e142
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483501"
---
# <a name="quickstart-anomaly-detector-client-library-for-nodejs"></a>Szybki Start: Biblioteka kliencka wykrywania anomalii dla środowiska Node. js

Rozpocznij pracę z biblioteką klienta wykrywania anomalii dla środowiska Node. js. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa wykrywania anomalii umożliwia znalezienie nieprawidłowych danych szeregów czasowych przez automatyczne użycie modeli najlepiej dopasowanej, niezależnie od wielkości branży, scenariusza lub ilości danych.

Użyj biblioteki klienta wykrywania anomalii dla środowiska Node. js, aby:

* Wykrywaj anomalie w zestawie danych szeregów czasowych jako żądanie wsadowe
* Wykrywanie stanu anomalii najnowszego punktu danych w szeregach czasowych

[Dokumentacja referencyjna](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest) |  | pakietu [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) [(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector) | [Znajdź kod w usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja środowiska [Node. js](https://nodejs.org/)
* Klucz wykrywania anomalii i punkt końcowy

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-an-anomaly-detector-azure-resource"></a>Tworzenie zasobu platformy Azure wykrywania anomalii

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom `npm init` polecenie, aby utworzyć aplikację Node przy użyciu pliku `package.json`. 

```console
npm init
```

Utwórz plik o nazwie `index.js` i zaimportuj następujące biblioteki:

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Utwórz zmienne i klucz punktu końcowego platformy Azure dla zasobu. Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę, na których jest uruchomiona, aby uzyskać dostęp do zmiennej. Utwórz kolejną zmienną dla przykładowego pliku danych, który zostanie pobrany w późniejszym kroku, i pustą listę punktów danych. Następnie Utwórz obiekt `ApiKeyCredentials`, aby zawierał klucz.

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Zainstaluj `ms-rest-azure` i `azure-cognitiveservices-anomalydetector` pakiety NPM. Biblioteka analizy woluminów CSV jest również używana w tym przewodniku szybki start:

```console
npm install  @azure/cognitiveservices-anomalydetector ms-rest-azure csv-parse
```

Plik `package.json` aplikacji zostanie zaktualizowany przy użyciu zależności.

## <a name="object-model"></a>Model obiektów

Klient wykrywania anomalii jest obiektem [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) , który jest uwierzytelniany na platformie Azure przy użyciu klucza. Klient oferuje dwie metody wykrywania anomalii: w całym zestawie danych przy użyciu [entireDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--)i najnowszego punktu danych przy użyciu [LastDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-). 

Dane szeregów czasowych są wysyłane jako seria [punktów](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) w obiekcie [żądania](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest) . Obiekt `Request` zawiera właściwości opisujące dane (na przykład[stopień szczegółowości](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity) ) oraz parametry wykrywania anomalii. 

Odpowiedź wykrywania anomalii jest obiektem [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) lub [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) , w zależności od używanej metody. 

## <a name="code-examples"></a>Przykłady kodu 

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta wykrywania anomalii dla środowiska Node. js:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Załaduj zestaw danych szeregów czasowych z pliku](#load-time-series-data-from-a-file)
* [Wykrywaj anomalie w całym zestawie danych](#detect-anomalies-in-the-entire-data-set) 
* [Wykrywanie stanu anomalii najnowszego punktu danych](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz wystąpienie obiektu [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) z punktem końcowym i poświadczeniami.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Załaduj dane szeregów czasowych z pliku

Pobierz przykładowe dane dla tego przewodnika Szybki Start z witryny [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv):
1. W przeglądarce kliknij prawym przyciskiem myszy pozycję **RAW**.
2. Kliknij pozycję **Zapisz łącze jako**.
3. Zapisz plik w katalogu aplikacji jako plik CSV.

Te dane szeregów czasowych są formatowane jako plik CSV i wysyłane do interfejsu API wykrywania anomalii.

Odczytaj plik danych za pomocą metody `readFileSync()` biblioteki analizy woluminów CSV i Przeanalizuj plik przy użyciu `parse()`. Dla każdego wiersza wypchnij obiekt [Point](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) zawierający sygnaturę czasową i wartość liczbową.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Wykrywaj anomalie w całym zestawie danych 

Wywołaj interfejs API w celu wykrycia anomalii przez całą serię czasową jako partię przy użyciu metody [entireDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) klienta. Zapisz zwrócony obiekt [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) . Wykonaj iterację na liście `isAnomaly` odpowiedzi i wydrukuj indeks wszelkich wartości `true`. Te wartości odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

Wywołaj interfejs API wykrywania anomalii, aby określić, czy najnowszy punkt danych jest anomalią przy użyciu metody [lastDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) klienta i przechowywanie zwróconego obiektu [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) . Wartość `isAnomaly` odpowiedzi jest wartością logiczną, która określa stan anomalii tego punktu.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą polecenia `node` w pliku szybkiego startu.

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
