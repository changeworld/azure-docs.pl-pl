---
title: 'Szybki start: Wykrywaj anomalie danych przy użyciu biblioteki wykrywanie anomalii i Python'
titleSuffix: Azure Cognitive Services
description: Wykrywanie nieprawidłowości w serii danych, jako partii albo na strumieniu danych za pomocą interfejsu API wykrywanie anomalii.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: aahi
ms.openlocfilehash: 9176ab84dd3f493604bd655e0498f5ad476776d0
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721516"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>Szybki start: Biblioteka kliencka wykrywanie anomalii dla języka Python

Rozpoczynanie pracy za pomocą biblioteki klienckiej wykrywanie anomalii dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbuj przykładowy kod dla podstawowych zadań. Wykrywanie anomalii usługi umożliwia znajdowania nieprawidłowości w danych szeregów czasowych, automatycznie przy użyciu najlepszego dopasowania modeli, niezależnie od branży, scenariusz i ilości danych.

## <a name="key-concepts"></a>Kluczowe pojęcia

Używanie biblioteki klienckiej wykrywanie anomalii dla języka Python:

* Wykrywaj anomalie w całym zestawie danych serii czasu, co żądanie wsadowe
* Wykrywanie anomalii stan najnowszego punktu danych w serii czasu

[Dokumentacja biblioteki](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [pakietu (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [przykłady](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja Azure — [utworzyć jedno za darmo](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* [Biblioteki analiz danych Pandas](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Konfigurowanie

### <a name="create-an-anomaly-detector-resource"></a>Utwórz zasób usługi Wykrywanie anomalii

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="install-the-client-library"></a>Zainstalować bibliotekę klienta

Po zainstalowaniu języka Python, możesz zainstalować biblioteki klienckie za pomocą:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Model obiektów

Klient wykrywanie anomalii jest [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) obiektu, który przeprowadza uwierzytelnianie na platformie Azure przy użyciu klucza. Klient udostępnia dwie metody wykrywania anomalii: W całym zestawie danych za pomocą [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-), a na najnowsze dane punktu, przy użyciu [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Dane szeregów czasowych są wysyłane w postaci serii obiektów [punktów](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) w [żądania](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) obiektu. `Request` Obiekt zawiera właściwości do opisu danych ([stopień szczegółowości](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) na przykład) i parametry w celu wykrywania anomalii. 

Wykrywanie anomalii odpowiedź jest [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) lub [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) obiekt, w zależności od metody używanej. 

## <a name="getting-started"></a>Wprowadzenie

Utwórz nową aplikację języka Python w preferowanego edytora lub w środowisku IDE. Następnie dodaj następujące deklaracje import do pliku. 

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

> [!NOTE]
> Ten przewodnik Szybki Start przyjęto użytkownik [utworzyć zmienną środowiskową](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) związane z kluczem wykrywanie anomalii o nazwie `ANOMALY_DETECTOR_KEY`.

Utwórz zmienne dla klucza usługi jako zmienną środowiskową, ścieżka do pliku danych serii czasu i lokalizacja platformy azure Twojej subskrypcji. Na przykład `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

## <a name="code-examples"></a>Przykłady kodu 

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienckiej wykrywanie anomalii dla platformy .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Ładowanie zestawu danych serii czasu z pliku](#load-time-series-data-from-a-file)
* [Wykrywanie anomalii w całego zestawu danych](#detect-anomalies-in-the-entire-data-set) 
* [Wykrywanie anomalii stan najnowszego punktu danych](#detect-the-anomaly-status-of-the-latest-data-point)

### <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Dodawanie zmiennej lokalizacji platformy azure do punktu końcowego i uwierzytelnianie klienta przy użyciu klucza.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

### <a name="load-time-series-data-from-a-file"></a>Ładowanie danych szeregów czasowych z pliku

Pobierz przykładowe dane dla tego przewodnika Szybki Start z [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. W przeglądarce, kliknij prawym przyciskiem myszy **Raw**
2. Kliknij przycisk **łącze Zapisz jako**
3. Zapisz plik do katalogu aplikacji, w formacie pliku CSV.

Ta danych szeregów czasowych są sformatowane jako plik CSV i zostanie wysłane do interfejsu API wykrywanie anomalii.

Załaduj plik danych z biblioteki Pandas `read_csv()` metoda i Utwórz zmienną pustej listy do przechowywania serii danych. Iterowanie za pomocą pliku i dołączyć dane jako [punktu](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) obiektu. Ten obiekt będzie zawierać sygnatur czasowych i wartości liczbowej z wierszy w pliku danych CSV. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Tworzenie [żądania](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) obiektu za pomocą usługi szeregów czasowych i [stopień szczegółowości](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (lub okresowości) z punktów danych. Na przykład `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>Wykrywanie anomalii w całego zestawu danych 

Wywoływanie interfejsu API wykrywania anomalii za pomocą danych serii cały czas, korzystając z klienta [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) metody. Store zwracanego [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) obiektu. Iteracyjne przeglądanie odpowiedzi `is_anomaly` listy i drukowanie indeksu dowolnego `true` wartości. Te wartości odpowiadają indeks punktów danych nietypowe, jeśli którekolwiek.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie anomalii stan najnowszego punktu danych

Wywoływanie interfejsu API wykrywanie anomalii w celu ustalenia, czy najnowsze punktu danych jest anomalii przy użyciu klienta programu [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) metoda i magazynu zwracanego [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) obiektu. Odpowiedź `is_anomaly` wartość jest wartością logiczną, która określa stan anomalii tego punktu.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację w środowisku IDE lub w wierszu polecenia za pomocą `python` polecenia i nazwę pliku.
 
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupa zasobów. Usunięcie grupy zasobów powoduje również usunięcie wszystkich innych zasobów skojarzonych z grupą zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Można również uruchomić następujące polecenia powłoki cloud, aby usunąć grupę zasobów i skojarzone z nią zasoby. Może to potrwać kilka minut. 

```azurecli-interactive
az group delete --name example-anomaly-detector-resource-group
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Przesyłanie strumieniowe wykrywanie anomalii przy użyciu usługi Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* Co to jest [interfejsu API wykrywanie anomalii?](../overview.md)
* [Najlepsze praktyki](../concepts/anomaly-detection-best-practices.md) podczas korzystania z interfejsu API wykrywanie anomalii.
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
