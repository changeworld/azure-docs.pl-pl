---
title: Używanie zadań Moderowanie przy użyciu platformy .NET — Content Moderator
titlesuffix: Azure Cognitive Services
description: Użyj zestawu SDK .NET Moderator zawartości do inicjowania zadań end-to-end moderowanie zawartości dla zawartości image lub text w usłudze Azure Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 24d5483cf3b418cada3c5b7f03eedbff13cc36d6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62113872"
---
# <a name="define-and-use-moderation-jobs-net"></a>Definiowanie i korzystanie z zadań moderation (.NET)

Zadania Moderowanie służy jako typ otoki dla funkcji moderowania zawartości, przepływy pracy i recenzje. Ten przewodnik zawiera informacje i przykłady kodu, które ułatwią Ci rozpoczęcie korzystania z [Content Moderator zestawu SDK dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) do:

- Uruchamianie zadania moderowania w celu skanowania i tworzenia przeglądów dla użytkowników pełniących rolę moderatorów
- Pobieranie stanu przeglądu oczekującego
- Śledzenie i pobieranie stanu końcowego przeglądu
- Przesyłanie wyników przeglądu, aby adres URL wywołania zwrotnego

## <a name="prerequisites"></a>Wymagania wstępne

- Zaloguj się lub Utwórz konto w pakiecie Content Moderator [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/) lokacji.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Upewnij się, że Twój klucz interfejsu API umożliwia wywołanie interfejsu API przeglądu w celu utworzenia przeglądu.

Po wykonaniu poprzednich kroków możesz mieć dwa klucze usługi Content Moderator, jeśli wykonywanie kroków rozpoczęto w witrynie Azure Portal.

Jeśli planujesz użyć klucza interfejsu API platformy Azure w przykładzie zestawu SDK, wykonaj kroki opisane w sekcji [Używanie klucza platformy Azure przy użyciu interfejsu API przeglądu](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis), aby umożliwić aplikacji wywołanie interfejsu API przeglądu i tworzenie przeglądów.

Jeśli używasz bezpłatnej wersji próbnej klucza wygenerowanej przez narzędzie do przeprowadzania przeglądów, narzędzie to będzie już znało klucz i dlatego dodatkowe kroki nie są wymagane.

## <a name="define-a-custom-moderation-workflow"></a>Definiowanie niestandardowego przepływu pracy moderowania

Zadanie moderowania skanuje zawartość przy użyciu interfejsów API i używa **przepływu pracy**, aby ustalić, czy należy utworzyć przeglądy.
Chociaż narzędzie do przeglądu zawiera domyślny przepływ pracy, [zdefiniujmy niestandardowy przepływ pracy](Review-Tool-User-Guide/Workflows.md) dla tego przewodnika Szybki start.

Należy użyć nazwy przepływu pracy w kodzie, który rozpoczyna zadanie moderowania.

## <a name="create-your-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Dodaj nowy projekt **Aplikacja konsoli (.NET Framework)** do rozwiązania.

   W przykładowym kodzie nadaj projektowi nazwę **CreateReviews**.

1. Wybierz ten projekt jako pojedynczy projekt startowy rozwiązania.

### <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Zainstaluj następujące pakiety NuGet:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizowanie programu za pomocą instrukcji

Zmodyfikuj program za pomocą instrukcji.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Tworzenie klienta usługi Content Moderator

Dodaj następujący kod, aby utworzyć klienta usługi Content Moderator dla Twojej subskrypcji.

> [!IMPORTANT]
> Zaktualizuj pola **AzureRegion** i **CMSubscriptionKey** wartościami identyfikatora regionu i klucza subskrypcji.

```csharp
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

        client.Endpoint = AzureBaseURL;
        return client;
    }
}
```

### <a name="initialize-application-specific-settings"></a>Inicjowanie ustawień specyficznych dla aplikacji

Dodaj następujące stałe i pola statyczne do klasy **Program** w pliku Program.cs.

> [!NOTE]
> Nadaj stałej TeamName nazwę użytą podczas tworzenia subskrypcji usługi Content Moderator. Pobierz stałą TeamName z [witryny sieci Web usługi Content Moderator](https://westus.contentmoderator.cognitive.microsoft.com/).
> Po zalogowaniu wybierz opcję **Poświadczenia** z menu (koła zębatego) **Ustawienia**.
>
> Nazwa zespołu to wartość pola **Id** w sekcji **Interfejs API**.

```csharp
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
/// the Content Moderator web site. Your team name is the Id associated
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
/// <remarks>Relative paths are relative to the execution directory.</remarks>
private const string OutputFile = "OutputLog.txt";

/// <summary>
/// The number of seconds to delay after a review has finished before
/// getting the review results from the server.
/// </summary>
private const int latencyDelay = 45;

/// <summary>
/// The callback endpoint for completed reviews.
/// </summary>
/// <remarks>Reviews show up for reviewers on your team.
/// As reviewers complete reviews, results are sent to the
/// callback endpoint using an HTTP POST request.</remarks>
private const string CallbackEndpoint = "";
```

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Dodanie kodu do automatycznego moderowania, tworzenia przeglądu oraz pobrania szczegółów zadania

> [!Note]
> W praktyce adres URL wywołania zwrotnego **CallbackEndpoint** jest ustawiany na adres URL używany do odbierania wyników przeglądu ręcznego (dostarczanych przy użyciu żądania POST HTTP).

Rozpocznij od dodania do metody **Main** następującego kodu.

```csharp
using (TextWriter writer = new StreamWriter(OutputFile, false))
{
    using (var client = Clients.NewClient())
    {
        writer.WriteLine("Create review job for an image.");
        var content = new Content(ImageUrl);

        // The WorkflowName contains the name of the workflow defined in the online review tool.
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
```

> [!NOTE]
> Klucz usługi Content Moderator ma limit szybkości wyrażany w żądaniach na sekundę (RPS). Po przekroczeniu tego limitu zestaw SDK zgłasza wyjątek z kodem błędu 429.
>
> Limit klucza warstwy bezpłatnej wynosi 1 RPS.

## <a name="run-the-program-and-review-the-output"></a>Uruchamianie program i przeglądanie danych wyjściowych

Zobaczysz w konsoli dane wyjściowe podobne do następujących:

```console
Perform manual reviews on the Content Moderator site.
Then, press any key to continue.
```

Zaloguj się do narzędzia do przeglądów usługi Content Moderator, aby zobaczyć oczekujący przegląd obrazów.

Użyj przycisku **Dalej**, aby przesłać dane.

![Przeglądanie obrazu przez moderatorów-ludzi](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Wyświetlanie przykładowych danych wyjściowych w pliku dziennika

> [!NOTE]
> W pliku danych wyjściowych ciągi **Teamname**, **ContentId**, **CallBackEndpoint** i **WorkflowId** odzwierciedlają użyte wcześniej wartości.

```json
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
```

## <a name="your-callback-url-if-provided-receives-this-response"></a>Ta odpowiedź jest wysyłana na adres URL wywołania zwrotnego, jeśli został określony

Zobaczysz odpowiedź podobną do następującej:

> [!NOTE]
> W odpowiedzi wywołania zwrotnego ciągi **ContentId** i **WorkflowId** odzwierciedlają użyte wcześniej wartości.

```json
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
```

## <a name="next-steps"></a>Kolejne kroki

Pobierz zestaw [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) i [rozwiązanie programu Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dla tego i innych przewodników Szybki start usługi Content Moderator dla platformy .NET i rozpocznij pracę nad integracją.
