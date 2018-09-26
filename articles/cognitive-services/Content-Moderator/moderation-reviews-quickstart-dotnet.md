---
title: Usługa Azure Content Moderator — tworzenie przeglądy przy użyciu platformy .NET | Dokumentacja firmy Microsoft
description: Jak utworzyć przeglądy dla platformy .NET przy użyciu zestawu SDK usługi Azure Content Moderator
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/10/2018
ms.author: sajagtap
ms.openlocfilehash: c5f301e7ed15100c39f0af77942147275b966ed9
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47180777"
---
# <a name="create-reviews-using-net"></a>Utwórz przeglądy przy użyciu platformy .NET

Ten artykuł zawiera informacje i przykłady kodu, które ułatwią Ci rozpoczęcie korzystania z [Content Moderator zestawu SDK dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) do:
 
- Utwórz zestaw przeglądy dla moderatorów ludzi
- Pobierz stan istniejących przeglądy dla moderatorów ludzi

Ogólnie rzecz biorąc zawartość przechodzi przez kilka zalet zautomatyzowanego moderowania przed zaplanowanym przeglądu przez ludzi. W tym artykule opisano tylko sposób tworzenia Przegląd dla człowieka moderowania. Bardziej kompletny scenariusz można znaleźć [moderowanie zawartości serwisu Facebook](facebook-post-moderation.md) i [Moderowanie katalogu handlu elektronicznego](ecommerce-retail-catalog-moderation.md) samouczków.

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

            client.Endpoint = AzureBaseURL;
            return client;
        }
    }

## <a name="create-a-class-to-associate-internal-content-information-with-a-review-id"></a>Utwórz klasę, aby skojarzyć wewnętrzne informacje o zawartości o identyfikatorze przeglądu

Dodaj poniższą klasę do **Program** klasy.
Klasa jest używana do kojarzenia identyfikator przeglądu do wewnętrznego Identyfikatora zawartości dla elementu.

    /// <summary>
    /// Associates the review ID (assigned by the service) to the internal
    /// content ID of the item.
    /// </summary>
    public class ReviewItem
    {
        /// <summary>
        /// The media type for the item to review.
        /// </summary>
        public string Type;

        /// <summary>
        /// The URL of the item to review.
        /// </summary>
        public string Url;

        /// <summary>
        /// The internal content ID for the item to review.
        /// </summary>
        public string ContentId;

        /// <summary>
        /// The ID that the service assigned to the review.
        /// </summary>
        public string ReviewId;
    }

### <a name="initialize-application-specific-settings"></a>Inicjowanie ustawienia specyficzne dla aplikacji

> [!NOTE]
> Klucz usługi Content Moderator ma żądań na drugi limit szybkości (jednostek Uzależnionych), a Jeśli przekroczysz limit, zestaw SDK zgłasza wyjątek z kodem błędu 429. 
>
> Klucz w warstwie bezpłatna obowiązuje limit szybkości jeden RPS.

#### <a name="add-the-following-constants-to-the-program-class-in-programcs"></a>Dodaj następujące stałe do **Program** klasy w pliku Program.cs.
    
    /// <summary>
    /// The minimum amount of time, in milliseconds, to wait between calls
    /// to the Image List API.
    /// </summary>
    private const int throttleRate = 3000;

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const double latencyDelay = 45;

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are relative to the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

#### <a name="add-the-following-constants-and-static-fields-to-the-program-class-in-programcs"></a>Dodaj następujące stałe i pola statyczne do **Program** klasy w pliku Program.cs.

Zaktualizuj te wartości, które zawiera informacje specyficzne dla Twojej subskrypcji i zespół.

