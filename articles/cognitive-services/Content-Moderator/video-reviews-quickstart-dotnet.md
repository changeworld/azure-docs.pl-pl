---
title: Azure moderatora zawartości — tworzenie przeglądami wideo przy użyciu platformy .NET | Dokumentacja firmy Microsoft
description: Jak utworzyć wideo przegląda przy użyciu zestawu SDK moderatora zawartości platformy Azure dla platformy .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/18/2018
ms.author: sajagtap
ms.openlocfilehash: cb487314b8695f3676fdb22a9d7e3ec5ca3ed9f2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347201"
---
# <a name="create-video-reviews-using-net"></a>Utwórz przeglądami wideo przy użyciu platformy .NET

Ten artykuł zawiera informacje i przykłady kodu w celu szybkiego rozpoczęcie pracy przy użyciu zawartości SDK moderatora w języku C# do:

- Utwórz opinię wideo dla człowieka moderatorów
- Dodaj ramki do przeglądu
- Pobierz ramek dla przeglądu 
- Pobierz stan i szczegółowe informacje przeglądu
- Publikowanie przeglądu

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz [moderowane wideo (zobacz Szybki Start)](video-moderation-api.md) i dane odpowiedzi. Potrzebne do tworzenia recenzji ramki dla człowieka moderatorów.

W artykule założono również, że znasz już program Visual Studio i C#.

### <a name="sign-up-for-content-moderator-services"></a>Załóż moderatora zawartości usług

Zanim użyjesz usługi moderatora zawartości za pośrednictwem interfejsu API REST lub zestawu SDK, należy klucza subskrypcji.

Na pulpicie nawigacyjnym moderatora zawartości, można znaleźć klucza subskrypcji w **ustawienia** > **poświadczenia** > **interfejsu API**  >  **Wersji próbnej Ocp Apim subskrypcji klucza**. Aby uzyskać więcej informacji, zobacz [omówienie](overview.md).

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Przygotowanie wideo i klatek wideo do przeglądu

Wideo i przykładowe klatek wideo do przeglądania musi opublikowane w trybie online, ponieważ należy ich adresy URL.

