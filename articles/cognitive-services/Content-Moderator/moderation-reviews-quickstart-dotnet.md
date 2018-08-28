---
title: Azure moderatora zawartości — tworzenie przeglądami przy użyciu platformy .NET | Dokumentacja firmy Microsoft
description: Jak utworzyć przegląda przy użyciu zestawu SDK moderatora zawartości platformy Azure dla platformy .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/04/2018
ms.author: sajagtap
ms.openlocfilehash: 6a0ff48f4ea17f9c800f3e6c096df2492699f87a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347177"
---
# <a name="create-reviews-using-net"></a>Utwórz przeglądami przy użyciu platformy .NET

Ten artykuł zawiera informacje i przykłady ułatwiające rozpoczęcie pracy korzystać z zawartości moderatora zestawu SDK dla platformy .NET:
 
- Tworzenie zestawu przeglądami dla człowieka moderatorów
- Pobierz stan istniejących przeglądów dla człowieka moderatorów

Ogólnie rzecz biorąc zawartość przechodzi przez niektóre automatyczne łagodzenia przed zaplanowanym człowieka przeglądu. Ten artykuł opisuje tylko sposób tworzenia przeglądu dla człowieka łagodzenia. Dla scenariusza bardziej szczegółowy [łagodzenia zawartości Facebook](facebook-post-moderation.md) i [handlu elektronicznego katalogu łagodzenia](ecommerce-retail-catalog-moderation.md) samouczki.

W tym artykule przyjęto założenie, że znasz już program Visual Studio i C#.

## <a name="sign-up-for-content-moderator-services"></a>Załóż moderatora zawartości usług

Zanim użyjesz usługi moderatora zawartości za pośrednictwem interfejsu API REST lub zestawu SDK, należy klucza subskrypcji.
Zapoznaj się [szybkiego startu](quick-start.md) Aby dowiedzieć się, jak można uzyskać klucz.

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

## <a name="create-a-class-to-associate-internal-content-information-with-a-review-id"></a>Utwórz klasę, aby skojarzyć zawartości informacji wewnętrznych o identyfikatorze przeglądu

Dodaj następujące klasy **Program** klasy.
Ta klasa umożliwia kojarzenie identyfikator recenzji do sieci wewnętrznej Identyfikatora zawartości dla elementu.

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
> Klucz usługi moderatora zawartości ma żądań na drugi limit szybkości (RPS), a Jeśli przekroczysz limit zestawu SDK zgłasza wyjątek z kodem błędu 429. 
>
> Klucz warstwa bezpłatna ma limit szybkości jeden RPS.

#### <a name="add-the-following-constants-to-the-program-class-in-programcs"></a>Dodaj następujące ograniczenia, aby **Program** klasy w pliku Program.cs.
    
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
    /// <remarks>Relative paths are ralative the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

#### <a name="add-the-following-constants-and-static-fields-to-the-program-class-in-programcs"></a>Dodaj następujące ograniczenia i pola statyczne do **Program** klasy w pliku Program.cs.

Zaktualizować te wartości zawiera informacje specyficzne dla subskrypcji i zespołu.

> [!NOTE]
> Wartość stała TeamName nazwa użyta podczas tworzenia Twojej [narzędzie przeglądu zawartości moderatora](https://contentmoderator.cognitive.microsoft.com/) subskrypcji. Pobrać TeamName z **poświadczenia** sekcji **ustawienia** menu (koło zębate).
>
> Twoja nazwa zespołu jest wartość **identyfikator** w **interfejsu API** sekcji.

    /// <summary>
    /// The name of the team to assign the review to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Conent Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "{teamname}";

    /// <summary>
    /// The optional name of the subteam to assign the review to.
    /// </summary>
    private const string Subteam = null;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Revies show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "{callbackUrl}";

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

#### <a name="add-the-following-static-fields-to-the-program-class-in-programcs"></a>Dodaj następujące pola statycznego do **Program** klasy w pliku Program.cs.

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

## <a name="create-a-method-to-write-messages-to-the-log-file"></a>Tworzenie metody, które mają być zapisywane w pliku dziennika

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

## <a name="create-a-method-to-create-a-set-of-reviews"></a>Tworzenie metody, aby utworzyć zestaw przeglądów

Zwykle niektóre logiki biznesowej do identyfikacji obrazy, tekst, można też wideo, która musi zostać sprawdzone. Jednak w tym miejscu wystarczy użyć listy obrazów.

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

## <a name="create-a-method-to-get-the-status-of-existing-reviews"></a>Tworzenie metody, stan istniejących przeglądów

Dodaj następującą metodę do klasy **Program**. 

> [!Note]
> W praktyce, należy ustawić adres URL wywołania zwrotnego `CallbackEndpoint` do adresu URL, który otrzyma wyniki ręczne przeglądu (za pomocą żądania HTTP POST).
> Można zmodyfikować tę metodę, aby sprawdzić stan oczekującego recenzje.

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

## <a name="add-code-to-create-a-set-of-reviews-and-check-its-status"></a>Dodaj kod, aby utworzyć zbiór przeglądy i sprawdzenie jego stanu

Dodaj następujący kod do **Main** metody.

Ten kod symuluje wielu operacji wykonywanych w definiowanie i zarządzanie listy, a także korzystanie z listy obrazów ekranu. Funkcje rejestrowania umożliwiają wyświetlanie obiektów odpowiedzi generowane przez wywołania SDK do usługi moderatora zawartości.

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

Zostanie wyświetlony następujący przykładowe dane wyjściowe:

    Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.

Zaloguj się do zawartości moderatora Przegląd narzędzia, aby wyświetlić obraz oczekujące, zapoznaj się z **sc** etykieta ustawiona **true**. Zobacz też domyślnie **i** **r** tagów i niestandardowych znaczników, które zostały zdefiniowane w narzędziu przeglądu. 

Użyj **dalej** przycisk przesyłania.

![Przejrzyj obrazu dla człowieka moderatorów](images/moderation-reviews-quickstart-dotnet.PNG)

Naciśnij dowolny klawisz, aby kontynuować.

    Waiting 45 seconds for results to propagate.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.

    Press any key to exit...

## <a name="check-out-the-following-output-in-the-log-file"></a>Zapoznaj się z następujących danych wyjściowych w pliku dziennika.

> [!NOTE]
> W pliku danych wyjściowych ciągi "\{teamname}" i "\{callbackUrl}" wartości dla `TeamName` i `CallbackEndpoint` pola odpowiednio.

Przegląd identyfikatorów i obrazu zawartości, adresy URL są różne za każdym razem, uruchom aplikację i ukończyć po dokonaniu przeglądu, `reviewerResultTags` pole określa, jak recenzenta oznakowane elementu.

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

## <a name="your-callback-url-if-provided-receives-this-response"></a>Adres Url wywołania zwrotnego, jeśli zostanie podana, odbiera odpowiedź

Zostanie wyświetlony odpowiedzi, jak w następującym przykładzie:

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

[Pobierz rozwiązania Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) to i inne elementy zawartości moderatora szybkiego startu dla platformy .NET i rozpocząć pracę na integracją.
