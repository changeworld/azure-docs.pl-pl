---
title: Rozpoznawania kognitywnych wyszukiwania umiejętności (Azure Search) | Dokumentacja firmy Microsoft
description: Wyodrębnienie tekstu z plików obrazów w potoku wzbogacenia usługi Azure Search.
services: search
manager: pablocas
author: luiscabrer
documentationcenter: ''
ms.assetid: ''
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 48253b68a329d17f213369e8e4ee2e06bdf17992
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="ocr-cognitive-skill"></a>Kognitywnych umiejętności Rozpoznawania

**Rozpoznawania** umiejętności umożliwia wyodrębnianie tekstu z plików obrazów. Obsługiwane formaty plików obejmują:

+ . JPEG
+ . JPG
+ . PNG
+ . BMP
+ . PLIK GIF


## <a name="skill-parameters"></a>Parametry kwalifikacji

Parametry jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| detectOrientation | Włącza automatycznego wykrywania orientacji obrazu. <br/> Prawidłowe wartości: PRAWDA / FAŁSZ.|
|defaultLanguageCode |  Kod języka wejściowego tekstu. Obsługiwane języki: `ar, cs, da, de, en, es, fi, fr, he, hu, it, ko, pt-br, pt`.  Jeśli kod języka jest nieokreślona lub wartość null, język jest autodetected.|
| textExtractionAlgorithm | "drukowane" lub "odręcznie". Algorytm Rozpoznawania rozpoznawania "pismo" jest obecnie w wersji zapoznawczej i obsługiwane tylko w języku angielskim. |

## <a name="skill-inputs"></a>Dane wejściowe umiejętności

| Wprowadź nazwę      | Opis                                          |
|---------------|------------------------------------------------------|
| Obraz         | Typ złożony. Obecnie tylko działania z polem "/ dokumentu/normalized_images" utworzonego przez indeksator obiektów Blob platformy Azure po ```imageAction``` ma ustawioną wartość ```generateNormalizedImages```. Zobacz [próbki](#sample-output) Aby uzyskać więcej informacji.|


## <a name="skill-outputs"></a>Dane wyjściowe umiejętności
| Nazwa wyjściowego     | Opis                   |
|---------------|-------------------------------|
| tekst          | Zwykły tekst wyodrębniony z obrazu.   |
| layoutText    | Typ złożony, który opisuje wyodrębnionego tekstu, a także lokalizacji, w którym tekst został znaleziony.|


## <a name="sample-definition"></a>Przykład definicji

```json
{
    "skills": [
      {
        "description": "Extracts text (plain and structured) from image."
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": null,
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "myText"
          },
          {
            "name": "layoutText",
            "targetName": "myLayoutText"
          }
        ]
      }
    ]
 }
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Przykładowe dane wyjściowe tekstu i layoutText

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Przykład: Scalanie tekst wyodrębniony z obrazów osadzonych o zawartości dokumentu.

Przypadek użycia wspólnego dla połączenia tekst jest możliwość scalania tekstową reprezentację obrazów (tekst z umiejętności Rozpoznawania lub podpis obrazu) do pola zawartości dokumentu. 

Tworzy następujące skillset przykład *merged_text* pole ma zawierać zawartość tekstową dokumentu, a także tekst OCRed z poszczególnych obrazów osadzonych w tym dokumencie. 

#### <a name="request-body-syntax"></a>Składnia treść żądania
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
          "name": "mergedText", "targetname" : "merged_text"
        }
      ]
    }
  ]
}
```
W powyższym przykładzie skillset założono, że pole znormalizowany obrazów istnieje. Aby wygenerować to pole, ustawić *imageAction* konfiguracji w definicji indeksatora do *generateNormalizedImages* w sposób przedstawiony poniżej:

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
+ [Umiejętności TextMerger](cognitive-search-skill-textmerger.md)
+ [Sposób definiowania skillset](cognitive-search-defining-skillset.md)
+ [Utwórz indeksator (REST)](ref-create-indexer.md)