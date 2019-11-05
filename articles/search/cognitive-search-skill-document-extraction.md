---
title: Umiejętność wyszukiwania poznawczego dokumentów
titleSuffix: Azure Cognitive Search
description: Wyodrębnia zawartość z pliku w ramach potoku wzbogacania.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: 8656896fe1a113ab143c43b4d1973e4196c5f087
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512199"
---
# <a name="document-extraction-cognitive-skill"></a>Umiejętność wyodrębniania dokumentów

Umiejętność **wyodrębniania dokumentu** wyodrębnia zawartość z pliku w ramach potoku wzbogacania. Dzięki temu można wykorzystać krok wyodrębniania dokumentu, który zwykle występuje przed wykonaniem zestawu umiejętności z plikami, które mogą być generowane przez inne umiejętności.

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu krakingania dokumentu w indeksie. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika](https://go.microsoft.com/fwlink/?linkid=2042400).
## <a name="odatatype"></a>@odata.type  
Microsoft. umiejętności. util. DocumentExtractionSkill

## <a name="skill-parameters"></a>Parametry umiejętności

W parametrach jest rozróżniana wielkość liter.

| Dane wejściowe            | Dozwolone wartości | Opis |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Ustaw na `default` na potrzeby wyodrębniania dokumentów z plików, które nie są czystym tekstem lub formatem JSON. Ustaw `text`, aby zwiększyć wydajność na zwykłych plikach tekstowych. Ustaw na `json`, aby wyodrębnić zawartość strukturalną z plików JSON. Jeśli `parsingMode` nie jest jawnie zdefiniowana, zostanie ustawiona na `default`. |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Ustaw na `contentAndMetadata`, aby wyodrębnić wszystkie metadane i zawartość tekstową z każdego pliku. Ustaw na `allMetadata`, aby wyodrębnić tylko [metadane specyficzne dla typu zawartości](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (na przykład metadane unikatowe dla plików PNG). Jeśli `dataToExtract` nie jest jawnie zdefiniowana, zostanie ustawiona na `contentAndMetadata`. |
| `configuration` | Zobacz poniżej. | Słownik parametrów opcjonalnych, który dostosowuje sposób wykonywania wyodrębniania dokumentu. Zapoznaj się z poniższą tabelą opisów obsługiwanych właściwości konfiguracji. |

| Parametr konfiguracji   | Dozwolone wartości | Opis |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Ustaw wartość `none` na ignorowanie obrazów osadzonych lub plików obrazów w zestawie danych. Jest to wartość domyślna. <br/>W celu przeprowadzenia [analizy obrazów przy użyciu umiejętności poznawczych](cognitive-search-concept-image-scenarios.md)ustaw wartość `generateNormalizedImages`, aby umiejętność utworzyła tablicę znormalizowanych obrazów jako część pęknięcia dokumentu. Ta akcja wymaga, aby `parsingMode` została ustawiona na `default` i `dataToExtract` jest ustawiona na `contentAndMetadata`. Znormalizowany obraz odnosi się do dodatkowego przetwarzania, które powoduje, że dane wyjściowe obrazu są skalowane, rozmiar i obrócony, aby podwyższyć poziom renderowania po dołączeniu obrazów do wyników wyszukiwania wizualnego [ Demonstracja](https://github.com/Microsoft/AzureSearch_JFK_Files)). Te informacje są generowane dla każdego obrazu przy użyciu tej opcji.  <br/>Jeśli ustawisz na `generateNormalizedImagePerPage`, pliki PDF będą traktowane inaczej niż w przypadku wyodrębniania obrazów osadzonych, każda strona będzie renderowana jako obraz i znormalizowana.  Typy plików inne niż PDF będą traktowane tak samo, jak w przypadku ustawienia `generateNormalizedImages`.
| `normalizedImageMaxWidth` | Dowolna liczba całkowita z zakresu od 50-10000 | Maksymalna szerokość (w pikselach) dla wygenerowanych znormalizowanych obrazów. Wartość domyślna to 2000. | 
| `normalizedImageMaxHeight` | Dowolna liczba całkowita z zakresu od 50-10000 | Maksymalna wysokość (w pikselach) dla wygenerowanych znormalizowanych obrazów. Wartość domyślna to 2000. |

> [!NOTE]
> Domyślnie 2000 pikseli dla znormalizowanych obrazów Maksymalna szerokość i wysokość opiera się na maksymalnym rozmiarze obsługiwanym przez [umiejętność OCR](cognitive-search-skill-ocr.md) i [umiejętności analizy obrazu](cognitive-search-skill-image-analysis.md). [Umiejętność OCR](cognitive-search-skill-ocr.md) obsługuje maksymalną szerokość i wysokość 4200 dla języków innych niż angielski i 10000 w języku angielskim.  W przypadku zwiększenia maksymalnych limitów przetwarzanie może zakończyć się niepowodzeniem w przypadku większych obrazów, w zależności od definicji zestawu umiejętności i języka dokumentów. 
## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

| Nazwa wejściowa     | Opis |
|--------------------|-------------|
| file_data | Plik, z którego ma zostać wyodrębniona zawartość. |

Dane wejściowe "file_data" muszą być obiektami zdefiniowanymi w następujący sposób:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Ten obiekt odwołania do pliku może być wygenerowany jeden z trzech sposobów:

 - Ustawianie parametru `allowSkillsetToReadFileData` w definicji indeksatora na wartość "true".  Spowoduje to utworzenie ścieżki `/document/file_data`, która jest obiektem reprezentującym oryginalne dane pliku pobrane ze źródła danych obiektu BLOB. Ten parametr ma zastosowanie tylko do danych w magazynie obiektów BLOB.

 - Ustawianie parametru `imageAction` w definicji indeksatora na wartość inną niż `none`.  Spowoduje to utworzenie tablicy obrazów `/document/normalized_images`, które są zgodne z wymaganą Konwencją dla danych wejściowych do tej umiejętności, jeśli są one przesyłane pojedynczo (tj. `/document/normalized_images/*`).

 - Posiadanie niestandardowej kwalifikacji zwraca obiekt JSON zdefiniowany dokładnie tak jak powyżej.  Parametr `$type` musi mieć ustawioną wartość dokładnie `file`, a parametr `data` musi być podstawowym 64 zakodowanym bajtem danych tablicy zawartości pliku.

## <a name="skill-outputs"></a>Wyniki umiejętności

| Nazwa wyjściowa    | Opis |
|--------------|-------------|
| content | Zawartość tekstowa dokumentu. |
| normalized_images | Gdy `imageAction` jest ustawiona na wartość inną niż `none`, nowe pole *normalized_images* będzie zawierać tablicę obrazów. Zapoznaj [się z dokumentacją dotyczącą wyodrębniania obrazów](cognitive-search-concept-image-scenarios.md) , aby uzyskać szczegółowe informacje na temat formatu danych wyjściowych każdego obrazu. |

##  <a name="sample-definition"></a>Definicja Przykładowa

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "content"
      },
      {
        "name": "normalized_images",
        "targetName": "normalized_images"
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
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
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
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Jak przetwarzać i wyodrębniać informacje z obrazów w scenariuszach wyszukiwania poznawczego](cognitive-search-concept-image-scenarios.md)