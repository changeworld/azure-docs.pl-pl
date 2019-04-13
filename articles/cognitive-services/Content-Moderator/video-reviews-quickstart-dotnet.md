---
title: Utwórz przeglądy wideo przy użyciu platformy .NET — Content Moderator
titlesuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje i przykłady kodu, które ułatwią Ci szybkie rozpoczęcie korzystania z zawartości SDK Moderator C# do tworzenia przeglądów wideo.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/19/2019
ms.author: sajagtap
ms.openlocfilehash: e4dd7299907168bb50ac8ebdf90b381c0bac01f2
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527374"
---
# <a name="create-video-reviews-using-net"></a>Utwórz przeglądy wideo przy użyciu platformy .NET

Ten artykuł zawiera informacje i przykłady kodu, aby pomóc Ci szybko rozpocząć pracę, przy użyciu [Content Moderator zestawu SDK przy użyciu języka C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) do:

- Utwórz Przegląd wideo dla moderatorów ludzi
- Dodaj ramek do przeglądu
- Pobierz ramki dla przeglądu
- Pobierz stan i szczegóły przeglądu
- Publikowanie przeglądu

## <a name="prerequisites"></a>Wymagania wstępne

- Zaloguj się lub Utwórz konto w pakiecie Content Moderator [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/) lokacji.
- W tym artykule założono, że masz [moderowane wideo (zobacz Przewodnik Szybki Start)](video-moderation-api.md) i mieć dane odpowiedzi. Będą potrzebne do tworzenia opartych na klatkach przeglądy dla moderatorów ludzi.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Upewnij się, że Twój klucz interfejsu API umożliwia wywołanie interfejsu API przeglądu w celu utworzenia przeglądu.

Po wykonaniu poprzednich kroków możesz mieć dwa klucze usługi Content Moderator, jeśli wykonywanie kroków rozpoczęto w witrynie Azure Portal.

Jeśli planujesz użyć klucza interfejsu API platformy Azure w przykładzie zestawu SDK, wykonaj kroki opisane w sekcji [Używanie klucza platformy Azure przy użyciu interfejsu API przeglądu](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis), aby umożliwić aplikacji wywołanie interfejsu API przeglądu i tworzenie przeglądów.

Jeśli używasz bezpłatnej wersji próbnej klucza wygenerowanej przez narzędzie do przeprowadzania przeglądów, narzędzie to będzie już znało klucz i dlatego dodatkowe kroki nie są wymagane.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Przygotowanie wideo i klatki wideo do przeglądu

Wideo i przykładowe klatki wideo do przeglądania muszą być publikowane online, ponieważ trzeba ich adresy URL.

