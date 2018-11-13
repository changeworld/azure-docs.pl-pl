---
title: 'Szybki start: analizowanie zawartości obrazu pod kątem niepożądanego materiału w języku C#'
titlesuffix: Azure Cognitive Services
description: Opis sposobu analizowania zawartości obrazu pod kątem różnych rodzajów niepożądanego materiału przy użyciu zestawu SDK pakietu Content Moderator dla platformy .NET
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: sajagtap
ms.openlocfilehash: 8f407a42ab2e1538193206dec1955257a5f9940a
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51005499"
---
# <a name="quickstart-analyze-image-content-for-objectionable-material-in-c"></a>Szybki start: analizowanie zawartości obrazu pod kątem niepożądanego materiału w języku C#

Ten artykuł zawiera informacje i przykłady kodu, które pomogą Ci rozpocząć korzystanie z [zestawu SDK pakietu Content Moderator dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). Dowiesz się, jak wyszukiwać zawartość dla dorosłych lub erotyczną, tekst możliwy do wyodrębnienia i ludzkie twarze w celu moderowania potencjalnie niepożądanego materiału.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="prerequisites"></a>Wymagania wstępne

- Klucz subskrypcji pakietu Content Moderator. Aby subskrybować pakiet Content Moderator i uzyskać klucz, postępuj zgodnie z instrukcjami z tematu [Create a Cognitive Services account (Tworzenie konta usług Cognitive Services)](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/)


> [!NOTE]
> Ten przewodnik korzysta z subskrypcji pakietu Content Moderator w warstwie Bezpłatna. Aby uzyskać informacje na temat funkcji oferowanych w poszczególnych warstwach subskrypcji, zobacz stronę [cennika i limitów](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/).

## <a name="create-the-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. W programie Visual Studio utwórz nowy projekt **Aplikacja konsoli (.NET Framework)** i nadaj jej nazwę **ImageModeration**. 
1. Jeśli w rozwiązaniu istnieją inne projekty, wybierz ten projekt jako pojedynczy projekt startowy.
1. Pobierz wymagane pakiety NuGet. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz pozycję **Zarządzaj pakietami NuGet**, a następnie znajdź i zainstaluj następujące pakiety:
    - Microsoft.Azure.CognitiveServices.ContentModerator
    - Microsoft.Rest.ClientRuntime
    - Newtonsoft.Json

## <a name="add-image-moderation-code"></a>Dodawanie kodu moderowania obrazów

Następnie skopiujesz kod z tego przewodnika i wkleisz go do projektu, aby zaimplementować podstawowy scenariusz moderowania zawartości.

### <a name="include-namespaces"></a>Uwzględnianie przestrzeni nazw

Dodaj następujące instrukcje `using` na początku pliku *Program.cs*.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Tworzenie klienta usługi Content Moderator

Dodaj następujący kod do pliku *Program.cs*, aby utworzyć dostawcę klienta usługi Content Moderator dla subskrypcji. W tej samej przestrzeni nazw dodaj kod wraz z klasą **Program**. Następnie trzeba będzie zaktualizować pola **AzureRegion** i **CMSubscriptionKey** przy użyciu wartości identyfikatora regionu i klucza subskrypcji.

```csharp
// Wraps the creation and configuration of a Content Moderator client.
public static class Clients
{
    // The region/location for your Content Moderator account, 
    // for example, westus.
    private static readonly string AzureRegion = "YOUR API REGION";

    // The base URL fragment for Content Moderator calls.
    private static readonly string AzureBaseURL =
        $"https://{AzureRegion}.api.cognitive.microsoft.com";

    // Your Content Moderator subscription key.
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    // Returns a new Content Moderator client for your subscription.
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureBaseURL;
        return client;
    }
}
```

### <a name="set-up-input-and-output-targets"></a>Konfigurowanie wejściowych i wyjściowych elementów docelowych

Dodaj następujące pola statyczne do klasy **Program** w pliku _Program.cs_. Spowoduje to określenie plików na potrzeby zawartości obrazów wejściowych i zawartości wyjściowego kodu JSON.

```csharp
//The name of the file that contains the image URLs to evaluate.
private static string ImageUrlFile = "ImageFiles.txt";

///The name of the file to contain the output from the evaluation.
private static string OutputFile = "ModerationOutput.json";
```

Musisz utworzyć plik wejściowy *_ImageFiles.txt*, a następnie odpowiednio zaktualizować jego ścieżkę (ścieżki względne są względne wobec katalogu wykonywania). Otwórz plik _ImageFiles.txt_ i dodaj adresy URL obrazów do moderowania. Ten przewodnik Szybki start używa następujących adresów URL jako przykładowych danych wejściowych.
```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="create-a-class-to-handle-results"></a>Tworzenie klasy do obsługi wyników

Dodaj następujący kod do pliku *Program.cs* wraz z klasą **Program** w tej samej przestrzeni nazw. Użyjesz wystąpienia tej klasy, aby zarejestrować wyniki moderowania poszczególnych przeglądanych obrazów.

```csharp
// Contains the image moderation results for an image, 
// including text and face detection results.
public class EvaluationData
{
    // The URL of the evaluated image.
    public string ImageUrl;

    // The image moderation results.
    public Evaluate ImageModeration;

    // The text detection results.
    public OCR TextDetection;

