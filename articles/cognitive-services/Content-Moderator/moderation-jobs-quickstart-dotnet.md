---
title: Azure Content Moderator — początek Moderowanie zadania przy użyciu platformy .NET | Dokumentacja firmy Microsoft
description: Jak zainicjować Moderowanie zadań dla platformy .NET przy użyciu zestawu SDK usługi Azure Content Moderator
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/10/2018
ms.author: sajagtap
ms.openlocfilehash: 0402d9dc1dfee5e146d3550d095f4fb53e52f12b
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720932"
---
# <a name="start-moderation-jobs-using-net"></a>Rozpoczynanie zadań Moderowanie przy użyciu platformy .NET

Ten artykuł zawiera informacje i przykłady kodu, które ułatwią Ci rozpoczęcie korzystania z [Content Moderator zestawu SDK dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) do:
 
- Uruchamianie zadania Moderowanie do skanowania i utworzyć przeglądy dla moderatorów ludzi
- Pobierz stan oczekujący
- Śledzenie i Uzyskaj ostateczny stan przeglądu
- Przesyłanie wyników do adresu Url wywołania zwrotnego

W tym artykule założono, że znasz już program Visual Studio i języka C#.

## <a name="sign-up-for-content-moderator"></a>Zarejestruj się w pakiecie Content Moderator

Zanim użyjesz usługi Content Moderator za pośrednictwem interfejsu API REST lub zestawu SDK, potrzebujesz klucza subskrypcji.
Zapoznaj się [Szybki Start](quick-start.md) Aby dowiedzieć się, jak można uzyskać klucz.

## <a name="sign-up-for-a-review-tool-account-if-not-completed-in-the-previous-step"></a>Załóż na konto narzędzia przeglądu, jeśli nie zostały wykonane w poprzednim kroku

Jeśli masz usługi Content Moderator w witrynie Azure portal, również [założyć konto narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/) i tworzenia zespołu przeglądu. Wymagany identyfikator zespołu oraz narzędzie do przeglądu do wywołania interfejsu API przeglądu, aby rozpocząć zadanie i wyświetlić te przeglądy w narzędzie do przeglądu.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Upewnij się, że klucz interfejsu API można wywołać interfejs API przeglądu tworzenia przeglądu

Po wykonaniu poprzednich kroków, użytkownik może pozostać przy użyciu dwóch kluczy pakietu Content Moderator w przypadku pracy w witrynie Azure portal. 

