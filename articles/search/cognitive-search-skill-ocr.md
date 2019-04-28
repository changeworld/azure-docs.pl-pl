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
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: fb7fe0689ce54031880565c0c6409afeab2ff523
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126978"
---
# <a name="ocr-cognitive-skill"></a>Optyczne rozpoznawanie znaków umiejętności cognitive

Optyczne rozpoznawanie znaków (OCR) umiejętności rozpoznaje drukowanych oraz pisma odręcznego tekstu w plikach obrazu. Modele dostarczone przez uczenia maszynowego korzysta z tej umiejętności [komputerowej](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) w usługach Cognitive Services. **Optyczne rozpoznawanie znaków** umiejętności mapuje następujące funkcje:

+ Gdy textExtractionAlgorithm jest ustawiona na "ręczne", ["RecognizeText"](../cognitive-services/computer-vision/quickstarts-sdk/csharp-hand-text-sdk.md) jest używana funkcja.
+ Gdy textExtractionAlgorithm jest ustawiona na "drukowanych", ["Optyczne rozpoznawanie znaków"](../cognitive-services/computer-vision/concept-extracting-text-ocr.md) funkcji jest używany w językach innych niż angielski. Dla języka angielskiego nowe ["Rozpoznaje Text"](../cognitive-services/computer-vision/concept-recognizing-text.md) jest używana funkcja dla drukowanego tekstu.

**Optyczne rozpoznawanie znaków** umiejętności umożliwia wyodrębnianie tekstu z plików obrazu. Obsługiwane formaty plików obejmują:

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF
+ .TIFF

> [!NOTE]
> Od 21 grudnia 2018 r. można [dołączenia zasobu usług Cognitive Services](cognitive-search-attach-cognitive-services.md) za pomocą usługi Azure Search zestawu umiejętności. Dzięki temu będą naliczane opłaty za wykonywanie zestawu umiejętności. W tym dniu również naliczamy opłaty za wyodrębniania obrazu jako część etap łamania dokumentów. Wyodrębnianie tekstu z dokumentów w dalszym ciągu oferowana bez ponoszenia dodatkowych kosztów.
>
> [Wbudowane umiejętności cognitive](cognitive-search-predefined-skills.md) wykonywania podlega opłacie [usług Cognitive Services, płatności — jako — można przejść cena](https://azure.microsoft.com/pricing/details/cognitive-services), w taki sam szybkości tak, jakby zadanie było wykonywane bezpośrednio. Obraz wyodrębniania jest opłata za usługę Azure Search, obecnie oferowana w cenie wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [usługi Azure Search stronę z cennikiem](https://go.microsoft.com/fwlink/?linkid=2042400) lub [sposób działania rozliczeń](search-sku-tier.md#how-billing-works).

## <a name="skill-parameters"></a>Parametry kwalifikacji

Parametrów jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| detectOrientation | Włącza automatycznego wykrywania orientacji obrazu. <br/> Prawidłowe wartości: PRAWDA / FAŁSZ.|
|defaultLanguageCode | <p>  Kod języka tekstu wejściowego. Obsługiwane języki: <br/> nazwy zh-Hans (ChineseSimplified) <br/> nazwy zh-Hant (ChineseTraditional) <br/>CS (czeski) <br/>Akcelerator deweloperski w wersji (duński) <br/>NL (holenderski) <br/>EN (angielski) <br/>Fi (fiński)  <br/>FR (francuski) <br/>  Niemcy (wersja niemiecka) <br/>EL (grecki) <br/> hu (Węgierski) <br/> on (włoska) <br/>  Japonia (japoński) <br/> Ko (koreański) <br/> NB (Norweski) <br/>   PL (Polski) <br/> czasu pacyficznego (portugalski) <br/>  RU (rosyjski) <br/>  ES (hiszpański) <br/>  SV (Szwedzki) <br/>  TR (turecki) <br/> AR (arabski) <br/> ro (Rumuński) <br/> Funkcja SR-Cyrl (SerbianCyrillic) <br/> Funkcja SR-Latn (SerbianLatin) <br/>  SK (Słowacki). <br/>  UNK (nieznany) <br/><br/> Jeśli kod języka jest nieokreślona lub ma wartość null, język zostaną ustawione na język angielski. Jeśli jawnie ustawiono język "unk", język będzie wykrywane automatycznie. </p> |
| textExtractionAlgorithm | "drukowanych" lub "ręczne". Algorytm optyczne rozpoznawanie znaków rozpoznawanie tekstu "ręczne" jest obecnie dostępna w wersji zapoznawczej i obsługiwany tylko w języku angielskim. |

## <a name="skill-inputs"></a>Dane wejściowe umiejętności

| Wprowadź nazwę      | Opis                                          |
|---------------|------------------------------------------------------|
| image         | Typ złożony. Obecnie działa z polem "/ dokumentu/normalized_images" utworzony przez indeksatora obiektów Blob platformy Azure po ```imageAction``` jest równa wartości innych niż ```none```. Zobacz [przykładowe](#sample-output) Aby uzyskać więcej informacji.|


## <a name="skill-outputs"></a>Dane wyjściowe umiejętności
| Nazwa wyjściowego     | Opis                   |
|---------------|-------------------------------|
| tekst          | Zwykły tekst wyodrębnione z obrazu.   |
| layoutText    | Typ złożony, który opisuje wyodrębnionego tekstu oraz lokalizacji, w którym tekst został znaleziony.|


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

Tworzy następujące zestawu umiejętności przykład *merged_text* pola. To pole zawiera zawartości tekstowej w dokumencie i tekst OCRed ze wszystkich obrazów osadzonych w tym dokumencie.

#### <a name="request-body-syntax"></a>Składnia treści żądania
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
W powyższym przykładzie zestawu umiejętności zakłada się, czy istnieje pole znormalizowane obrazów. Aby wygenerować to pole, należy ustawić *imageAction* konfiguracji w definicji indeksator do *generateNormalizedImages* jak pokazano poniżej:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
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
