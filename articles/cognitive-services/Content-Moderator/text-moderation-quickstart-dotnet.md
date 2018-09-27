---
title: Moderowanie tekstu — Content Moderator .NET
titlesuffix: Azure Cognitive Services
description: Jak Moderowanie tekstu, korzystanie z Content Moderator zestawu SDK dla platformy .NET
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: sajagtap
ms.openlocfilehash: 6db97231b2dc8905f1a3b3a6bc63580e9f371f84
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227269"
---
# <a name="moderate-text-using-net"></a>Moderowanie tekstu przy użyciu platformy .NET

Ten artykuł zawiera informacje i przykłady kodu, które ułatwią Ci rozpoczęcie korzystania z [Content Moderator zestawu SDK dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) do:

- Wykrywać potencjalne wulgaryzmy w tekście do filtrowania na podstawie terminu
- Modele oparte na nauce maszyny, aby użyć [klasyfikowania tekst](text-moderation-api.md#classification) na trzy kategorie.
- Wykryj identyfikowalne dane osobowe (PII), takich jak Stany Zjednoczone i Zjednoczone Królestwo numery telefonów, adresy e-mail i Stanów Zjednoczonych, adresów pocztowych.
- Normalizuj literówki tekstu i Autokorekty

W tym artykule założono, że znasz już program Visual Studio i języka C#.

## <a name="sign-up-for-content-moderator-services"></a>Załóż konto usługi Content Moderator

Zanim użyjesz usługi Content Moderator za pośrednictwem interfejsu API REST lub zestawu SDK, potrzebujesz klucza subskrypcji.
Zapoznaj się [Szybki Start](quick-start.md) Aby dowiedzieć się, jak można uzyskać klucz.

## <a name="create-your-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Dodaj nową **Aplikacja konsoli (.NET Framework)** projektu do rozwiązania.

   W przykładowym kodzie, nadaj projektowi nazwę **TextModeration**.

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

### <a name="initialize-application-specific-settings"></a>Inicjowanie ustawienia specyficzne dla aplikacji

Dodaj następujące pola statyczne do **Program** klasy w pliku Program.cs.

    /// <summary>
    /// The name of the file that contains the text to evaluate.
    /// </summary>
    /// <remarks>You will need to create an input file and update this path
    /// accordingly. Relative paths are relative to the execution directory.</remarks>
    private static string TextFile = "TextFile.txt";

    /// <summary>
    /// The name of the file to contain the output from the evaluation.
    /// </summary>
    /// <remarks>Relative paths are relative to the execution directory.</remarks>
    private static string OutputFile = "TextModerationOutput.txt";

Użyliśmy poniższy tekst do generowania danych wyjściowych dla tego przewodnika Szybki Start:

> [!NOTE]
> Nieprawidłowy numer ubezpieczenia społecznego w następujący przykładowy tekst jest celowe. Celem jest przekazać przykładowe dane wejściowe i wyjściowe formatu.

    Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
    These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
    0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).

## <a name="add-code-to-load-and-evaluate-the-input-text"></a>Dodaj kod, aby załadować i ocena tekst wejściowy

Dodaj następujący kod do **Main** metody.

    // Load the input text.
    string text = File.ReadAllText(TextFile);
    Console.WriteLine("Screening {0}", TextFile);

    text = text.Replace(System.Environment.NewLine, " ");

    // Save the moderation results to a file.
    using (StreamWriter outputWriter = new StreamWriter(OutputFile, false))
    {
        // Create a Content Moderator client and evaluate the text.
        using (var client = Clients.NewClient())
        {
            // Screen the input text: check for profanity, classify the text into three categories,
                // do autocorrect text, and check for personally identifying
                // information (PII)
                outputWriter.WriteLine("Autocorrect typos, check for matching terms, PII, and classify.");
                var screenResult =
                    client.TextModeration.ScreenText("eng", "text/plain", text, true, true, null, true);
                outputWriter.WriteLine(
                        JsonConvert.SerializeObject(screenResult, Formatting.Indented));
        }
        outputWriter.Flush();
        outputWriter.Close();
    }

> [!NOTE]
> Klucz usługi Content Moderator ma żądań na drugi limit szybkości (jednostek Uzależnionych), a Jeśli przekroczysz limit, zestaw SDK zgłasza wyjątek z kodem błędu 429.
>
> Jeśli za pomocą klucza warstwy bezpłatnej, liczba żądań jest ograniczona do jednego żądania na sekundę.

## <a name="run-the-program-and-review-the-output"></a>Uruchom program i przejrzyj dane wyjściowe

Przykładowe dane wyjściowe dla programu, gdyż zapisany w pliku dziennika to:

    Autocorrect typos, check for matching terms, PII, and classify.
    {
    "OriginalText": "\"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).\"",
    "NormalizedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
    "AutoCorrectedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
    "Misrepresentation": null,
    
    "Classification": {
            "Category1": {
            "Score": 1.5113095059859916E-06
            },
            "Category2": {
            "Score": 0.12747249007225037
            },
            "Category3": {
            "Score": 0.98799997568130493
            },
            "ReviewRecommended": true
    },
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
    },
    "PII": {
            "Email": [
                {
                "Detected": "abcdef@abcd.com",
                "SubType": "Regular",
                "Text": "abcdef@abcd.com",
                "Index": 33
                }
                ],
            "IPA": [
                {
                "SubType": "IPV4",
                "Text": "255.255.255.255",
                "Index": 73
                }
                ],
            "Phone": [
                {
                "CountryCode": "US",
                "Text": "6657789887",
                "Index": 57
                },
                {
                "CountryCode": "US",
                "Text": "870 608 4000",
                "Index": 211
                },
                {
                "CountryCode": "UK",
                "Text": "+44 870 608 4000",
                "Index": 207
                },
                {
                "CountryCode": "UK",
                "Text": "0344 800 2400",
                "Index": 227
                },
                {
            "   CountryCode": "UK",
                "Text": "0800 820 3300",
                "Index": 244
                }
                ],
             "Address": [{
                 "Text": "1 Microsoft Way, Redmond, WA 98052",
                 "Index": 89
                    }]
        },
    "Language": "eng",
    "Terms": [
        {
            "Index": 22,
            "OriginalIndex": 22,
            "ListId": 0,
            "Term": "crap"
        }
    ],
    "TrackingId": "9392c53c-d11a-441d-a874-eb2b93d978d3"
    }

## <a name="next-steps"></a>Kolejne kroki

Pobierz [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) i [rozwiązania Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dla tego programu oraz inne Przewodniki Szybki Start pakietu Content Moderator dla platformy .NET i Rozpocznij pracę nad integracją.
