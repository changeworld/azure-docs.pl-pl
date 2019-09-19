---
title: 'Szybki start: Wykrywanie anomalii danych za pomocą biblioteki klienta wykrywania anomalii dla języka Python'
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API wykrywania anomalii w celu wykrycia nieprawidłowości w seriach danych jako partii lub przesyłania strumieniowego danych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/17/2019
ms.author: aahi
ms.openlocfilehash: 320c690eb873f760af89b7514893f14ecc209323
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106960"
---
# <a name="quickstart-anomaly-detector-client-library-for-nodejs"></a>Szybki start: Biblioteka klienta wykrywania anomalii dla środowiska Node. js

Rozpocznij pracę z biblioteką klienta wykrywania anomalii dla środowiska Node. js. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa wykrywania anomalii umożliwia znalezienie nieprawidłowych danych szeregów czasowych przez automatyczne użycie modeli najlepiej dopasowanej, niezależnie od wielkości branży, scenariusza lub ilości danych.

Użyj biblioteki klienta wykrywania anomalii dla środowiska Node. js, aby:

* Wykrywaj anomalie w zestawie danych szeregów czasowych jako żądanie wsadowe
* Wykrywanie stanu anomalii najnowszego punktu danych w szeregach czasowych

[](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest) | [Przykłady](https://github.com/Azure-Samples/anomalydetector) pakietu | [kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector)[](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector)źródłowego biblioteki dokumentacji referencyjnej (npm) | 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja środowiska [Node. js](https://nodejs.org/)

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-an-anomaly-detector-azure-resource"></a>Tworzenie zasobu platformy Azure wykrywania anomalii

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla detektora anomalii przy użyciu [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Uzyskaj [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez 7 dni bezpłatnie. Po utworzeniu rejestracji będzie on dostępny w [witrynie sieci Web systemu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Wyświetl zasób na [Azure Portal](https://portal.azure.com/).

Po otrzymaniu klucza z subskrypcji próbnej lub zasobu [Utwórz zmienną środowiskową](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza o nazwie `ANOMALY_DETECTOR_KEY`. Następnie Utwórz jeden dla punktu końcowego platformy Azure o `ANOMALY_DETECTOR_ENDPOINT`nazwie.
 
### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom polecenie, aby utworzyć aplikację Node `package.json` z plikiem. `npm init` 

```console
npm init
```

Utwórz plik o nazwie `index.js` i zaimportuj następujące biblioteki:

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Utwórz zmienne i klucz punktu końcowego platformy Azure dla zasobu. Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę, na których jest uruchomiona, aby uzyskać dostęp do zmiennej. Utwórz kolejną zmienną dla przykładowego pliku danych, który zostanie pobrany w późniejszym kroku. Następnie Utwórz obiekt ApiKeyCredentials, aby zawierał klucz.

[!code-javascript[Initial variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Zainstaluj pakiety `azure-cognitiveservices-anomalydetector`inpm. `ms-rest-azure` Biblioteka analizy woluminów CSV jest również używana w tym przewodniku szybki start:

```console
npm install  @azure/cognitiveservices-anomalydetector ms-rest-azure csv-parse
```

`package.json` Plik aplikacji zostanie zaktualizowany z zależnościami.

## <a name="object-model"></a>Model obiektów

Klient wykrywania anomalii jest obiektem [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) , który jest uwierzytelniany na platformie Azure przy użyciu klucza. Klient oferuje dwie metody wykrywania anomalii: W całym zestawie danych przy użyciu [entireDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--)i najnowszego punktu danych przy użyciu [LastDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-). 

Dane szeregów czasowych są wysyłane jako seria [punktów](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) w obiekcie [żądania](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest) . Obiekt zawiera właściwości opisujące dane (na przykład[stopień szczegółowości](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity) ) oraz parametry wykrywania anomalii. `Request` 

Odpowiedź wykrywania anomalii jest obiektem [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) lub [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) , w zależności od używanej metody. 

## <a name="code-examples"></a>Przykłady kodu 

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta wykrywania anomalii dla środowiska Node. js:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Załaduj zestaw danych szeregów czasowych z pliku](#load-time-series-data-from-a-file)
* [Wykrywaj anomalie w całym zestawie danych](#detect-anomalies-in-the-entire-data-set) 
* [Wykrywanie stanu anomalii najnowszego punktu danych](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

> [!NOTE]
> W tym przewodniku szybki start przyjęto założenie, że [utworzono zmienną środowiskową](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla `ANOMALY_DETECTOR_KEY`klucza wykrywania anomalii o nazwie.

Utwórz wystąpienie obiektu [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) z punktem końcowym i poświadczeniami.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Załaduj dane szeregów czasowych z pliku

Pobierz przykładowe dane dla tego przewodnika Szybki Start z witryny [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv):
1. W przeglądarce kliknij prawym przyciskiem myszy pozycję **RAW**.
2. Kliknij pozycję **Zapisz łącze jako**.
3. Zapisz plik w katalogu aplikacji jako plik CSV.

Te dane szeregów czasowych są formatowane jako plik CSV i wysyłane do interfejsu API wykrywania anomalii.

Odczytaj plik danych za pomocą `readFileSync()` metody biblioteki analizy woluminów CSV i Przeanalizuj plik za pomocą `parse()`programu. Dla każdego wiersza wypchnij obiekt [Point](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) zawierający sygnaturę czasową i wartość liczbową.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Wykrywaj anomalie w całym zestawie danych 

Wywołaj interfejs API w celu wykrycia anomalii przez całą serię czasową jako partię przy użyciu metody [entireDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) klienta. Zapisz zwrócony obiekt [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) . Wykonaj iterację na `isAnomaly` liście odpowiedzi i wydrukuj indeks wszelkich `true` wartości. Te wartości odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

Wywołaj interfejs API wykrywania anomalii, aby określić, czy najnowszy punkt danych jest anomalią przy użyciu metody [lastDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) klienta i przechowywanie zwróconego obiektu [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) . `isAnomaly` Wartość odpowiedzi jest wartością logiczną, która określa stan anomalii tego punktu.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node` polecenia w pliku szybkiego startu.

```console
node index.js
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Wykrywanie anomalii przesyłania strumieniowego za pomocą Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* Co to jest [interfejs API wykrywania anomalii?](../overview.md)
* [Najlepsze rozwiązania](../concepts/anomaly-detection-best-practices.md) w przypadku korzystania z interfejsu API wykrywania anomalii.
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