> [!NOTE]
> Stała TeamName jest ustawiona na nazwę używaną podczas tworzenia usługi [narzędzie do przeglądu usługi Content Moderator](https://contentmoderator.cognitive.microsoft.com/) subskrypcji. Możesz pobrać TeamName z **poświadczenia** sekcji **ustawienia** menu (koło zębate).
>
> Twoja nazwa zespołu jest wartość **identyfikator** pole **API** sekcji.

    /// <summary>
    /// The name of the team to assign the review to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Content Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "YOUR REVIEW TEAM ID";

    /// <summary>
    /// The optional name of the subteam to assign the review to.
    /// </summary>
    private const string Subteam = null;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Reviews show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "YOUR API ENDPOINT";

    /// <summary>
    /// The media type for the item to review.
    /// </summary>
    /// <remarks>Valid values are "image", "text", and "video".</remarks>
    private const string MediaType = "image";

    /// <summary>
    /// The URLs of the images to create review jobs for.
    /// </summary>
    private static readonly string[] ImageUrls = new string[] {
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg"
        // add more if you want
    };

    /// <summary>
    /// The metadata key to initially add to each review item.
    /// </summary>
    private const string MetadataKey = "sc";

    /// <summary>
    /// The metadata value to initially add to each review item.
    /// </summary>
    private const string MetadataValue = "true";

#### <a name="add-the-following-static-fields-to-the-program-class-in-programcs"></a>Dodaj następujące pola statyczne do **Program** klasy w pliku Program.cs.

Użyj tych pól do śledzenia stanu aplikacji.

    /// <summary>
    /// A static reference to the text writer to use for logging.
    /// </summary>
    private static TextWriter writer;

    /// <summary>
    /// The cached review information, associating a local content ID
    /// to the created review ID for each item.
    /// </summary>
    private static List<ReviewItem> reviewItems =
        new List<ReviewItem>();

## <a name="create-a-method-to-write-messages-to-the-log-file"></a>Utwórz metodę, aby zapisywać komunikaty w pliku dziennika

Dodaj następującą metodę do klasy **Program**. 

    /// <summary>
    /// Writes a message to the log file, and optionally to the console.
    /// </summary>
    /// <param name="message">The message.</param>
    /// <param name="echo">if set to <c>true</c>, write the message to the console.</param>
    private static void WriteLine(string message = null, bool echo = false)
    {
        writer.WriteLine(message ?? String.Empty);

        if (echo)
        {
            Console.WriteLine(message ?? String.Empty);
        }
    }

## <a name="create-a-method-to-create-a-set-of-reviews"></a>Utwórz metodę, aby utworzyć zestaw przeglądów

Zwykle mają niektóre logiki biznesowej do identyfikowania przychodzących obrazów, tekstu, lub wideo, które wymaga przejrzenia. Jednak w tym miejscu po prostu użyć listy obrazów.

Dodaj następującą metodę do klasy **Program**.

    /// <summary>
    /// Create the reviews using the fixed list of images.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    private static void CreateReviews(ContentModeratorClient client)
    {
        WriteLine(null, true);
        WriteLine("Creating reviews for the following images:", true);

        // Create the structure to hold the request body information.
        List<CreateReviewBodyItem> requestInfo =
            new List<CreateReviewBodyItem>();

        // Create some standard metadata to add to each item.
        List<CreateReviewBodyItemMetadataItem> metadata =
            new List<CreateReviewBodyItemMetadataItem>(
            new CreateReviewBodyItemMetadataItem[] {
                new CreateReviewBodyItemMetadataItem(
                    MetadataKey, MetadataValue)
            });

        // Populate the request body information and the initial cached review information.
        for (int i = 0; i < ImageUrls.Length; i++)
        {
            // Cache the local information with which to create the review.
            var itemInfo = new ReviewItem()
            {
                Type = MediaType,
                ContentId = i.ToString(),
                Url = ImageUrls[i],
                ReviewId = null
            };

            WriteLine($" - {itemInfo.Url}; with id = {itemInfo.ContentId}.", true);

            // Add the item informaton to the request information.
            requestInfo.Add(new CreateReviewBodyItem(
                itemInfo.Type, itemInfo.Url, itemInfo.ContentId,
                CallbackEndpoint, metadata));

            // Cache the review creation information.
            reviewItems.Add(itemInfo);
        }

        var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync(
            "application/json", TeamName, requestInfo);

        // Update the local cache to associate the created review IDs with
        // the associated content.
        var reviewIds = reviewResponse.Result.Body;
        for (int i = 0; i < reviewIds.Count; i++)
        {
            Program.reviewItems[i].ReviewId = reviewIds[i];
        }

        WriteLine(JsonConvert.SerializeObject(
        reviewIds, Formatting.Indented));

        Thread.Sleep(throttleRate);
    }

## <a name="create-a-method-to-get-the-status-of-existing-reviews"></a>Utwórz metodę, aby wyświetlić stan istniejących przeglądów

Dodaj następującą metodę do klasy **Program**. 

> [!Note]
> W praktyce należy ustawić adres URL wywołania zwrotnego `CallbackEndpoint` do adresu URL, który będzie otrzymywać wyniki ręcznego przeglądu (za pośrednictwem żądania HTTP POST).
> Możesz zmodyfikować tę metodę, aby sprawdzić stan oczekujących przeglądów.

    /// <summary>
    /// Gets the review details from the server.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    private static void GetReviewDetails(ContentModeratorClient client)
    {
        WriteLine(null, true);
        WriteLine("Getting review details:", true);
        foreach (var item in reviewItems)
        {
            var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(
                TeamName, item.ReviewId);

            WriteLine(
                $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                $"{reviewDetail.Result.Body.Status}.", true);
            WriteLine(JsonConvert.SerializeObject(
                reviewDetail.Result.Body, Formatting.Indented));

            Thread.Sleep(throttleRate);
        }
    }

## <a name="add-code-to-create-a-set-of-reviews-and-check-its-status"></a>Dodaj kod, aby utworzyć zestaw przeglądów i sprawdź jej stan

Dodaj następujący kod do **Main** metody.

Ten kod symuluje wielu operacji wykonywanych w definiowanie i zarządzanie listy, a także korzystanie z listy obrazów do ekranu. Funkcje rejestrowania umożliwiają wyświetlanie obiektów odpowiedzi generowane przez wywołania SDK do usługi Content Moderator.

    using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
    {
        writer = outputWriter;
        using (var client = Clients.NewClient())
        {
            CreateReviews(client);
            GetReviewDetails(client);

            Console.WriteLine();
            Console.WriteLine("Perform manual reviews on the Content Moderator site.");
            Console.WriteLine("Then, press any key to continue.");
            Console.ReadKey();

            Console.WriteLine();
            Console.WriteLine($"Waiting {latencyDelay} seconds for results to propigate.");
            Thread.Sleep(latencyDelay * 1000);

            GetReviewDetails(client);
        }

        writer = null;
        outputWriter.Flush();
        outputWriter.Close();
    }

    Console.WriteLine();
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();

## <a name="run-the-program-and-review-the-output"></a>Uruchom program i przejrzyj dane wyjściowe

Zobaczysz następujące przykładowe dane wyjściowe:

    Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.

Zaloguj się do pakietu Content Moderator Przegląd narzędzia, aby zobaczyć oczekujące obrazu, zapoznaj się z **sc** etykieta ustawiona **true**. Zobacz też domyślnie **i** **r** tagów i niestandardowych znaczników, które zostały zdefiniowane w narzędziu przeglądu. 

Użyj **dalej** przycisk Prześlij.

![Przegląd obrazu dla moderatorów ludzi](images/moderation-reviews-quickstart-dotnet.PNG)

Naciśnij dowolny klawisz, aby kontynuować.

    Waiting 45 seconds for results to propagate.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.

    Press any key to exit...

## <a name="check-out-the-following-output-in-the-log-file"></a>Sprawdź następujące dane wyjściowe w pliku dziennika.

> [!NOTE]
> W pliku danych wyjściowych, ciągi "\{teamname}" i "\{callbackUrl}" wartości dla odzwierciedlenia `TeamName` i `CallbackEndpoint` pola, odpowiednio.

Przegląd identyfikatorów i obrazu zawartości, adresy URL są różne za każdym razem, uruchom aplikację i ukończyć po przeglądu, `reviewerResultTags` pole określa, jak recenzenta tagiem elementu.

    Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.
    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Pending",
        "reviewerResultTags": [],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
            "key": "a",
            "value": "False"
        },
        {
            "key": "r",
            "value": "True"
        },
        {
            "key": "sc",
            "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

## <a name="your-callback-url-if-provided-receives-this-response"></a>Adres Url wywołania zwrotnego, jeśli podana, odbiera odpowiedź

Zostanie wyświetlony odpowiedzi, jak w poniższym przykładzie:

    {
        "ReviewId": "201801i48a2937e679a41c7966e838c92f5e649",
        "ModifiedOn": "2018-01-06T05:04:40.5525865Z",
        "ModifiedBy": "yourusername",
        "CallBackType": "Review",
        "ContentId": "0",
        "ContentType": "Image",
        "Metadata": {
            "sc": "true"
            },
        "ReviewerResultTags": {
            "a": "False",
            "r": "False",
        }
    }


## <a name="next-steps"></a>Kolejne kroki

Pobierz [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) i [rozwiązania Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dla tego programu oraz inne Przewodniki Szybki Start pakietu Content Moderator dla platformy .NET i Rozpocznij pracę nad integracją.
