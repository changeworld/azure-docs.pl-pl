---
title: Wyodrębnianie tekstu z obrazów
titleSuffix: Azure Cognitive Search
description: Przetwarzaj i wyodrębniaj tekst i inne informacje z obrazów w potokach usługi Azure Cognitive Search.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 98054060210f55803d6e2811e1f494fd3ff00e48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76838262"
---
# <a name="how-to-process-and-extract-information-from-images-in-ai-enrichment-scenarios"></a>Jak przetwarzać i wyodrębniać informacje z obrazów w scenariuszach wzbogacania SI

Usługa Azure Cognitive Search ma kilka możliwości pracy z obrazami i plikami obrazów. Podczas pękania dokumentu można użyć parametru *imageAction,* aby wyodrębnić tekst ze zdjęć lub obrazów zawierających tekst alfanumeryczny, na przykład słowo "STOP" w znaku stop. Inne scenariusze obejmują generowanie reprezentacji tekstowej obrazu, takie jak "mniszek lekarski" dla zdjęcia mniszka lekarskiego lub kolor "żółty". Można również wyodrębnić metadane dotyczące obrazu, takie jak jego rozmiar.

W tym artykule opisano przetwarzania obrazu bardziej szczegółowo i zawiera wskazówki dotyczące pracy z obrazami w potoku wzbogacania AI.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Uzyskaj znormalizowane obrazy

W ramach pękania dokumentów, istnieje nowy zestaw parametrów konfiguracji indeksatora do obsługi plików obrazów lub obrazów osadzonych w plikach. Parametry te są używane do normalizacji obrazów do dalszego przetwarzania niższego rzędu. Normalizacja obrazów sprawia, że są bardziej jednolite. Duże obrazy są przesunięte na maksymalną wysokość i szerokość, aby były używane. W przypadku obrazów przedstawiających metadane w orientacji obrót obrazu jest dostosowywany do ładowania pionowego. Dopasowania metadanych są przechwytywane w typie złożonym utworzonym dla każdego obrazu. 

Nie można wyłączyć normalizacji obrazu. Umiejętności, które iterują obrazy, oczekują znormalizowanych obrazów. Włączanie normalizacji obrazu w indeksatorze wymaga, aby zestaw umiejętności był dołączony do tego indeksatora.

| Parametr konfiguracji | Opis |
|--------------------|-------------|
| imageAction (akcją)   | Ustaw na "brak", jeśli nie należy podejmować żadnych działań po napotkaniu osadzonych obrazów lub plików obrazów. <br/>Ustaw "generateNormalizedImages", aby wygenerować tablicę znormalizowanych obrazów w ramach pękania dokumentów.<br/>Ustaw "generateNormalizedImagePerPage", aby wygenerować tablicę znormalizowanych obrazów, w których dla plików PDF w źródle danych każda strona jest renderowana na jednym obrazie wyjściowym.  Funkcja jest taka sama jak "generateNormalizedImages" dla typów plików innych niż PDF.<br/>Dla każdej opcji, która nie jest "none", obrazy będą widoczne w polu *normalized_images.* <br/>Wartość domyślna to "brak". Ta konfiguracja jest odpowiednia tylko dla źródeł danych obiektów blob, gdy "dataToExtract" jest ustawiona na "contentAndMetadata". <br/>Z danego dokumentu zostanie wyodrębnionych maksymalnie 1000 obrazów. Jeśli w dokumencie znajduje się więcej niż 1000 obrazów, pierwsze 1000 zostanie wyodrębnione i zostanie wygenerowane ostrzeżenie. |
|  normalizedImageMaxWidth | Maksymalna szerokość (w pikselach) dla generowanych obrazów znormalizowanych. Wartość domyślna to 2000. Maksymalna dozwolona wartość wynosi 10000. | 
|  normalizedImageMaxHeight | Maksymalna wysokość (w pikselach) dla generowanych obrazów znormalizowanych. Wartość domyślna to 2000. Maksymalna dozwolona wartość wynosi 10000.|

