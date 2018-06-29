---
title: Rozpoznawania kognitywnych wyszukiwania umiejętności (Azure Search) | Dokumentacja firmy Microsoft
description: Wyodrębnienie tekstu z plików obrazów w potoku wzbogacenia usługi Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 478afe81ed739b98487973eb092ee9cad0aa17fd
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059081"
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
|defaultLanguageCode | <p>  Kod języka wejściowego tekstu. Obsługiwane języki: <br/> zh-Hans (ChineseSimplified) <br/> zh-Hant (ChineseTraditional) <br/>CS (czeski) <br/>da (duński) <br/>NL (holenderski) <br/>EN (angielski) <br/>Fi (fiński)  <br/>FR (francuski) <br/>  Niemcy (wersja niemiecka) <br/>EL (grecki) <br/> hu (Węgierski) <br/> on (włoska) <br/>  Japonia (wersja japońska) <br/> Ko (koreański) <br/> NB (Norweski) <br/>   PL (Polski) <br/> PT (portugalski) <br/>  RU (rosyjski) <br/>  ES (wersja hiszpańska) <br/>  SV (Szwedzki) <br/>  TR (turecki) <br/> AR (arabski) <br/> ro (Rumuński) <br/> SR-Cyrl (SerbianCyrillic) <br/> SR-Latn (SerbianLatin) <br/>  SK (Słowacki). <br/>  UNK (nieznany) <br/><br/> Jeśli kod języka jest nieokreślona lub wartość null, język jest autodetected. </p> |
| textExtractionAlgorithm | "drukowane" lub "odręcznie". Algorytm Rozpoznawania rozpoznawania "pismo" jest obecnie w wersji zapoznawczej i obsługiwane tylko w języku angielskim. |

## <a name="skill-inputs"></a>Dane wejściowe umiejętności

| Wprowadź nazwę      | Opis                                          |
|---------------|------------------------------------------------------|
| image         | Typ złożony. Obecnie tylko działania z polem "/ dokumentu/normalized_images" utworzonego przez indeksator obiektów Blob platformy Azure po ```imageAction``` ma ustawioną wartość ```generateNormalizedImages```. Zobacz [próbki](#sample-output) Aby uzyskać więcej informacji.|


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
        "description": "Extracts text (plain and structured) from image.",
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

#### <a name="request-body-syntax"></a>Składnia treści żądania
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
+ [Utwórz indeksator (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
