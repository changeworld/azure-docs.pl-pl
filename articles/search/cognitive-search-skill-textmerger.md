---
title: Umiejętności poznawcze scalania tekstu
titleSuffix: Azure Cognitive Search
description: Scalanie tekstu z kolekcji pól w jedno pole skonsolidowane. Użyj tej umiejętności poznawczej w potoku wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 98ea416305f080850d85498f74693eb2d45b0944
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162348"
---
#   <a name="text-merge-cognitive-skill"></a>Umiejętności poznawcze scalania tekstu

Umiejętność **Scalanie tekstu** konsoliduje tekst z kolekcji pól w jedno pole. 

> [!NOTE]
> Ta umiejętność nie jest powiązana z interfejsem API usług Cognitive Services i nie są naliczane opłaty za korzystanie z niej. Należy jednak [dołączyć zasób usług Cognitive Services,](cognitive-search-attach-cognitive-services.md)aby zastąpić opcję **Wolny** zasób, która ogranicza liczbę wzbogacenia dziennie.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| insertPreTag  | Ciąg do dołączona przed każdym wstawieniem. Wartością domyślną jest `" "`. Aby pominąć spację, ustaw `""`wartość na .  |
| InsertPostTag (Wstawianie PostTag) | Ciąg do dołączona po każdym włożeniu. Wartością domyślną jest `" "`. Aby pominąć spację, ustaw `""`wartość na .  |


##  <a name="sample-input"></a>Przykładowe dane wejściowe
Dokument JSON zapewniający użyteczne dane wejściowe dla tej umiejętności może być:

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "text": "The brown fox jumps over the dog",
        "itemsToInsert": ["quick", "lazy"],
        "offsets": [3, 28]
      }
    }
  ]
}
```

##  <a name="sample-output"></a>Przykładowe dane wyjściowe
W tym przykładzie pokazano dane wyjściowe poprzedniego wejścia, `" "`przy założeniu, że *insertPreTag* jest ustawiona na , a *insertPostTag* jest ustawiona na `""`. 

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "mergedText": "The quick brown fox jumps over the lazy dog"
      }
    }
  ]
}
```

## <a name="extended-sample-skillset-definition"></a>Rozszerzona definicja narzędzia umiejętności próbkowania

Typowym scenariuszem użycia scalania tekstu jest scalanie tekstowej reprezentacji obrazów (tekstu z umiejętności OCR lub podpisu obrazu) z polem zawartości dokumentu. 

Poniższy przykładowy zestaw umiejętności używa umiejętności OCR do wyodrębniania tekstu z obrazów osadzonych w dokumencie. Następnie tworzy *pole merged_text,* które zawiera zarówno oryginalny, jak i tekstowy OCRed z każdego obrazu. Możesz dowiedzieć się więcej o umiejętności OCR [tutaj](https://docs.microsoft.com/azure/search/cognitive-search-skill-ocr).

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
Powyższy przykład zakłada, że istnieje pole znormalizowane obrazy. Aby uzyskać pole znormalizowane obrazy, ustaw konfigurację *imageAction* w definicji indeksatora, aby *generateNormalizedImages,* jak pokazano poniżej:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":{
    "configuration":{
        "dataToExtract":"contentAndMetadata",
        "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie indeksatora (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
