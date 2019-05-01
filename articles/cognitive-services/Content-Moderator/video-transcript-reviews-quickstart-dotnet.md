---
title: Utwórz przeglądy transkrypcji wideo przy użyciu platformy .NET — Content Moderator
titlesuffix: Azure Cognitive Services
description: Utwórz przeglądy transkrypcji wideo za pomocą Content Moderator zestawu SDK dla platformy .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/19/2019
ms.author: sajagtap
ms.openlocfilehash: fa782f687979f1d32cdf1c18bd08f6672e39adfe
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868590"
---
# <a name="create-video-transcript-reviews-using-net"></a>Utwórz przeglądy transkrypcji wideo przy użyciu platformy .NET

Ten artykuł zawiera informacje i przykłady kodu, aby pomóc Ci szybko rozpocząć pracę, przy użyciu [Content Moderator zestawu SDK przy użyciu języka C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) do:

- Utwórz Przegląd wideo dla moderatorów ludzi
- Dodaj moderowanych transkrypcji do przeglądu
- Publikowanie przeglądu

## <a name="prerequisites"></a>Wymagania wstępne

- Zaloguj się lub Utwórz konto w pakiecie Content Moderator [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/) lokacji, jeśli nie zostało to jeszcze zrobione.
- W tym artykule założono, że masz [moderowane wideo](video-moderation-api.md) i [utworzony Przegląd wideo](video-reviews-quickstart-dotnet.md) w narzędzie do przeglądu do podejmowania decyzji przez ludzi. Teraz należy dodać moderowanych transkrypcji wideo w narzędzie do przeglądu.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Upewnij się, że klucz interfejsu API można wywołać interfejsu API przeglądu (Tworzenie zadania)

Po wykonaniu poprzednich kroków możesz mieć dwa klucze usługi Content Moderator, jeśli wykonywanie kroków rozpoczęto w witrynie Azure Portal.

Jeśli planujesz użyć klucza interfejsu API platformy Azure w przykładzie zestawu SDK, wykonaj kroki opisane w sekcji [Używanie klucza platformy Azure przy użyciu interfejsu API przeglądu](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis), aby umożliwić aplikacji wywołanie interfejsu API przeglądu i tworzenie przeglądów.

Jeśli używasz bezpłatnej wersji próbnej klucza wygenerowanej przez narzędzie do przeprowadzania przeglądów, narzędzie to będzie już znało klucz i dlatego dodatkowe kroki nie są wymagane.

## <a name="prepare-your-video-for-review"></a>Przygotowywanie filmu wideo do przeglądu

Dodaj transkrypcji do wideo przeglądu. Wideo musi być opublikowane w trybie online. Konieczne jest jej punkt końcowy przesyłania strumieniowego. Punkt końcowy przesyłania strumieniowego umożliwia przegląd narzędzie odtwarzacza wideo do odtwarzania wideo.

![Pokaz wideo, miniatury](images/ams-video-demo-view.PNG)

- Kopiuj **adresu URL** na tym [pokaz usługi Azure Media Services](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) stronę o adresie URL manifestu.

## <a name="create-your-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Dodaj nowy projekt **Aplikacja konsoli (.NET Framework)** do rozwiązania.

1. Nadaj projektowi nazwę **VideoTranscriptReviews**.

1. Wybierz ten projekt jako pojedynczy projekt startowy rozwiązania.

### <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Zainstaluj następujące pakiety NuGet projektu TermLists.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizowanie programu za pomocą instrukcji

Modyfikowanie programu za pomocą instrukcji w następujący sposób.


```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Dodawanie właściwości prywatnych

Dodaj następujące właściwości prywatnej do przestrzeni nazw VideoTranscriptReviews, klasy programu.

W przypadku, gdy wskazane, Zastąp przykładowe wartości tych właściwości.

```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Replace this example location with the location for your Content Moderator account.
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "YOUR CONTENT MODERATOR REGION";

        // NOTE: Replace this example key with a valid subscription key.
        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

        // NOTE: Replace this example team name with your Content Moderator team name.
        /// <summary>
        /// The name of the team to assign the job to.
        /// </summary>
        /// <remarks>This must be the team name you used to create your 
        /// Content Moderator account. You can retrieve your team name from
        /// the Content Moderator web site. Your team name is the Id associated 
        /// with your subscription.</remarks>
        private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Utworzenie obiektu klienta usługi Content Moderator

Dodaj następującą definicję metody do przestrzeni nazw VideoTranscriptReviews, klasy programu.