    // The face detection results;
    public FoundFaces FaceDetection;
}
```

### <a name="define-the-image-evaluation-method"></a>Definiowanie metody oceny obrazu

Dodaj następującą metodę do klasy **Program**. Ta metoda ocenia pojedynczy obraz na trzy różne sposoby i zwraca wyniki oceny. Jeśli chcesz dowiedzieć się więcej o sposobie działania poszczególnych operacji, kliknij link w sekcji [Następne kroki](#next-steps).

```csharp
// Evaluates an image using the Image Moderation APIs.
private static EvaluationData EvaluateImage(
  ContentModeratorClient client, string imageUrl)
{
    var url = new BodyModel("URL", imageUrl.Trim());

    var imageData = new EvaluationData();

    imageData.ImageUrl = url.Value;

    // Evaluate for adult and racy content.
    imageData.ImageModeration =
        client.ImageModeration.EvaluateUrlInput("application/json", url, true);
    Thread.Sleep(1000);

    // Detect and extract text.
    imageData.TextDetection =
        client.ImageModeration.OCRUrlInput("eng", "application/json", url, true);
    Thread.Sleep(1000);

    // Detect faces.
    imageData.FaceDetection =
        client.ImageModeration.FindFacesUrlInput("application/json", url, true);
    Thread.Sleep(1000);

    return imageData;
}
```

### <a name="load-the-input-images"></a>Ładowanie obrazów wejściowych

Dodaj następujący kod do metody **Main** w klasie **Program**. Spowoduje to utworzenie programu do pobierania danych oceny dla każdego adresu URL obrazu w pliku wejściowym. Te dane są następnie zapisywane w pojedynczym pliku wyjściowym.

```csharp
// Create an object to store the image moderation results.
List<EvaluationData> evaluationData = new List<EvaluationData>();

// Create an instance of the Content Moderator API wrapper.
using (var client = Clients.NewClient())
{
    // Read image URLs from the input file and evaluate each one.
    using (StreamReader inputReader = new StreamReader(ImageUrlFile))
    {
        while (!inputReader.EndOfStream)
        {
            string line = inputReader.ReadLine().Trim();
            if (line != String.Empty)
            {
                EvaluationData imageData = EvaluateImage(client, line);
                evaluationData.Add(imageData);
            }
        }
    }
}

// Save the moderation results to a file.
using (StreamWriter outputWriter = new StreamWriter(OutputFile, false))
{
    outputWriter.WriteLine(JsonConvert.SerializeObject(
        evaluationData, Formatting.Indented));

    outputWriter.Flush();
    outputWriter.Close();
}
```

## <a name="run-the-program"></a>Uruchamianie programu

Program zapisze dane ciągu JSON w pliku _ModerationOutput.json_. Przykładowe obrazy używane w tym przewodniku Szybki start powodują wygenerowanie następujących danych wyjściowych. Należy pamiętać, że każdy obraz ma inne sekcje `ImageModeration`, `FaceDetection` i `TextDetection`, co odpowiada trzem wywołaniom interfejsu API w ramach metody **EvaluateImage**.

```json
[{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg",
  "ImageModeration": {
    "cacheID": "7733c303-3b95-4710-a41e-7a322ae81a15_636488005858745661",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_687c356d-0f00-4aeb-ae5f-c7555af80247",
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "116"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "12"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_814fa162-c5d6-4ca6-997b-30ed0686ca83",
    "cacheId": "3fb69496-c64b-4de9-affd-6dd6d23f3e78_636488005876558920",
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_a2c40dbe-609d-4eb8-b01c-9988388804ea",
    "cacheId": "e4c0b500-ea8e-4a31-8fb3-35f98c4fbd65_636488005889528303",
    "result": false,
    "count": 0,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "11"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "faces": []
  }
},
{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png",
  "ImageModeration": {
    "cacheID": "b4866aa2-5e69-44ed-806a-f9a5d618c8ae_636488005930693926",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_fdce5510-f689-4791-b081-c2ad54dcfe78",
    "adultClassificationScore": 0.0035635426174849272,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.021369094029068947,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "109"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "46"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_08a4bc19-6010-41bb-a440-a77278e167d8",
    "cacheId": "28b37471-41b3-4f79-bd23-965498bcff51_636488005950851288",
    "language": "eng",
    "text": "",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_40f2ce07-14ba-47cd-ba09-58b557a89854",
    "cacheId": "ec9c1be3-99b7-4bd9-8bc4-dc958c74459f_636488005964914299",
    "result": true,
    "count": 6,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "60"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "faces": [
      {
        "bottom": 598,
        "left": 44,
        "right": 268,
        "top": 374
      },
      {
        "bottom": 620,
        "left": 308,
        "right": 532,
        "top": 396
      },
      {
        "bottom": 575,
        "left": 594,
        "right": 773,
        "top": 396
      },
      {
        "bottom": 563,
        "left": 812,
        "right": 955,
        "top": 420
      },
      {
        "bottom": 611,
        "left": 972,
        "right": 1151,
        "top": 432
      },
      {
        "bottom": 510,
        "left": 1232,
        "right": 1456,
        "top": 286
      }
    ]
  }
}]
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono prostą aplikację platformy .NET, która zwraca odpowiednie informacje o danym obrazie przykładowym dzięki użyciu usługi Content Moderator. Następnie dowiedz się więcej na temat znaczenia różnych flag i klasyfikacji, aby móc podejmować decyzje dotyczące potrzebnych danych oraz sposobu ich obsługi przez aplikację.

> [!div class="nextstepaction"]
> [Przewodnik dotyczący moderowania obrazów](image-moderation-api.md)
