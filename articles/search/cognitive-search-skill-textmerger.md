---
title: Tekst scalania wyszukiwania kognitywnego umiejętności — usługa Azure Search
description: Scal tekstu z kolekcji pól w jednym skonsolidowanym polu. Użyj tego cognitive umiejętności w wzbogacony potok usługi Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 2ef5d285c19900fd2896279edde8841581d7e947
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54388162"
---
#    <a name="text-merge-cognitive-skill"></a>Umiejętności cognitive scalania tekstu

**Scalania tekstu** umiejętności konsoliduje tekst z kolekcji pól do pojedynczego pola. 

> [!NOTE]
> Umiejętności, to nie jest powiązany z interfejsu API usług Cognitive Services i nie nie trzeba ponosić opłat skojarzonych z tym umiejętności. Jednak należy [dołączenia zasobu usług Cognitive Services](cognitive-search-attach-cognitive-services.md) mimo to do przesłonięcia opcję bezpłatny zasób, która ogranicza do niewielkiej liczby wzbogacenia dzienny.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>Parametry kwalifikacji

Parametrów jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| insertPreTag  | Ciąg do uwzględnienia przed każdym wstawiania. Wartość domyślna to `" "`. Aby pominąć miejsce, ustaw wartość `""`.  |
| insertPostTag | Ciąg zostanie uwzględniona po każdym wstawiania. Wartość domyślna to `" "`. Aby pominąć miejsce, ustaw wartość `""`.  |


##  <a name="sample-input"></a>Przykładowe dane wejściowe
Dokument JSON, zapewniając użyteczne dane wejściowe to umiejętności, może być:

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The brown fox jumps over the dog" ,
             "itemsToInsert": ["quick", "lazy"],
             "offsets": [3, 28],
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Przykładowe dane wyjściowe
Ten przykład przedstawia dane wyjściowe poprzedniego danych wejściowych, przy założeniu, że *insertPreTag* ustawiono `" "`, i *insertPostTag* ustawiono `""`. 

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

## <a name="extended-sample-skillset-definition"></a>Definicja zestawu umiejętności rozszerzonej próbki

Typowy scenariusz dla za pomocą scalania tekstu jest scalanie tekstowa reprezentacja obrazów (tekst z umiejętności optyczne rozpoznawanie znaków lub podpis obrazu) do pola zawartości dokumentu. 

Poniższy przykład zestawu umiejętności używa umiejętności optyczne rozpoznawanie znaków do wyodrębniania tekstu z obrazów osadzonych w dokumencie. Następnie tworzy *merged_text* pole będzie zawierać zarówno oryginał, jak i OCRed tekst z każdego obrazu. 

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "name": "OCR skill",
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
      "insertPostTag": " "
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
W powyższym przykładzie zakłada się, czy istnieje pole znormalizowane obrazów. Aby pobrać obrazy znormalizowane pola, należy ustawić *imageAction* konfiguracji w definicji indeksator do *generateNormalizedImages* jak pokazano poniżej:

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

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie indeksatora (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