```csharp
/// <summary>
/// Returns a new Content Moderator client for your subscription.
/// </summary>
/// <returns>The new client.</returns>
/// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
/// When you have finished using the client,
/// you should dispose of it either directly or indirectly. </remarks>
public static ContentModeratorClient NewClient()
{
    return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
    {
        Endpoint = AzureBaseURL
    };
}
```

## <a name="create-a-video-review"></a>Utwórz Przegląd wideo

Tworzenie przeglądu wideo za pomocą **ContentModeratorClient.Reviews.CreateVideoReviews**. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** ma następujące wymagane parametry:
1. Ciąg zawierający typ MIME, który powinien być "application/json". 
1. Nazwa zespołu usługi Content Moderator.
1. **IList\<CreateVideoReviewsBodyItem >** obiektu. Każdy **CreateVideoReviewsBodyItem** obiekt reprezentuje wideo przeglądu. Ten przewodnik Szybki Start tworzy jeden przegląd w danym momencie.

**CreateVideoReviewsBodyItem** ma kilka właściwości. Jako minimum można ustawić następujące właściwości:
- **Zawartość**. Adres URL filmu wideo do przeglądu.
- **ContentId**. Identyfikator, aby przypisać do przeglądu wideo.
- **Stan**. Ustaw wartość na "Nieopublikowane." Jeśli nie zostanie zdefiniowany, wartość domyślna, jako "Oczekujące", co oznacza, że przegląd wideo został opublikowany i oczekujące przeglądu przez ludzi. Po opublikowaniu wideo przeglądu klatki wideo, transkrypcji lub wynik Moderowanie transkrypcji można już dodać do niego.

> [!NOTE]
> **CreateVideoReviews** zwraca interfejs IList\<ciągu >. Każdy z tych ciągów zawiera identyfikator wideo przeglądu. Te identyfikatory są identyfikatory GUID i nie są takie same jak wartość **ContentId** właściwości.

Dodaj następującą definicję metody do przestrzeni nazw VideoReviews, klasy programu.

```csharp
/// <summary>
/// Create a video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="id">The ID to assign to the video review.</param>
/// <param name="content">The URL of the video to review.</param>
/// <returns>The ID of the video review.</returns>
private static string CreateReview(ContentModeratorClient client, string id, string content)
{
    Console.WriteLine("Creating a video review.");

    List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
        new CreateVideoReviewsBodyItem
        {
            Content = content,
            ContentId = id,
            /* Note: to create a published review, set the Status to "Pending".
            However, you cannot add video frames or a transcript to a published review. */
            Status = "Unpublished",
        }
    };

    var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

    Thread.Sleep(throttleRate);

    // We created only one review.
    return result[0];
}
```

> [!NOTE]
> Klucz usługi Content Moderator ma limit szybkości wyrażany w żądaniach na sekundę (RPS). Po przekroczeniu tego limitu zestaw SDK zgłasza wyjątek z kodem błędu 429.
>
> Limit klucza warstwy bezpłatnej wynosi 1 RPS.

## <a name="add-transcript-to-video-review"></a>Dodaj transkrypcji do wideo przeglądu

Dodaj transkrypcji do wideo weryfikacji z **ContentModeratorClient.Reviews.AddVideoTranscript**. **AddVideoTranscript** ma następujące wymagane parametry:
1. Identyfikator zespołu pakietu Content Moderator.
1. Identyfikator przeglądu wideo zwrócony przez **CreateVideoReviews**.
1. A **Stream** obiekt, który zawiera transkrypcję.

