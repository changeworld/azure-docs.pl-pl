---
title: Użyj Custom Vision Service interfejs API REST — usług Azure Cognitive Services | Dokumentacja firmy Microsoft
description: Tworzenie, szkolenie, testowanie i eksportowanie niestandardowego modelu przetwarzania obrazów za pomocą interfejsu API REST.
services: cognitive-services
author: blackmist
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 08/07/2018
ms.author: larryfr
ms.openlocfilehash: 44fa4d45c33f3064c089724ee761a70d0a8421ab
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "40250268"
---
# <a name="tutorial-use-the-custom-vision-rest-api"></a>Samouczek: Używanie usługi Custom Vision Service interfejsu API REST

Dowiedz się, jak używać niestandardowego interfejsu API REST przetwarzania do tworzenia, uczenia, testowanie i eksportowanie modelu.

Informacje przedstawione w tym dokumencie pokazano, jak używać klienta REST do pracy z interfejsem API REST dla szkolenia usługi Custom Vision. W przykładach pokazano, jak używać interfejsu API przy użyciu `curl` narzędzie z poziomu środowiska bash oraz `Invoke-WebRequest` za pomocą programu Windows PowerShell.

> [!div class="checklist"]
> * Pobierz klucze
> * Tworzenie projektu
> * Utworzenie tagów
> * Dodawanie obrazów
> * Uczenie i testowanie modelu
> * Eksportowanie modelu

## <a name="prerequisites"></a>Wymagania wstępne

* Podstawowe znajomość z REST Representational State Transfer (). W tym dokumencie nie przejdź do szczegółów elementów, takich jak zleceń HTTP, JSON lub innymi często używane z użyciem usług REST.

