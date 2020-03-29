---
title: Umiejętności poznawcze wyodrębniania dokumentów (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Wyodrębnia zawartość z pliku w potoku wzbogacania. Ta umiejętność jest obecnie w publicznej wersji zapoznawczej.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: 0f67caad03c4ebd1cf8f3721f377d8362219016a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76837735"
---
# <a name="document-extraction-cognitive-skill"></a>Umiejętności poznawcze wyodrębniania dokumentów

> [!IMPORTANT] 
> Ta umiejętność jest obecnie w publicznej wersji zapoznawczej. Funkcja w wersji zapoznawczej jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Obecnie nie ma obsługi portalu ani SDK .NET.

**Umiejętności wyodrębniania dokumentów** wyodrębnia zawartość z pliku w potoku wzbogacania. Dzięki temu można skorzystać z kroku wyodrębniania dokumentu, który zwykle dzieje się przed wykonaniem zestawu umiejętności z plikami, które mogą być generowane przez inne umiejętności.

> [!NOTE]
> W miarę rozszerzania zakresu poprzez zwiększanie częstotliwości przetwarzania, dodawanie większej liczby dokumentów lub dodawanie kolejnych algorytmów sztucznej inteligencji należy [dołączyć rozliczany zasób usług Cognitive Services.](cognitive-search-attach-cognitive-services.md) Opłaty naliczane podczas wywoływania interfejsów API w usługach Cognitive Services i wyodrębniania obrazu jako część etapu pękania dokumentu w indeksowaniu. Nie ma żadnych opłat za wyodrębnianie tekstu z dokumentów.
>
> Wykonanie wbudowanych umiejętności jest naliczane według istniejącej [ceny płatności zgodnie z rzeczywistymi oczekiwaniami.](https://azure.microsoft.com/pricing/details/cognitive-services/) Ceny ekstrakcji obrazów są opisane na [stronie cenowej](https://go.microsoft.com/fwlink/?linkid=2042400).
## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Dane wejściowe            | Dozwolone wartości | Opis |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Ustaw `default` do wyodrębniania dokumentów z plików, które nie są czystym tekstem lub json. Ustaw, `text` aby poprawić wydajność plików tekstowych w postaci zwykłego tekstu. Ustaw, `json` aby wyodrębnić zawartość strukturalną z plików json. Jeśli `parsingMode` nie jest wyraźnie zdefiniowany, `default`zostanie ustawiony na . |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Ustaw, `contentAndMetadata` aby wyodrębnić wszystkie metadane i zawartość tekstową z każdego pliku. Ustaw, `allMetadata` aby wyodrębnić tylko [metadane określonego typu zawartości](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (na przykład metadane unikatowe tylko dla plików png). Jeśli `dataToExtract` nie jest wyraźnie zdefiniowany, `contentAndMetadata`zostanie ustawiony na . |
| `configuration` | Sprawdź poniżej. | Słownik parametrów opcjonalnych, które dostosowują sposób wyodrębniania dokumentu jest wykonywana. Opisy obsługiwanych właściwości konfiguracji można znaleźć w poniższej tabeli. |

| Parametr konfiguracji   | Dozwolone wartości | Opis |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Ustawienie `none` do ignorowania osadzonych obrazów lub plików obrazów w zestawie danych. Domyślnie włączone. <br/>Do [analizy obrazu przy użyciu umiejętności poznawczych,](cognitive-search-concept-image-scenarios.md)ustawić, aby `generateNormalizedImages` umiejętności utworzyć tablicę znormalizowanych obrazów w ramach pękania dokumentów. Ta akcja `parsingMode` wymaga, `default` aby `dataToExtract` ustawiono `contentAndMetadata`i jest ustawiona na . Znormalizowany obraz odnosi się do dodatkowego przetwarzania, co powoduje jednolite wydruki obrazu, rozmiar i obrócony w celu promowania spójnego renderowania podczas dołączania obrazów do wizualnych wyników wyszukiwania (na przykład zdjęć o tym samym rozmiarze w formancie wykresu, jak pokazano w [pokazie JFK).](https://github.com/Microsoft/AzureSearch_JFK_Files) Te informacje są generowane dla każdego obrazu podczas korzystania z tej opcji.  <br/>Jeśli ustawisz , `generateNormalizedImagePerPage`pliki PDF będą traktowane inaczej w tym, że zamiast wyodrębniania osadzonych obrazów, każda strona będzie renderowana jako obraz i odpowiednio znormalizowane.  Typy plików innych niż PDF będą `generateNormalizedImages` traktowane tak samo, jak gdyby zostały ustawione.
| `normalizedImageMaxWidth` | Dowolna liczna liczą od 50 do 10000 | Maksymalna szerokość (w pikselach) dla generowanych obrazów znormalizowanych. Wartość domyślna to 2000. | 
| `normalizedImageMaxHeight` | Dowolna liczna liczą od 50 do 10000 | Maksymalna wysokość (w pikselach) dla generowanych obrazów znormalizowanych. Wartość domyślna to 2000. |

> [!NOTE]
> Domyślna wartość 2000 pikseli dla znormalizowanych obrazów maksymalna szerokość i wysokość jest oparta na maksymalnych rozmiarach obsługiwanych przez [umiejętność OCR](cognitive-search-skill-ocr.md) i [analizę obrazu](cognitive-search-skill-image-analysis.md). Umiejętność [OCR](cognitive-search-skill-ocr.md) obsługuje maksymalną szerokość i wysokość 4200 dla języków innych niż angielski i 10000 dla języka angielskiego.  Jeśli zwiększysz maksymalne limity, przetwarzanie może zakończyć się niepowodzeniem na większych obrazach w zależności od definicji zestawu umiejętności i języka dokumentów. 
## <a name="skill-inputs"></a>Wprowadzanie umiejętności

| Nazwa wejściowa     | Opis |
|--------------------|-------------|
| file_data | Plik, z których zawartość powinna zostać wyodrębniona. |

Wejście "file_data" musi być obiektem zdefiniowanym w następujący sposób:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Ten obiekt odwołania do pliku może być generowany na jeden z 3 sposobów:

 - Ustawianie `allowSkillsetToReadFileData` parametru w definicji indeksatora na "true".  Spowoduje to utworzenie `/document/file_data` ścieżki, która jest obiektem reprezentującym oryginalne dane pliku pobrane ze źródła danych obiektu blob. Ten parametr dotyczy tylko danych w magazynie obiektów Blob.

 - Ustawianie `imageAction` parametru w definicji indeksatora na wartość inną niż `none`.  Spowoduje to utworzenie tablicy obrazów, która jest zgodna z wymaganą konwencją `/document/normalized_images/*`dla wprowadzania danych do tej umiejętności, jeśli zostanie przekazana indywidualnie (tj. ).

 - Posiadanie umiejętności niestandardowych zwraca obiekt json zdefiniowany dokładnie tak jak powyżej.  Parametr `$type` musi być ustawiony `file` dokładnie `data` i parametr musi być podstawową 64 zakodowanych danych tablicy bajtów zawartości pliku.

## <a name="skill-outputs"></a>Wyniki umiejętności

| Nazwa wyjściowa    | Opis |
|--------------|-------------|
| content | Tekstowa treść dokumentu. |
| normalized_images | Gdy `imageAction` wartość jest ustawiona `none`na wartość inną, nowe pole *normalized_images* będzie zawierać tablicę obrazów. Więcej informacji na temat formatu wyjściowego każdego obrazu można znaleźć [w dokumentacji wyodrębniania obrazów.](cognitive-search-concept-image-scenarios.md) |

##  <a name="sample-definition"></a>Przykładowa definicja

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
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
+ [Jak przetwarzać i wyodrębniać informacje z obrazów w scenariuszach wyszukiwania poznawczego](cognitive-search-concept-image-scenarios.md)