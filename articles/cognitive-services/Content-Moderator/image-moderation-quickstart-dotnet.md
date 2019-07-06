---
title: 'Szybki start: analizowanie obrazów pod kątem niepożądanej zawartości w języku C# — Content Moderator'
titlesuffix: Azure Cognitive Services
description: Jak analizować zawartość obrazu dla różnych materiałów Kodeksem przy użyciu zestawu SDK Moderator zawartości dla platformy .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: sajagtap
ms.openlocfilehash: d9a28dcd4af68cf9c00eb3d338c4bd83c8d89ecc
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604118"
---
# <a name="quickstart-analyze-images-for-objectionable-content-in-c"></a>Szybki start: Analizuj obrazy niepożądanej zawartości wC#

Ten artykuł zawiera informacje i przykłady kodu, które pomogą Ci rozpocząć korzystanie z [zestawu SDK pakietu Content Moderator dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). Dowiesz się, jak skanować pod kątem zawartości dla dorosłych lub erotycznej, możliwy do wyodrębnienia tekstu i ludzkich twarzy w celu Moderowanie potencjalnie groźnego materiału.

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

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>Tworzenie klienta usługi Content Moderator

Dodaj następujący kod do pliku *Program.cs*, aby utworzyć dostawcę klienta usługi Content Moderator dla subskrypcji. W tej samej przestrzeni nazw dodaj kod wraz z klasą **Program**. Następnie trzeba będzie zaktualizować pola **AzureRegion** i **CMSubscriptionKey** przy użyciu wartości identyfikatora regionu i klucza subskrypcji.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=84-107)]


### <a name="set-up-input-and-output-targets"></a>Konfigurowanie wejściowych i wyjściowych elementów docelowych

Dodaj następujące pola statyczne do klasy **Program** w pliku _Program.cs_. Te pola określ pliki zawartości obrazu wejściowego, a dane wyjściowe JSON zawartości.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=49-53)]

Musisz utworzyć plik wejściowy *_ImageFiles.txt*, a następnie odpowiednio zaktualizować jego ścieżkę (ścieżki względne są względne wobec katalogu wykonywania). Otwórz plik _ImageFiles.txt_ i dodaj adresy URL obrazów do moderowania. Ten przewodnik Szybki start używa następujących adresów URL jako przykładowych danych wejściowych.

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="create-a-class-to-handle-results"></a>Tworzenie klasy do obsługi wyników

Dodaj następujący kod do pliku *Program.cs* wraz z klasą **Program** w tej samej przestrzeni nazw. Użyjesz wystąpienia tej klasy, aby zarejestrować wyniki moderowania poszczególnych przeglądanych obrazów.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=109-124)]


### <a name="define-the-image-evaluation-method"></a>Definiowanie metody oceny obrazu

Dodaj następującą metodę do klasy **Program**. Ta metoda ocenia pojedynczy obraz na trzy różne sposoby i zwraca wyniki oceny. Jeśli chcesz dowiedzieć się więcej o sposobie działania poszczególnych operacji, kliknij link w sekcji [Następne kroki](#next-steps).

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=55-81)]

### <a name="load-the-input-images"></a>Ładowanie obrazów wejściowych

Dodaj następujący kod do metody **Main** w klasie **Program**. Ten kod konfiguruje program do pobierania danych oceny dla każdej adres URL obrazu w pliku wejściowym. Te dane są następnie zapisywane w pojedynczym pliku wyjściowym.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=17-46)]

## <a name="run-the-program"></a>Uruchamianie programu

Program zapisze dane ciągu JSON w pliku _ModerationOutput.json_. Przykładowe obrazy używane w tym przewodniku Szybki start powodują wygenerowanie następujących danych wyjściowych. Każdy obraz ma różne sekcje dla `ImageModeration`, `FaceDetection`, i `TextDetection`, odnoszą się do trzech wywołań interfejsu API w sieci **EvaluateImage** metody.

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

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start utworzono prostą aplikację platformy .NET, która zwraca odpowiednie informacje o danym obrazie przykładowym dzięki użyciu usługi Content Moderator. Następnie dowiedz się więcej na temat znaczenia różnych flag i klasyfikacji, aby móc podejmować decyzje dotyczące potrzebnych danych oraz sposobu ich obsługi przez aplikację.

> [!div class="nextstepaction"]
> [Przewodnik dotyczący moderowania obrazów](image-moderation-api.md)
