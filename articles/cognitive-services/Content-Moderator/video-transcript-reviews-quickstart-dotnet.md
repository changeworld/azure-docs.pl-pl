---
title: Azure moderatora zawartości — tworzenie przeglądami wykaz wideo przy użyciu platformy .NET | Dokumentacja firmy Microsoft
description: Jak utworzyć wykaz wideo przegląda przy użyciu zestawu SDK moderatora zawartości platformy Azure dla platformy .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/19/2018
ms.author: sajagtap
ms.openlocfilehash: 3286da6e38f0fba02386d877a835fb694ed0fdec
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347584"
---
# <a name="create-video-transcript-reviews-using-net"></a>Utwórz przeglądami wykaz wideo przy użyciu platformy .NET

Ten artykuł zawiera informacje i przykłady kodu w celu szybkiego rozpoczęcie pracy przy użyciu zawartości SDK moderatora w języku C# do:

- Utwórz opinię wideo dla człowieka moderatorów
- Dodaj moderowane zapis do przeglądu
- Publikowanie przeglądu

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz [moderowane wideo](video-moderation-api.md) i [utworzone wideo przeglądu](video-reviews-quickstart-dotnet.md) w narzędziu przeglądu ułatwiającym podejmowanie decyzji człowieka. Chcesz teraz dodać moderowane zapisy rozmów wideo w narzędziu przeglądu.

W artykule założono również, że znasz już program Visual Studio i C#.

### <a name="sign-up-for-content-moderator-services"></a>Załóż moderatora zawartości usług

Zanim użyjesz usługi moderatora zawartości za pośrednictwem interfejsu API REST lub zestawu SDK, należy klucza subskrypcji.

Na pulpicie nawigacyjnym moderatora zawartości, można znaleźć klucza subskrypcji w **ustawienia** > **poświadczenia** > **interfejsu API**  >   **Wersja próbna Ocp Apim subskrypcji klucza**. Aby uzyskać więcej informacji, zobacz [omówienie](overview.md).

### <a name="prepare-your-video-for-review"></a>Przygotowanie wideo do przeglądu

Dodaj zapis do przeglądu wideo. Wideo musi zostać opublikowany w trybie online. Konieczne jest jego punktu końcowego przesyłania strumieniowego. Punkt końcowy przesyłania strumieniowego umożliwia przegląd narzędzia odtwarzacza wideo do odtwarzania wideo.

![Pokaz wideo miniatur](images/ams-video-demo-view.PNG)

- Kopiuj **adres URL** na tym [pokaz usługi Azure Media Services](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) strony dla manifest adresu URL.

## <a name="create-your-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Dodaj nową **aplikacji konsoli (.NET Framework)** projektu do rozwiązania.

1. Nazwij projekt **VideoTranscriptReviews**.

1. Wybierz ten projekt jako projekt startowy pojedynczego dla rozwiązania.

### <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Zainstaluj następujące pakiety NuGet dla projektu TermLists.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizacja programu użytkownika za pomocą instrukcji

Zmodyfikuj program użytkownika przy użyciu instrukcji w następujący sposób.

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;
    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;


### <a name="add-private-properties"></a>Dodaj właściwości prywatnych

Dodaj następujące prywatne właściwości do przestrzeni nazw VideoTranscriptReviews, klasy Program.

W przypadku, gdy wskazane, Zastąp przykładowe wartości tych właściwości.


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
            /// the Conent Moderator web site. Your team name is the Id associated 
            /// with your subscription.</remarks>
            public static readonly string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

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


### <a name="create-content-moderator-client-object"></a>Utworzenie obiektu klienta moderatora zawartości

Dodaj następującą definicję metody do przestrzeni nazw VideoTranscriptReviews, klasy Program.

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
            BaseUrl = AzureBaseURL
        };
    }

## <a name="create-a-video-review"></a>Utwórz wideo przeglądu

Utwórz opinię wideo z **ContentModeratorClient.Reviews.CreateVideoReviews**. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** ma następujące wymagane parametry:
1. Ciąg zawierający typ MIME, który powinien być "application/json". 
1. Nazwę zespołu moderatora zawartości.
1. **IList<CreateVideoReviewsBodyItem>**  obiektu. Każdy **CreateVideoReviewsBodyItem** obiekt reprezentuje wideo przeglądu. Ta opcja szybkiego startu tworzy jeden przeglądu w czasie.

**CreateVideoReviewsBodyItem** ma kilka właściwości. Co najmniej można ustawić następujące właściwości:
- **Zawartości**. Adres URL wideo, należy sprawdzić.
- **ContentId**. Identyfikator można przypisać do przeglądu wideo.
- **Stan**. Ustaw wartość na "Unpublished." Jeśli nie zostanie ustawiony, domyślnie jako "Oczekujące", co oznacza, że przegląd wideo został opublikowany i oczekiwania ludzi przeglądu. Po opublikowaniu wideo przeglądu klatek, zapisu lub wynik łagodzenia wykaz można już dodać do niego.

> [!NOTE]
> **CreateVideoReviews** zwraca IList<string>. Każdy z tych ciągów zawiera identyfikator wideo przeglądu. Te identyfikatory są identyfikatorami GUID i nie są takie same jak wartości **ContentId** właściwości. 

Dodaj następującą definicję metody do przestrzeni nazw VideoReviews, klasy Program.

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

> [!NOTE]
> Klucz usługi moderatora zawartości ma żądań na drugi limit szybkości (RPS). Jeśli przekroczysz limit zestawu SDK zgłasza wyjątek z kodem błędu 429. 
>
> Klucz warstwa bezpłatna ma limit szybkości jeden RPS.

