---
title: Obraz analizy usługa cognitive search umiejętności — usługa Azure Search
description: Wyodrębniania semantycznego tekstu za pomocą analizy obrazów przy użyciu umiejętności cognitive ImageAnalysis w usłudze Azure Search wzbogacony potok.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: bb18c858a17e290a8ce2cc88dc1e7d88d21afe0f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021905"
---
#   <a name="image-analysis-cognitive-skill"></a>Umiejętności cognitive analizy obrazów

**Analizy obrazów** umiejętności wyodrębnia bogaty zestaw funkcji visual na podstawie zawartości obrazu. Na przykład można wygenerować podpis z obrazu, generowanie tagi lub zidentyfikować osobistości i charakterystycznych elementów krajobrazu. Modele dostarczone przez uczenia maszynowego korzysta z tej umiejętności [komputerowej](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) w usługach Cognitive Services. 

> [!NOTE]
> Możesz rozwiń zakres, zwiększając częstotliwości przetwarzania, dodając więcej dokumentów lub dodanie więcej algorytmów sztucznej Inteligencji, konieczne będzie [dołączyć płatnych zasobu usług Cognitive Services](cognitive-search-attach-cognitive-services.md). Opłaty są naliczane podczas wywoływania interfejsów API w usługach Cognitive Services i wyodrębniania obrazu jako część etap łamania dokumentów w usłudze Azure Search. Opłaty nie będą naliczane do wyodrębniania tekstu z dokumentów.
>
> Wykonanie wbudowanego umiejętności podlega opłacie za istniejącą [usług Cognitive Services, płatności — jako — można przejść cena](https://azure.microsoft.com/pricing/details/cognitive-services/). Cennik wyodrębniania obraz został opisany na [usługi Azure Search stronę z cennikiem](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Parametry kwalifikacji

Parametrów jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| defaultLanguageCode   |  Ciąg wskazujący język do zwrócenia. Usługa zwraca wyniki rozpoznawania w określonym języku. Jeśli ten parametr nie jest określony, wartością domyślną jest "en". <br/><br/>Są obsługiwane języki: <br/>*EN* — angielski (ustawienie domyślne) <br/> *nazwy zh* — chiński (uproszczony)|
|visualFeatures |   Tablica ciągów wskazujący typy funkcji visual do zwrócenia. Nieprawidłowa funkcja visual typy:  <ul><li> *kategorie* -klasyfikuje zawartość obrazu zgodnie z taksonomię zdefiniowane w usługach Cognitive Services [dokumentacji](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).</li><li> *tagi* — tagi obraz z szczegółową listę słów, związane z zawartością obrazu.</li><li>*Opis* — w tym artykule opisano zawartość z pełnym zdaniem angielskiej obrazu.</li><li>*Twarze* — wykrywa, czy twarzy są obecne. Jeśli jest obecny, generuje współrzędne, płeć i wieku.</li><li> *ImageType* — wykrywa, czy obrazu clipart lub rysowanie linii.</li><li>  *Kolor* -Określa kolor akcentu, dominującego koloru i tego, czy obraz jest czarno- & białe.</li><li>*Treści dla dorosłych* — wykrywa, czy obraz ma pornograficznej charakter (przedstawia nagość lub act płeć). Dwuznaczne przekleństwa zawartości jest również wykrywany.</li></ul> Nazwy funkcji visual jest rozróżniana wielkość liter.|
| szczegóły   | Tablica ciągów, wskazujące, której specyficznego dla domeny szczegóły, aby zwrócić. Nieprawidłowa funkcja visual typy: <ul><li>*Osobistości* — identyfikuje osobistości, w przypadku wykrycia na obrazie.</li><li>*Charakterystycznych elementów krajobrazu* — identyfikuje charakterystycznych elementów krajobrazu, w przypadku wykrycia na obrazie.</li></ul>
 |

## <a name="skill-inputs"></a>Dane wejściowe umiejętności

| Wprowadź nazwę      | Opis                                          |
|---------------|------------------------------------------------------|
| image         | Typ złożony. Obecnie działa z polem "/ dokumentu/normalized_images" utworzony przez indeksatora obiektów Blob platformy Azure po ```imageAction``` jest równa wartości innych niż ```none```. Zobacz [przykładowe](#sample-output) Aby uzyskać więcej informacji.|



##  <a name="sample-definition"></a>Przykładowa definicja
```json
{
    "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
    "context": "/document/normalized_images/*",
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
                "image": {
                    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                    "width": 500,
                    "height": 300,
                    "originalWidth": 5000,
                    "originalHeight": 3000,
                    "rotationFromOriginal": 90,
                    "contentOffset": 500
                }
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
                                    "faceBoundingBox": {
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
                        "faceBoundingBox": {
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
                    "isBwImg": false
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
| InvalidImageUrl | Adres URL obrazu jest niepoprawnie sformatowana lub jest niedostępny.|
| InvalidImageFormat | Dane wejściowe nie jest prawidłowym obrazem. |
| InvalidImageSize | Obraz danych wejściowych jest za duży. |
| NotSupportedVisualFeature  | Typ określona funkcja nie jest prawidłowy. |
| NotSupportedImage | Nieobsługiwany obraz, na przykład pornografii dziecięcej. |
| InvalidDetails | Nieobsługiwana modelu specyficznego dla domeny. |

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie indeksatora (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
