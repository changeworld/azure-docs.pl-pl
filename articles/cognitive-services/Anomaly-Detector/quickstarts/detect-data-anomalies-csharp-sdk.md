---
title: 'Szybki Start: wykrywanie anomalii w danych szeregów czasowych przy użyciu biblioteki klienta wykrywania anomalii dla platformy .NET'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start pokazano, jak używać interfejsu API wykrywania anomalii w celu wykrywania niezależności w seriach danych jako partii lub przesyłania strumieniowego danych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: fdb35edc35e07ed4ee718281942565a8f1d061d4
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402692"
---
# <a name="quickstart-anomaly-detector-client-library-for-net"></a>Szybki Start: Biblioteka kliencka wykrywania anomalii dla platformy .NET

Rozpocznij pracę z biblioteką klienta wykrywania anomalii dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa wykrywania anomalii umożliwia znalezienie nieprawidłowych danych szeregów czasowych przez automatyczne użycie modeli najlepiej dopasowanej, niezależnie od wielkości branży, scenariusza lub ilości danych.

Użyj biblioteki klienta wykrywania anomalii dla platformy .NET, aby:

* Wykrywaj anomalie w zestawie danych szeregów czasowych jako żądanie wsadowe
* Wykrywanie stanu anomalii najnowszego punktu danych w szeregach czasowych

[Dokumentacja biblioteki referencyjnej](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [biblioteki kodu źródłowego](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [pakietu (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | [Znajdź kod w usłudze GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Klucz wykrywania anomalii i punkt końcowy

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-an-anomaly-detector-resource"></a>Tworzenie zasobu wykrywania anomalii

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-net-core-application"></a>Tworzenie nowej aplikacji platformy .NET Core

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj polecenia `dotnet new`, aby utworzyć nową aplikację konsolową o nazwie `anomaly-detector-quickstart`. To polecenie tworzy prosty projekt "Hello world" z pojedynczym C# plikiem źródłowym: *program.cs*. 

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

```dotnetcli
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

W katalogu aplikacji zainstaluj bibliotekę klienta wykrywania anomalii dla platformy .NET przy użyciu następującego polecenia:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

W katalogu projektu Otwórz plik *program.cs* i dodaj następujący `directives`:

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

W metodzie `main()` aplikacji Utwórz zmienne dla lokalizacji platformy Azure dla zasobu i klucz jako zmienną środowiskową. Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie załadować Edytor, środowisko IDE lub powłokę, aby uzyskać dostęp do zmiennej.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Model obiektów

Klient wykrywania anomalii jest obiektem [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) , który jest uwierzytelniany na platformie Azure za pomocą [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), który zawiera klucz. Klient oferuje dwie metody wykrywania anomalii: w całym zestawie danych przy użyciu [EntireDetectAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync)i najnowszego punktu danych przy użyciu [LastDetectAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). 

Dane szeregów czasowych są wysyłane jako serie [punktów](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) w obiekcie [żądania](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) . Obiekt `Request` zawiera właściwości opisujące dane (na przykład[stopień szczegółowości](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) ) oraz parametry wykrywania anomalii. 

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

Utwórz nową metodę odczytywania danych szeregu czasowego i Dodaj ją do obiektu [żądania](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) . Wywołaj `File.ReadAllLines()` ze ścieżką pliku i Utwórz listę obiektów [punktów](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) , a następnie wszystkie znaki nowego wiersza. Wyodrębnij wartości i oddziel dateStamp od wartości liczbowej, a następnie dodaj je do nowego obiektu `Point`. 

Utwórz obiekt `Request` z serią punktów i `Granularity.Daily` do [stopnia szczegółowości](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (lub okresowości) punktów danych.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Wykrywaj anomalie w całym zestawie danych 

Utwórz metodę wywołującą metodę [EntireDetectAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) klienta z obiektem `Request` i oczekującą na odpowiedź jako obiekt [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) . Jeśli serie czasowe zawierają jakiekolwiek anomalie, wykonaj iterację przez wartości [Isanomalii](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) odpowiedzi i wydrukuj wszystkie `true`. Te wartości odpowiadają indeksowi nietypowych punktów danych, jeśli zostały znalezione.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Wykrywanie stanu anomalii najnowszego punktu danych

Utwórz metodę wywołującą metodę [LastDetectAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) klienta z obiektem `Request` i oczekującą na odpowiedź jako obiekt [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) . Sprawdź atrybut [Isanomalia](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) odpowiedzi, aby określić, czy ostatni wysłany punkt danych był anomalią. 

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą polecenia `dotnet run` z katalogu aplikacji.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
