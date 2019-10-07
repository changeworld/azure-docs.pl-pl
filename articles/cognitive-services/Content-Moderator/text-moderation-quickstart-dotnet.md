---
title: 'Szybki Start: analizowanie zawartości tekstowej w C# Content moderator'
titleSuffix: Azure Cognitive Services
description: Jak analizować zawartość tekstową dla różnych niezwiązanych materiałów przy użyciu zestawu Content Moderator SDK dla platformy .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: sajagtap
ms.openlocfilehash: 69c190b9ce9e5a99d39a46c147f7159ace47e4f7
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001430"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-c"></a>Szybki Start: analizowanie zawartości tekstowej w poszukiwaniu niepożądanego materiałuC#

Ten artykuł zawiera informacje i przykłady kodu ułatwiające rozpoczęcie pracy z [zestawem SDK Content Moderator dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). Dowiesz się, jak wykonywać filtrowanie na podstawie warunkowe i klasyfikację zawartości tekstowej w celu moderowania potencjalnie niepożądanego materiału.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) . 

## <a name="prerequisites"></a>Wymagania wstępne
- Klucz subskrypcji Content Moderator. Postępuj zgodnie z instrukcjami w temacie [Tworzenie konta Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) , aby subskrybować Content moderator i uzyskać klucz.
- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/)

> [!NOTE]
> W tym przewodniku jest stosowana bezpłatna subskrypcja Content Moderator. Informacje o tym, co jest dostępne w poszczególnych warstwach subskrypcji, można znaleźć na stronie [Cennik i limity](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/) .

## <a name="create-the-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. W programie Visual Studio Utwórz nowy projekt **aplikacja konsoli (.NET Framework)** i nadaj mu nazwę **textmoderowanie**. 
1. Jeśli w rozwiązaniu istnieją inne projekty, wybierz je jako pojedynczy projekt startowy.
1. Pobierz wymagany pakiet NuGet. Kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz pozycję **Zarządzaj pakietami NuGet**. Następnie Znajdź i zainstaluj **pakiet `Microsoft.Azure.CognitiveServices.ContentModerator`** . Alternatywnie można uruchomić następujące polecenie w katalogu rozwiązania:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator
```

## <a name="add-text-moderation-code"></a>Dodaj kod moderowania tekstu

Następnie skopiujesz i wkleisz kod z tego przewodnika do projektu w celu zaimplementowania podstawowego scenariusza moderowania zawartości.

### <a name="include-namespaces"></a>Uwzględnij przestrzenie nazw

Dodaj następujące instrukcje `using` na początku pliku *program.cs* .

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>Tworzenie klienta Content Moderator

Dodaj następujący kod do pliku *program.cs* , aby utworzyć dostawcę klienta Content Moderator dla subskrypcji. Dodaj kod wraz z klasą **programu** , w tej samej przestrzeni nazw. Należy zaktualizować pola **AzureRegion** i **CMSubscriptionKey** wartościami identyfikatora regionu i klucza subskrypcji.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=54-77)]

### <a name="set-up-input-and-output-targets"></a>Konfigurowanie wejściowych i wyjściowych elementów docelowych

Dodaj następujące pola statyczne do klasy **program** w _program.cs_. Te pola określają pliki dla wejściowej zawartości tekstowej i wyjściowej zawartości JSON.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=15-19)]

Należy utworzyć plik wejściowy *textfile. txt* i zaktualizować jego ścieżkę (ścieżki odnoszą się do katalogu wykonywania). Otwórz _plik textfile. txt_ i Dodaj tekst do średnika. Ten przewodnik Szybki Start używa następującego przykładowego tekstu:

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="load-the-input-text"></a>Załaduj tekst wejściowy

Dodaj następujący kod do metody **Main** . Metoda **ScreenText** jest istotną operacją. Jego parametry określają, które operacje moderowania zawartości zostaną wykonane. W tym przykładzie metoda jest skonfigurowana do:
- Wykryj potencjalną nieżywotność w tekście.
- Normalizuj tekst i Autokorekta pisowni.
- Wykrywaj dane osobowe, takie jak numery telefonów US i BRYTYJSKIch, adresy e-mail i adresy pocztowe w Stanach Zjednoczonych.
- Używaj modeli opartych na uczeniu maszynowym do klasyfikowania tekstu do trzech kategorii.

Jeśli chcesz dowiedzieć się więcej na temat tego, co robią te operacje, Skorzystaj z linku w sekcji [następne kroki](#next-steps) .

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=23-48)]

## <a name="run-the-program"></a>Uruchom program

Program zapisze dane ciągu JSON w pliku _TextModerationOutput. txt_ . Przykładowy tekst używany w tym przewodniku szybki start daje następujące dane wyjściowe:

```json
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
        "CountryCode": "UK",
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
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start opracowano prostą aplikację platformy .NET, która używa usługi Content Moderator do zwrócenia istotnych informacji o danym przykładzie tekstu. Następnie Dowiedz się więcej na temat tego, co oznaczają różne flagi i klasyfikacje, aby określić, które dane są potrzebne, oraz sposób obsługi aplikacji przez aplikację.

> [!div class="nextstepaction"]
> [Przewodnik po moderowaniu tekstu](text-moderation-api.md)