* Albo funkcji bash (Bourne ponownie Shell) z [curl](https://curl.haxx.se) narzędzia lub środowiska Windows PowerShell 3.0 (lub nowszego).

* Konto niestandardowe wizji. Aby uzyskać więcej informacji, zobacz [tworzenie klasyfikatora](getting-started-build-a-classifier.md) dokumentu.

## <a name="get-keys"></a>Pobierz klucze

Korzystając z interfejsu API REST, należy uwierzytelnić przy użyciu klucza. Podczas zarządzania lub operacji uczenia, możesz użyć __klucz szkolenia__. Przy użyciu modelu do prognozowania, za pomocą funkcji __klucz prognozowania__.

Podczas wykonywania żądaniem, klucz zostaje wysłany jako nagłówek żądania.

Aby pobrać klucze służące do swojego konta, odwiedź stronę [strony sieci web Custom Vision](https://customvision.ai) i wybierz __ikonę koła zębatego__ w prawym górnym rogu. W __kont__ sekcji, skopiuj wartości z __klucz szkolenia__ i __klucz prognozowania__ pola.

![Obraz przedstawiający klucze interfejsu użytkownika](./media/rest-api-tutorial/training-prediction-keys.png)

> [!IMPORTANT]
> Ponieważ klucze są używane do uwierzytelniania każdego żądania, przykłady w tym dokumencie założono, że wartości klucza są zawarte w zmiennych środowiskowych. Użyj następujących poleceń, aby klucze były przechowywane w zmiennych środowiskowych przed rozpoczęciem korzystania z żadnych innych fragmentów w tym dokumencie:
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

Poniższe przykłady tworzą nowy projekt o nazwie `myproject` w wystąpieniu usługi Custom Vision service. Domyślnie ta usługa `General` domeny:

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

Odpowiedź na żądanie jest podobne do następujących dokumentów JSON:

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
> `id` Wpis w odpowiedzi jest identyfikator nowego projektu. Jest on używany w innych przykładach w dalszej części tego dokumentu.

Aby uzyskać więcej informacji na temat tego żądania, zobacz [CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8290).

### <a name="specific-domains"></a>Określone domeny

Aby utworzyć projekt dla określonej domeny, możesz podać __Identyfikatora domeny__ jako parametr opcjonalny. W poniższych przykładach pokazano, jak pobrać listę dostępnych domen:

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

Odpowiedź na żądanie jest podobne do następujących dokumentów JSON:

```json
[
    {
        "id": "ee85a74c-405e-4adc-bb47-ffa8ca0c9f31",
        "name": "General",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "c151d5b5-dd07-472a-acc8-15d29dea8518",
        "name": "Food",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "ca455789-012d-4b50-9fec-5bb63841c793",
        "name": "Landmarks",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    ...
]
```

Aby uzyskać więcej informacji na temat tego żądania, zobacz [GetDomains](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a827d).

W poniższym przykładzie pokazano tworzenie nowego projektu, który używa __charakterystycznych elementów krajobrazu__ domeny:

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

## <a name="create-tags"></a>Utworzenie tagów

Można tagować obrazy należy użyć identyfikatora tagu Poniższy przykład pokazuje, jak utworzyć nowy tag o nazwie `cat` i Pobierz identyfikator tagu Zastąp `{projectId}` z Identyfikatorem projektu. Użyj `name=` parametru do określenia nazwy tagu:

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

Odpowiedź na żądanie jest podobny do dokumentu JSON: 

```json
{"id":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43","name":"cat","description":null,"imageCount":0}
```

Zapisz `id` wartości, ponieważ jest używany, gdy tagowanie obrazów.

Aby uzyskać więcej informacji na temat tego żądania, zobacz [CreateTag](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829d).

## <a name="add-images"></a>Dodawanie obrazów

W poniższych przykładach pokazano, dodanie pliku z adresu URL. Zastąp `{projectId}` z Identyfikatorem projektu. Zastąp `{tagId}` o identyfikatorze tagu obrazu:

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

Odpowiedź na żądanie jest podobne do następujących dokumentów JSON:

```json
{
    "isBatchSuccessful": true,
    "images": [
        {
            "sourceUrl": "http://myimages/cat.jpg",
            "status": "OK",
            "image": {
                "id": "081adaee-a76b-4d94-a70e-e4fd0935a28f",
                "created": "2018-08-13T13:24:22.0815638",
                "width": 640,
                "height": 480,
                "imageUri": "https://linktoimage",
                "thumbnailUri": "https://linktothumbnail",
                "tags": [
                    {
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936"
                    }
                ],
                "regions": [
                    {
                        "regionId": "40f206a1-3f8a-4de7-a6c3-c7b4643117df",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936",
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "left": 119,
                        "top": 94,
                        "width": 240,
                        "height": 140
                    }
                ]
            }
        }
    ]
}
```

Aby uzyskać więcej informacji na temat tego żądania, zobacz [CreateImagesFromUrls](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8287).

## <a name="train-the-model"></a>Uczenie modelu

Poniższe przykłady pokazują, jak do nauczenia modelu. Zastąp `{projectId}` z Identyfikatorem projektu:

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

Odpowiedź na żądanie jest podobne do następujących dokumentów JSON:

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

Zapisz `id` wartości, ponieważ jest używane do testowania i wyeksportuj model.

Aby uzyskać więcej informacji, zobacz [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8294).

## <a name="test-the-model"></a>Testowanie modelu

Poniższe przykłady pokazują, jak przeprowadzić test modelu. Zastąp `{projectId}` z Identyfikatorem projektu. Zastąp `{iterationId}` o identyfikatorze zwrócone w wyniku uczenia modelu. Zastąp `https://linktotestimage` ze ścieżką do obrazu testu.

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

Odpowiedź na żądanie jest podobne do następujących dokumentów JSON:

```json
{
    "id": "369b010b-2a92-4f48-a918-4c1a0af91888",
    "project": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "iteration": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "created": "2018-08-16T17:39:20.7944508Z",
    "predictions": [
        {
            "probability": 0.8390652,
            "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
            "tagName": "cat"
        }
    ]
}
```

Aby uzyskać więcej informacji, zobacz [QuickTestImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a828d).

## <a name="export-the-model"></a>Eksportowanie modelu

Eksportowanie modelu jest procesem dwuetapowym. Najpierw należy określić format modelu i Utwórz żądanie adresu URL dla eksportowanego modelu.

### <a name="request-a-model-export"></a>Żądanie eksportu modelu

W poniższych przykładach pokazano sposób eksportowania `coreml` modelu. Zastąp `{projectId}` z Identyfikatorem projektu. Zastąp `{iterationId}` o identyfikatorze zwrócone w wyniku uczenia modelu.

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

Odpowiedź na żądanie jest podobne do następujących dokumentów JSON:

```json
{
    "platform": "CoreML",
    "status": "Exporting",
    "downloadUri": null,
    "flavor": null
}
```

Aby uzyskać więcej informacji, zobacz [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829b).

### <a name="download-the-exported-model"></a>Pobierz wyeksportowane modelu

Poniższe przykłady pokazują, jak można pobrać adresu URL wyeksportowanego modelu. Zastąp `{projectId}` z Identyfikatorem projektu. Zastąp `{iterationId}` o identyfikatorze zwrócone w wyniku uczenia modelu.

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

Odpowiedź na żądanie jest podobne do następujących dokumentów JSON:

```json
[
    {
        "platform": "CoreML",
        "status": "Done",
        "downloadUri": "https://linktoexportedmodel",
        "flavor": null
    }
]
```

Aby uzyskać więcej informacji, zobacz [GetExports](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829a).