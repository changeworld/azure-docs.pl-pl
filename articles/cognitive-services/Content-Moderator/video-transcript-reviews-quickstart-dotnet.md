---
title: Tworzenie recenzji transkrypcji wideo przy użyciu platformy .NET — moderator zawartości
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak tworzyć przeglądy transkrypcji wideo przy użyciu sdk moderatora zawartości usług Azure Cognitive Services dla platformy .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: b2d763454b86570b57a16fb9ae2107a2a2bcd23d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73744379"
---
# <a name="create-video-transcript-reviews-using-net"></a>Tworzenie recenzji transkrypcji wideo przy użyciu platformy .NET

Ten artykuł zawiera informacje i przykłady kodu, które pomogą Ci szybko rozpocząć korzystanie [z SDK moderatora zawartości z C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) do:

- Tworzenie recenzji wideo dla moderatorów ludzkich
- Dodawanie moderowanej transkrypcji do recenzji
- Opublikuj recenzję

## <a name="prerequisites"></a>Wymagania wstępne

- Zaloguj się lub utwórz konto w witrynie narzędzia Content Moderator [Review,](https://contentmoderator.cognitive.microsoft.com/) jeśli jeszcze tego nie zrobiłeś.
- W tym artykule założono, że [film został moderowany](video-moderation-api.md) i [utworzył recenzję wideo](video-reviews-quickstart-dotnet.md) w narzędziu do oceny do podejmowania decyzji przez człowieka. Teraz chcesz dodać moderowane transkrypcje wideo w narzędziu do recenzji.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Upewnij się, że klucz interfejsu API może wywołać interfejs API przeglądu (tworzenie zadań)

Po wykonaniu poprzednich kroków możesz mieć dwa klucze usługi Content Moderator, jeśli wykonywanie kroków rozpoczęto w witrynie Azure Portal.

Jeśli planujesz użyć klucza interfejsu API platformy Azure w przykładzie zestawu SDK, wykonaj kroki opisane w sekcji [Używanie klucza platformy Azure przy użyciu interfejsu API przeglądu](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis), aby umożliwić aplikacji wywołanie interfejsu API przeglądu i tworzenie przeglądów.

Jeśli używasz bezpłatnej wersji próbnej klucza wygenerowanej przez narzędzie do przeprowadzania przeglądów, narzędzie to będzie już znało klucz i dlatego dodatkowe kroki nie są wymagane.

## <a name="prepare-your-video-for-review"></a>Przygotowanie filmu do recenzji

Dodaj transkrypcję do recenzji wideo. Film musi zostać opublikowany online. Potrzebny jest jego punkt końcowy przesyłania strumieniowego. Punkt końcowy przesyłania strumieniowego umożliwia odtwarzaczowi wideo narzędzia do recenzji odtwarzanie wideo.

![Miniatura wersji demonstracyjnej wideo](images/ams-video-demo-view.PNG)

- Skopiuj **adres URL** na tej stronie [demonstracyjnej usługi Azure Media Services](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) dla adresu URL manifestu.

## <a name="create-your-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Dodaj nowy projekt **Aplikacja konsoli (.NET Framework)** do rozwiązania.

1. Nazwij projekt **VideoTranscriptReviews**.

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

Dodaj następujące właściwości prywatne do obszaru nazw **VideoTranscriptReviews**, class **Program**. Zaktualizuj `AzureEndpoint` pola i `CMSubscriptionKey` wartości adresu URL punktu końcowego i klucza subskrypcji. Można je znaleźć na karcie **Szybki start** zasobu w witrynie Azure portal.

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

Dodaj następującą definicję metody do obszaru nazw VideoTranscriptReviews, class Program.

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
> Klucz usługi Content Moderator ma limit szybkości wyrażany w żądaniach na sekundę (RPS). Po przekroczeniu tego limitu zestaw SDK zgłasza wyjątek z kodem błędu 429.
>
> Limit klucza warstwy bezpłatnej wynosi 1 RPS.

## <a name="add-transcript-to-video-review"></a>Dodawanie transkrypcji do recenzji wideo

Do recenzji wideo można dodać do recenzji wideo za pomocą **contentmoderatorclient.Reviews.AddVideoTranscript**. **AddVideoTranscript** ma następujące wymagane parametry:
1. Identyfikator zespołu moderatora zawartości.
1. Identyfikator recenzji wideo zwrócony przez **CreateVideoReviews**.
1. Stream **Stream** obiektu, który zawiera transkrypcję.

Transkrypcja musi być w formacie WebVTT. Aby uzyskać więcej informacji, zobacz [WebVTT: Web Video Text Tracks Format](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> Program używa przykładowej transkrypcji w formacie VTT. W rozwiązaniu w świecie rzeczywistym, można użyć usługi Azure Media Indexer do [generowania transkrypcji](https://docs.microsoft.com/azure/media-services/media-services-index-content) z wideo.

Dodaj następującą definicję metody do obszaru nazw VideotranscriptReviews, class Program.

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

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Dodawanie wyniku moderowania transkrypcji do recenzji wideo

Oprócz dodawania transkrypcji do recenzji wideo, można również dodać wynik moderowania tej transkrypcji. Można to zrobić z **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** ma następujące wymagane parametry:
1. Ciąg, który zawiera typ MIME, który powinien być "application/json". 
1. Nazwa zespołu moderatora zawartości.
1. Identyfikator recenzji wideo zwrócony przez **CreateVideoReviews**.
1. IList\<TranscriptModerationBodyItem>. A **TranscriptModerationBodyItem** ma następujące właściwości:
1. **Warunki**. IList\<TranscriptModerationBodyItemTermsItemItem>. A **TranscriptModerationBodyItemTermsItem** ma następujące właściwości:
1. **Indeks**. Indeks zerowy terminu.
1. **Termin**. Ciąg, który zawiera termin.
1. **Sygnatura czasowa**. Ciąg, który zawiera w sekundach czas w transkrypcji, w którym znajdują się warunki.

Transkrypcja musi być w formacie WebVTT. Aby uzyskać więcej informacji, zobacz [WebVTT: Web Video Text Tracks Format](https://www.w3.org/TR/webvtt1/).

Dodaj następującą definicję metody do obszaru nazw VideoTranscriptReviews, class Program. Ta metoda przesyła transkrypcję do **ContentModeratorClient.TextModeration.ScreenText** metody. Przekłada również wynik na IList\<TranscriptModerationBodyItem> i przesyła do **AddVideoTranscriptModerationResult**.

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

Dodaj definicję metody **głównej** do obszaru nazw VideoTranscriptReviews, klasa Program. Na koniec zamknij Program klasy i VideoTranscriptReviews przestrzeni nazw.

> [!NOTE]
> Program używa przykładowej transkrypcji w formacie VTT. W rozwiązaniu w świecie rzeczywistym, można użyć usługi Azure Media Indexer do [generowania transkrypcji](https://docs.microsoft.com/azure/media-services/media-services-index-content) z wideo.

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

Po uruchomieniu aplikacji zostanie wyświetlenie danych wyjściowych w następujących wierszach:

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>Przejdź do recenzji transkrypcji wideo

Przejdź do recenzji transkrypcji wideo w narzędziu do recenzji moderatora zawartości na ekranie **Przeglądanie**>**transkrypcji** **wideo.**>

Są widoczne następujące funkcje:
- Dwa dodane wiersze transkrypcji
- Wulgaryzmy znalezione i wyróżnione przez usługę moderowania tekstu
- Wybranie tekstu transkrypcji powoduje rozpoczęcie filmu od tego sygnatury czasowej

![Przegląd transkrypcji wideo dla moderatorów ludzkich](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Następne kroki

Pobierz [pakiet Szybkifikatek .NET SDK moderatora zawartości](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) i [rozwiązanie programu Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dla tego i innych przewodników content moderator dla platformy .NET.

Dowiedz się, jak generować [recenzje wideo](video-reviews-quickstart-dotnet.md) w narzędziu do recenzji.

Zapoznaj się ze szczegółowym samouczkiem, jak opracować [kompletne rozwiązanie do moderowania wideo](video-transcript-moderation-review-tutorial-dotnet.md).