> [!NOTE]
> Używana jest ręcznie zapisane zrzuty ekranu z wideo losowe zawartości dla dorosłych/pikantnej wyniki do pokazują korzystanie z interfejsu API przeglądu. W sytuacji rzeczywistej, użyjesz [Moderowanie filmów wideo w danych wyjściowych](video-moderation-api.md#run-the-program-and-review-the-output) do tworzenia obrazów i przypisać wyniki. 

Wideo wymaga punktu końcowego przesyłania strumieniowego, aby narzędzie do przeglądu jest odtwarzany film wideo w widoku odtwarzacza.

![Pokaz wideo, miniatury](images/ams-video-demo-view.PNG)

- Kopiuj **adresu URL** na tym [pokaz usługi Azure Media Services](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) stronę o adresie URL manifestu.

Klatki wideo (obrazy) można użyć poniższych ilustracjach:

![Miniatura wideo ramka 1](images/ams-video-frame-thumbnails-1.PNG) | ![Miniatura wideo ramka 2](images/ams-video-frame-thumbnails-2.PNG) | ![Miniatura wideo ramka 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[Ramka 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [Ramka 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [Ramka 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Dodaj nowy projekt **Aplikacja konsoli (.NET Framework)** do rozwiązania.

1. Nadaj projektowi nazwę **VideoReviews**.

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

Dodaj następujące właściwości prywatnej do przestrzeni nazw VideoReviews, klasy programu.

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

Dodaj następującą definicję metody do przestrzeni nazw VideoReviews, klasy programu.

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
> **CreateVideoReviews** zwraca interfejs IList<string>. Każdy z tych ciągów zawiera identyfikator wideo przeglądu. Te identyfikatory są identyfikatory GUID i nie są takie same jak wartość **ContentId** właściwości. 

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
> Klucz usługi Content Moderator ma limit liczby żądań na sekundę (RPS), a w razie przekroczenia tego limitu zestaw SDK zgłasza wyjątek z kodem błędu 429.
>
> Limit klucza warstwy bezpłatnej wynosi 1 RPS.

## <a name="add-video-frames-to-the-video-review"></a>Dodaj klatki wideo na przegląd wideo

Dodaj klatki wideo na przegląd wideo za pomocą **ContentModeratorClient.Reviews.AddVideoFrameUrl** (jeśli jest to Twoja klatki wideo są hostowanego w trybie online) lub **ContentModeratorClient.Reviews.AddVideoFrameStream** () Jeśli Twoje klatki wideo są hostowane lokalnie). Ten przewodnik Szybki Start założono ramek wideo hostowanego w trybie online, a więc używa **AddVideoFrameUrl**. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** ma następujące wymagane parametry:
1. Ciąg zawierający typ MIME, który powinien być "application/json".
1. Nazwa zespołu usługi Content Moderator.
1. Identyfikator przeglądu wideo zwrócony przez **CreateVideoReviews**.
1. **IList\<VideoFrameBodyItem >** obiektu. Każdy **VideoFrameBodyItem** obiekt reprezentuje klatki wideo.

**VideoFrameBodyItem** ma następujące właściwości:
- **Sygnatura czasowa**. Ciąg, który zawiera, w sekundach czas w trakcie filmu wideo, z którego wykonano klatki wideo.
- **FrameImage**. Adres URL wideo ramki.
- **Metadane**. An IList\<VideoFrameBodyItemMetadataItem>. **VideoFrameBodyItemMetadataItem** jest po prostu pary klucz/wartość. Klucze obejmują:
- **reviewRecommended**. Wartość true, jeśli przeglądu przez ludzi klatki wideo jest zalecane.
- **adultScore**. Wartość z zakresu od 0 do 1, która ocenia ważności zawartości dla dorosłych w ramce wideo.
- **A** Wartość true, jeśli film wideo zawiera treści dla dorosłych.
- **racyScore**. Wartość z zakresu od 0 do 1, która ocenia ważności zawartości erotycznej w ramce wideo.
- **r**. Wartość true, jeśli ramka wideo zawiera zawartości erotycznej.
- **ReviewerResultTags**. An IList\<VideoFrameBodyItemReviewerResultTagsItem>. **VideoFrameBodyItemReviewerResultTagsItem** jest po prostu pary klucz/wartość. Aplikacja może użyć tych tagów do organizowania klatki wideo.

> [!NOTE]
> Ten przewodnik Szybki Start generuje losowe wartości dla **adultScore** i **racyScore** właściwości. W przypadku aplikacji produkcyjnej można uzyskać te wartości z [usługa moderowania klipów wideo](video-moderation-api.md)wdrożone jako usługa multimediów Azure.

Dodaj następujące definicje metody do przestrzeni nazw VideoReviews, klasy programu.

```csharp
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
```

```csharp
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
```

## <a name="get-video-frames-for-video-review"></a>Pobierz klatki wideo do przeglądu wideo

Możesz też uzyskać klatki wideo recenzję wideo za pomocą **ContentModeratorClient.Reviews.GetVideoFrames**. **GetVideoFrames** ma następujące wymagane parametry:
1. Nazwa zespołu usługi Content Moderator.
1. Identyfikator przeglądu wideo zwrócony przez **CreateVideoReviews**.
1. Liczony od zera indeks pierwszego klatki wideo, aby uzyskać.
1. Liczba ramek wideo do pobrania.

Dodaj następującą definicję metody do przestrzeni nazw VideoReviews, klasy programu.

```csharp
/// <summary>
/// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
static void GetFrames(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

    Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="get-video-review-information"></a>Uzyskaj informacje Przegląd wideo

Możesz uzyskać informacje o przegląd wideo za pomocą **ContentModeratorClient.Reviews.GetReview**. **GetReview** ma następujące wymagane parametry:
1. Nazwa zespołu usługi Content Moderator.
1. Identyfikator przeglądu wideo zwrócony przez **CreateVideoReviews**.

Dodaj następującą definicję metody do przestrzeni nazw VideoReviews, klasy programu.

```csharp
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

Dodaj **Main** definicję metody do przestrzeni nazw VideoReviews, klasy programu. Na koniec można zamknąć, klasę Program i VideoReviews przestrzeni nazw.

```csharp
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
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>Uruchamianie programu i przeglądanie danych wyjściowych
Po uruchomieniu aplikacji, zobaczysz dane wyjściowe w następujących wierszach:

```json
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
```

## <a name="check-out-your-video-review"></a>Zapoznaj się z wideo zapoznania się z nimi

Na koniec zostanie wyświetlony przegląd wideo w usługi Content Moderator, zapoznaj się uwagę narzędzia na **Przejrzyj**>**wideo** ekranu.

![Przegląd wideo dla moderatorów ludzi](images/ams-video-review.PNG)

## <a name="next-steps"></a>Kolejne kroki

Pobierz [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) i [rozwiązania Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dla tego programu oraz inne Przewodniki Szybki Start pakietu Content Moderator dla platformy .NET.

Dowiedz się, jak dodać [Moderowanie transkrypcji](video-transcript-moderation-review-tutorial-dotnet.md) do przeglądu wideo. 

Zapoznaj się z szczegółowy samouczek dotyczący sposobu tworzenia [kompletne rozwiązanie Moderowanie filmów wideo](video-transcript-moderation-review-tutorial-dotnet.md).