Jeśli planujesz użyć klucza interfejsu API platformy Azure w Twoim przykładzie zestawu SDK, wykonaj kroki opisane w [klucza przy użyciu platformy Azure przy użyciu interfejsu API przeglądu](review-tool-user-guide/credentials.md#use-the-azure-account-with-the-review-tool-and-review-api) sekcji, aby umożliwić aplikacji do wywołania interfejsu API przeglądu i tworzenia przeglądów.

Jeśli używasz bezpłatnej wersji próbnej klucz wygenerowany przez narzędzie do przeglądu, Twoje konto narzędzie do przeglądu już zna klucz i dlatego są wymagane żadne dodatkowe kroki.

## <a name="define-a-custom-moderation-workflow"></a>Definiowanie niestandardowego Moderowanie przepływu pracy

Zadanie Moderowanie skanuje zawartością za pomocą interfejsów API i używa **przepływu pracy** do ustalenia, czy należy utworzyć recenzje, czy nie.
Gdy narzędzie do przeglądu zawiera domyślny przepływ pracy umożliwia [zdefiniować niestandardowe przepływ pracy](Review-Tool-User-Guide/Workflows.md) dla tego przewodnika Szybki Start.

Nazwa przepływu pracy jest użyć w kodzie, który uruchamia zadanie moderowania.

## <a name="create-your-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Dodaj nową **Aplikacja konsoli (.NET Framework)** projektu do rozwiązania.

   W przykładowym kodzie, nadaj projektowi nazwę **CreateReviews**.

1. Wybierz ten projekt jako pojedynczy projekt startowy rozwiązania.

### <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Zainstaluj następujące pakiety NuGet:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizacja programu za pomocą instrukcji

Modyfikowanie programu za pomocą instrukcji.

    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="create-the-content-moderator-client"></a>Tworzenie klienta usługi Content Moderator

Dodaj następujący kod, aby utworzyć pakiet Content Moderator klienta dla Twojej subskrypcji.

> [!IMPORTANT]
> Aktualizacja **Region_świadczenia_usługi_azure** i **CMSubscriptionKey** pola z wartościami Twojego regionu identyfikatora i klucza subskrypcji.


    /// <summary>
    /// Wraps the creation and configuration of a Content Moderator client.
    /// </summary>
    /// <remarks>This class library contains insecure code. If you adapt this 
    /// code for use in production, use a secure method of storing and using
    /// your Content Moderator subscription key.</remarks>
    public static class Clients
    {
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "YOUR API REGION";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"https://{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR API KEY";

        /// <summary>
        /// Returns a new Content Moderator client for your subscription.
        /// </summary>
        /// <returns>The new client.</returns>
        /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
        /// When you have finished using the client,
        /// you should dispose of it either directly or indirectly. </remarks>
        public static ContentModeratorClient NewClient()
        {
            // Create and initialize an instance of the Content Moderator API wrapper.
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

            client.BaseUrl = AzureBaseURL;
            return client;
        }
    }

### <a name="initialize-application-specific-settings"></a>Inicjowanie ustawienia specyficzne dla aplikacji

Dodaj następujące stałe i pola statyczne do **Program** klasy w pliku Program.cs.

> [!NOTE]
> Stała TeamName jest ustawiona na nazwę użytą podczas tworzenia subskrypcji usługi Content Moderator. Możesz pobrać TeamName z [witryny sieci web pakietu Content Moderator](https://westus.contentmoderator.cognitive.microsoft.com/).
> Po zalogowaniu wybierz **poświadczenia** z **ustawienia** menu (koło zębate).
>
> Twoja nazwa zespołu jest wartość **identyfikator** pole **API** sekcji.


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

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Dodaj kod do auto średni, utworzy Przegląd i uzyskać szczegółów zadania

> [!Note]
> W praktyce należy ustawić adres URL wywołania zwrotnego **CallbackEndpoint** do adresu URL, który otrzymuje wyniki ręcznego przeglądu (za pośrednictwem żądania HTTP POST).

Rozpocznij, dodając następujący kod, aby **Main** metody.

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
> Klucz usługi Content Moderator ma żądań na drugi limit szybkości (jednostek Uzależnionych). Jeśli przekroczysz limit, zestaw SDK zgłasza wyjątek z kodem błędu 429. 
>
> Klucz w warstwie bezpłatna obowiązuje limit szybkości jeden RPS.

## <a name="run-the-program-and-review-the-output"></a>Uruchom program i przejrzyj dane wyjściowe

Następujące przykładowe dane wyjściowe w konsoli zostanie wyświetlony:

    Perform manual reviews on the Content Moderator site.
    Then, press any key to continue.

Zaloguj się do pakietu Content Moderator Przegląd narzędzia, aby zobaczyć oczekujące obrazu, zapoznaj się.

Użyj **dalej** przycisk Prześlij.

![Przegląd obrazu dla moderatorów ludzi](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Zobacz przykładowe dane wyjściowe w pliku dziennika

> [!NOTE]
> W pliku danych wyjściowych, ciągi **Teamname**, **ContentId**, **CallBackEndpoint**, i **WorkflowId** odzwierciedlają wartości użyto wcześniej.

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


## <a name="your-callback-url-if-provided-receives-this-response"></a>Adres Url wywołania zwrotnego, jeśli podana, otrzymuje tej odpowiedzi.

Zostanie wyświetlony odpowiedzi, jak w poniższym przykładzie:

> [!NOTE]
> W odpowiedzi wywołania zwrotnego, ciągi **ContentId** i **WorkflowId** odzwierciedlał odpowiednie wartości, które było wcześniej używane.

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

Pobierz [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) i [rozwiązania Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dla tego programu oraz inne Przewodniki Szybki Start pakietu Content Moderator dla platformy .NET i Rozpocznij pracę nad integracją.
