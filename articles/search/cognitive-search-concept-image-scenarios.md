---
title: Przetwarzanie i wyodrębnianie tekstu z obrazów w usłudze wyszukiwania poznawczego — usługa Azure Search
description: Przetwarzanie i wyodrębnianie tekstu oraz inne informacje z obrazów w cognitive Wyszukaj potoki w usłudze Azure Search.
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
ms.openlocfilehash: 8bea47467d141869b1a668668bc57451a882a54b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448447"
---
#  <a name="how-to-process-and-extract-information-from-images-in-cognitive-search-scenarios"></a>Jak przetwarzanie i wyodrębnianie informacji z obrazów w scenariuszach wyszukiwania kognitywnego

Usługa cognitive search ma kilka możliwości do pracy z obrazami i pliki obrazów. Podczas łamania dokumentów można używać *imageAction* parametru do wyodrębniania tekstu z zdjęć lub obrazów zawierających alfanumeryczne tekstu, na przykład wyraz "STOP" Logowanie zatrzymania. Inne scenariusze obejmują generowania tekstowa reprezentacja obrazu, takie jak "dandelion" zdjęcia dandelion lub kolor "żółty". Można również wyodrębnić metadane dotyczące obrazu, na przykład jego rozmiar.

Ten artykuł obejmuje przetwarzania bardziej szczegółowo obrazów i znajdują się wskazówki dotyczące pracy z obrazów w usłudze wyszukiwania poznawczego potoku.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Pobieranie znormalizowane obrazów

W ramach łamania dokumentów istnieje nowy zestaw parametrów konfiguracji indeksatora do obsługi plików obrazów lub obrazów osadzonych w plikach. Te parametry są używane do normalizacji obrazy do dalszego przetwarzania transmisji dla klientów. Normalizowanie obrazów sprawia, że jest ich bardziej jednolite. Duże obrazy zmieniany jest rozmiar do maksymalnej wysokości i szerokości, aby były w użyciu. W przypadku obrazów udostępniające metadane dotyczące orientacji obracanie obrazów jest uwzględniany pionowy ładowania. Korekty metadane są przechwytywane w typie złożonym utworzone dla każdego obrazu. 

Nie można wyłączyć normalizacji obrazu. Umiejętności, które przechodzą przez obrazów oczekiwać, że obrazy znormalizowana. Włączenie normalizacji obrazu w indeksatorze, wymaga, że zestawu umiejętności jest dołączony do ten indeksator.

| Parametr konfiguracji | Opis |
|--------------------|-------------|
| imageAction   | Jeśli nie działania powinny zostać podjęte, gdy wystąpią obrazy osadzone lub pliki obrazów, należy ustawić na "none". <br/>Ustawienie "generateNormalizedImages" powoduje generowanie tablicę znormalizowane obrazów jako część łamania dokumentów.<br/>Ustawienie "generateNormalizedImagePerPage" Generowanie tablicę znormalizowane obrazów gdzie dla plików PDF w źródle danych, każda strona jest renderowany do jednego obrazu danych wyjściowych.  Funkcjonalność jest taka sama jak "generateNormalizedImages" dla typów plików innych niż PDF.<br/>Dla każdej opcji, która nie jest "none", obrazy będą widoczne w *normalized_images* pola. <br/>Wartość domyślna to "none". Ta konfiguracja jest tylko odpowiednie do obiektu blob źródła danych, gdy "dataToExtract" ma wartość "contentAndMetadata." <br/>Maksymalnie 1000 obrazów zostaną wyodrębnione z danego dokumentu. Jeśli istnieje więcej niż 1000 obrazów w dokumencie, zostaną wyodrębnione pierwsze 1000 i zostaną wygenerowane ostrzeżenie. |
|  normalizedImageMaxWidth | Maksymalna szerokość (w pikselach) dla obrazów znormalizowane wygenerowanych. Wartość domyślna to 2000.|
|  normalizedImageMaxHeight | Maksymalna wysokość (w pikselach) dla obrazów znormalizowane wygenerowanych. Wartość domyślna to 2000.|

> [!NOTE]
> Jeśli ustawisz *imageAction* właściwość coś innego niż "none", nie będzie możliwe ustawienie *parsingMode* właściwość coś innego niż "default".  Możesz mogą ustawić tylko jedną z tych dwóch właściwości do wartości innych niż domyślne konfiguracji indeksatora.

Ustaw **parsingMode** parametr `json` (do indeksowania każdy obiekt blob jako pojedynczy dokument) lub `jsonArray` (jeżeli obiektów blob zawierają tablice notacji JSON i potrzebny każdego elementu tablicy, powinien być traktowany jako oddzielny dokument).

Wartość domyślna pikseli 2000 znormalizowane obrazów maksymalną szerokość i wysokość opiera się na maksymalne rozmiary obsługiwane przez [umiejętności optyczne rozpoznawanie znaków](cognitive-search-skill-ocr.md) i [obrazu umiejętności analizy](cognitive-search-skill-image-analysis.md). Zwiększenie maksymalnych limitów przetwarzania może nie działać w przypadku większych obrazów.


Określ imageAction w swojej [definicja indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer) w następujący sposób:

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

Gdy *imageAction* jest ustawiona na wartość innych następnie "none", nowy *normalized_images* pole będzie zawierać tablicę obrazów. Każdy obraz jest typ złożony, który ma następujące składowe:

