---
title: 'Szybki Start: analizowanie zawartości tekstowej w C# Content moderator'
titleSuffix: Azure Cognitive Services
description: Jak analizować zawartość tekstową dla różnych niezwiązanych materiałów przy użyciu zestawu Content Moderator SDK dla platformy .NET
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 51202597aaee2940a764d0385d4d93c7409f4905
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935929"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-c"></a>Szybki start: analizowanie zawartości tekstowej pod kątem niepożądanego materiału w języku C#

Ten artykuł zawiera informacje i przykłady kodu, które pomogą Ci rozpocząć korzystanie z [zestawu SDK pakietu Content Moderator dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). Dowiesz się, jak wykonywać filtrowanie na podstawie warunkowe i klasyfikację zawartości tekstowej w celu moderowania potencjalnie niepożądanego materiału.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="prerequisites"></a>Wymagania wstępne
- Klucz subskrypcji pakietu Content Moderator. Postępuj zgodnie z instrukcjami w temacie [Tworzenie konta Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) , aby subskrybować Content Moderator. Następnie [Utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla adresu URL klucza i punktu końcowego, odpowiednio nazwane `CONTENT_MODERATOR_SUBSCRIPTION_KEY` i `CONTENT_MODERATOR_ENDPOINT`.
- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/)

> [!NOTE]
> Ten przewodnik korzysta z subskrypcji pakietu Content Moderator w warstwie Bezpłatna. Aby uzyskać informacje na temat funkcji oferowanych w poszczególnych warstwach subskrypcji, zobacz stronę [cennika i limitów](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/).

## <a name="create-the-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. W programie Visual Studio utwórz nowy projekt **Aplikacja konsoli (.NET Framework)** i nadaj jej nazwę **TextModeration**. 
1. Jeśli w rozwiązaniu istnieją inne projekty, wybierz ten projekt jako pojedynczy projekt startowy.
1. Pobierz wymagany pakiet NuGet. Kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz pozycję **Zarządzaj pakietami NuGet**. Następnie Znajdź i zainstaluj **pakiet `Microsoft.Azure.CognitiveServices.ContentModerator`** . Alternatywnie można uruchomić następujące polecenie w katalogu rozwiązania:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator
```

## <a name="add-text-moderation-code"></a>Dodawanie kodu moderowania tekstu

Następnie skopiujesz kod z tego przewodnika i wkleisz go do projektu, aby zaimplementować podstawowy scenariusz moderowania zawartości.

### <a name="include-namespaces"></a>Uwzględnianie przestrzeni nazw

Dodaj następujące instrukcje `using` na początku pliku *Program.cs*.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?name=snippet_using)]

### <a name="create-the-content-moderator-client"></a>Tworzenie klienta usług Content Moderator

Dodaj następujący kod do pliku *Program.cs*, aby utworzyć dostawcę klienta usługi Content Moderator dla subskrypcji. Dodaj klasę obok klasy **program** w tej samej przestrzeni nazw. Musisz zaktualizować pola **AzureBaseURL** i **CMSubscriptionKey** za pomocą wartości adresu URL punktu końcowego i klucza subskrypcji. Te informacje można znaleźć na karcie **Szybki Start** zasobu w Azure Portal.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?name=snippet_client)]

### <a name="set-up-input-and-output-targets"></a>Konfigurowanie wejściowych i wyjściowych elementów docelowych

Dodaj następujące pola statyczne do klasy **Program** w pliku _Program.cs_. Te pola określają pliki dla wejściowej zawartości tekstowej i wyjściowej zawartości JSON.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?name=snippet_fields)]

Należy utworzyć plik wejściowy *textfile. txt* i zaktualizować jego ścieżkę (ścieżki odnoszą się do katalogu wykonywania). Otwórz plik _TextFile.txt_ i dodaj tekst moderowania. Ten przewodnik Szybki start używa następującego przykładowego tekstu:

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="load-the-input-text"></a>Ładowanie tekstu wejściowego

Dodaj następujący kod do metody **Main**. Metoda **ScreenText** to niezbędna operacja. Jej parametry określają, które operacje moderowania zawartości zostaną wykonane. W tym przykładzie metoda została skonfigurowana do wykonywania następujących czynności:
- Wykrywanie potencjalnych przekleństw w tekście.
- Normalizowanie tekstu i automatyczne poprawianie błędów pisowni.
- Wykrywaj dane osobowe, takie jak numery telefonów US i BRYTYJSKIch, adresy e-mail i adresy pocztowe w Stanach Zjednoczonych.
- Używanie modeli opartych na uczeniu maszynowym do klasyfikowania tekstu w ramach trzech kategorii.

Jeśli chcesz dowiedzieć się więcej o sposobie działania operacji, kliknij link w sekcji [Następne kroki](#next-steps).

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?name=snippet_main)]

## <a name="run-the-program"></a>Uruchamianie programu

Program zapisze dane ciągu JSON w pliku _TextModerationOutput.txt_. Przykładowy tekst używany w tym przewodniku Szybki start powoduje wygenerowanie następujących danych wyjściowych:

```json
Autocorrect typos, check for matching terms, check for personal data, and classify.
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

W tym przewodniku Szybki start utworzono prostą aplikację platformy .NET, która zwraca odpowiednie informacje o danym tekście przykładowym dzięki użyciu usługi Content Moderator. Następnie dowiedz się więcej na temat znaczenia różnych flag i klasyfikacji, aby móc podejmować decyzje dotyczące potrzebnych danych oraz sposobu ich obsługi przez aplikację.

> [!div class="nextstepaction"]
> [Przewodnik dotyczący moderowania tekstu](text-moderation-api.md)
