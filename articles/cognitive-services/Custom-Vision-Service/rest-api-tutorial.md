---
title: 'Samouczek: Tworzenie, szkolenie i eksportowanie modelu przy użyciu niestandardowego interfejsu API REST Vision'
titlesuffix: Azure Cognitive Services
description: Utwórz, wyszkol, przetestuj i wyeksportuj model Custom Vision za pomocą interfejsu API REST.
services: cognitive-services
author: blackmist
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: larryfr
ms.openlocfilehash: 54b5f7bb16803adf91a0a8ea60cfa68d1e322d07
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351103"
---
# <a name="tutorial-create-train-and-export-a-model-with-rest"></a>Samouczek: Tworzenie, szkolenie i eksportowanie modelu z użyciem usług REST

W tym artykule pokazujemy, jak używać klienta REST do pracy z interfejsem API REST do trenowania usługi Custom Vision. W przykładach pokazano sposób używania interfejsu API za pomocą narzędzia `curl` w środowisku bash i narzędzia `Invoke-WebRequest` w środowisku Windows PowerShell.

> [!div class="checklist"]
> * Uzyskiwanie kluczy
> * Tworzenie projektu
> * Tworzenie tagów
> * Dodawanie obrazów
> * Trenowanie i testowanie modelu
> * Eksportowanie modelu

## <a name="prerequisites"></a>Wymagania wstępne

