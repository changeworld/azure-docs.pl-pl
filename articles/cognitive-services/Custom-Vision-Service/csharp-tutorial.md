---
title: 'Samouczek: kompilowanie aplikacji Windows for Custom Vision Service w języku C#'
titlesuffix: Azure Cognitive Services
description: Utwórz projekt, dodaj tagi, prześlij obrazy, wyszkol projekt i wykonaj przewidywanie przy użyciu domyślnego punktu końcowego.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 9e5ed71d4620f7ffeac8acb15f90d67964a86870
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366645"
---
# <a name="tutorial-use-the-custom-vision-service-from-a-c-application"></a>Samouczek: używanie funkcji Custom Vision Service z poziomu aplikacji C#

Dowiedz się, jak używać funkcji Custom Vision Service z poziomu aplikacji C#. Po jej utworzeniu możesz dodać tagi, przesłać obrazy, wyszkolić projekt, uzyskać adres URL punktu końcowego domyślnego przewidywania projektu i użyć punktu końcowego do programowego przetestowania obrazu. Wykorzystaj ten przykład open-source jako szablon do kompilowania własnych aplikacji dla systemu Windows przy użyciu interfejsu API usługi Custom Vision Service.

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna wersja programu Visual Studio 2017.

## <a name="get-the-custom-vision-sdk-and-samples"></a>Pobranie pakietu Custom Vision SDK i przykładów
Aby skompilować ten przykład, potrzebne są pakiety NuGet zestawu SDK Custom Vision:

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Obrazy można pobrać razem z [przykładami w języku C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/CustomVision).

## <a name="get-the-training-and-prediction-keys"></a>Uzyskanie kluczy szkoleniowego i predykcyjnego

Aby uzyskać klucze używane w tym przykładzie, przejdź na [stronę Custom Vision](https://customvision.ai) i wybierz __ikonę koła zębatego__ w prawym górnym rogu. W sekcji __Accounts__ (Konta) skopiuj wartości z pól __Training Key__ (Klucz szkoleniowy) i __Prediction Key__ (Klucz predykcyjny).

![Obraz interfejsu użytkownika do uzyskiwania kluczy](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>Zrozumienie kodu

W programie Visual Studio otwórz projekt w katalogu `Samples/CustomVision.Sample/` projektu SDK.

Ta aplikacja używa uzyskanego wcześniej klucza szkoleniowego do utworzenia nowego projektu o nazwie __Mój nowy projekt__. Następnie zostaną przesłane obrazy do szkolenia i testowania klasyfikatora. Klasyfikator określa, czy drzewo jest __Choiną__ czy __Wiśnią japońską__.

Poniższe fragmenty kodu implementują podstawową funkcjonalność tej przykładowej aplikacji:

* __Utwórz nowy projekt Custom Vision Service__:

    ```csharp
     // Create a new project
    Console.WriteLine("Creating new project:");
    var project = trainingApi.CreateProject("My New Project");
    ```

* __Utwórz tagi w projekcie__:

    ```csharp
    // Make two tags in the new project
    var hemlockTag = trainingApi.CreateTag(project.Id, "Hemlock");
    var japaneseCherryTag = trainingApi.CreateTag(project.Id, "Japanese Cherry");
    ```

* __Prześlij i oznacz tagami obrazy__:

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

* __Wykonaj szkolenie klasyfikatora__:

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

* __Ustaw domyślną iterację dla punktu końcowego przewidywania__:

    ```csharp
    // The iteration is now trained. Make it the default project endpoint
    iteration.IsDefault = true;
    trainingApi.UpdateIteration(project.Id, iteration.Id, iteration);
    Console.WriteLine("Done!\n");
    ```

* __Utwórz punkt końcowy przewidywania__:
 
    ```csharp
    // Create a prediction endpoint, passing in obtained prediction key
    PredictionEndpoint endpoint = new PredictionEndpoint() { ApiKey = predictionKey };
    ```
 
* __Wyślij obraz do punktu końcowego przewidywania__:

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

1. Aby dodać klucze szkoleniowe i predykcyjne do aplikacji, należy wprowadzić następujące zmiany:

    * Dodaj swój __klucz szkoleniowy__ do poniższej linii:

        ```csharp
        string trainingKey = "<your key here>";
        ```

    * Dodaj swój __klucz predykcyjny__ do poniższej linii:

        ```csharp
        string predictionKey = "<your key here>";
        ```

2. Uruchom aplikację. Po uruchomieniu aplikacji następujące dane wyjściowe są zapisywane w konsoli:

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
