---
title: Obraz analizy kognitywnych wyszukiwania umiejętności (Azure Search) | Dokumentacja firmy Microsoft
description: Wyodrębnienie tekstu semantycznego za pomocą analizy obrazu przy użyciu umiejętności kognitywnych ImageAnalysis w potoku wzbogacenia usługi Azure Search.
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
ms.openlocfilehash: dd26dbe34cd04d1ad3184e2cd62afae5166ac914
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640510"
---
#   <a name="image-analysis-cognitive-skill"></a>Umiejętności kognitywnych analizy obrazu

**Analizy obrazu** umiejętności wyodrębnia bogaty zestaw funkcji visual na podstawie zawartości obrazu. Na przykład można wygenerować podpisu z obrazu, generowanie tagi lub identyfikowanie znanych osób oraz punkty.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Parametry kwalifikacji

Parametry jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| defaultLanguageCode   |  Ciąg wskazujący język do zwrócenia. Usługa zwraca wyniki rozpoznawania w określonym języku. Jeśli ten parametr nie jest określony, wartością domyślną jest "en". <br/><br/>Obsługiwane języki są: <br/>*EN* -angielski (ustawienie domyślne) <br/> *zh* — chiński uproszczony|
|visualFeatures |   Tablica ciągów wskazująca typów visual funkcji do zwrócenia. Nieprawidłowa funkcja visual typy:  <ul><li> *kategorie* -kategoryzuje zawartości obrazu zgodnie z taksonomię zdefiniowane w usługach kognitywnych [dokumentacji](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).</li><li> *tagi* -znaczniki obrazu o szczegółową listę słów powiązane do zawartości obrazu.</li><li>*Opis elementu* — zawiera opis obrazu zawartości z pełnym zdaniem angielskiej wersji językowej.</li><li>*Kroje* -wykrywa, czy kroje są obecne. Jeśli jest obecny, generuje współrzędne, płeć i wiek.</li><li> *ImageType* -wykrywa, czy obraz clipart lub rysowanie linii.</li><li>   *Kolor* — Określa kolor akcentu dominującą kolorów i określa, czy obraz jest czarny & białe.</li><li>*Dla dorosłych* -wykrywa, czy obraz ma pornograficznych charakteru (przedstawia nagość lub act płci). Zawartość płciowo sugerujących również została wykryta.</li></ul> Nazwy funkcji visual jest rozróżniana wielkość liter.|
| szczegóły   | Tablica ciągów wskazująca, które specyficznego dla domeny szczegółów do zwrócenia. Nieprawidłowa funkcja visual typy: <ul><li>*Znanych osób* -identyfikuje znanych osób, w przypadku wykrycia w obrazie.</li><li>*Punkty orientacyjne* -identyfikuje punkty orientacyjne, w przypadku wykrycia w obrazie.</li></ul>
 |

## <a name="skill-inputs"></a>Dane wejściowe umiejętności

| Wprowadź nazwę      | Opis                                          |
|---------------|------------------------------------------------------|
| Obraz         | Typ złożony. Obecnie tylko działania z polem "/ dokumentu/normalized_images" utworzonego przez indeksator obiektów Blob platformy Azure po ```imageAction``` ma ustawioną wartość ```generateNormalizedImages```. Zobacz [próbki](#sample-output) Aby uzyskać więcej informacji.|



##  <a name="sample-definition"></a>Przykład definicji

```json
{
    "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
    "visualFeatures": [
        "Tags",
        "Faces",
        "Categories",
        "Adult",
        "Description",
        "ImageType",
        "Color"
    ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "image",
            "source": "/document/normalized_images/*"
        }
    ],
    "outputs": [
        {
            "name": "categories",
            "targetName": "myCategories"
        },
        {
            "name": "tags",
            "targetName": "myTags"
        },
        {
            "name": "description",
            "targetName": "myDescription"
        },
        {
            "name": "faces",
            "targetName": "myFaces"
        },
        {
            "name": "imageType",
            "targetName": "myImageType"
        },
        {
            "name": "color",
            "targetName": "myColor"
        },
        {
            "name": "adult",
            "targetName": "myAdultCategory"
        }
    ]
}
```

##  <a name="sample-input"></a>Przykładowe dane wejściowe

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "url": "https://storagesample.blob.core.windows.net/sample-container/image.jpg"
            }
        }
    ]
}
```


##  <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
    "values": [
      {
        "recordId": "1",
            "data": {
                "categories": [
           {
                        "name": "abstract_",
                        "score": 0.00390625
                    },
                    {
                "name": "people_",
                        "score": 0.83984375,
                "detail": {
                            "celebrities": [
                                {
                                    "name": "Satya Nadella",
                                    "faceRectangle": {
                                        "left": 597,
                                        "top": 162,
                                        "width": 248,
                                        "height": 248
                                    },
                                    "confidence": 0.999028444
                                }
                            ],
                            "landmarks": [
                                {
                                    "name": "Forbidden City",
                                    "confidence": 0.9978346
                                }
                            ]
                        }
                    }
                ],
                "adult": {
                    "isAdultContent": false,
                    "isRacyContent": false,
                    "adultScore": 0.0934349000453949,
                    "racyScore": 0.068613491952419281
                },
                "tags": [
                    {
                        "name": "person",
                        "confidence": 0.98979085683822632
                    },
                    {
                        "name": "man",
                        "confidence": 0.94493889808654785
                    },
                    {
                        "name": "outdoor",
                        "confidence": 0.938492476940155
                    },
                    {
                        "name": "window",
                        "confidence": 0.89513939619064331
                    }
                ],
                "description": {
                    "tags": [
                        "person",
                        "man",
                        "outdoor",
                        "window",
                        "glasses"
                    ],
                    "captions": [
                        {
                            "text": "Satya Nadella sitting on a bench",
                            "confidence": 0.48293603002174407
                        }
                    ]
                },
                "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
                "metadata": {
                    "width": 1500,
                    "height": 1000,
                    "format": "Jpeg"
                },
                "faces": [
                    {
                        "age": 44,
                        "gender": "Male",
                    "faceRectangle": {
                            "left": 593,
                            "top": 160,
                            "width": 250,
                            "height": 250
                        }
                    }
                ],
                "color": {
                    "dominantColorForeground": "Brown",
                    "dominantColorBackground": "Brown",
                    "dominantColors": [
                        "Brown",
                        "Black"
                    ],
                    "accentColor": "873B59",
                    "isBWImg": false
                    },
                "imageType": {
                    "clipArtType": 0,
                    "lineDrawingType": 0
                }
           }
      }
    ]
}
```


## <a name="error-cases"></a>W przypadku wystąpienia błędów
W następujących przypadkach błąd są wyodrębniane żadnych elementów.

| Kod błędu | Opis |
|------------|-------------|
| NotSupportedLanguage | Podany język nie jest obsługiwane. |
| InvalidImageUrl | Adres URL obrazu jest nieprawidłowo sformatowany lub jest niedostępny.|
| InvalidImageFormat | Dane wejściowe nie jest prawidłowym obrazem. |
| InvalidImageSize | Obraz wejściowy jest zbyt duży. |
| NotSupportedVisualFeature  | Określona funkcja typ jest nieprawidłowy. |
| NotSupportedImage | Nieobsługiwany obraz, na przykład pornografia. |
| InvalidDetails | Nieobsługiwany model specyficznego dla domeny. |

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Sposób definiowania skillset](cognitive-search-defining-skillset.md)
+ [Utwórz indeksator (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
