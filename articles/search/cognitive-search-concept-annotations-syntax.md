---
title: Wejście referencyjne i wyjście w potokach wyszukiwania poznawczego — Azure Search
description: Objaśnia składnię adnotacji oraz sposób odwoływania się do adnotacji w danych wejściowych i wyjściowych zestawu umiejętności w potoku wyszukiwania poznawczego w Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.subservice: cognitive-search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 1868e9fd3a7dde5d6302753986019f481a577007
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841300"
---
# <a name="how-to-reference-annotations-in-a-cognitive-search-skillset"></a>Jak odwoływać się do adnotacji w zestawu umiejętności wyszukiwania poznawczego

W tym artykule dowiesz się, jak odwoływać się do adnotacji w definicjach umiejętności, używając przykładów do zilustrowania różnych scenariuszy. Zawartość dokumentu jest przepływa przez zestaw umiejętności, ale jest wzbogacana z adnotacjami. Adnotacje mogą być używane jako dane wejściowe do dalszej wzbogacenia lub zamapowane do pola danych wyjściowych w indeksie. 
 
Przykłady w tym artykule są oparte na polu *zawartości* wygenerowanej automatycznie przez [indeksatory obiektów blob platformy Azure](search-howto-indexing-azure-blob-storage.md) jako część fazy tworzenia dokumentu. W przypadku odwoływania się do dokumentów z kontenera obiektów BLOB Użyj formatu, `"/document/content"`takiego jak, gdzie pole *Content* jest częścią *dokumentu*. 

## <a name="background-concepts"></a>Koncepcje w tle

Przed przejrzeniem składni należy ponownie odwiedzić kilka ważnych koncepcji, aby lepiej zrozumieć przykłady opisane w dalszej części tego artykułu.

| Termin | Opis |
|------|-------------|
| Wzbogacony dokument | Wzbogacony dokument to wewnętrzna struktura utworzona i używana przez potok do przechowywania wszystkich adnotacji związanych z dokumentem. Zastanów się, że wzbogacony dokument jest drzewem adnotacji. Na ogół adnotacja utworzona na podstawie poprzedniej adnotacji staną się jej elementami podrzędnymi.<p/>Dokumenty wzbogacone istnieją tylko na czas wykonywania zestawu umiejętności. Gdy zawartość jest zamapowana na indeks wyszukiwania, wzbogacony dokument nie będzie już potrzebne. Chociaż nie można bezpośrednio korzystać z ulepszonych dokumentów, warto mieć model psychiczny dokumentów podczas tworzenia zestawu umiejętności. |
| Kontekst wzbogacania | Kontekst, w którym odbywa się wzbogacanie, w odniesieniu do którego element jest wzbogacany. Domyślnie kontekst wzbogacania jest na `"/document"` poziomie, w zakresie do poszczególnych dokumentów. Po uruchomieniu umiejętności dane wyjściowe tej umiejętności stają się właściwościami [zdefiniowanego kontekstu](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Przykład 1: Proste odwołanie do adnotacji

Załóżmy, że w usłudze Azure Blob Storage istnieje wiele plików zawierających odwołania do nazw osób, które mają zostać wyodrębnione przy użyciu funkcji rozpoznawania jednostek. W poniższej `"/document/content"` definicji umiejętności jest tekstową reprezentacją całego dokumentu, a "osoby" to wyodrębnianie pełnych nazw dla jednostek zidentyfikowanych jako osoby.

Ze względu na to `"/document"`, że domyślny kontekst to, lista osób może teraz `"/document/people"`być przywoływana jako. W tym konkretnym `"/document/people"` przypadku jest adnotacją, która została teraz zamapowana na pole w indeksie lub użyte w innej umiejętności w tym samym zestawu umiejętności.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```

<a name="example-2"></a>

## <a name="example-2-reference-an-array-within-a-document"></a>Przykład 2: Odwołuje się do tablicy w dokumencie

Ten przykład kompiluje na poprzednim, pokazujący, jak wywołać krok wzbogacania wielokrotnie w tym samym dokumencie. Założono, że poprzedni przykład wygenerował tablicę ciągów z 10 nazwami osób z jednego dokumentu. Rozsądny następny krok może stanowić drugie wzbogacanie, które wyodrębnia nazwisko z pełnej nazwy. Ponieważ istnieją 10 nazw, ten krok ma zostać wywołany 10 razy w tym dokumencie raz dla każdej osoby. 

Aby wywoływać właściwą liczbę iteracji, ustaw dla kontekstu jako `"/document/people/*"`, gdzie gwiazdka (`"*"`) reprezentuje wszystkie węzły w wzbogaconym `"/document/people"`dokumencie jako elementy podrzędne. Chociaż ta umiejętność jest definiowana tylko raz w tablicy umiejętności, jest wywoływana dla każdego elementu członkowskiego w dokumencie do momentu przetworzenia wszystkich członków.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the last name from a full name",
    "uri": "http://names.azurewebsites.net/api/GetLastName",
    "context" : "/document/people/*",
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "fullname",
        "source": "/document/people/*"
      }
    ],
    "outputs": [
      {
        "name": "lastname",
        "targetName": "last"
      }
    ]
  }
```

Gdy adnotacje są tablicami lub kolekcjami ciągów, możesz chcieć wskazać konkretne elementy członkowskie zamiast tablicy jako całości. Powyższy przykład generuje adnotację o `"last"` nazwie w każdym węźle reprezentowanego przez kontekst. Jeśli chcesz odwołać się do tej rodziny adnotacji, możesz użyć składni `"/document/people/*/last"`. Jeśli chcesz odwołać się do konkretnej adnotacji, możesz użyć jawnego indeksu: `"/document/people/1/last`", aby odwołać się do nazwiska pierwszej osoby wskazanej w dokumencie. Zwróć uwagę, że w tej tablicy składni są "0 indeksowane".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Przykład 3: Odwołania do elementów członkowskich w tablicy

Czasami trzeba zgrupować wszystkie adnotacje danego typu, aby przekazać je do określonej umiejętności. Rozważmy hipotetyczną niestandardową umiejętność, która identyfikuje najbardziej typowe nazwisko ze wszystkich ostatnich nazw wyodrębnionych w przykładzie 2. Aby podać tylko ostatnie nazwy niestandardowej umiejętności, Określ kontekst jako `"/document"` i dane wejściowe jako. `"/document/people/*/lastname"`

Zauważ, że Kardynalność `"/document/people/*/lastname"` jest większa niż wartość dokumentu. Może istnieć 10 węzłów LastName, gdy istnieje tylko jeden węzeł dokumentu dla tego dokumentu. W takim przypadku system automatycznie utworzy tablicę `"/document/people/*/lastname"` zawierającą wszystkie elementy w dokumencie.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the most common string from an array of strings",
    "uri": "http://names.azurewebsites.net/api/MostCommonString",
    "context" : "/document",
    "inputs": [
      {
        "name": "strings",
        "source": "/document/people/*/lastname"
      }
    ],
    "outputs": [
      {
        "name": "mostcommon",
        "targetName": "common-lastname"
      }
    ]
  }
```



## <a name="see-also"></a>Zobacz także
+ [Jak zintegrować niestandardową umiejętność z potokiem wzbogacania](cognitive-search-custom-skill-interface.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Utwórz zestawu umiejętności (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Jak zmapować wzbogacone pola na indeks](cognitive-search-output-field-mapping.md)
