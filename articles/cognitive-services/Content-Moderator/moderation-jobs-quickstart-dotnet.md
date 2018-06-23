---
title: Azure moderatora zawartości - Start łagodzenia zadania przy użyciu platformy .NET | Dokumentacja firmy Microsoft
description: Inicjowanie zadania łagodzenia przy użyciu zestawu SDK moderatora zawartości platformy Azure dla platformy .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/06/2018
ms.author: sajagtap
ms.openlocfilehash: a103875607355993e216ce1ddea02009fc8fa1c4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347080"
---
# <a name="start-moderation-jobs-using-net"></a>Rozpocznij zadania łagodzenia przy użyciu platformy .NET

Ten artykuł zawiera informacje i przykłady ułatwiające rozpoczęcie pracy korzystać z zawartości moderatora zestawu SDK dla platformy .NET:
 
- Uruchomić zadanie łagodzenia do skanowania i utworzyć przeglądami dla człowieka moderatorów
- Pobierz stan oczekujący
- Śledzenie i Pobierz stan końcowy przeglądu
- Przedstawia wynik do wywołania zwrotnego adresu Url

W tym artykule przyjęto założenie, że znasz już program Visual Studio i C#.

## <a name="sign-up-for-content-moderator-services"></a>Załóż moderatora zawartości usług

Zanim użyjesz usługi moderatora zawartości za pośrednictwem interfejsu API REST lub zestawu SDK, należy klucza subskrypcji.
Zapoznaj się [szybkiego startu](quick-start.md) Aby dowiedzieć się, jak można uzyskać klucz.

## <a name="define-a-custom-moderation-workflow"></a>Zdefiniuj niestandardowe łagodzenia przepływu pracy

Zadanie łagodzenia skanuje zawartości przy użyciu interfejsów API i używa **przepływu pracy** do ustalenia, czy można utworzyć recenzji, czy nie.
Podczas umożliwia domyślny przepływ pracy zawiera narzędzie przeglądu [zdefiniowanie niestandardowego przepływu pracy](Review-Tool-User-Guide/Workflows.md) dla tego przewodnika Szybki Start.

Nazwa przepływu pracy programu swój kod, który uruchamia zadanie łagodzenia.

## <a name="create-your-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Dodaj nową **aplikacji konsoli (.NET Framework)** projektu do rozwiązania.

   W przykładowym kodzie nazwij projekt **CreateReviews**.

1. Wybierz ten projekt jako projekt startowy pojedynczego dla rozwiązania.

1. Dodaj odwołanie do **ModeratorHelper** projektu zestawu, który został utworzony w [szybkiego startu pomocnika klienta moderatora zawartości](content-moderator-helper-quickstart-dotnet.md).

### <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Zainstaluj następujące pakiety NuGet:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizacja programu użytkownika za pomocą instrukcji

Zmodyfikuj program użytkownika przy użyciu instrukcji.

    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="initialize-application-specific-settings"></a>Inicjowanie ustawienia specyficzne dla aplikacji

Dodaj następujące ograniczenia i pola statyczne do **Program** klasy w pliku Program.cs.

