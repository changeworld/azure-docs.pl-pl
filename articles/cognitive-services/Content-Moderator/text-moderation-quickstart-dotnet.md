---
title: Azure moderatora zawartości — umiarkowane tekstu przy użyciu platformy .NET | Dokumentacja firmy Microsoft
description: Jak średnie tekst przy użyciu zestawu SDK moderatora zawartości platformy Azure dla platformy .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/04/2018
ms.author: sajagtap
ms.openlocfilehash: 238d086e87b0e52f0887af5c4db58e8f72796b49
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347745"
---
# <a name="moderate-text-using-net"></a>Umiarkowany tekstu przy użyciu platformy .NET

Ten artykuł zawiera informacje i przykłady ułatwiające rozpoczęcie pracy korzystać z zawartości moderatora zestawu SDK dla platformy .NET:
- Wykrywanie potencjalnych niestosownych wyrażeń w tekście z filtrowanie na podstawie terminu
- Użyj machine learning na podstawie modeli do [klasyfikowania tekst](text-moderation-api.md#classification) na trzy kategorie.
- Wykryj dane osobowe lub informacje takie jak USA i Wielka Brytania numery telefonów, adresy e-mail i Stanów Zjednoczonych, adresów pocztowych.
- Normalizuj tekst i Autokorekty błędów pisowni

W tym artykule przyjęto założenie, że znasz już program Visual Studio i C#.

## <a name="sign-up-for-content-moderator-services"></a>Załóż moderatora zawartości usług

Zanim użyjesz usługi moderatora zawartości za pośrednictwem interfejsu API REST lub zestawu SDK, należy klucza subskrypcji.
Zapoznaj się [szybkiego startu](quick-start.md) Aby dowiedzieć się, jak można uzyskać klucz.

## <a name="create-your-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Dodaj nową **aplikacji konsoli (.NET Framework)** projektu do rozwiązania.

   W przykładowym kodzie nazwij projekt **TextModeration**.

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


### <a name="initialize-application-specific-settings"></a>Inicjowanie ustawienia specyficzne dla aplikacji

Dodaj następujące pola statycznego do **Program** klasy w pliku Program.cs.

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

Użyliśmy następującego tekstu do generowania danych wyjściowych dla tego przewodnika Szybki Start:

> [!NOTE]
> Nieprawidłowy numer ubezpieczenia społecznego w następujący przykładowy tekst jest zamierzone. Ma na celu przedstawienia przykładowe dane wejściowe i wyjściowe formatu.

    Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
    These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
    0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).

## <a name="add-code-to-load-and-evaluate-the-input-text"></a>Dodaj kod, aby załadować i ocena wejściowego tekstu

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
> Klucz usługi moderatora zawartości ma żądań na drugi limit szybkości (RPS), a Jeśli przekroczysz limit zestawu SDK zgłasza wyjątek z kodem błędu 429.
>
> Klucz warstwa bezpłatna ma limit szybkości jeden RPS.

## <a name="run-the-program-and-review-the-output"></a>Uruchom program i przejrzyj dane wyjściowe

Przykładowe dane wyjściowe w przypadku programu podczas zapisywania do pliku dziennika to:

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

[Pobierz rozwiązania Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) to i inne elementy zawartości moderatora szybkiego startu dla platformy .NET i rozpocząć pracę na integracją.