## <a name="add-transcript-to-video-review"></a>Dodaj zapis do przeglądu wideo

Dodaj zapisu do przeglądu wideo z **ContentModeratorClient.Reviews.AddVideoTranscript**. **AddVideoTranscript** ma następujące wymagane parametry:
1. Identyfikator zawartości moderatora zespołu.
1. Identyfikator wideo przeglądu zwrócony przez **CreateVideoReviews**.
1. A **strumienia** obiekt, który zawiera wykaz.

Wykaz musi być w formacie WebVTT. Aby uzyskać więcej informacji, zobacz [WebVTT: Format ścieżek tekstu wideo w sieci Web](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> Wykaz próbki jest używana w formacie VTT. W rozwiązaniu rzeczywistych korzystania z usługi Azure Media indeksatora do [Generowanie zapisu](https://docs.microsoft.com/azure/media-services/media-services-index-content) z wideo.

Dodaj następującą definicję metody do przestrzeni nazw VideotranscriptReviews, klasy Program.

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

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Dodanie wyniku łagodzenia zapis do przeglądu wideo

Oprócz dodania zapisu do przeglądu wideo, można też dodać wynik Moderowanie ten wykaz. Możesz to zrobić z **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** ma następujące wymagane parametry:
1. Ciąg zawierający typ MIME, który powinien być "application/json". 
1. Nazwę zespołu moderatora zawartości.
1. Identyfikator wideo przeglądu zwrócony przez **CreateVideoReviews**.
1. IList<TranscriptModerationBodyItem>. A **TranscriptModerationBodyItem** ma następujące właściwości:
- **Warunki**. IList<TranscriptModerationBodyItemTermsItem>. A **TranscriptModerationBodyItemTermsItem** ma następujące właściwości:
- **Indeks**. Liczony od zera indeks terminu.
- **Termin**. Ciąg, który zawiera wyrażenie.
- **Sygnatura czasowa**. Ciąg zawierający w sekundach czas w wykazie, w którym znajdują się warunki.

Wykaz musi być w formacie WebVTT. Aby uzyskać więcej informacji, zobacz [WebVTT: Format ścieżek tekstu wideo w sieci Web](https://www.w3.org/TR/webvtt1/).

Dodaj następującą definicję metody do przestrzeni nazw VideoTranscriptReviews, klasy Program. Ta metoda przesyła zapis do **ContentModeratorClient.TextModeration.ScreenText** metody. Również przekształca wynik w interfejsie IList<TranscriptModerationBodyItem>i przekazuje do **AddVideoTranscriptModerationResult**.

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


## <a name="publish-video-review"></a>Publikowanie wideo przeglądu

Publikowanie Przegląd wideo z **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** ma następujące wymagane parametry:
1. Nazwę zespołu moderatora zawartości.
1. Identyfikator wideo przeglądu zwrócony przez **CreateVideoReviews**.

Dodaj następującą definicję metody do przestrzeni nazw VideoReviews, klasy Program.

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

## <a name="putting-it-all-together"></a>Składanie wszystkiego razem

Dodaj **Main** definicję metody do przestrzeni nazw VideoTranscriptReviews, klasy Program. Na koniec Zamknij klasy Program i VideoTranscriptReviews przestrzeni nazw.

> [!NOTE]
> Wykaz próbki jest używana w formacie VTT. W rozwiązaniu rzeczywistych korzystania z usługi Azure Media indeksatora do [Generowanie zapisu](https://docs.microsoft.com/azure/media-services/media-services-index-content) z wideo. 

    static void Main(string[] args)
    {
        using (ContentModeratorClient client = NewClient())
        {
            // Create a review with the content pointing to a streaming endpoint (manifest)
            var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
            string review_id = CreateReview(client, "review1", streamingcontent);

            var transcript = @"WEBVTT

            01:01.000 --> 02:02.000
            First line with a crap word in a transcript.

            02:03.000 --> 02:25.000
            This is another line in the transcript.
            ";

            AddTranscript(client, review_id, transcript);

            AddTranscriptModerationResult(client, review_id, transcript);

            // Publish the review
            PublishReview(client, review_id);

            Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
            Console.WriteLine("Press any key to close the application.");
            Console.Read();
        }
    }

## <a name="run-the-program-and-review-the-output"></a>Uruchom program i przejrzyj dane wyjściowe

Po uruchomieniu aplikacji pojawić się dane wyjściowe na następujące wiersze:

    Creating a video review.
    Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Open your Content Moderator Dashboard and select Review > Video to see the review.
    Press any key to close the application.

## <a name="navigate-to-your-video-transcript-review"></a>Przejdź do zapoznania się z nimi wykaz wideo

Przejdź do strony Przegląd wykaz wideo w narzędzie przeglądu moderatora zawartości w **Przejrzyj**>**wideo**>**wykaz** ekranu.

Zostaną wyświetlone następujące funkcje:
- Dwa wiersze wykaz dodane
- Termin niestosownych wyrażeń znaleziono i wyróżnione przez usługę łagodzenia tekstu
- Wybór tekstu przekształcania rozpoczyna się od tego znacznika czasu wideo

![Przejrzyj wykaz wideo dla człowieka moderatorów](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak Generowanie [przegląda wideo](video-reviews-quickstart-dotnet.md) w narzędziu przeglądu.

Zapoznaj się z szczegółowy samouczek dotyczący tworzenia [ukończyć rozwiązania wideo łagodzenia](video-transcript-moderation-review-tutorial-dotnet.md).

[Pobierz rozwiązania Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) to i inne elementy zawartości moderatora szybkiego startu dla platformy .NET.
