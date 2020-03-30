---
title: Umiejętności poznawcze OCR
titleSuffix: Azure Cognitive Search
description: Wyodrębnij tekst z plików obrazów przy użyciu optycznego rozpoznawania znaków (OCR) w potoku wzbogacania w usłudze Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bdb510113a8d65ac04b54e77158f46d03cccd9de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791921"
---
# <a name="ocr-cognitive-skill"></a>Umiejętności poznawcze OCR

**Umiejętność Optyczne rozpoznawanie znaków (OCR)** rozpoznaje drukowany i odręczny tekst w plikach obrazów. Ta umiejętność używa modeli uczenia maszynowego dostarczonych przez [computer vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) w usługach Cognitive Services. Umiejętności **OCR** są mape do następujących funkcji:

+ Interfejs API ["OCR"](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-optical-character-recognition-api) jest używany w językach innych niż angielski. 
+ W języku angielskim używany jest nowy interfejs API ["Odczytu".](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api)

Umiejętność **OCR** wyodrębnia tekst z plików obrazów. Obsługiwane formaty plików obejmują:

+ . Jpeg
+ . Jpg
+ . Png
+ . Bmp
+ . Gif
+ . Tiff

> [!NOTE]
> W miarę rozszerzania zakresu poprzez zwiększanie częstotliwości przetwarzania, dodawanie większej liczby dokumentów lub dodawanie kolejnych algorytmów sztucznej inteligencji należy [dołączyć rozliczany zasób usług Cognitive Services.](cognitive-search-attach-cognitive-services.md) Opłaty naliczane podczas wywoływania interfejsów API w usługach Cognitive Services i wyodrębniania obrazu w ramach etapu pękania dokumentów w usłudze Azure Cognitive Search. Nie ma żadnych opłat za wyodrębnianie tekstu z dokumentów.
>
> Wykonanie wbudowanych umiejętności jest naliczane według istniejącej [ceny płatności zgodnie z rzeczywistymi oczekiwaniami.](https://azure.microsoft.com/pricing/details/cognitive-services/) Ceny wyodrębniania obrazów są opisane na [stronie cennika usługi Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| wykrywaniaOrientacja | Umożliwia automatyczne wykrywanie orientacji obrazu. <br/> Prawidłowe wartości: prawda / fałsz.|
|domyślny Kod Języka | <p>  Kod języka tekstu wejściowego. Obsługiwane języki: <br/> zh-Hans (chiński uproszczony) <br/> zh-Hant (ChińskiTraditional) <br/>cs (czeski) <br/>da (duński) <br/>nl (niderlandzki) <br/>pl (angielski) <br/>fi (fiński)  <br/>fr (francuski) <br/>  de (niemiecki) <br/>el (grecki) <br/> hu (węgierski) <br/> to (włoski) <br/>  ja (japoński) <br/> ko (koreański) <br/> nb (norweski) <br/>   pl (polski) <br/> pt (portugalski) <br/>  ru (rosyjski) <br/>  es (hiszpański) <br/>  sv (szwedzki) <br/>  tr (turecki) <br/> ar (arabski) <br/> ro (rumuński) <br/> sr-Cyrl (serbskicyrilliczny) <br/> sr-Latn (SerbskiLatin) <br/>  sk (słowacki). <br/>  unk (Nieznany) <br/><br/> Jeśli kod języka jest nieokreślony lub null, język zostanie ustawiony na angielski. Jeśli język jest jawnie ustawiony na "unk", język zostanie automatycznie wykryty. </p> |
|liniaWykańczenie | Wartość używana między każdą wykrytą linią. Możliwe wartości: 'Space','CarriageReturn','LineFeed'.  Wartość domyślna to "Spacja" |

Wcześniej istniał parametr o nazwie "textExtractionAlgorithm" określający, czy umiejętność powinna wyodrębnić tekst "wydrukowany" czy "odręczny".  Ten parametr jest przestarzały i nie jest już konieczne, ponieważ najnowszy algorytm interfejsu API odczytu jest w stanie wyodrębnić oba typy tekstu naraz.  Jeśli definicja umiejętności zawiera już ten parametr, nie trzeba go usuwać, ale nie będzie już używany i oba typy tekstu zostaną wyodrębnione w przyszłości, niezależnie od tego, co jest ustawione.

## <a name="skill-inputs"></a>Wprowadzanie umiejętności

| Nazwa wejściowa      | Opis                                          |
|---------------|------------------------------------------------------|
| image         | Typ złożony. Obecnie działa tylko z polem "/document/normalized_images", utworzonym przez indeksator ```imageAction``` obiektów Blob platformy ```none```Azure, gdy jest ustawiona na wartość inną niż . Zobacz [przykład,](#sample-output) aby uzyskać więcej informacji.|


## <a name="skill-outputs"></a>Wyniki umiejętności
| Nazwa wyjściowa     | Opis                   |
|---------------|-------------------------------|
| tekst          | Zwykły tekst wyodrębniony z obrazu.   |
| layoutText    | Typ złożony opisujący wyodrębniony tekst i lokalizację, w której znaleziono tekst.|


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

## <a name="sample-text-and-layouttext-output"></a>Przykładowy tekst i layoutU dane wyjściowe tekstu

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

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Przykład: Scalanie tekstu wyodrębnianego z osadzonych obrazów z zawartością dokumentu.

Typowym przypadkiem użycia dla połączenia tekstu jest możliwość scalenia tekstowej reprezentacji obrazów (tekstu z umiejętności OCR lub podpisu obrazu) z polem zawartości dokumentu.

Poniższy przykładowy zestaw umiejętności tworzy pole *merged_text.* To pole zawiera tekstową zawartość dokumentu i tekst OCRed z każdego z obrazów osadzonych w tym dokumencie.

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
Powyższy przykład zestaw umiejętności zakłada, że istnieje pole znormalizowane obrazy. Aby wygenerować to pole, ustaw konfigurację *imageAction* w definicji indeksatora, aby *wygenerowaćNormalizowane obrazy,* jak pokazano poniżej:

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

## <a name="see-also"></a>Zobacz też
+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Umiejętność TextMerger](cognitive-search-skill-textmerger.md)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie indeksatora (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