> [!NOTE]
> Jeśli ustawisz *imageAction* właściwość do niczego innego niż "none", nie będzie można ustawić *parsingMode* właściwości do niczego innego niż "default".  Można ustawić tylko jedną z tych dwóch właściwości na wartość nie-domyślną w konfiguracji indeksatora.

Ustaw **parsingMode** parametru (indeksować `json` każdy obiekt blob `jsonArray` jako pojedynczy dokument) lub (jeśli obiekty blob zawierają tablice JSON i trzeba każdy element tablicy, które mają być traktowane jako oddzielny dokument).

Domyślna wartość 2000 pikseli dla znormalizowanych obrazów maksymalna szerokość i wysokość jest oparta na maksymalnych rozmiarach obsługiwanych przez [umiejętność OCR](cognitive-search-skill-ocr.md) i [analizę obrazu](cognitive-search-skill-image-analysis.md). Umiejętność [OCR](cognitive-search-skill-ocr.md) obsługuje maksymalną szerokość i wysokość 4200 dla języków innych niż angielski i 10000 dla języka angielskiego.  Jeśli zwiększysz maksymalne limity, przetwarzanie może zakończyć się niepowodzeniem na większych obrazach w zależności od definicji zestawu umiejętności i języka dokumentów. 

ImageAction w [definicji indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer) można określić w następujący sposób:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":
  {
    "configuration": 
    {
        "dataToExtract": "contentAndMetadata",
        "imageAction": "generateNormalizedImages"
    }
  }
}
```

Gdy *imageAction* jest ustawiona na wartość inną, a następnie "brak", nowe pole *normalized_images* będzie zawierać tablicę obrazów. Każdy obraz jest typem złożonym, który ma następujące elementy członkowskie:

| Element członkowski obrazu       | Opis                             |
|--------------------|-----------------------------------------|
| dane               | BASE64 zakodowany ciąg znormalizowanego obrazu w formacie JPEG.   |
| szerokość              | Szerokość znormalizowanego obrazu w pikselach. |
| height             | Wysokość znormalizowanego obrazu w pikselach. |
| originalWidth      | Oryginalna szerokość obrazu przed normalizacją. |
| oryginalnyHeight      | Oryginalna wysokość obrazu przed normalizacją. |
| rotationFromOriginal |  Obrót w kierunku przeciwnym do ruchu wskazówek zegara w stopniach, które wystąpiły w celu utworzenia znormalizowanego obrazu. Wartość od 0 stopni do 360 stopni. Ten krok odczytuje metadane z obrazu generowanego przez aparat fotograficzny lub skaner. Zwykle wielokrotność 90 stopni. |
| zawartośćOffset | Przesunięcie znaku w polu zawartości, z którego obraz został wyodrębniony. To pole ma zastosowanie tylko do plików z osadzonymi obrazami. |
| Pagenumber | Jeśli obraz został wyodrębniony lub renderowany z pliku PDF, to pole zawiera numer strony w pliku PDF, z którym został wyodrębniony lub renderowany, począwszy od 1.  Jeśli obraz nie pochodzi z pliku PDF, to pole będzie 0.  |

 Wartość próbki *normalized_images:*
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500,
    "pageNumber": 2
  }
]
```

## <a name="image-related-skills"></a>Umiejętności związane z obrazem

Istnieją dwie wbudowane umiejętności poznawcze, które przyjmują obrazy jako dane wejściowe: [OCR](cognitive-search-skill-ocr.md) i [Analiza obrazu.](cognitive-search-skill-image-analysis.md) 

Obecnie te umiejętności działają tylko z obrazami wygenerowanymi na etapie pękania dokumentu. Jako takie, jedynym obsługiwanym `"/document/normalized_images"`wejściem jest .

### <a name="image-analysis-skill"></a>Analiza obrazu — umiejętność

Analiza [obrazu Umiejętności](cognitive-search-skill-image-analysis.md) wyodrębnia bogaty zestaw funkcji wizualnych na podstawie zawartości obrazu. Na przykład możesz wygenerować podpis z obrazu, wygenerować tagi lub zidentyfikować gwiazdy i punkty orientacyjne.

