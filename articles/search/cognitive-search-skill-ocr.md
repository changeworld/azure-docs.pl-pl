---
title: Umiejętności wyszukiwania kognitywnego optyczne rozpoznawanie znaków (Azure Search) | Dokumentacja firmy Microsoft
description: Wyodrębnianie tekstu z plików obrazów w usłudze Azure Search wzbogacony potok.
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
ms.openlocfilehash: 234651ad3672982e4de9617561a926712697945a
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734037"
---
# <a name="ocr-cognitive-skill"></a>Optyczne rozpoznawanie znaków umiejętności cognitive

**Optyczne rozpoznawanie znaków** umiejętności umożliwia wyodrębnianie tekstu z plików obrazu. Obsługiwane formaty plików obejmują:

+ . JPEG
+ . JPG
+ . PNG
+ . BMP
+ . OBRAZ GIF

> [!NOTE]
> Wyszukiwanie poznawcze jest dostępne w publicznej wersji zapoznawczej. Wykonywanie zestawu umiejętności i wyodrębniania obrazu i normalizacji są obecnie oferowane bezpłatnie. W późniejszym czasie cen tych funkcji zostanie ogłoszona. 

## <a name="skill-parameters"></a>Parametry kwalifikacji

Parametrów jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| detectOrientation | Włącza automatycznego wykrywania orientacji obrazu. <br/> Prawidłowe wartości: PRAWDA / FAŁSZ.|
|defaultLanguageCode | <p>  Kod języka tekstu wejściowego. Obsługiwane języki: <br/> nazwy zh-Hans (ChineseSimplified) <br/> nazwy zh-Hant (ChineseTraditional) <br/>CS (czeski) <br/>Akcelerator deweloperski w wersji (duński) <br/>NL (holenderski) <br/>EN (angielski) <br/>Fi (fiński)  <br/>FR (francuski) <br/>  Niemcy (wersja niemiecka) <br/>EL (grecki) <br/> hu (Węgierski) <br/> on (włoska) <br/>  Japonia (japoński) <br/> Ko (koreański) <br/> NB (Norweski) <br/>   PL (Polski) <br/> czasu pacyficznego (portugalski) <br/>  RU (rosyjski) <br/>  ES (hiszpański) <br/>  SV (Szwedzki) <br/>  TR (turecki) <br/> AR (arabski) <br/> ro (Rumuński) <br/> Funkcja SR-Cyrl (SerbianCyrillic) <br/> Funkcja SR-Latn (SerbianLatin) <br/>  SK (Słowacki). <br/>  UNK (nieznany) <br/><br/> Jeśli kod języka jest nieokreślona lub ma wartość null, język jest autodetected. </p> |
| textExtractionAlgorithm | "drukowanych" lub "ręczne". Algorytm optyczne rozpoznawanie znaków rozpoznawanie tekstu "ręczne" jest obecnie dostępna w wersji zapoznawczej i obsługiwany tylko w języku angielskim. |

## <a name="skill-inputs"></a>Dane wejściowe umiejętności

| Wprowadź nazwę      | Opis                                          |
|---------------|------------------------------------------------------|
| image         | Typ złożony. Obecnie działa z polem "/ dokumentu/normalized_images" utworzony przez indeksatora obiektów Blob platformy Azure po ```imageAction``` ustawiono ```generateNormalizedImages```. Zobacz [przykładowe](#sample-output) Aby uzyskać więcej informacji.|


## <a name="skill-outputs"></a>Dane wyjściowe umiejętności
| Nazwa wyjściowego     | Opis                   |
|---------------|-------------------------------|
| tekst          | Zwykły tekst wyodrębnione z obrazu.   |
| layoutText    | Typ złożony, który opisuje wyodrębnionego tekstu, a także lokalizacji, w którym tekst został znaleziony.|


## <a name="sample-definition"></a>Przykładowa definicja

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

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Przykład: Scalanie tekstu wyodrębnionego z obrazów osadzonych w zawartości dokumentu.

Typowy przypadek użycia dla funkcja scalająca tekst jest możliwość scalania tekstowa reprezentacja obrazów (tekst z umiejętności optyczne rozpoznawanie znaków lub podpis obrazu) do pola zawartości dokumentu. 

Tworzy następujące zestawu umiejętności przykład *merged_text* pole będzie zawierać zawartości tekstowej w dokumencie, a także tekst OCRed ze wszystkich obrazów osadzonych w tym dokumencie. 

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
W powyższym przykładzie zestawu umiejętności zakłada się, czy istnieje pole znormalizowane obrazów. Aby wygenerować to pole, należy ustawić *imageAction* konfiguracji w definicji indeksator do *generateNormalizedImages* jak pokazano poniżej:

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
+ [TextMerger umiejętności](cognitive-search-skill-textmerger.md)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie indeksatora (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
