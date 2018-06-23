---
title: Użyj usługi wizji niestandardowe z aplikacji C# — kognitywnych usług Azure | Dokumentacja firmy Microsoft
description: Poznaj podstawowe C# aplikacji, który używa interfejsu API wizji niestandardowe w kognitywnych usług firmy Microsoft. Utwórz projekt, Dodaj tagi, przekazywanie obrazów, uczenia projektu i prognozowania przy użyciu domyślnego punktu końcowego.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 80cb022808748ed2c60dff7c363d6020cb4043a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347864"
---
# <a name="use-the-custom-vision-service-from-a-c35-application"></a>Korzystanie z usługi wizji niestandardowe z C&#35; aplikacji

Dowiedz się, jak korzystać z aplikacji C# usługi wizji niestandardowe. Po jego utworzeniu można można dodać tagów, przekazywanie obrazów uczenia projektu, uzyskać URL punktu końcowego prognozowania domyślnego projektu i użycia punktu końcowego do testowania programistyczne obrazu. Użyj w tym przykładzie open source jako szablon do tworzenia własnych aplikacji dla systemu Windows przy użyciu interfejsu API usługi wizji niestandardowe.

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolnej wersji programu Visual Studio 2015 lub 2017 r dla systemu Windows.

* [Zestawu SDK usług niestandardowych wizji](http://github.com/Microsoft/Cognitive-CustomVision-Windows/). W tym próbki i obrazy używane w tym dokumencie.

## <a name="get-the-training-and-prediction-keys"></a>Pobieranie kluczy szkolenia i prognozowanie

Aby uzyskać klucze używane w tym przykładzie, odwiedź stronę [wizji niestandardowe strony sieci web](https://customvision.ai) i wybierz __koło zębate ikonę__ w prawym górnym rogu. W __kont__ sekcji, skopiuj wartości z __klucza szkolenia__ i __klucza prognozowania__ pola.

![Obraz kluczy interfejsu użytkownika](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>Zrozumienie kodu

W programie Visual Studio Otwórz projekt znajduje się w `Samples/CustomVision.Sample/` katalogu projektu do zestawu SDK.

Ta aplikacja używa klucza szkolenia pobranym wcześniej do utworzenia nowego projektu o nazwie __Mój nowy projekt__. Następnie zostanie przesłany obrazów do nauczenia i przetestowania klasyfikatora. Klasyfikator identyfikuje czy drzewo jest __Lipa__ lub __japońskiego tę__.

Poniższe fragmenty kodu zaimplementować podstawowe funkcje w tym przykładzie:

* __Utwórz nowy projekt usługi wizji niestandardowe__:

    ```csharp
     // Create a new project
    Console.WriteLine("Creating new project:");
    var project = trainingApi.CreateProject("My New Project");
    ```

* __Tworzenie tagów w projekcie__:

    ```csharp
    // Make two tags in the new project
    var hemlockTag = trainingApi.CreateTag(project.Id, "Hemlock");
    var japaneseCherryTag = trainingApi.CreateTag(project.Id, "Japanese Cherry");
    ```

* __Przekazywanie i tagów obrazów__:

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

* __Szkolenie Klasyfikator__:

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

* __Ustaw iterację domyślną dla punktu końcowego prognozowania__:

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
 
* __Wysyłanie obrazu do punktu końcowego prognozowania__:

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

1. Wprowadź następujące zmiany do dodania szkolenia i prognozowanie klucze do aplikacji:

    * Dodaj użytkownika __klucza szkolenia__ do następującego:

        ```csharp
        string trainingKey = "<your key here>";
        ```

    * Dodaj użytkownika __klucza prognozowania__ do następującego:

        ```csharp
        string predictionKey = "<your key here>";
        ```

2. Uruchom aplikację. Jak aplikacja zostanie uruchomiona, następujące dane wyjściowe są zapisywane do konsoli:

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
