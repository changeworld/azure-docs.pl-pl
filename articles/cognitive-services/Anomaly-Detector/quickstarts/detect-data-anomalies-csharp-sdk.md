---
title: 'Szybki start: Wykrywanie anomalii w danych szeregów czasowych dla platformy .NET przy użyciu zestawu SDK wykrywanie anomalii'
titleSuffix: Azure Cognitive Services
description: Rozpocznij, wykrywanie anomalii w danych szeregów czasowych za pomocą usługi Wykrywanie anomalii.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: aahi
ms.openlocfilehash: a75196e035585a7501cdd842fb5b80ceff424dcc
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721578"
---
# <a name="quickstart-anomaly-detector-client-library-for-net"></a>Szybki start: Biblioteka kliencka wykrywanie anomalii dla platformy .NET

Rozpoczynanie pracy za pomocą biblioteki klienckiej wykrywanie anomalii dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbuj przykładowy kod dla podstawowych zadań. Wykrywanie anomalii usługi umożliwia znajdowania nieprawidłowości w danych szeregów czasowych, automatycznie przy użyciu najlepszego dopasowania modeli, niezależnie od branży, scenariusz i ilości danych.

Używanie biblioteki klienckiej wykrywanie anomalii dla platformy .NET do:

* Wykrywaj anomalie jako zadania wsadowego
* Wykrywanie anomalii stan najnowszego punktu danych

[Dokumentacja interfejsu API](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [pakietów (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | [przykłady](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja Azure — [utworzyć jedno za darmo](https://azure.microsoft.com/free/)
* Bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-an-anomaly-detector-resource"></a>Utwórz zasób usługi Wykrywanie anomalii

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-c-app"></a>Utwórz nową C# aplikacji

Utwórz nową aplikację platformy .NET Core w preferowanego edytora lub w środowisku IDE. 

W oknie konsoli (np. cmd, PowerShell lub Bash), za pomocą polecenia dotnet `new` polecenie, aby utworzyć nową aplikację konsoli o nazwie `anomaly-detector-quickstart`. To polecenie umożliwia utworzenie prostego "Hello World" C# projektu z jednym pliku źródłowym: **Program.cs**. 

```console
dotnet new console -n anomaly-detector-quickstart
```

Zmień katalog na folder nowo utworzoną aplikację. Można tworzyć aplikację za pomocą:

```console
dotnet build
```

Dane wyjściowe kompilacji powinien zawierać żadnych ostrzeżeń ani błędów. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Zainstalować bibliotekę klienta

W ramach katalogu aplikacji należy zainstalować bibliotekę klienta wykrywanie anomalii dla platformy .NET za pomocą następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

Jeśli używasz programu Visual Studio IDE z biblioteki klienta jest dostępna jako pakiet NuGet. 

## <a name="object-model"></a>Model obiektów

Klient wykrywanie anomalii jest [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) obiektu, który przeprowadza uwierzytelnianie na platformie Azure przy użyciu [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), który zawiera klucz. Klient udostępnia dwie metody wykrywania anomalii: W całym zestawie danych za pomocą [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync), a na najnowsze dane punktu, przy użyciu [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). 

Dane szeregów czasowych są wysyłane w postaci serii obiektów [punktów](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) w [żądania](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) obiektu. `Request` Obiekt zawiera właściwości do opisu danych ([stopień szczegółowości](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) na przykład) i parametry w celu wykrywania anomalii. 

Odpowiedź na wykrywanie anomalii jest albo [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) lub [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) obiektu, w zależności od metody używanej. 

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienckiej wykrywanie anomalii dla platformy .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Ładowanie zestawu danych serii czasu z pliku](#load-time-series-data-from-a-file)
* [Wykrywanie anomalii w całego zestawu danych](#detect-anomalies-in-the-entire-data-set) 
* [Wykrywanie anomalii stan najnowszego punktu danych](#detect-the-anomaly-status-of-the-latest-data-point)

### <a name="add-the-main-method"></a>Dodaj metodę głównego

Z katalogu projektu:

1. Otwórz plik Program.cs w preferowanego edytora lub w środowisku IDE
2. Dodaj następujący kod `using` dyrektywy

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

> [!NOTE]
> Ten przewodnik Szybki Start przyjęto użytkownik [utworzyć zmienną środowiskową](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) związane z kluczem wykrywanie anomalii o nazwie `ANOMALY_DETECTOR_KEY`.

Przy stosowaniu `main()` metody tworzenia zmiennych dla lokalizacji platformy Azure z Twojego zasobu i klucz jako zmienną środowiskową. Jeśli zmienna środowiskowa jest utworzony po uruchomieniu aplikacji, edytor, środowiska IDE lub powłoki, na których jest on uruchomiony musisz zamknąć i ponownie załadować uzyskiwania dostępu do zmiennej.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

### <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

W nowej metodzie należy utworzyć wystąpienie klienta z punktu końcowego i klucz. Tworzenie [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) obiektu za pomocą klucza usługi i korzystać z punktem końcowym usługi, aby utworzyć [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview) obiektu. 

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]
    
### <a name="load-time-series-data-from-a-file"></a>Ładowanie danych szeregów czasowych z pliku

Pobierz przykładowe dane dla tego przewodnika Szybki Start z [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. W przeglądarce, kliknij prawym przyciskiem myszy **Raw**
2. Kliknij przycisk **łącze Zapisz jako**
3. Zapisz plik do katalogu aplikacji, w formacie pliku CSV.

Ta danych szeregów czasowych są sformatowane jako plik CSV i zostanie wysłane do interfejsu API wykrywanie anomalii.

Utwórz nową metodę do odczytu w danych szeregów czasowych i dodać go do [żądania](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) obiektu. Wywołaj `File.ReadAllLines()` ze ścieżką pliku i tworzenie listy [punktu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) obiektów i Usuń wszystkie nowe wiersz znaków. Wyodrębnij wartości oddzielić datestamp od wartości liczbowych i dodać je do nowego `Point` obiektu. 

Wprowadź `Request` obiektu z serii punktów, i `Granularity.Daily` dla [stopień szczegółowości](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (lub okresowości) punktów danych.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>Wykrywanie anomalii w całego zestawu danych 

Utwórz metodę do wywołania klienta [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) metody z `Request` obiektu i poczekać na odpowiedź jako [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) obiektu. Jeśli szeregów czasowych zawiera wszelkie anomalie, iterację odpowiedzi [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) wartości i drukowania, wszelkie będące `true`. Te wartości odpowiadają indeks punktów danych nietypowe, jeśli którekolwiek.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie anomalii stan najnowszego punktu danych

Utwórz metodę do wywołania klienta [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) metody z `Request` obiektu i poczekać na odpowiedź jako [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) obiektu. Sprawdź odpowiedzi [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) atrybutu, aby określić, jeśli najnowszego punktu danych wysyłane było anomalii. 

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą polecenia dotnet `run` polecenie z katalogu aplikacji.

```dotnet
dotnet run
```

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