| Element członkowski obrazu       | Opis                             |
|--------------------|-----------------------------------------|
| data               | Ciąg znormalizowane obrazu w formacie JPEG, zakodowany w formacie BASE64.   |
| Szerokość              | Szerokość znormalizowane obrazu w pikselach. |
| Wysokość             | Wysokość znormalizowane obrazu w pikselach. |
| originalWidth      | Oryginalna szerokość obrazu przed normalizacji. |
| originalHeight      | Oryginalna wysokość obrazu przed normalizacji. |
| rotationFromOriginal |  Przeciwnie do ruchu wskazówek zegara obrotu w stopniach, który wystąpił, aby utworzyć obraz znormalizowana. Wartość z zakresu od 0 stopni do 360 stopni. W tym kroku czyta metadane z obrazu, który jest generowany przez aparat fotograficzny lub skaner. Zazwyczaj wielu 90 stopni. |
| contentOffset |Przesunięcie znaku w polu zawartości, których obraz został wyodrębniony z. To pole dotyczy tylko plików przy użyciu obrazów osadzonych. |

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

## <a name="image-related-skills"></a>Umiejętności związane z obrazu

Istnieją dwie wbudowane umiejętności poznawcze, które przyjmują obrazów jako dane wejściowe: [Optyczne rozpoznawanie znaków](cognitive-search-skill-ocr.md) i [obrazu analizy](cognitive-search-skill-image-analysis.md). 

Obecnie tych umiejętności działają tylko z obrazów wygenerowanych z kroku łamania dokumentów. W efekcie tylko obsługiwane dane wejściowe są `"/document/normalized_images"`.

### <a name="image-analysis-skill"></a>Umiejętności analizy obrazów

[Umiejętności analizy obrazów](cognitive-search-skill-image-analysis.md) wyodrębnia bogaty zestaw funkcji visual na podstawie zawartości obrazu. Na przykład można generować podpis z obrazu, generowanie tagi lub zidentyfikować osobistości i charakterystycznych elementów krajobrazu.

### <a name="ocr-skill"></a>Optyczne rozpoznawanie znaków umiejętności

[Umiejętności optyczne rozpoznawanie znaków](cognitive-search-skill-ocr.md) umożliwia wyodrębnianie tekstu z plików obrazów, takie jak mapy bitowe, jpg i PNG. Można wyodrębnić, tekst oraz informacji o układzie. Informacje o układzie zapewnia obwiedni ciągi zidentyfikowane.

Umiejętności optyczne rozpoznawanie znaków, można wybrać algorytm używany do wykrywania tekstu do obrazów. Obecnie obsługuje dwa algorytmy, jeden dla drukowanego tekstu i inny dla tekstu odręcznego.

## <a name="embedded-image-scenario"></a>Scenariusz osadzony obraz

Typowy scenariusz obejmuje, tworząc jeden ciąg zawierający całą zawartość pliku, tekst i tekstu początek obrazu, wykonując następujące czynności:  

1. [Wyodrębnij normalized_images](#get-normalized-images)
1. Uruchamianie przy użyciu umiejętności optyczne rozpoznawanie znaków `"/document/normalized_images"` jako dane wejściowe
1. Scal tekstowa reprezentacja tych obrazów z nieprzetworzony tekst wyodrębnione z pliku. Możesz użyć [scalania tekstu](cognitive-search-skill-textmerger.md) konsolidować zarówno fragmentów tekstu w jeden ciąg duże i umiejętności.

Tworzy następujące zestawu umiejętności przykład *merged_text* pole zawierające zawartości tekstowej dokumentu. Zawiera on również tekst OCRed ze wszystkich obrazów osadzonych. 

#### <a name="request-body-syntax"></a>Składnia treść żądania
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

Teraz, gdy pole merged_text, możesz zamapować jako polu możliwym do przeszukania w definicji indeksatora. Całą zawartość plików, w tym tekstu, obrazów, można wyszukać.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Wizualizuj blokujących pola wyodrębniony tekst

Innym typowym scenariuszem jest wizualizowanie informacji o układzie wyników wyszukiwania. Na przykład można wyróżnić, gdzie fragment tekstu można odnaleźć obrazu jako część wyników wyszukiwania.

Ponieważ krok optyczne rozpoznawanie znaków jest wykonywana na znormalizowaną obrazów, układ współrzędne są podawane w miejsce znormalizowane obrazu. Podczas wyświetlania obrazu znormalizowany, obecności współrzędne zwykle nie jest problemem, ale w niektórych sytuacjach możesz chcieć wyświetlić oryginalny obraz. W tym przypadku przekonwertować każdego współrzędnych punktu w układzie w oryginalnym układzie współrzędnych obrazu. 

Jako pomocnika Jeśli potrzebujesz do przekształcania znormalizowanych współrzędnych do oryginalnego przestrzeni współrzędnych, można użyć następującego algorytmu:

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
+ [Tworzenie indeksatora (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ [Analizowanie obrazu umiejętności](cognitive-search-skill-image-analysis.md)
+ [Optyczne rozpoznawanie znaków umiejętności](cognitive-search-skill-ocr.md)
+ [Umiejętności scalania tekstu](cognitive-search-skill-textmerger.md)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Sposób mapowania pól wzbogacony](cognitive-search-output-field-mapping.md)
