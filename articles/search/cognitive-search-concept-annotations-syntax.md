---
title: Odwołanie adnotacji w danych wejściowych i wyjściowych w potoku kognitywnych wyszukiwania w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Opisano składni adnotacji oraz odwołania adnotacji w danych wejściowych i wyjściowych skillset w potoku kognitywnych wyszukiwania w usłudze Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 2e838e9c94d5b19565bea3d02890fe6164bb37d0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-reference-annotations-in-a-cognitive-search-skillset"></a>Jak adnotacje odwołania w skillset kognitywnych wyszukiwania

W tym artykule należy Dowiedz się, jak odwołać adnotacje w definicjach umiejętności pomocą przykładów w celu zilustrowania różnych scenariuszy. Jak zawartość dokumentu przechodzi przez zestaw umiejętności, pobiera wzbogacone przy użyciu adnotacji. Adnotacje mogą być używane jako dane wejściowe dla dalszego podrzędne wzbogacenia lub zamapowane do pola danych wyjściowych w indeksie. 
 
Przykłady w tym artykule są oparte na *zawartości* pola wygenerowany automatycznie przez [indeksatory obiektów Blob platformy Azure](search-howto-indexing-azure-blob-storage.md) jako część dokumentu łamania fazy. Podczas odwoływania się do dokumentów z kontenera obiektów Blob, użyj formatu takiego jak `"/document/content"`, gdzie *zawartości* pole jest częścią *dokumentu*. 

## <a name="background-concepts"></a>Pojęcia dotyczące tła

Przed przeglądu składnię, możemy ponownie kilka ważnych pojęć, aby lepiej zrozumieć przykłady podane w dalszej części tego artykułu.

| Termin | Opis |
|------|-------------|
| Wzbogaconego dokumentu | Dokument wzbogaconego to wewnętrznej struktury tworzone i używane przez potok do przechowywania wszystkie adnotacje związane z dokumentu. Dokument wzbogaconego można traktować jako drzewo adnotacji. Ogólnie rzecz biorąc adnotacja utworzone na podstawie poprzedniego adnotacji staje się elementem podrzędnym.<p/>Istnieją dokumenty wzbogaconego tylko skillset wykonywania. Zmapowaniu zawartość do indeksu wyszukiwania wzbogaconego dokumentu nie jest już potrzebne. Chociaż użytkownik nie obsługuje dokumenty wzbogaconego bezpośrednio, jest warto mieć psychicznego modelu dokumentów, podczas tworzenia skillset. |
| Kontekst wzbogacenia | Kontekst, w którym wzbogacenie ma miejsce, zgodnie z którą jest wzbogacone elementu. Domyślnie, kontekst wzbogacenia jest `"/document"` poziom ograniczone do poszczególnych dokumentów. Jeśli program umiejętności, dane wyjściowe staną się umiejętności [właściwości kontekstu zdefiniowanych](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Przykład 1: Odwołanie adnotacji prosty

W magazynie obiektów Blob platformy Azure Załóżmy, że mają różne pliki zawierające odwołania do nazwy osób, które mają zostać wyodrębnione za pomocą nazwanej jednostki rozpoznawania. W poniższych definicji umiejętności `"/document/content"` jest tekstowa reprezentacja całego dokumentu, a "osoby" wyodrębniania pełne nazwy dla jednostek zidentyfikowane jako osób.

Ponieważ jest domyślny kontekst `"/document"`, listy osób, można teraz odwoływać się jak `"/document/people"`. W tym przypadku określone `"/document/people"` jest adnotacja, które mogą teraz zostać zamapowane do pola w indeksie, lub używane w innym umiejętności w tej samej skillset.

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

## <a name="example-2-reference-an-array-within-a-document"></a>Przykład 2: Odwołanie tablicy w dokumencie

W tym przykładzie opiera się na poprzedni prezentujący do wywołania etap wzbogacenia wiele razy w tym samym dokumencie. Załóżmy, że w poprzednim przykładzie wygenerowane tablicy ciągów 10 nazwy osób z pojedynczego dokumentu. Uzasadnione, następnym krokiem może być wzbogacenia drugiej, który wyodrębnia nazwisko z pełnej nazwy. Ponieważ istnieje 10 nazwy, ma ten krok, aby wywołać 10 razy w tym dokumencie raz dla każdej osoby. 

Aby wywołać prawo liczba iteracji, Ustaw kontekst jako `"/document/people/*"`, gdzie gwiazdka (`"*"`) reprezentuje wszystkie węzły w dokumencie wzbogaconego jako elementy podrzędne elementu `"/document/people"`. Chociaż w tablicy umiejętności tego umiejętności jest zdefiniowana tylko raz, dopóki wszystkie elementy członkowskie są przetwarzane jest wywoływana dla każdego elementu członkowskiego w tym dokumencie.

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

Adnotacje są tablice lub kolekcji ciągów, możesz docelowych określonych członków zamiast tablicy jako całość. Powyższy przykład generuje adnotacji o nazwie `"last"` w każdym węźle reprezentowanej przez kontekst. Jeśli chcesz odwołać się do tej rodziny adnotacje, można użyć składni `"/document/people/*/last"`. Jeśli chcesz odwołać się do konkretnej adnotacji, można użyć indeksu jawne: `"/document/people/1/last`"odwołanie do nazwisko osoby pierwszej określone w dokumencie. Zwróć uwagę, w tej składni tablice są "indeksowane 1".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Przykład 3: Odwołań do elementów członkowskich w tablicy

Czasami trzeba grupy wszystkie adnotacje określonego typu w celu przekazania ich do określonej umiejętności. Należy wziąć pod uwagę hipotetyczny umiejętności niestandardowej, która identyfikuje najbardziej typowych nazwisko od ostatniego nazw w przykładzie 2. Aby podać właśnie nazwisk do niestandardowych umiejętności, określ kontekst jako `"/document"` i danych wejściowych jako `"/document/people/*/lastname"`.

Należy pamiętać, że relacja `"/document/people/*/lastname"` jest większy niż dokumentu. Może istnieć 10 węzłów lastname, gdy istnieje tylko jeden węzeł dokumentu dla tego dokumentu. W takim przypadku system automatycznie utworzy tablicę `"/document/people/*/lastname"` zawierający wszystkie elementy w dokumencie.

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
+ [Integrowanie niestandardowych umiejętności potokiem wzbogacenia](cognitive-search-custom-skill-interface.md)
+ [Sposób definiowania skillset](cognitive-search-defining-skillset.md)
+ [Utwórz Skillset (REST)](ref-create-skillset.md)
+ [Sposób mapowania pól wzbogaconego do indeksu](cognitive-search-output-field-mapping.md)
