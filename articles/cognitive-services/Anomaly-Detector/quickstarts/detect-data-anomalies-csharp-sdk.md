---
title: 'Szybki start: Wykrywanie anomalii w danych szeregów czasowych przy użyciu biblioteki klienta wykrywania anomalii dla platformy .NET'
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API wykrywania anomalii w celu wykrycia nieprawidłowości w seriach danych jako partii lub przesyłania strumieniowego danych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 08/01/2019
ms.author: aahi
ms.openlocfilehash: c1dd5e4f469b24918eaa03e694a95fa90c91b481
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725571"
---
# <a name="quickstart-anomaly-detector-client-library-for-net"></a>Szybki start: Biblioteka klienta wykrywania anomalii dla platformy .NET

Rozpocznij pracę z biblioteką klienta wykrywania anomalii dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa wykrywania anomalii umożliwia znalezienie nieprawidłowych danych szeregów czasowych przez automatyczne użycie modeli najlepiej dopasowanej, niezależnie od wielkości branży, scenariusza lub ilości danych.

Użyj biblioteki klienta wykrywania anomalii dla platformy .NET, aby:

* Wykrywaj anomalie w zestawie danych szeregów czasowych jako żądanie wsadowe
* Wykrywanie stanu anomalii najnowszego punktu danych w szeregach czasowych

[](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [Przykłady](https://github.com/Azure-Samples/anomalydetector) pakietu | [Code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector)[](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/)Source Library (NuGet) biblioteki dokumentacji dotyczącej biblioteki | 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-an-anomaly-detector-resource"></a>Tworzenie zasobu wykrywania anomalii

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

Po otrzymaniu klucza z subskrypcji próbnej lub zasobu [Utwórz zmienną środowiskową](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) dla klucza o nazwie `ANOMALY_DETECTOR_KEY`.

### <a name="create-a-new-c-application"></a>Utwórz nową C# aplikację

Utwórz nową aplikację platformy .NET Core w preferowanym edytorze lub środowisku IDE. 

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj polecenia dotnet `new` , aby utworzyć nową aplikację konsolową o nazwie. `anomaly-detector-quickstart` To polecenie tworzy prosty projekt "Hello world" C# z pojedynczym plikiem źródłowym: *Program.cs*. 

```console
dotnet new console -n anomaly-detector-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

W katalogu projektu Otwórz plik *program.cs* w preferowanym edytorze lub w środowisku IDE. Dodaj następujące elementy przy `directives`użyciu:

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

W `main()` metodzie aplikacji Utwórz zmienne dla lokalizacji platformy Azure dla zasobu i klucz jako zmienną środowiskową. Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie załadować Edytor, środowisko IDE lub powłokę, aby uzyskać dostęp do zmiennej.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

W katalogu aplikacji zainstaluj bibliotekę klienta wykrywania anomalii dla platformy .NET przy użyciu następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

Jeśli używasz środowiska IDE programu Visual Studio, Biblioteka kliencka jest dostępna jako pakiet NuGet. 

## <a name="object-model"></a>Model obiektów

Klient wykrywania anomalii jest obiektem [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) , który jest uwierzytelniany na platformie Azure za pomocą [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), który zawiera klucz. Klient oferuje dwie metody wykrywania anomalii: W całym zestawie danych przy użyciu [EntireDetectAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync)i najnowszego punktu danych przy użyciu [LastDetectAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). 

Dane szeregów czasowych są wysyłane jako serie [punktów](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) w obiekcie [żądania](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) . Obiekt zawiera właściwości opisujące dane (na przykład[stopień szczegółowości](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) ) oraz parametry wykrywania anomalii. `Request` 

Odpowiedź wykrywania anomalii jest obiektem [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) lub [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) , w zależności od używanej metody. 

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta wykrywania anomalii dla platformy .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Załaduj zestaw danych szeregów czasowych z pliku](#load-time-series-data-from-a-file)
* [Wykrywaj anomalie w całym zestawie danych](#detect-anomalies-in-the-entire-data-set) 
* [Wykrywanie stanu anomalii najnowszego punktu danych](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

W nowej metodzie Utwórz wystąpienie klienta z punktem końcowym i kluczem. Utwórz obiekt [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview) . 

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]
    
## <a name="load-time-series-data-from-a-file"></a>Załaduj dane szeregów czasowych z pliku

Pobierz przykładowe dane dla tego przewodnika Szybki Start z witryny [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. W przeglądarce kliknij prawym przyciskiem myszy pozycję **RAW**.
2. Kliknij pozycję **Zapisz łącze jako**.
3. Zapisz plik w katalogu aplikacji jako plik CSV.

Te dane szeregów czasowych są formatowane jako plik CSV i wysyłane do interfejsu API wykrywania anomalii.

Utwórz nową metodę odczytywania danych szeregu czasowego i Dodaj ją do obiektu [żądania](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) . Połącz `File.ReadAllLines()` się z ścieżką pliku i Utwórz listę obiektów [punktu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) , a następnie wszystkie znaki nowego wiersza. Wyodrębnij wartości i oddziel dateStamp od wartości liczbowej, a następnie dodaj je do nowego `Point` obiektu. 

Utwórz obiekt z serią punktów oraz `Granularity.Daily` [stopień szczegółowości](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (lub okresowość) punktów danych. `Request`

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Wykrywaj anomalie w całym zestawie danych 

Utwórz metodę wywołującą metodę [EntireDetectAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) klienta z `Request` obiektem i oczekującą na odpowiedź jako obiekt [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) . Jeśli szeregi czasowe zawierają jakiekolwiek anomalie, wykonaj iterację przez [](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) wartości isanomalii odpowiedzi i wydrukuj wszystkie te `true`dane. Te wartości odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

Utwórz metodę wywołującą metodę [LastDetectAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) klienta z `Request` obiektem i oczekującą na odpowiedź jako obiekt [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) . Sprawdź atrybut isanomalia odpowiedzi, aby określić, czy ostatni wysłany punkt danych był anomalią. [](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) 

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą polecenia dotnet `run` z katalogu aplikacji.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich innych zasobów skojarzonych z tą grupą zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Możesz również uruchomić następujące polecenie usługi Cloud Shell, aby usunąć grupę zasobów i skojarzone z nią zasoby. Może to potrwać kilka minut. 

```azurecli-interactive
az group delete --name example-anomaly-detector-resource-group
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
>[Wykrywanie anomalii przesyłania strumieniowego za pomocą Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* Co to jest [interfejs API wykrywania anomalii?](../overview.md)
* [Najlepsze rozwiązania](../concepts/anomaly-detection-best-practices.md) w przypadku korzystania z interfejsu API wykrywania anomalii.
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
