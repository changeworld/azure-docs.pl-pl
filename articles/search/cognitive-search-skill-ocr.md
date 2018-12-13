---
title: Optyczne rozpoznawanie znaków wyszukiwania kognitywnego umiejętności — usługa Azure Search
description: Wyodrębnianie tekstu z plików obrazów za pomocą optycznego rozpoznawania znaków (OCR) w usłudze Azure Search wzbogacony potok.
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
ms.custom: seodec2018
ms.openlocfilehash: 097fd93955a4ca3fd96ae6452fa3b503b029ffc3
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313228"
---
# <a name="ocr-cognitive-skill"></a>Optyczne rozpoznawanie znaków umiejętności cognitive

**Optyczne rozpoznawanie znaków** umiejętności umożliwia wyodrębnianie tekstu z plików obrazu. Obsługiwane formaty plików obejmują:

+ . JPEG
+ . JPG
+ . PNG
+ . BMP
+ . OBRAZ GIF

> [!NOTE]
> Od 21 grudnia 2018 r. można skojarzyć zasobu usług Cognitive Services za pomocą usługi Azure Search zestawu umiejętności. Pozwoli to nam będą naliczane opłaty za wykonywanie zestawu umiejętności. W tym dniu również Zaczniemy naliczać opłaty do wyodrębnienia obrazu jako część etap łamania dokumentów. Wyodrębnianie tekstu z dokumentów nadal będzie oferowane bez dodatkowych opłat.
>
> Wykonanie wbudowanego umiejętności będzie powodować obciążenie opłatami istniejące [usług Cognitive Services, płatności — jako — można przejść cena](https://azure.microsoft.com/pricing/details/cognitive-services/) . Cennik wyodrębniania obrazu będzie powodować obciążenie opłatami ceny za wersję zapoznawczą i został opisany na [usługi Azure Search stronę z cennikiem](https://go.microsoft.com/fwlink/?linkid=2042400). Dowiedz się, [więcej](cognitive-search-attach-cognitive-services.md).
>
>  Umiejętności optyczne rozpoznawanie znaków mapuje następujące funkcje usług cognitive services: Gdy textExtractionAlgorithm jest ustawiona na "ręczne", ["RecognizeText"](../cognitive-services/computer-vision/quickstarts-sdk/csharp-hand-text-sdk.md) jest używana funkcja.
>  Gdy textExtractionAlgorithm jest ustawiona na "drukowanych", ["Optyczne rozpoznawanie znaków"](../cognitive-services/computer-vision/concept-extracting-text-ocr.md) funkcji jest używany w językach innych niż angielski. Dla języka angielskiego nowe ["Rozpoznaje Text"](../cognitive-services/computer-vision/concept-recognizing-text.md) jest używana funkcja dla drukowanego tekstu.

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

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Przykład: Scalanie z tekstu wyodrębnionego z obrazów osadzonych w zawartości dokumentu.

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
          "name": "mergedText", "targetName" : "merged_text"
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