Transkrypcja musi być w formacie WebVTT. Aby uzyskać więcej informacji, zobacz [WebVTT: Tekst wideo w sieci Web śledzi Format](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> Zapis próbki jest używana w formacie VTT. W rozwiązaniu do świata rzeczywistego, użyj usługi Azure Media Indexer [Generowanie transkrypcji](https://docs.microsoft.com/azure/media-services/media-services-index-content) z filmu wideo.

Dodaj następującą definicję metody do przestrzeni nazw VideotranscriptReviews, klasy programu.

```csharp
/// <summary>
/// Add a transcript to the indicated video review.
/// The transcript must be in the WebVTT format.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
    client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Dodaj wynik Moderowanie transkrypcji do wideo przeglądu

Oprócz dodawania transkrypcji do wideo przeglądu, można też dodać wynik Moderowanie tej transkrypcji. Możesz to zrobić za pomocą **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** ma następujące wymagane parametry:
1. Ciąg zawierający typ MIME, który powinien być "application/json". 
1. Nazwa zespołu usługi Content Moderator.
1. Identyfikator przeglądu wideo zwrócony przez **CreateVideoReviews**.
1. IList\<TranscriptModerationBodyItem >. A **TranscriptModerationBodyItem** ma następujące właściwości:
1. **Warunki**. IList\<TranscriptModerationBodyItemTermsItem >. A **TranscriptModerationBodyItemTermsItem** ma następujące właściwości:
1. **Indeks**. Liczony od zera indeks termin.
1. **Termin**. Ciąg, który zawiera wyrażenie.
1. **Sygnatura czasowa**. Ciąg, który zawiera, w sekundach czas w transkrypcji, w którym znajdują się warunki.

Transkrypcja musi być w formacie WebVTT. Aby uzyskać więcej informacji, zobacz [WebVTT: Tekst wideo w sieci Web śledzi Format](https://www.w3.org/TR/webvtt1/).

Dodaj następującą definicję metody do przestrzeni nazw VideoTranscriptReviews, klasy programu. Ta metoda przesyła transkrypcję, aby **ContentModeratorClient.TextModeration.ScreenText** metody. Również tłumaczy wynik na IList\<TranscriptModerationBodyItem > i przekazuje do **AddVideoTranscriptModerationResult**.

```csharp
/// <summary>
/// Add the results of moderating a video transcript to the indicated video review.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

    // Screen the transcript using the Text Moderation API. For more information, see:
    // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
    Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

    // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
    List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
    if (null != screen.Terms)
    {
        foreach (var term in screen.Terms)
        {
            if (term.Index.HasValue)
            {
                terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
            }
        }
    }

    List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
    {
        new TranscriptModerationBodyItem()
        {
            Timestamp = "0",
            Terms = terms
        }
    };

    client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

    Thread.Sleep(throttleRate);
}
```

## <a name="publish-video-review"></a>Publikowanie wideo przeglądu

Publikowanie wideo weryfikacji z **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** ma następujące wymagane parametry:
1. Nazwa zespołu usługi Content Moderator.
1. Identyfikator przeglądu wideo zwrócony przez **CreateVideoReviews**.

Dodaj następującą definicję metody do przestrzeni nazw VideoReviews, klasy programu.

```csharp
/// <summary>
/// Publish the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void PublishReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Publishing the review with ID {0}.", review_id);
    client.Reviews.PublishVideoReview(TeamName, review_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="putting-it-all-together"></a>Zebranie wszystkich elementów

Dodaj **Main** definicję metody do przestrzeni nazw VideoTranscriptReviews, klasy programu. Na koniec można zamknąć, klasę Program i VideoTranscriptReviews przestrzeni nazw.

> [!NOTE]
> Zapis próbki jest używana w formacie VTT. W rozwiązaniu do świata rzeczywistego, użyj usługi Azure Media Indexer [Generowanie transkrypcji](https://docs.microsoft.com/azure/media-services/media-services-index-content) z filmu wideo.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var transcript = @"WEBVTT

        01:01.000 --> 02:02.000
        First line with a negative word in a transcript.

        02:03.000 --> 02:25.000
        This is another line in the transcript.
        ";

        AddTranscript(client, review_id, transcript);

        AddTranscriptModerationResult(client, review_id, transcript);

        // Publish the review
        PublishReview(client, review_id);

        Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
        Console.WriteLine("Press any key to close the application.");
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>Uruchamianie programu i przeglądanie danych wyjściowych

Po uruchomieniu aplikacji, zobaczysz dane wyjściowe w następujących wierszach:

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>Przejdź do zapoznania się z nimi transkrypcji wideo

Przejdź do strony Przegląd transkrypcji wideo w narzędzie do przeglądu usługi Content Moderator w **Przejrzyj**>**wideo**>**transkrypcji** ekranu.

Zostaną wyświetlone następujące funkcje:
- Dwa wiersze transkrypcji, którą dodałeś
- Termin wulgaryzmów znalezione i wyróżniony przez usługę Moderowanie tekstu
- Zaznaczanie tekstu transkrypcji wideo jest uruchamiany z tej sygnatury czasowej

![Przegląd transkrypcji wideo dla moderatorów ludzi](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Kolejne kroki

Pobierz [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) i [rozwiązania Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dla tego programu oraz inne Przewodniki Szybki Start pakietu Content Moderator dla platformy .NET.

Dowiedz się, jak wygenerować [przeglądy wideo](video-reviews-quickstart-dotnet.md) w narzędzie do przeglądu.

Zapoznaj się z szczegółowy samouczek dotyczący sposobu tworzenia [kompletne rozwiązanie Moderowanie filmów wideo](video-transcript-moderation-review-tutorial-dotnet.md).
