---
title: Tworzenie recenzji wideo przy użyciu platformy .NET — moderator zawartości
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje i przykłady kodu, które pomogą Ci szybko rozpocząć korzystanie z SDK moderatora zawartości z C# do tworzenia recenzji wideo.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 7130ed43183d64b00f8f5ef1697b9a3b456ad396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72931672"
---
# <a name="create-video-reviews-using-net"></a>Tworzenie recenzji wideo przy użyciu platformy .NET

Ten artykuł zawiera informacje i przykłady kodu, które pomogą Ci szybko rozpocząć korzystanie [z SDK moderatora zawartości z C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) do:

- Tworzenie recenzji wideo dla moderatorów ludzkich
- Dodawanie ramek do recenzji
- Pobierz ramki do recenzji
- Uzyskaj status i szczegóły recenzji
- Opublikuj recenzję

## <a name="prerequisites"></a>Wymagania wstępne

- Zaloguj się lub utwórz konto w witrynie narzędzia Content Moderator [Review.](https://contentmoderator.cognitive.microsoft.com/)
- W tym artykule założono, że film został [moderowany (zobacz szybki start)](video-moderation-api.md) i masz dane odpowiedzi. Jest potrzebny do tworzenia recenzji opartych na ramkach dla moderatorów ludzkich.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Upewnij się, że Twój klucz interfejsu API umożliwia wywołanie interfejsu API przeglądu w celu utworzenia przeglądu.

Po wykonaniu poprzednich kroków możesz mieć dwa klucze usługi Content Moderator, jeśli wykonywanie kroków rozpoczęto w witrynie Azure Portal.

Jeśli planujesz użyć klucza interfejsu API platformy Azure w przykładzie zestawu SDK, wykonaj kroki opisane w sekcji [Używanie klucza platformy Azure przy użyciu interfejsu API przeglądu](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis), aby umożliwić aplikacji wywołanie interfejsu API przeglądu i tworzenie przeglądów.

Jeśli używasz bezpłatnej wersji próbnej klucza wygenerowanej przez narzędzie do przeprowadzania przeglądów, narzędzie to będzie już znało klucz i dlatego dodatkowe kroki nie są wymagane.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Przygotowanie filmu i klatek wideo do recenzji

Wideo i przykładowe klatki wideo do sprawdzenia muszą być publikowane online, ponieważ potrzebne są ich adresy URL.

> [!NOTE]
> Program używa ręcznie zapisanych zrzutów ekranu z filmu z losowymi wynikami dla dorosłych / racy, aby zilustrować użycie api przeglądu. W rzeczywistych sytuacjach można użyć [wyjścia moderowania wideo](video-moderation-api.md#run-the-program-and-review-the-output) do tworzenia obrazów i przypisywania wyników. 

W przypadku filmu potrzebny jest punkt końcowy przesyłania strumieniowego, aby narzędzie do przeglądania odtwarzał wideo w widoku odtwarzacza.

![Miniatura wersji demonstracyjnej wideo](images/ams-video-demo-view.PNG)

- Skopiuj **adres URL** na tej stronie [demonstracyjnej usługi Azure Media Services](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) dla adresu URL manifestu.

W przypadku klatek wideo (obrazów) użyj następujących obrazów:

![Miniatura klatki wideo 1](images/ams-video-frame-thumbnails-1.PNG) | ![Miniatura klatki wideo 2](images/ams-video-frame-thumbnails-2.PNG) | ![Miniatura klatki wideo 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[Ramka 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [Ramka 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [Ramka 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Dodaj nowy projekt **Aplikacja konsoli (.NET Framework)** do rozwiązania.

1. Nazwij projekt **VideoReviews**.

1. Wybierz ten projekt jako pojedynczy projekt startowy rozwiązania.

### <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Zainstaluj następujące pakiety NuGet dla projektu TermLists.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizowanie programu za pomocą instrukcji

Zmodyfikuj program przy użyciu instrukcji w następujący sposób.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Dodawanie właściwości prywatnych

Dodaj następujące właściwości prywatne do obszaru nazw **VideoReviews**, klasa **Program**. Zaktualizuj `AzureEndpoint` pola i `CMSubscriptionKey` wartości adresu URL punktu końcowego i klucza subskrypcji. Można je znaleźć na karcie **Szybki start** zasobu w witrynie Azure portal.


```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Enter a valid endpoint URL
        /// <summary>
        /// The endpoint URL of your subscription
        /// </summary>
        private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

        // NOTE: Enter a valid subscription key.
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
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Tworzenie obiektu klienta moderatora zawartości

Dodaj następującą definicję metody do obszaru nazw **VideoReviews**, klasa **Program**.

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
        Endpoint = AzureEndpoint
    };
}
```

## <a name="create-a-video-review"></a>Tworzenie recenzji wideo

Utwórz recenzję wideo za pomocą **ContentModeratorClient.Reviews.CreateVideoReviews**. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** ma następujące wymagane parametry:
1. Ciąg, który zawiera typ MIME, który powinien być "application/json". 
1. Nazwa zespołu moderatora zawartości.
1. **\<IList CreateVideoReviewsBodyItem>** obiektu. Każdy **CreateVideoReviewsBodyItem** obiekt reprezentuje przegląd wideo. Ten przewodnik Szybki start tworzy jedną recenzję naraz.

**CreateVideoReviewsBodyItem** ma kilka właściwości. Można co najmniej ustawić następujące właściwości:
- **Zawartość**. Adres URL filmu, który ma zostać zweryfikowany.
- **ContentId**. Identyfikator do przypisania do recenzji wideo.
- **Status**. Ustaw wartość "Nieopublikowane". Jeśli go nie ustawisz, domyślnie jest to "Oczekujące", co oznacza, że recenzja wideo jest publikowana i oczekująca na przegląd ludzki. Po opublikowaniu recenzji wideo nie można już dodawać do niej klatek wideo, transkrypcji ani wyniku moderowania transkrypcji.

> [!NOTE]
> **CreateVideoReviews** zwraca ciąg\<IList>. Każdy z tych ciągów zawiera identyfikator do przeglądu wideo. Te identyfikatory są identyfikatorami GUID i nie są takie same jak wartość **ContentId** właściwości. 

Dodaj następującą definicję metody do obszaru nazw VideoReviews, class Program.

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
> Klucz usług Content Moderator ma limit liczby żądań na sekundę (RPS), a w razie przekroczenia tego limitu zestaw SDK zgłasza wyjątek z kodem błędu 429.
>
> Limit klucza warstwy bezpłatnej wynosi 1 RPS.

## <a name="add-video-frames-to-the-video-review"></a>Dodawanie klatek wideo do recenzji wideo

Dodawania klatek wideo do recenzji wideo za pomocą **contentmoderatorclient.Reviews.AddVideoFrameUrl** (jeśli klatki wideo są hostowane w trybie online) lub **ContentModeratorClient.Reviews.AddVideoFrameStream** (jeśli klatki wideo są hostowane lokalnie). Ten przewodnik Szybki start zakłada, że klatki wideo są hostowane w trybie online, dlatego używa **AddVideoFrameUrl**. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** ma następujące wymagane parametry:
1. Ciąg, który zawiera typ MIME, który powinien być "application/json".
1. Nazwa zespołu moderatora zawartości.
1. Identyfikator recenzji wideo zwrócony przez **CreateVideoReviews**.
1. **IList\<VideoFrameBodyItem>** obiektu. Każdy **obiekt VideoFrameBodyItem** reprezentuje klatkę wideo.

**VideoFrameBodyItem** ma następujące właściwości:
- **Sygnatura czasowa**. Ciąg zawierający w sekundach czas w filmie, z którego pobrano klatkę wideo.
- **FrameImage**. Adres URL klatki wideo.
- **Metadane**. IList\<VideoFrameBodyItemMetadataItemItem>. **VideoFrameBodyItemMetadataItem** jest po prostu parą klucz/wartość. Prawidłowe klucze obejmują:
- **przeglądZakady.** Prawda, jeśli zaleca się przegląd ludzkiej klatki wideo.
- **adultScore**. Wartość od 0 do 1, która ocenia ważność treści dla dorosłych w ramce wideo.
- **a**. Prawda, jeśli film zawiera treści dla dorosłych.
- **racyScore**. Wartość od 0 do 1, która ocenia ważność zawartości rasistowskiej w ramce wideo.
- **r**. Prawda, jeśli klatka wideo zawiera treści rasistowskie.
- **ReviewerResultTags**. IList\<VideoFrameBodyItemReviewerResultTagsItem>. **VideoFrameBodyItemReviewerResultTagsItem** jest po prostu parą klucz/wartość. Aplikacja może używać tych tagów do organizowania klatek wideo.

> [!NOTE]
> Ten szybki start generuje losowe wartości dla **adultScore** i **racyScore** właściwości. W aplikacji produkcyjnej można uzyskać te wartości z [usługi moderowania wideo,](video-moderation-api.md)wdrożony jako usługa Azure Media Service.

Dodaj następujące definicje metod do obszaru nazw VideoReviews, klasa Program.

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

## <a name="get-video-frames-for-video-review"></a>Pobierz klatki wideo do recenzji wideo

Możesz uzyskać klatki wideo do przeglądu wideo z **ContentModeratorClient.Reviews.GetVideoFrames**. **GetVideoFrames** ma następujące wymagane parametry:
1. Nazwa zespołu moderatora zawartości.
1. Identyfikator recenzji wideo zwrócony przez **CreateVideoReviews**.
1. Indeks oparty na wartości zero pierwszej klatki wideo do uzyskania.
1. Liczba klatek wideo do uzyskania.

Dodaj następującą definicję metody do obszaru nazw VideoReviews, class Program.

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

## <a name="get-video-review-information"></a>Uzyskaj informacje o recenzji wideo

Otrzymasz informacje dotyczące przeglądu wideo z **ContentModeratorClient.Reviews.GetReview**. **GetReview** ma następujące wymagane parametry:
1. Nazwa zespołu moderatora zawartości.
1. Identyfikator recenzji wideo zwrócony przez **CreateVideoReviews**.

Dodaj następującą definicję metody do obszaru nazw VideoReviews, class Program.

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

## <a name="publish-video-review"></a>Publikowanie recenzji wideo

Publikujesz recenzję wideo z **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** ma następujące wymagane parametry:
1. Nazwa zespołu moderatora zawartości.
1. Identyfikator recenzji wideo zwrócony przez **CreateVideoReviews**.

Dodaj następującą definicję metody do obszaru nazw VideoReviews, class Program.

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

Dodaj definicję metody **głównej** do obszaru nazw VideoReviews, klasa Program. Na koniec zamknij Program klasy i VideoReviews przestrzeni nazw.

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
Po uruchomieniu aplikacji zostanie wyświetlenie danych wyjściowych w następujących wierszach:

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

## <a name="check-out-your-video-review"></a>Obejrzyj recenzję wideo

Na koniec zobaczysz recenzję wideo na koncie narzędzia do recenzji moderatora zawartości na ekranie **Recenzja**>**wideo.**

![Przegląd wideo dla moderatorów ludzkich](images/ams-video-review.PNG)

## <a name="next-steps"></a>Następne kroki

Pobierz [pakiet Szybkifikatek .NET SDK moderatora zawartości](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) i [rozwiązanie programu Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dla tego i innych przewodników content moderator dla platformy .NET.

Dowiedz się, jak dodać [moderowanie transkrypcji](video-transcript-moderation-review-tutorial-dotnet.md) do recenzji wideo. 

Zapoznaj się ze szczegółowym samouczkiem, jak opracować [kompletne rozwiązanie do moderowania wideo](video-transcript-moderation-review-tutorial-dotnet.md).