> [!NOTE]
> Stała TeamName jest ustawiona na nazwę, która zostanie użyta podczas tworzenia subskrypcji moderatora zawartości. Pobrać TeamName z [moderatora zawartości witryny sieci web](https://westus.contentmoderator.cognitive.microsoft.com/).
> Po zalogowaniu wybierz **poświadczenia** z **ustawienia** menu (koło zębate).
>
> Twoja nazwa zespołu jest wartość **identyfikator** w **interfejsu API** sekcji.


    /// <summary>
    /// The moderation job will use this workflow that you defined earlier.
    /// See the quickstart article to learn how to setup custom workflows.
    /// </summary>
    private const string WorkflowName = "OCR";
    
    /// <summary>
    /// The name of the team to assign the job to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Conent Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "***";

    /// <summary>
    /// The URL of the image to create a review job for.
    /// </summary>
    private const string ImageUrl =
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png";

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are ralative the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const int latencyDelay = 45;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Revies show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "";

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Dodaj kod, aby automatycznie umiarkowany, Utwórz opinię i Pobierz szczegóły zadania

> [!Note]
> W praktyce, należy ustawić adres URL wywołania zwrotnego **CallbackEndpoint** do adresu URL, który odbiera wyniki ręczne przeglądu (za pomocą żądania HTTP POST).

Najpierw dodaj następujący kod, aby **Main** metody.

    using (TextWriter writer = new StreamWriter(OutputFile, false))
    {
        using (var client = Clients.NewClient())
        {
            writer.WriteLine("Create review job for an image.");
            var content = new Content(ImageUrl);
        
            // The WorkflowName contains the nameof the workflow defined in the online review tool.
            // See the quickstart article to learn more.
            var jobResult = client.Reviews.CreateJobWithHttpMessagesAsync(
                    TeamName, "image", "contentID", WorkflowName, "application/json", content, CallbackEndpoint);

            // Record the job ID.
            var jobId = jobResult.Result.Body.JobIdProperty;

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                jobResult.Result.Body, Formatting.Indented));

            Thread.Sleep(2000);
            writer.WriteLine();

            writer.WriteLine("Get review job status.");
            var jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
                    TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                    jobDetails.Result.Body, Formatting.Indented));

            Console.WriteLine();
            Console.WriteLine("Perform manual reviews on the Content Moderator site.");
            Console.WriteLine("Then, press any key to continue.");
            Console.ReadKey();

            Console.WriteLine();
            Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
            Thread.Sleep(latencyDelay * 1000);

            writer.WriteLine("Get review details.");
            jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
            TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
            jobDetails.Result.Body, Formatting.Indented));
        }
        writer.Flush();
        writer.Close();
    }

> [!NOTE]
> Klucz usługi moderatora zawartości ma żądań na drugi limit szybkości (RPS). Jeśli przekroczysz limit zestawu SDK zgłasza wyjątek z kodem błędu 429. 
>
> Klucz warstwa bezpłatna ma limit szybkości jeden RPS.

## <a name="run-the-program-and-review-the-output"></a>Uruchom program i przejrzyj dane wyjściowe

Następujące przykładowe dane wyjściowe w konsoli zostanie wyświetlony:

    Perform manual reviews on the Content Moderator site.
    Then, press any key to continue.

Zaloguj się do zawartości moderatora Przejrzyj narzędzia, aby wyświetlić obraz oczekujące przejrzeć.

Użyj **dalej** przycisk przesyłania.

![Przejrzyj obrazu dla człowieka moderatorów](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Zobacz przykładowe dane wyjściowe w pliku dziennika

> [!NOTE]
> W pliku danych wyjściowych ciągi **Teamname**, **ContentId**, **CallBackEndpoint**, i **WorkflowId** odzwierciedlają użyto wartości wcześniej.

    Create moderation job for an image.
    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

    Get review details.
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": [
        {
            "Ts": "2018-01-07T00:38:26.7714671",
            "Msg": "Successfully got hasText response from Moderator"
        },
        {
            "Ts": "2018-01-07T00:38:26.4181346",
            "Msg": "Getting hasText from Moderator"
        },
        {
            "Ts": "2018-01-07T00:38:25.5122828",
            "Msg": "Starting Execution - Try 1"
        }
        ]
    }


## <a name="your-callback-url-if-provided-receives-this-response"></a>Adres Url wywołania zwrotnego, jeśli zostanie podana, otrzymuje tej odpowiedzi.

Zostanie wyświetlony odpowiedzi, jak w następującym przykładzie:

> [!NOTE]
> Do wywołania zwrotnego odpowiedzi ciągi **ContentId** i **WorkflowId** wartości użyte wcześniej.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe",
        "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
        "WorkFlowId": "OCR",
        "Status": "Complete",
        "ContentType": "Image",
        "CallBackType": "Job",
        "ContentId": "contentID",
        "Metadata": {
            "hastext": "True",
            "ocrtext": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
            "imagename": "contentID"
        }
    }


## <a name="next-steps"></a>Kolejne kroki

[Pobierz rozwiązania Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) to i inne elementy zawartości moderatora szybkiego startu dla platformy .NET i rozpocząć pracę na integracją.
