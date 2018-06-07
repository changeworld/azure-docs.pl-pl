---
title: Przetwarzanie i Wyodrębnij tekst z obrazów w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Proces i wyodrębniania tekstu i innych informacji z obrazów w kognitywnych wyszukiwania potoki w usłudze Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 566b1e731f137863e9d4bc284d8868d408c7a575
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640204"
---
#  <a name="how-to-process-and-extract-information-from-images-in-cognitive-search-scenarios"></a>Jak informacje o procesie i wyodrębniania z obrazów w scenariuszach kognitywnych wyszukiwania

Wyszukiwanie kognitywnych ma kilka możliwości do pracy z obrazami i pliki obrazów. Podczas łamania dokumentu, można użyć *imageAction* parametr, aby wyodrębnić tekst z zdjęć lub obrazów zawierających alfanumeryczne, takie jak word "STOP" w znak stop. Inne scenariusze obejmują generowania tekstowa reprezentacja obrazu, takie jak "dandelion" zdjęcia dandelion lub kolor "żółty". Można również wyodrębnić metadane dotyczące obrazu, na przykład jego rozmiar.

W tym artykule opisano przetwarzania bardziej szczegółowo obrazu oraz znajdują się wskazówki dotyczące pracy z obrazami w potoku kognitywnych wyszukiwania.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Pobierz znormalizowane obrazów

W ramach łamania dokumentu istnieją nowy zestaw parametrów konfiguracji indeksatora obsługi plików obrazu lub obrazów osadzonych w plikach. Te parametry są używane do normalizacji obrazy dla dalszego przetwarzania podrzędnego. Obrazy normalizacji sprawia, że jest ich bardziej jednolite. Duże obrazy zmieniany jest rozmiar do maksymalnej wysokości i szerokości aby stały się dostępne. Udostępnia metadane na orientację obrazów obracanie obrazów jest dostosowana do ładowania pionowej. Metadane dostosowania są przechwytywane w typie złożonym utworzone dla każdego obrazu. 

Nie można wyłączyć normalizacji obrazu. Umiejętności, które iteracja obrazy oczekiwać znormalizowane obrazów.

| Parametr konfiguracji | Opis |
|--------------------|-------------|
| imageAction   | Jeśli podjęta żadna akcja, gdy wystąpią obrazów osadzonych lub pliki obrazów, należy ustawić na "Brak". <br/>Wartość "generateNormalizedImages" Generowanie tablicę znormalizowane obrazów jako część łamania dokumentu. Te obrazy mają być widoczne w *normalized_images* pola. <br/>Wartość domyślna to "none." Ta konfiguracja jest tylko odpowiednie do obiektu blob źródeł danych, gdy "dataToExtract" ma wartość "contentAndMetadata." |
|  normalizedImageMaxWidth | Maksymalna szerokość (w pikselach) dla obrazów znormalizowane wygenerowanych. Wartość domyślna to 2000.|
|  normalizedImageMaxHeight | Maksymalna wysokość (w pikselach) dla obrazów znormalizowane wygenerowanych. Wartość domyślna to 2000.|

> [!NOTE]
> Jeśli ustawisz *imageAction* właściwości na inny niż "none", nie będzie możliwe ustawienie *parsingMode* właściwości na inny niż "domyślny".  Można ustawić tylko jedną z tych dwóch właściwości do wartości innych niż domyślne w konfiguracji indeksatora.

Ustaw **parsingMode** parametr `json` (do indeksowania każdy obiekt blob jako pojedynczego dokumentu) lub `jsonArray` (jeśli obiektów blob zawiera tablice notacji JSON, należy każdy element tablicy powinien być traktowany jako osobny dokument).

Wartość domyślna pikseli 2000 znormalizowane obrazy maksymalną szerokość i wysokość jest oparta na maksymalne rozmiary obsługiwane przez [umiejętności Rozpoznawania](cognitive-search-skill-ocr.md) i [obrazu umiejętności analizy](cognitive-search-skill-image-analysis.md). Jeśli zwiększysz maksymalnych przetwarzanie może zakończyć się niepowodzeniem na większych obrazów.


Określ imageAction w Twojej [definicji indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer) w następujący sposób:

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

Gdy *imageAction* ma ustawioną wartość "generateNormalizedImages", nowy *normalized_images* pole będzie zawierać tablicę obrazów. Obraz jest typ złożony, który ma następujące elementy:

| Element członkowski obrazu       | Opis                             |
|--------------------|-----------------------------------------|
| dane               | Ciąg znormalizowane obrazu w formacie JPEG kodowany w formacie BASE64.   |
| Szerokość              | Szerokość znormalizowane obrazu w pikselach. |
| Wysokość             | Wysokość znormalizowane obrazu w pikselach. |
| originalWidth      | Oryginalny szerokość obrazu przed normalizacji. |
| originalHeight      | Oryginalny wysokość obrazu przed normalizacji. |
| rotationFromOriginal |  Przeciwnie obrót w prawo w stopniach, który wystąpił podczas tworzenia obrazu znormalizowaną. Wartość z zakresu od 0 stopni do 360 stopni. Ten krok odczytuje metadanych z obrazu, który jest generowany przez aparat fotograficzny lub skaner. Zazwyczaj wielu 90 stopni. |
| contentOffset |Przesunięcie znaku w polu zawartości, których obraz został wyodrębniony z. To pole ma zastosowanie tylko do plików z obrazów osadzonych. |

 Przykładowa wartość *normalized_images*:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500  
  }
]
```

## <a name="image-related-skills"></a>Umiejętności dotyczące obrazów

Istnieją dwa wbudowane umiejętności kognitywnych przyjmujących obrazów jako dane wejściowe: [Rozpoznawania](cognitive-search-skill-ocr.md) i [analizy obrazu](cognitive-search-skill-image-analysis.md). 

Obecnie te umiejętności pracować tylko z obrazy generowane przez krok łamania dokumentu. W efekcie tylko obsługiwane dane wejściowe są `"/document/normalized_images"`.

### <a name="image-analysis-skill"></a>Umiejętności analizy obrazu

[Umiejętności analizy obrazu](cognitive-search-skill-image-analysis.md) wyodrębnia bogaty zestaw funkcji visual na podstawie zawartości obrazu. Można na przykład Generowanie podpisu z obrazu, generowanie tagi lub identyfikowanie znanych osób oraz punkty.

### <a name="ocr-skill"></a>Umiejętności Rozpoznawania

[Umiejętności Rozpoznawania](cognitive-search-skill-ocr.md) umożliwia wyodrębnianie tekstu z plików obrazów, takich jak mapy bitowe, jpg i PNG. Można wyodrębnić tekst oraz informacje o układzie. Informacje o układzie zapewnia obwiedni dla każdego z ciągów zidentyfikowane.

Umiejętności Rozpoznawania można wybrać algorytm używany do wykrywania tekstu w obrazów. Obecnie obsługuje dwa algorytmy, jedno dla tekstu wydruku i jedno dla pisma ręcznego.

## <a name="embedded-image-scenario"></a>Scenariusz osadzonego obrazu

Typowy scenariusz polega na tworzeniu jeden ciąg zawierający całą zawartość pliku, tekst i tekst początek obrazu, wykonując następujące czynności:  

1. [Wyodrębnij normalized_images](#get-normalized-images)
1. Uruchom przy użyciu umiejętności Rozpoznawania `"/document/normalized_images"` jako dane wejściowe
1. Scal Reprezentacja tekstowa tych obrazów tekstem raw wyodrębniony z pliku. Można użyć [scalania tekst](cognitive-search-skill-textmerger.md) umiejętności skonsolidować obie fragmentów tekstu w jednym ciągu duże.

Tworzy następujące skillset przykład *merged_text* pole zawierające tekstowej zawartości dokumentu. Obejmuje on też OCRed tekst ze wszystkich obrazów osadzonych. 

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

Teraz, gdy masz pola merged_text może zamapować go jako pole wyszukiwania w definicji indeksatora. Cała zawartość plików, łącznie z tekstem obrazów, można wyszukiwać.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Wizualizuj dane ograniczenia pola wyodrębnionego tekstu

Inny typowy scenariusz jest wizualizowanie informacji o układzie wyników wyszukiwania. Na przykład można wyróżnić, gdzie fragment tekstu został znaleziony w obrazie w ramach wyników wyszukiwania.

Ponieważ krok Rozpoznawania jest wykonywana na znormalizowane obrazów, układ współrzędnych są w obszarze znormalizowane obrazu. Podczas wyświetlania obrazu znormalizowane, obecności współrzędne zwykle nie jest problem, ale w niektórych sytuacjach możesz chcieć wyświetlić oryginalnego obrazu. W takim przypadku przekonwertować każdego współrzędnych punktu w układzie współrzędnych oryginalnego obrazu. 

Jako obiekt pomocnika Jeśli potrzebujesz do przekształcenia współrzędnych znormalizowane do oryginalnego przestrzeni współrzędnych, można użyć następującego algorytmu:

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

## <a name="see-also"></a>Zobacz także
+ [Utwórz indeksator (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ [Analizowanie umiejętności obrazu](cognitive-search-skill-image-analysis.md)
+ [Umiejętności Rozpoznawania](cognitive-search-skill-ocr.md)
+ [Tekst umiejętności scalania](cognitive-search-skill-textmerger.md)
+ [Sposób definiowania skillset](cognitive-search-defining-skillset.md)
+ [Sposób mapowania pól wzbogaconego](cognitive-search-output-field-mapping.md)