> [!NOTE]
> Używana jest ręcznie zapisane zrzuty ekranu z wideo losowe wyniki dorosłą/luksusowych przedstawiający zastosowanie interfejsu API przeglądu. W przypadku rzeczywistych używasz [wyjście wideo łagodzenia](video-moderation-api.md#run-the-program-and-review-the-output) do tworzenia obrazów i przypisać wyniki. 

Wideo wymaga punktu końcowego przesyłania strumieniowego, aby narzędzie przeglądu odtwarzania wideo w widoku player.

![Pokaz wideo miniatur](images/ams-video-demo-view.PNG)

- Kopiuj **adres URL** na tym [pokaz usługi Azure Media Services](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) strony dla manifest adresu URL.

Klatek wideo (obrazy) należy użyć następujących obrazów:

![Miniatur wideo ramki 1](images/ams-video-frame-thumbnails-1.PNG) | ![Miniatur wideo ramki 2](images/ams-video-frame-thumbnails-2.PNG) | ![Miniatur wideo ramki 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[Ramka 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [Ramki 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [Ramka 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Dodaj nową **aplikacji konsoli (.NET Framework)** projektu do rozwiązania.

1. Nazwij projekt **VideoReviews**.

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

Dodaj następujące prywatne właściwości do przestrzeni nazw VideoReviews, klasy Program.

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

Dodaj następującą definicję metody do przestrzeni nazw VideoReviews, klasy Program.

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
> Klucz usługi moderatora zawartości ma żądań na drugi limit szybkości (RPS), a Jeśli przekroczysz limit zestawu SDK zgłasza wyjątek z kodem błędu 429. 
>
> Klucz warstwa bezpłatna ma limit szybkości jeden RPS.

## <a name="add-video-frames-to-the-video-review"></a>Dodaj klatek do przeglądu wideo

Dodaj klatek przeglądu wideo z **ContentModeratorClient.Reviews.AddVideoFrameUrl** (jeśli ramek wideo są obsługiwane w trybie online) lub **ContentModeratorClient.Reviews.AddVideoFrameStream** () Jeśli wideo ramek znajdują się lokalnie). Tego przewodnika Szybki Start założono ramek wideo są obsługiwane w trybie online, a więc używa **AddVideoFrameUrl**. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** ma następujące wymagane parametry:
1. Ciąg zawierający typ MIME, który powinien być "application/json".
1. Nazwę zespołu moderatora zawartości.
1. Identyfikator wideo przeglądu zwrócony przez **CreateVideoReviews**.
1. **IList<VideoFrameBodyItem>**  obiektu. Każdy **VideoFrameBodyItem** obiekt reprezentuje ramkę wideo.

**VideoFrameBodyItem** ma następujące właściwości:
- **Sygnatura czasowa**. Ciąg zawierający w sekundach czas wideo, z którego wykonano wideo ramki.
- **FrameImage**. Adres URL wideo ramki.
- **Metadane**. IList<VideoFrameBodyItemMetadataItem>. **VideoFrameBodyItemMetadataItem** jest po prostu pary klucza/wartości. Klucze obejmują:
- **reviewRecommended**. Wartość true, jeśli zaleca się przegląd człowieka wideo ramki.
- **adultScore**. Wartość z zakresu od 0 do 1, która ocenia ważność zawartość dla dorosłych w ramce wideo.
- **A** Wartość true, jeśli klip wideo zawiera zawartość dla dorosłych.
- **racyScore**. Wartość z zakresu od 0 do 1, która ocenia ważność luksusowych zawartości w ramce wideo.
- **r**. Wartość true, jeśli ramka wideo zawiera luksusowych zawartości.
- **ReviewerResultTags**. IList<VideoFrameBodyItemReviewerResultTagsItem>. **VideoFrameBodyItemReviewerResultTagsItem** jest po prostu pary klucza/wartości. Aplikacji można użyć tych tagów do organizowania klatek.

> [!NOTE]
> Ta opcja szybkiego startu generuje losowe wartości **adultScore** i **racyScore** właściwości. W aplikacji produkcyjnej, możesz uzyskać te wartości z [usługi wideo łagodzenia](video-moderation-api.md), wdrożonego jako usługi Azure Media.

Dodaj następujące definicje metody do przestrzeni nazw VideoReviews, klasy Program.

    <summary>
    /// Create a video frame to add to a video review after the video review is created.
    /// </summary>
    /// <param name="url">The URL of the video frame image.</param>
    /// <returns>The video frame.</returns>
    private static VideoFrameBodyItem CreateFrameToAddToReview(string url, string timestamp_seconds)
    {
        // We generate random "adult" and "racy" scores for the video frame.
        Random rand = new Random();

        var frame = new VideoFrameBodyItem
        {
            // The timestamp is measured in milliseconds. Convert from seconds.
            Timestamp = (int.Parse(timestamp_seconds) * 1000).ToString(),
            FrameImage = url,

            Metadata = new List<VideoFrameBodyItemMetadataItem>
            {
                new VideoFrameBodyItemMetadataItem("reviewRecommended", "true"),
                new VideoFrameBodyItemMetadataItem("adultScore", rand.NextDouble().ToString()),
                new VideoFrameBodyItemMetadataItem("a", "false"),
                new VideoFrameBodyItemMetadataItem("racyScore", rand.NextDouble().ToString()),
                new VideoFrameBodyItemMetadataItem("r", "false")
            },

            ReviewerResultTags = new List<VideoFrameBodyItemReviewerResultTagsItem>()
            {
                new VideoFrameBodyItemReviewerResultTagsItem("tag1", "value1")
            }
        };

        return frame;
    }

    /// <summary>
    /// Add a video frame to the indicated video review. For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    /// <param name="url">The URL of the video frame image.</param>
    static void AddFrame(ContentModeratorClient client, string review_id, string url, string timestamp_seconds)
    {
        Console.WriteLine("Adding a frame to the review with ID {0}.", review_id);

        var frames = new List<VideoFrameBodyItem>()
        {
            CreateFrameToAddToReview(url, timestamp_seconds)
        };
            
        client.Reviews.AddVideoFrameUrl("application/json", TeamName, review_id, frames);

        Thread.Sleep(throttleRate);
    

## <a name="get-video-frames-for-video-review"></a>Pobierz klatek do przeglądu wideo

Możesz też uzyskać klatek wideo Przegląd wideo z **ContentModeratorClient.Reviews.GetVideoFrames**. **GetVideoFrames** ma następujące wymagane parametry:
1. Nazwę zespołu moderatora zawartości.
1. Identyfikator wideo przeglądu zwrócony przez **CreateVideoReviews**.
1. Liczony od zera indeks pierwszej ramki wideo do pobrania.
1. Liczba ramek wideo do pobrania.

Dodaj następującą definicję metody do przestrzeni nazw VideoReviews, klasy Program.

    /// <summary>
    /// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    static void GetFrames(ContentModeratorClient client, string review_id)
    {
        Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

        Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0, Int32.MaxValue);
        Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

        Thread.Sleep(throttleRate);
    }

## <a name="get-video-review-information"></a>Pobierz informacje wideo przeglądu

Uzyskiwanie informacji o przegląd wideo z **ContentModeratorClient.Reviews.GetReview**. **GetReview** ma następujące wymagane parametry:
1. Nazwę zespołu moderatora zawartości.
1. Identyfikator wideo przeglądu zwrócony przez **CreateVideoReviews**.

Dodaj następującą definicję metody do przestrzeni nazw VideoReviews, klasy Program.

    /// <summary>
    /// Get the information for the indicated video review. For more information, see the reference API:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    private static void GetReview(ContentModeratorClient client, string review_id)
    {
        Console.WriteLine("Getting the status for the review with ID {0}.", review_id);

        var result = client.Reviews.GetReview(ModeratorHelper.Clients.TeamName, review_id);
        Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

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

Dodaj **Main** definicję metody do przestrzeni nazw VideoReviews, klasy Program. Na koniec Zamknij klasy Program i VideoReviews przestrzeni nazw.

    static void Main(string[] args)
    {
        using (ContentModeratorClient client = NewClient())
        {
            // Create a review with the content pointing to a streaming endpoint (manifest)
            var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
            string review_id = CreateReview(client, "review1", streamingcontent);

            var frame1_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG";
            var frame2_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG";
            var frame3_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG";

            // Add the frames from 17, 64, and 144 seconds.
            AddFrame(client, review_id, frame1_url, "17");
            AddFrame(client, review_id, frame2_url, "64");
            AddFrame(client, review_id, frame3_url, "144");

            // Get frames information and show
            GetFrames(client, review_id);
            GetReview(client, review_id);

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
    Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Getting frames for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    {
        "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
        "VideoFrames": [
        {
            "Timestamp": "17000",
            "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_17000.PNG",
            "Metadata": [
            {
                "Key": "reviewRecommended",
                "Value": "true"
            },
            {
                "Key": "adultScore",
                "Value": "0.808312381528463"
            },
            {
                "Key": "a",
                "Value": "false"
            },
            {
                "Key": "racyScore",
                "Value": "0.846378884206702"
            },
            {
                "Key": "r",
                "Value": "false"
            }
            ],
            "ReviewerResultTags": [
            {
                "Key": "tag1",
                "Value": "value1"
            }
        ]
        },
        {
            "Timestamp": "64000",
            "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_64000.PNG",
            "Metadata": [
            {
                "Key": "reviewRecommended",
                "Value": "true"
            },
            {
                "Key": "adultScore",
                "Value": "0.576078300166912"
            },
            {
                "Key": "a",
                "Value": "false"
            },
            {
                "Key": "racyScore",
                "Value": "0.244768953064815"
            },
            {
                "Key": "r",
                "Value": "false"
            }
            ],
            "ReviewerResultTags": [
            {
                "Key": "tag1",
                "Value": "value1"
            }
        ]
        },
        {
            "Timestamp": "144000",
            "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_144000.PNG",
            "Metadata": [
            {
                "Key": "reviewRecommended",
                "Value": "true"
            },
            {
                "Key": "adultScore",
                "Value": "0.664480847150311"
            },
            {
                "Key": "a",
                "Value": "false"
            },
            {
                "Key": "racyScore",
                "Value": "0.933817870418456"
            },
            {
                "Key": "r",
                "Value": "false"
            }
            ],
            "ReviewerResultTags": [
            {
                "Key": "tag1",
                "Value": "value1"
            }
            ]
        }
        ]
    }
    
    Getting the status for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    {
        "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
        "SubTeam": "public",
        "Status": "UnPublished",
        "ReviewerResultTags": [],
        "CreatedBy": "testreview6",
        "Metadata": [
        {
            "Key": "FrameCount",
            "Value": "3"
        }
        ],
        "Type": "Video",
        "Content": "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        "ContentId": "review1",
        "CallbackEndpoint": null
    }

    Publishing the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Open your Content Moderator Dashboard and select Review > Video to see the review.
    Press any key to close the application.

## <a name="check-out-your-video-review"></a>Sprawdź przeglądu wideo

Ponadto zobacz Przegląd wideo w sieci zawartości moderatora Sprawdź narzędzie konta na **Przejrzyj**>**wideo** ekranu.

![Przejrzyj wideo dla człowieka moderatorów](images/ams-video-review.PNG)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak dodać [łagodzenia wykaz](video-transcript-moderation-review-tutorial-dotnet.md) do przeglądu wideo. 

Zapoznaj się z szczegółowy samouczek dotyczący tworzenia [ukończyć rozwiązania wideo łagodzenia](video-transcript-moderation-review-tutorial-dotnet.md).

[Pobierz rozwiązania Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) to i inne elementy zawartości moderatora szybkiego startu dla platformy .NET.
