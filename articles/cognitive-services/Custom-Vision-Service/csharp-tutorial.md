---
title: 'Szybki start: tworzenie projektu klasyfikacji obrazów przy użyciu zestawu Custom Vision SDK dla języka C#'
titleSuffix: Azure Cognitive Services
description: Utwórz projekt, dodaj tagi, przekaż obrazy, naucz projekt i wykonaj przewidywanie przy użyciu zestawu .NET SDK i języka C#.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: anroth
ms.openlocfilehash: 7faad2c432e15ed363bd1caf290e03dc75e9d298
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141062"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>Szybki start: tworzenie projektu klasyfikacji obrazów przy użyciu zestawu Custom Vision .NET SDK

Ten artykuł zawiera informacje i przykładowy kod, dzięki którym można łatwiej rozpocząć tworzenie modelu klasyfikacji obrazów za pomocą zestawu Custom Vision SDK i języka C#. Po jej utworzeniu możesz dodać tagi, przesłać obrazy, wyszkolić projekt, uzyskać adres URL punktu końcowego domyślnego przewidywania projektu i użyć punktu końcowego do programowego przetestowania obrazu. Użyj tego przykładu jako szablonu do utworzenia własnej aplikacji platformy .NET. Jeśli chcesz przejść przez proces tworzenia i używania modelu klasyfikacji _bez_ kodu, zobacz zamiast tego [wskazówki dotyczące przeglądarki](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/)

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Pobieranie zestawu Custom Vision SDK i przykładowego kodu

W celu napisania aplikacji .NET używającej usługi Custom Vision będą potrzebne pakiety NuGet usługi Custom Vision. Te pakiety są zawarte w przykładowym projekcie, który zostanie pobrany, ale możesz uzyskać do nich dostęp osobno.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Sklonuj lub pobierz projekt [Cognitive Services .NET Samples (Przykłady dotyczące platformy .NET dla usług Cognitive Services)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Przejdź do folderu **CustomVision/ImageClassification** i otwórz plik _ImageClassification.csproj_ w programie Visual Studio.

Ten projekt programu Visual Studio tworzy nowy projekt Custom Vision o nazwie __My New Project__, który będzie dostępny za pośrednictwem [witryny internetowej Custom Vision](https://customvision.ai/). Następnie zostaną przesłane obrazy do szkolenia i testowania klasyfikatora. W tym projekcie klasyfikator jest używany do ustalania, czy drzewo jest __choiną__ (Hemlock), czy __wiśnią japońską__ (Japanese Cherry).

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Zrozumienie kodu

Otwórz plik _Program.cs_ i przejrzyj kod. Wstaw klucze subskrypcji w odpowiednich definicjach w metodzie **Main**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=21-30)]

Parametr Endpoint powinien wskazywać region, w którym utworzono grupę zasobów platformy Azure zawierającą odpowiednie zasoby usługi Custom Vision. Na potrzeby tego przykładu przyjmujemy region Południowo-środkowe stany USA i używasz:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

Następujące wiersze kodu wykonują podstawową funkcjonalność projektu.

### <a name="create-a-new-custom-vision-service-project"></a>Tworzenie nowego projektu usługi Custom Vision

Utworzony projekt będzie widoczny w odwiedzonej wcześniej [witrynie internetowej Custom Vision](https://customvision.ai/). Aby określić inne opcje podczas tworzenia projektu (wyjaśnione w przewodniku tworzenia portalu sieci Web [klasyfikatora](getting-started-build-a-classifier.md) ), zobacz metodę "setproject". [](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__)   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=32-34)]

### <a name="create-tags-in-the-project"></a>Tworzenie tagów w projekcie

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=36-38)]

### <a name="upload-and-tag-images"></a>Przekazywanie i tagowanie obrazów

Obrazy dla tego projektu są dołączone. Są one przywoływane w metodzie **LoadImagesFromDisk** w pliku _Program.cs_. Można przekazać do 64 obrazów w pojedynczej partii.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=40-55)]

### <a name="train-the-classifier-and-publish"></a>Uczenie klasyfikatora i publikowanie

Ten kod tworzy pierwszą iterację w projekcie, a następnie publikuje tę iterację w punkcie końcowym przewidywania. Nazwa nadana do publikowanej iteracji może służyć do wysyłania żądań przewidywania. Iteracja nie jest dostępna w punkcie końcowym przewidywania do momentu opublikowania.

```csharp
var iteration = trainingApi.TrainProject(project.Id);
// The returned iteration will be in progress, and can be queried periodically to see when it has completed
while (iteration.Status == "Training")
{
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
}

// The iteration is now trained. Publish it to the prediction end point.
var publishedModelName = "treeClassModel";
var predictionResourceId = "<target prediction resource ID>";
trainingApi.PublishIteration(project.Id, iteration.Id, publishedModelName, predictionResourceId);
Console.WriteLine("Done!\n");
```

### <a name="set-the-prediction-endpoint"></a>Ustawianie punktu końcowego przewidywania

Punkt końcowy przewidywania jest odwołaniem umożliwiającym przesłanie obrazu do bieżącego modelu i uzyskanie przewidywania klasyfikacji.

```csharp
// Create a prediction endpoint, passing in obtained prediction key
CustomVisionPredictionClient endpoint = new CustomVisionPredictionClient()
{
        ApiKey = predictionKey,
        Endpoint = SouthCentralUsEndpoint
};
```

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Przesyłanie obrazu do domyślnego punktu końcowego przewidywania

W tym skrypcie obraz testowy jest ładowany w metodzie **LoadImagesFromDisk**, a dane wyjściowe przewidywania modelu są wyświetlane w konsoli. Wartość zmiennej publishedModelName powinna odpowiadać wartości "opublikowany jako" znajdującej się na karcie **wydajność** portalu Custom Vision. 

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var result = endpoint.ClassifyImage(project.Id, publishedModelName, testImage);

// Loop over each prediction and write out the results
foreach (var c in result.Predictions)
{
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
}
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Po uruchomieniu aplikacja powinna otworzyć okno konsoli i zapisać w nim następujące dane wyjściowe:

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Następnie możesz sprawdzić, czy obraz testowy (znajdujący się w folderze **Images/Test/** ) został odpowiednio otagowany. Naciśnij dowolny klawisz, aby zakończyć działanie aplikacji. Możesz też wrócić do [witryny internetowej Custom Vision](https://customvision.ai) i wyświetlić bieżący stan nowo utworzonego projektu.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz wiesz już, jak wykonać każdy krok procesu klasyfikacji obrazów przy użyciu kodu. W tym przykładzie jest wykonywana jedna iteracja szkolenia, ale często trzeba szkolić i testować model wiele razy, aby zwiększyć jego dokładność.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](test-your-model.md)