### <a name="ocr-skill"></a>Umiejętność OCR

Umiejętność [OCR](cognitive-search-skill-ocr.md) wyodrębnia tekst z plików obrazów, takich jak jpg, PNG i mapy bitowe. Może wyodrębnić tekst, a także informacje o układzie. Informacje o układzie zawiera granice dla każdego z ciągów zidentyfikowanych.

## <a name="embedded-image-scenario"></a>Osadzony scenariusz obrazu

Typowy scenariusz polega na utworzeniu pojedynczego ciągu zawierającego całą zawartość pliku, zarówno tekstu, jak i tekstu pochodzenia obrazu, wykonując następujące kroki:  

1. [normalized_images wyciągu](#get-normalized-images)
1. Uruchom umiejętność OCR jako `"/document/normalized_images"` dane wejściowe
1. Scal reprezentację tekstu tych obrazów z nieprzetworzonym tekstem wyodrębnionym z pliku. Za pomocą umiejętności [Scalanie tekstu](cognitive-search-skill-textmerger.md) można skonsolidować oba fragmenty tekstu w jeden duży ciąg.

Poniższy przykładowy zestaw umiejętności tworzy pole *merged_text* zawierające tekstową zawartość dokumentu. Zawiera również tekst OCRed z każdego z osadzonych obrazów. 

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

Teraz, gdy masz pole merged_text, możesz zamapować go jako pole z wyszukujalne w definicji indeksatora. Przeszukiwania całej zawartości plików, w tym tekstu obrazów.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Wizualizuj obwiednia wyodrębnionego tekstu

Innym typowym scenariuszem jest wizualizacja informacji o układzie wyników wyszukiwania. Na przykład można wyróżnić miejsce znalezienia fragmentu tekstu na obrazie w ramach wyników wyszukiwania.

Ponieważ krok OCR jest wykonywany na znormalizowanych obrazach, współrzędne układu znajdują się w znormalizowanej przestrzeni obrazu. Podczas wyświetlania znormalizowanego obrazu obecność współrzędnych zazwyczaj nie stanowi problemu, ale w niektórych sytuacjach można wyświetlić oryginalny obraz. W takim przypadku przekonwertuj każdy z punktów współrzędnych układu na oryginalny układ współrzędnych obrazu. 

Jako pomocnik, jeśli chcesz przekształcić znormalizowane współrzędne do oryginalnej przestrzeni współrzędnych, można użyć następującego algorytmu:

```csharp
        /// <summary>
        ///  Converts a point in the normalized coordinate space to the original coordinate space.
        ///  This method assumes the rotation angles are multiples of 90 degrees.
        /// </summary>
        public static Point GetOriginalCoordinates(Point normalized,
                                    int originalWidth,
                                    int originalHeight,
                                    int width,
                                    int height,
                                    double rotationFromOriginal)
        {
            Point original = new Point();
            double angle = rotationFromOriginal % 360;

            if (angle == 0 )
            {
                original.X = normalized.X;
                original.Y = normalized.Y;
            } else if (angle == 90)
            {
                original.X = normalized.Y;
                original.Y = (width - normalized.X);
            } else if (angle == 180)
            {
                original.X = (width -  normalized.X);
                original.Y = (height - normalized.Y);
            } else if (angle == 270)
            {
                original.X = height - normalized.Y;
                original.Y = normalized.X;
            }

            double scalingFactor = (angle % 180 == 0) ? originalHeight / height : originalHeight / width;
            original.X = (int) (original.X * scalingFactor);
            original.Y = (int)(original.Y * scalingFactor);

            return original;
        }
```

## <a name="see-also"></a>Zobacz też
+ [Tworzenie indeksatora (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ [Analiza obrazu — umiejętność](cognitive-search-skill-image-analysis.md)
+ [Umiejętność OCR](cognitive-search-skill-ocr.md)
+ [Umiejętność scalania tekstu](cognitive-search-skill-textmerger.md)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
+ [Jak mapować wzbogacone pola](cognitive-search-output-field-mapping.md)