* Podstawowa znajomość koncepcji Representational State Transfer (REST). W tym dokumencie nie zajmujemy się kwestiami szczegółowymi, takimi jak czasowniki HTTP, JSON czy inne zagadnienia związane z REST.
* Powłoka bash (Bourne Again Shell) i program [curl](https://curl.haxx.se) albo środowisko Windows PowerShell 3.0 (lub nowsza wersja).
* Konto Custom Vision. Więcej informacji można znaleźć w artykule [Build a classifier](getting-started-build-a-classifier.md) (Tworzenie klasyfikatora).

## <a name="get-keys"></a>Uzyskiwanie kluczy

Podczas używania interfejsu API REST należy się uwierzytelniać za pomocą klucza. Podczas wykonywania operacji zarządzania lub szkolenia należy używać __klucza do szkolenia__. Podczas używania modelu do przewidywania, należy używać __klucza do przewidywania__.

Klucz jest wysyłany w nagłówku żądania.

Aby uzyskać klucze dla swojego konta, odwiedź [stronę Custom Vision](https://customvision.ai) i wybierz __ikonę koła zębatego__ w prawym górnym rogu. W sekcji __Accounts__ (Konta) skopiuj wartości z pól __Training Key__ (Klucz szkolenia) i __Prediction Key__ (Klucz przewidywania).

![Obraz interfejsu użytkownika do uzyskiwania kluczy](./media/rest-api-tutorial/training-prediction-keys.png)

> [!IMPORTANT]
> Ponieważ klucze są używane do uwierzytelniania każdego żądania, w tym dokumencie zakładamy, że są zapisane w zmiennych środowiskowych. Przed użyciem innych podanych tu fragmentów kodu zapisz klucze w zmiennych środowiskowych za pomocą następujących poleceń:
>
> ```bash
> read -p "Enter the training key: " TRAININGKEY
> read -p "Enter the prediction key: " PREDICTIONKEY
> ```
>
> ```powershell
> $trainingKey = Read-Host 'Enter the training key'
> $predictionKey = Read-Host 'Enter the prediction key'
> ```

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

W poniższych przykładach utworzymy nowy projekt o nazwie `myproject` w wystąpieniu usługi Custom Vision. Domyślną domeną tej usługi jest `General`:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

Odpowiedź na żądanie wygląda podobnie do tego dokumentu JSON:

```json
{
  "id":"45d1b19b-69b6-4a22-8e7e-d1ca37504686",
  "name":"myproject",
  "description":"",
  "settings":{
    "domainId":"ee85a72c-405e-4adc-bb47-ffa8ca0c9f31",
    "useNegativeSet":true,
    "classificationType":"Multilabel",
    "detectionParameters":null
  },
  "created":"2018-08-10T17:39:02.5633333",
  "lastModified":"2018-08-10T17:39:02.5633333",
  "thumbnailUri":null
}
```

> [!TIP]
> Element `id` w odpowiedzi to identyfikator nowego projektu. Jest on używany również w kolejnych przykładach w tym dokumencie.

Aby uzyskać więcej informacji o tym żądaniu, zobacz temat [CreateProject](https://go.microsoft.com/fwlink/?linkid=865446).

### <a name="specific-domains"></a>Określone domeny

Aby utworzyć projekt dla określonej domeny, możesz podać __identyfikator domeny__ jako parametr opcjonalny. W poniższych przykładach pokazano sposoby pobierania listy dostępnych domen:

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

Odpowiedź na żądanie wygląda podobnie do tego dokumentu JSON:

```json
[  
  {  
    "id":"ee85a74c-405e-4adc-bb47-ffa8ca0c9f31",
    "name":"General",
    "type":"Classification",
    "exportable":false,
    "enabled":true
  },
  {  
    "id":"c151d5b5-dd07-472a-acc8-15d29dea8518",
    "name":"Food",
    "type":"Classification",
    "exportable":false,
    "enabled":true
  },
  {  
    "id":"ca455789-012d-4b50-9fec-5bb63841c793",
    "name":"Landmarks",
    "type":"Classification",
    "exportable":false,
    "enabled":true
  },
  ...
]
```

Aby uzyskać więcej informacji o tym żądaniu, zobacz temat [GetDomains](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a827d).

W poniższym przykładzie pokazano tworzenie nowego projektu z wykorzystaniem domeny __Charakterystyczne elementy krajobrazu__:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

## <a name="create-tags"></a>Tworzenie tagów

Do oznaczania obrazów należy używać identyfikatora tagu. W poniższym przykładzie pokazano, jak utworzyć nowy tag o nazwie `cat` i uzyskać jego identyfikator. Zastąp `{projectId}` identyfikatorem swojego projektu. Użyj parametru `name=` w celu określenia nazwy tagu:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

Odpowiedź na żądanie wygląda podobnie do tego dokumentu JSON: 

```json
{"id":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43","name":"cat","description":null,"imageCount":0}
```

Zapisz wartość `id`, ponieważ jest używana podczas oznaczania obrazów.

Aby uzyskać więcej informacji o tym żądaniu, zobacz temat [CreateTag](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829d).

## <a name="add-images"></a>Dodawanie obrazów

W poniższym przykładzie pokazano dodawanie pliku za pomocą jego adresu URL. Zastąp `{projectId}` identyfikatorem swojego projektu. Zastąp `{tagId}` identyfikatorem tagu obrazu:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
$resp.Content
```

Odpowiedź na żądanie wygląda podobnie do tego dokumentu JSON:

```json
{  
  "isBatchSuccessful":true,
  "images":[  
    {  
      "sourceUrl":"http://myimages/cat.jpg",
      "status":"OK",
      "image":{  
        "id":"081adaee-a76b-4d94-a70e-e4fd0935a28f",
        "created":"2018-08-13T13:24:22.0815638",
        "width":640,
        "height":480,
        "imageUri":"https://linktoimage",
        "thumbnailUri":"https://linktothumbnail",
        "tags":[  
          {  
            "tagId":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
            "tagName":null,
            "created":"2018-08-13T13:24:22.104936"
          }
        ],
        "regions":[  
          {  
            "regionId":"40f206a1-3f8a-4de7-a6c3-c7b4643117df",
            "tagName":null,
            "created":"2018-08-13T13:24:22.104936",
            "tagId":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
            "left":119,
            "top":94,
            "width":240,
            "height":140
          }
        ]
      }
    }
  ]
}
```

Aby uzyskać więcej informacji o tym żądaniu, zobacz temat [CreateImagesFromUrls](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8287).

## <a name="train-the-model"></a>Uczenie modelu

Poniższe przykłady pokazują sposób szkolenia modelu. Zastąp `{projectId}` identyfikatorem swojego projektu:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } 
$resp.Content
```

Odpowiedź na żądanie wygląda podobnie do tego dokumentu JSON:

```json
{
    "id": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "name": "Iteration 1",
    "isDefault": false,
    "status": "Training",
    "created": "2018-08-10T17:39:02.5766667",
    "lastModified": "2018-08-16T17:15:07.5250661",
    "projectId": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "exportable": false,
    "domainId": null
}
```

Zapisz wartość `id`, ponieważ będzie używana do testowania i eksportowania modelu.

Aby uzyskać więcej informacji, zobacz temat [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8294).

## <a name="test-the-model"></a>Testowanie modelu

W poniższych przykładach pokazano sposób testowania modelu. Zastąp `{projectId}` identyfikatorem swojego projektu. Zastąp `{iterationId}` identyfikatorem zwróconym ze szkolenia modelu. Zastąp `https://linktotestimage` ścieżką do obrazu testowego.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"url":"https://linktotestimage"}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"url":"https://linktotestimage"}'
$resp.Content
```

Odpowiedź na żądanie wygląda podobnie do tego dokumentu JSON:

```json
{  
  "id":"369b010b-2a92-4f48-a918-4c1a0af91888",
  "project":"45d1b19b-69b8-4b22-8e7e-d1ca37504686",
  "iteration":"23de09d6-42a1-413e-839e-8db6ee6d3496",
  "created":"2018-08-16T17:39:20.7944508Z",
  "predictions":[  
    {  
      "probability":0.8390652,
      "tagId":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
      "tagName":"cat"
    }
  ]
}
```

Aby uzyskać więcej informacji, zobacz temat [QuickTestImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a828d).

## <a name="export-the-model"></a>Eksportowanie modelu

Eksportowanie modelu jest procesem dwuetapowym. Najpierw należy określić format modelu, a następnie wysłać żądanie z zapytaniem o adres URL eksportowanego modelu.

### <a name="request-a-model-export"></a>Żądanie wyeksportowania modelu

W poniższych przykładach pokazano sposób eksportowania modelu `coreml`. Zastąp `{projectId}` identyfikatorem swojego projektu. Zastąp `{iterationId}` identyfikatorem zwróconym ze szkolenia modelu.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

Odpowiedź na żądanie wygląda podobnie do tego dokumentu JSON:

```json
{  
  "platform":"CoreML",
  "status":"Exporting",
  "downloadUri":null,
  "flavor":null
}
```

Aby uzyskać więcej informacji, zobacz temat [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829b).

### <a name="download-the-exported-model"></a>Pobieranie wyeksportowanego modelu

W poniższych przykładach pokazano sposób uzyskania adresu URL wyeksportowanego modelu. Zastąp `{projectId}` identyfikatorem swojego projektu. Zastąp `{iterationId}` identyfikatorem zwróconym ze szkolenia modelu.

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

Odpowiedź na żądanie wygląda podobnie do tego dokumentu JSON:

```json
[  
  {  
    "platform":"CoreML",
    "status":"Done",
    "downloadUri":"https://linktoexportedmodel",
    "flavor":null
  }
]
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz temat [GetExports](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829a).
