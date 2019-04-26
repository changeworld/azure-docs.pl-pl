---
title: Dokumentacja wejściami i wyjściami, w cognitive Wyszukaj potoki — usługa Azure Search
description: Opisano składnia adnotacji oraz odwołania do adnotacji w wejść i wyjść zestawu umiejętności w usłudze wyszukiwania poznawczego potoku w usłudze Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: bfb8f5ca9b4d204b7a5efdc1b54a0fdd150e5ed6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344210"
---
# <a name="how-to-reference-annotations-in-a-cognitive-search-skillset"></a>Jak odwoływać się do adnotacji w usłudze wyszukiwania poznawczego zestawu umiejętności

W tym artykule dowiesz się, jak odwoływać się do adnotacji w definicjach umiejętności za pomocą przykładów w celu przedstawienia różnych scenariuszy. Podczas przepływu zawartości dokumentu za pomocą zestawu umiejętności, pobiera wzbogacone adnotacji. Adnotacje mogą być używane jako dane wejściowe w dalszych podrzędnego wzbogacania lub zamapowane do pola danych wyjściowych w indeksie. 
 
Przykłady w niniejszym artykule opierają się na *zawartości* pola wygenerowane automatycznie przez [indeksatory obiektów Blob Azure](search-howto-indexing-azure-blob-storage.md) w ramach fazy łamania dokumentów. Przy odwoływaniu się do dokumentów z kontenera obiektów Blob, użyj formatu takich jak `"/document/content"`, gdzie *zawartości* pole jest częścią *dokumentu*. 

## <a name="background-concepts"></a>Pojęcia dotyczące tła

Przed zapoznaniem się składni, Wróćmy do kilku ważnych pojęć, aby lepiej zrozumieć przykłady w dalszej części tego artykułu.

| Termin | Opis |
|------|-------------|
| Wzbogaconego dokumentu | Dokument wzbogaconego to wewnętrznej struktury tworzone i używane przez potok, aby pomieścić wszystkie adnotacje związane z dokumentu. Dokument wzbogaconego należy traktować jako drzewo adnotacji. Ogólnie rzecz biorąc adnotacja utworzone na podstawie poprzedniej adnotacji staje się jego podrzędny.<p/>Dokumenty wzbogaconego istnieją tylko na czas trwania wykonywanie zestawu umiejętności. Gdy zawartość jest mapowany do indeksu wyszukiwania, wzbogaconego dokumentu nie jest już potrzebny. Mimo że możesz nie wchodzą w interakcję z dokumentami wzbogaconego bezpośrednio, warto mieć mentalnego modelu dokumentów, podczas tworzenia zestawu umiejętności. |
| Wzbogacanie kontekstu | Kontekst, w którym wzbogacanie ma miejsce, zgodnie z którą jest wzbogacony elementu. Domyślnie, jest kontekst wzbogacania `"/document"` poziomu, w zakresie do poszczególnych dokumentów. Podczas wykonywania umiejętności, dane wyjściowe stają się umiejętności [właściwości zdefiniowanych kontekstu](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Przykład 1: Odwołanie proste adnotacji

W usłudze Azure Blob storage Załóżmy, że mają różne pliki zawierające odwołania do nazw osób, które mają zostać wyodrębnione przy użyciu rozpoznawanie jednostek znaku. W poniższej definicji umiejętności `"/document/content"` jest tekstową reprezentację całego dokumentu, a "ludzie" wyodrębniania pełne nazwy dla jednostek zidentyfikowane jako osoby.

Ponieważ jest domyślny kontekst `"/document"`, listy osób, można teraz przywoływać jako `"/document/people"`. W tym konkretnym przypadku `"/document/people"` jest adnotacja, który może teraz zostać zamapowane do pola w indeksie ani wykorzystywać w ramach innego umiejętności, w tym samym zestawu umiejętności.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
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

## <a name="example-2-reference-an-array-within-a-document"></a>Przykład 2: Odwoływać się do tablicy w dokumencie

W tym przykładzie opiera się na poprzedni omawiający wywoływanie etap wzbogacania wiele razy, za pomocą tego samego dokumentu. Załóżmy, że w poprzednim przykładzie wygenerowany tablicy ciągów z 10 nazwiska osób na podstawie pojedynczego dokumentu. Uzasadnione, następnym krokiem może być wzbogacania drugi, który wyodrębnia nazwiska z pełną nazwę. Ponieważ nie zawiera nazwy 10, chcesz, aby ten krok jest wywoływana 10 razy w tym dokumencie, raz dla każdej osoby. 

Aby wywołać odpowiednią liczbę iteracji, Ustaw kontekst jako `"/document/people/*"`, gdzie znak gwiazdki (`"*"`) reprezentuje wszystkie węzły w dokumencie wzbogaconego jako elementy podrzędne `"/document/people"`. Mimo że w tablicy umiejętności tej umiejętności jest definiowana tylko raz, dopóki wszystkie elementy członkowskie są przetwarzane jest wywoływana dla każdego elementu członkowskiego, w tym dokumencie.

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

W przypadku tablic lub kolekcji ciągów adnotacji można pod kątem określonych członków, a nie do tablicy jako całości. Powyższy przykład generuje adnotacja o nazwie `"last"` w każdym węźle reprezentowanej przez kontekst. Jeśli chcesz odwołać się do tej rodziny adnotacji, można użyć składni `"/document/people/*/last"`. Jeśli chcesz odwołać się do określonego adnotacji, można użyć indeksu jawne: `"/document/people/1/last`"Aby odwołać się do nazwisko pierwszą osobą, która identyfikowane w dokumencie. Zwróć uwagę, w tej składni tablice są "indeksowane 0".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Przykład 3: Składowe odwołania w tablicy

Czasami zachodzi potrzeba grupy wszystkie adnotacje konkretnego typu do przekazania ich do określonej umiejętności. Należy wziąć pod uwagę hipotetyczny umiejętności niestandardowe, która identyfikuje najbardziej typowe nazwisko ostatniej nazw wyodrębnionych w przykładzie 2. Aby zapewnić tylko nazwiska do niestandardowych umiejętności, określanie kontekstu jako `"/document"` i dane wejściowe jako `"/document/people/*/lastname"`.

Należy pamiętać, że relacja `"/document/people/*/lastname"` jest większy niż w przypadku dokumentów. Może istnieć 10 węzłów lastname, gdy istnieje tylko jeden węzeł dokumentu dla tego dokumentu. W takim przypadku system automatycznie utworzy tablicę `"/document/people/*/lastname"` zawierający wszystkie elementy w dokumencie.

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
+ [Jak integrowanie umiejętności niestandardowych wzbogacony potok](cognitive-search-custom-skill-interface.md)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie zestawu umiejętności (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Sposób mapowania pól wzbogaconego do indeksu](cognitive-search-output-field-mapping.md)
