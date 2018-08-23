---
title: Użyj usługi Custom Vision Service z aplikacji C# — usług Azure Cognitive Services | Dokumentacja firmy Microsoft
description: Poznaj podstawowe języka C# aplikacją, która używa niestandardowego interfejsu API przetwarzania w usługach Microsoft Cognitive Services. Utwórz projekt, dodać tagi, przekazywać obrazy, szkolenie projektu i przewiduje przy użyciu domyślnego punktu końcowego.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: d3c2ffb0fd9578458bd07241eed4a87cf70d3c3c
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617438"
---
# <a name="use-the-custom-vision-service-from-a-c35-application"></a>Użyj usługi Custom Vision Service from C&#35; aplikacji

Dowiedz się, jak używać usługi Custom Vision Service z aplikacją C#. Po jego utworzeniu możesz można dodać tagi, przekazywać obrazy, szkolenie projektu, projektu domyślne prognozowania — adres URL punktu końcowego uzyskać i używać punktu końcowego programowo testować obrazu. Użyj w tym przykładzie typu open-source jako szablon do tworzenia własnych aplikacji dla Windows za pomocą interfejsu API usługi Custom Vision.

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolnej wersji programu Visual Studio 2017 for Windows.

## <a name="get-the-custom-vision-sdk-and-samples"></a>Custom Vision SDK i przykłady
Aby skompilować ten przykład, potrzebne są pakiety NuGet zestawu SDK wizji niestandardowe:

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Możesz pobrać obrazy wraz z [przykłady w języku C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/CustomVision).

## <a name="get-the-training-and-prediction-keys"></a>Pobierz klucze uczenia i przewidywania

Aby uzyskać klucze używane w tym przykładzie, odwiedź stronę [strony sieci web Custom Vision](https://customvision.ai) i wybierz __ikonę koła zębatego__ w prawym górnym rogu. W __kont__ sekcji, skopiuj wartości z __klucz szkolenia__ i __klucz prognozowania__ pola.

![Obraz przedstawiający klucze interfejsu użytkownika](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>Zrozumienie kodu

W programie Visual Studio, otwórz projekt na terenie `Samples/CustomVision.Sample/` katalogu projekt zestawu SDK.

Ta aplikacja używa klucza szkolenia pobranym wcześniej do utworzenia nowego projektu o nazwie __mój projekt nowej__. Następnie przekazuje obrazy do nauczenia i przetestowania klasyfikatora. Klasyfikator Określa, czy drzewo __Lipa__ lub __japoński wykonywanie operacji Cherry__.

Poniższe fragmenty kodu implementuje podstawowe funkcje w tym przykładzie:

* __Utwórz nowy projekt usługi Custom Vision Service__:

    ```csharp
     // Create a new project
    Console.WriteLine("Creating new project:");
    var project = trainingApi.CreateProject("My New Project");
    ```

* __Utworzenie tagów w projekcie__:

    ```csharp
    // Make two tags in the new project
    var hemlockTag = trainingApi.CreateTag(project.Id, "Hemlock");
    var japaneseCherryTag = trainingApi.CreateTag(project.Id, "Japanese Cherry");
    ```

* __Przekazywanie i tagować obrazy__:

    ```csharp
    // Add some images to the tags
    Console.WriteLine("\tUploading images");
    LoadImagesFromDisk();

    // Images can be uploaded one at a time
    foreach (var image in hemlockImages)
    {
        using (var stream = new MemoryStream(File.ReadAllBytes(image)))
        {
            trainingApi.CreateImagesFromData(project.Id, stream, new List<string>() { hemlockTag.Id.ToString() });
        }
    }

    // Or uploaded in a single batch 
    var imageFiles = japaneseCherryImages.Select(img => new ImageFileCreateEntry(Path.GetFileName(img), File.ReadAllBytes(img))).ToList();
    trainingApi.CreateImagesFromFiles(project.Id, new ImageFileCreateBatch(imageFiles, new List<Guid>() { japaneseCherryTag.Id }));
    ```

* __Szkolenie klasyfikatora__:

    ```csharp
    // Now there are images with tags start training the project
    Console.WriteLine("\tTraining");
    var iteration = trainingApi.TrainProject(project.Id);

    // The returned iteration will be in progress, and can be queried periodically to see when it has completed
    while (iteration.Status == "Completed")
    {
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
    }
    ```

* __Ustawianie domyślnej iteracji do endpoint prognoz__:

    ```csharp
    // The iteration is now trained. Make it the default project endpoint
    iteration.IsDefault = true;
    trainingApi.UpdateIteration(project.Id, iteration.Id, iteration);
    Console.WriteLine("Done!\n");
    ```

* __Tworzenie punktu końcowego prognozowania__:
 
    ```csharp
    // Create a prediction endpoint, passing in obtained prediction key
    PredictionEndpoint endpoint = new PredictionEndpoint() { ApiKey = predictionKey };
    ```
 
* __Wysyłanie obrazu do endpoint prognoz__:

    ```csharp
    // Make a prediction against the new project
    Console.WriteLine("Making a prediction:");
    var result = endpoint.PredictImage(project.Id, testImage);

    // Loop over each prediction and write out the results
    foreach (var c in result.Predictions)
    {
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
    }
    ```

## <a name="run-the-application"></a>Uruchamianie aplikacji

1. Wprowadź następujące zmiany do Dodaj klucze uczenia i przewidywania do aplikacji:

    * Dodaj swoje __klucz szkolenia__ na następujący wiersz:

        ```csharp
        string trainingKey = "<your key here>";
        ```

    * Dodaj swoje __klucz prognozowania__ na następujący wiersz:

        ```csharp
        string predictionKey = "<your key here>";
        ```

2. Uruchom aplikację. Po uruchomieniu aplikacji następujące dane wyjściowe są zapisywane do konsoli:

    ```
    Creating new project:
            Uploading images
            Training
    Done!

    Making a prediction:
            Hemlock: 95.0%
            Japanese Cherry: 0.0%
    ```

3. Naciśnij dowolny klawisz, aby zakończyć działanie aplikacji.
