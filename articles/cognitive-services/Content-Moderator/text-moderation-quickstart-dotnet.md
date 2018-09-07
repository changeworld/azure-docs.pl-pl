---
title: Usługa Azure Content Moderator — Moderowanie tekstu przy użyciu platformy .NET | Dokumentacja firmy Microsoft
description: Jak Moderowanie tekstu dla platformy .NET przy użyciu zestawu SDK usługi Azure Content Moderator
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/04/2018
ms.author: sajagtap
ms.openlocfilehash: 87ed816077d2c742223a0350851cdf2f0c5653f6
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44023042"
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

1. Dodaj odwołanie do **ModeratorHelper** projektu zestawu, który został utworzony w [pakietu Content Moderator klienta pomocnika Przewodnik Szybki Start](content-moderator-helper-quickstart-dotnet.md).

### <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Zainstaluj następujące pakiety NuGet:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizacja programu za pomocą instrukcji

Modyfikowanie programu za pomocą instrukcji.

    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;


### <a name="initialize-application-specific-settings"></a>Inicjowanie ustawienia specyficzne dla aplikacji

Dodaj następujące pola statyczne do **Program** klasy w pliku Program.cs.

    /// <summary>
    /// The name of the file that contains the text to evaluate.
    /// </summary>
    /// <remarks>You will need to create an input file and update this path
    /// accordingly. Relative paths are ralative the execution directory.</remarks>
    private static string TextFile = "TextFile.txt";

    /// <summary>
    /// The name of the file to contain the output from the evaluation.
    /// </summary>
    /// <remarks>Relative paths are ralative the execution directory.</remarks>
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
    text = text.Replace(System.Environment.NewLine, " ");

    // Save the moderation results to a file.
    using (StreamWriter outputWriter = new StreamWriter(OutputFile, false))
    {
        // Create a Content Moderator client and evaluate the text.
        using (var client = Clients.NewClient())
        {
            // Screen the input text: check for profanity, classify the text into three categories
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
