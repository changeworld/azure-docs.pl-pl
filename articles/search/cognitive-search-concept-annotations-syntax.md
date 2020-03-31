---
title: Odniesienia do danych wejściowych i wyjściowych w zestawach umiejętności
titleSuffix: Azure Cognitive Search
description: W tym artykule wyjaśniono składnię adnotacji i sposób odwoływania się do adnotacji w danych wejściowych i wyjściowych zestaw umiejętności w potoku wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e27f61239c0631fb248217777a311b13ee48a3f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113872"
---
# <a name="how-to-reference-annotations-in-an-azure-cognitive-search-skillset"></a>Jak odwoływać się do adnotacji w zauchwieniu usługi Azure Cognitive Search

W tym artykule dowiesz się, jak odwoływać się do adnotacji w definicjach umiejętności, używając przykładów do zilustrowania różnych scenariuszy. Gdy zawartość dokumentu przepływa przez zestaw umiejętności, jest wzbogacony o adnotacje. Adnotacje mogą służyć jako dane wejściowe do dalszego wzbogacenia niższego rzędu lub mapowane na pole wyjściowe w indeksie. 
 
Przykłady w tym artykule są oparte na polu *zawartości* generowane automatycznie przez [indeksatory obiektów Blob platformy Azure](search-howto-indexing-azure-blob-storage.md) w ramach fazy pękania dokumentu. Odwołując się do dokumentów z kontenera `"/document/content"`obiektów Blob, należy użyć formatu, takiego jak , gdzie pole *zawartości* jest częścią *dokumentu*. 

## <a name="background-concepts"></a>Pojęcia tła

Przed przejrzeniem składni przyjrzyjmy się kilku ważnym pojęćom, aby lepiej zrozumieć przykłady podane w dalszej części tego artykułu.

| Termin | Opis |
|------|-------------|
| Wzbogacony dokument | Wzbogacony dokument jest wewnętrzną strukturą utworzoną i używaną przez potok do przechowywania wszystkich adnotacji związanych z dokumentem. Wzbogać wzbogacony dokument jako drzewo adnotacji. Ogólnie rzecz biorąc adnotacja utworzona na podstawie poprzedniej adnotacji staje się jej elementem podrzędnym.<p/>Wzbogacone dokumenty istnieją tylko na czas wykonywania skillset. Gdy zawartość jest mapowana do indeksu wyszukiwania, wzbogacony dokument nie jest już potrzebny. Chociaż nie wchodzisz bezpośrednio w interakcję z wzbogaconymi dokumentami, warto mieć mentalny model dokumentów podczas tworzenia zestawu umiejętności. |
| Kontekst wzbogacania | Kontekst, w którym ma miejsce wzbogacenie, w odniesieniu do którego element jest wzbogacony. Domyślnie kontekst wzbogacania znajduje `"/document"` się na poziomie, ograniczonym do poszczególnych dokumentów. Po uruchomieniu umiejętności wyniki tej umiejętności stają się [właściwościami zdefiniowanego kontekstu](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Przykład 1: Proste odwołanie do adnotacji

W magazynie obiektów Blob platformy Azure załóżmy, że masz wiele plików zawierających odwołania do nazw osób, które chcesz wyodrębnić przy użyciu rozpoznawania jednostek. W poniższej definicji umiejętności `"/document/content"` jest tekstowa reprezentacja całego dokumentu, a "ludzie" jest wyodrębnienie pełnych nazw dla jednostek zidentyfikowanych jako osoby.

Ponieważ domyślnym `"/document"`kontekstem jest , lista osób `"/document/people"`może teraz odwoływać się jako . W tym `"/document/people"` konkretnym przypadku jest adnotacja, która może być teraz mapowane do pola w indeksie lub używane w innej umiejętności w tym samym skillset.

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

## <a name="example-2-reference-an-array-within-a-document"></a>Przykład 2: Odwoływanie się do tablicy w dokumencie

W tym przykładzie opiera się na poprzednim, pokazując, jak wywołać krok wzbogacania wiele razy w tym samym dokumencie. Przyjmij poprzedni przykład wygenerował tablicę ciągów z 10 nazwami osób z jednego dokumentu. Rozsądnym następnym krokiem może być drugie wzbogacenie, które wyodrębnia nazwisko z pełnej nazwy. Ponieważ istnieje 10 nazw, chcesz, aby ten krok był wywoływany 10 razy w tym dokumencie, raz dla każdej osoby. 

Aby wywołać odpowiednią liczbę iteracji, ustaw `"/document/people/*"`kontekst jako ,`"*"`gdzie gwiazdka ( ) reprezentuje wszystkie węzły `"/document/people"`w wzbogaconym dokumencie jako elementy podrzędne . Mimo że ta umiejętność jest zdefiniowana tylko raz w tablicy umiejętności, jest wywoływana dla każdego elementu członkowskiego w dokumencie, dopóki nie zostaną przetworzone wszystkie elementy członkowskie.

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

Gdy adnotacje są tablice lub kolekcje ciągów, można kierować określonych elementów członkowskich, a nie tablicy jako całości. Powyższy przykład generuje adnotację `"last"` wywoływaną w każdym węźle reprezentowanym przez kontekst. Jeśli chcesz odwołać się do tej rodziny adnotacji, `"/document/people/*/last"`możesz użyć składni . Jeśli chcesz odwołać się do określonej adnotacji, `"/document/people/1/last`możesz użyć jawnego indeksu: " w celu odniesienia się do nazwiska pierwszej osoby zidentyfikowanej w dokumencie. Należy zauważyć, że w tej składni tablice są "0 indeksowane".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Przykład 3: Odwoływanie się do elementów członkowskich w tablicy

Czasami trzeba zgrupować wszystkie adnotacje określonego typu, aby przekazać je do określonej umiejętności. Należy wziąć pod uwagę hipotetyczną umiejętność niestandardową, która identyfikuje najczęściej nazwisko ze wszystkich nazwisk wyodrębnionych w przykładzie 2. Aby podać tylko nazwiska do umiejętności niestandardowej, `"/document"` należy określić kontekst jako i dane wejściowe jako `"/document/people/*/lastname"`.

Należy zauważyć, że `"/document/people/*/lastname"` kardynalność jest większa niż dokument. Może istnieć 10 węzłów lastname, podczas gdy istnieje tylko jeden węzeł dokumentu dla tego dokumentu. W takim przypadku system automatycznie utworzy `"/document/people/*/lastname"` tablicę zawierającą wszystkie elementy w dokumencie.

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



## <a name="see-also"></a>Zobacz też
+ [Jak zintegrować niestandardową umiejętność z potokiem wzbogacania](cognitive-search-custom-skill-interface.md)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Jak mapować wzbogacone pola do indeksu](cognitive-search-output-field-mapping.md)
