---
title: Tekst scalania kognitywnych wyszukiwania umiejętności (Azure Search) | Dokumentacja firmy Microsoft
description: Scal tekst z kolekcji pól w jedno pole skonsolidowany. Użyj tego kognitywnych umiejętności w potoku wzbogacenia usługi Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: e288748d7433f4b3c7da7db1ab1ef2ee487318df
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
#    <a name="text-merge-cognitive-skill"></a>Umiejętności kognitywnych scalania tekstu

**Scalania tekst** umiejętności konsoliduje tekstu z kolekcji pól w jedno pole. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.TextMerger

## <a name="skill-parameters"></a>Parametry kwalifikacji

Parametry jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| insertPreTag  | Ciąg do uwzględnienia przed każdym wstawiania. Wartość domyślna to `" "`. Aby pominąć miejsce, ustaw wartość na `""`.  |
| insertPostTag | Ciąg, które zostaną uwzględnione po każdym wstawiania. Wartość domyślna to `" "`. Aby pominąć miejsce, ustaw wartość na `""`.  |


##  <a name="sample-input"></a>Przykładowe dane wejściowe
Dokument JSON, zapewniający użyteczne dane wejściowe tego umiejętności może być:

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
Ten przykład przedstawia dane wyjściowe poprzedniego danych wejściowych, przy założeniu, że *insertPreTag* ustawiono `" "`, i *insertPostTag* ma ustawioną wartość `""`. 

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

## <a name="extended-sample-skillset-definition"></a>Definicja skillset rozszerzonej próbki

Typowy scenariusz dla za pomocą scalania tekstu polega na scaleniu tekstową reprezentację obrazów (tekst z umiejętności Rozpoznawania lub podpis obrazu) do pola zawartości dokumentu. 

Następujące skillset przykład używa umiejętności Rozpoznawania można wyodrębnić tekst z obrazów osadzonych w dokumencie. Następnie tworzy *merged_text* pola zawierają zarówno oryginalny i OCRed tekst z każdego obrazu. 

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
      "@odata.type": "#Microsoft.Skills.Util.TextMerger",
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
          "name": "mergedText", "targetname" : "merged_text"
        }
      ]
    }
  ]
}
```
W powyższym przykładzie założono, że pole znormalizowany obrazów istnieje. Aby uzyskać pola z znormalizowanych obrazów, ustaw *imageAction* konfiguracji w definicji indeksatora do *generateNormalizedImages* w sposób przedstawiony poniżej:

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

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Sposób definiowania skillset](cognitive-search-defining-skillset.md)