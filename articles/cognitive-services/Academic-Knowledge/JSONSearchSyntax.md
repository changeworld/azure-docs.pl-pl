---
title: Składnia wyszukiwania JSON — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Więcej informacji na temat składni wyszukiwania JSON, których można używać w interfejsu Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: fddd2291fe7fbb46c57d31e9aebc7fc6244df971
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61336970"
---
# <a name="json-search-syntax"></a>Składnia wyszukiwania JSON

```javascript
/* Query Object:
   Suppose we have a query path /v0/e0/v1/e1/...
   A query object contains constraints to be applied to graph nodes in a path.
   Constraints are given in <"node identifier", {constraint object}> key-value pairs: 
*/
{
    /* query path can also be set in the query object */
    path: "/v0/e0/v1/e1/.../vn/",
    v0: { /* A Constraint Object */ },
    v1: { /* A Constraint Object */ },
}
```

Nazwy węzłów w ścieżce zapytania (_v0-v1..._ ) służą jako identyfikatory węzłów, które mogą być przywoływane w obiekcie zapytania; nazwy krawędzi (_e0, e1..._ ) w ścieżce reprezentują typy odpowiednich krawędzi. Możemy użyć gwiazdki _*_ jako nazwę węzła lub krawędzi (z wyjątkiem począwszy od węzła, który należy zachować szczególną) do deklarowania, istnieją bez ograniczeń w odniesieniu do takiego elementu. Na przykład ścieżka kwerendy `/v0/*/v1/e1/*/` pobiera ścieżki z wykresu bez ograniczania typie krawędzi _(v0, wersja 1)_. W międzyczasie zapytanie nie ma albo ograniczenia na komputerze docelowym (ostatni węzeł) ścieżki.

Jeśli ścieżka zawiera tylko jeden węzeł, powiedz _v0_, zapytanie po prostu zwrócenia wszystkich jednostek, które spełniają ograniczenia. Obiekt ograniczenia stosowane do węzeł początkowy jest wywoływana *od obiektu kwerendy*, którego specyfikacji znajduje się w następujący sposób.

```javascript
/* Starting Query Object:
   Specifies constraints on the starting node
*/
{
    /* "match" operator searches for entities with the specified properties. 
       All properties defined in the graph schema be queried such as "Name" and "NormalizedTitle".
     */
    "match": { 
        "Name" : "some name",
        ...
    },
    /* "id" operator directly specifies the IDs of the starting nodes. 
       When it is present, the "match" operator is ignored. 
     */
    "id": [ id1, id2, id3... ],
    /* "type" operator limits results to a certain type of entities,
       for example, "Author" or "Paper".
     */
    "type": "Author",
    /* "select" operator pulls properties from the database and 
       returns them to the client.
     */
    "select": ["PaperRank", ...]
}
```

Jeśli ścieżka zawiera więcej niż tylko węzeł początkowy, procesor zapytań będzie wykonywać przechodzenie grafu, następującego wzorca określonej ścieżki. Po dostarczeniu do węzła, akcje przechodzenie określonych przez użytkownika zostaną wyzwolone, oznacza to, czy zatrzyma bieżącego węzła i zwracają zrozumienie wykresu w dalszym ciągu. Jeśli nie określono żadnej akcji przechodzenia, zostaną wykonane akcje domyślne. Dla węzła pośredniego domyślna akcja jest zrozumienie wykresu w dalszym ciągu. Dla ostatniego węzła ścieżki domyślnej akcji jest zatrzymanie i powrót. Obiekt ograniczenia, który określa akcje przechodzenie nosi nazwę *przechodzenia przez akcję obiektu*. Jego specyfikacji znajduje się w następujący sposób:

```javascript
/* Traversal Action Object:
   Specifies graph traversal actions on a node (except for the starting node).
 */
{
    /* Set the continue condition here. */
    continue: { 
        /* simple property exact match */
        "property_key" : "property_value", 
        /* Advanced query operators */
        /* -- Numerical comparisons */
        "property_key_2" : { "gt" /* or simply ">" */ : 123 },
        /* -- Substring query */
        "property_key_3" : { "substring" : "456" },
        /* -- CellID query */
        "id": [ 111, 222, 333... ],
        /* -- Entity type query */
        "type": "cell_type",
        /* -- Property existance query */
        "has" : "property_key_4",
        /* -- Logical operators */
        /* ---- Note that, by default the operators are combined with AND semantics */
        
        /* -- OR operator */
        "or": {
          /* Query operators... */
        },
        /* -- NOT operator */
        "not": {
            /* Query operators... */
        }
    },
    /* Set the return condition here. */
    return: {
        /* Same operators as the continue object */
    },
    /* The selected properties to return. */
    select: ["property_key_1", ...]
}
```

Treść wpisu *json* zapytanie wyszukiwania powinno zawierać co najmniej jeden *ścieżki* wzorca. Przechodzenie akcji obiekty są opcjonalne. Poniżej przedstawiono dwa przykłady.

```JSON
{
  "path": "/series/ConferenceInstanceIDs/conference/FieldOfStudyIDs/field",
  "series": {
    "type": "ConferenceSeries",
    "FullName": "graph",
    "select": [ "FullName", "ShortName" ]
  },
  "conference": {
    "type": "ConferenceInstance",
    "select": [ "FullName" ]
  },
  "field": {
    "type": "FieldOfStudy",
    "select": [ "Name" ],
    "return": { "Name": { "substring" : "World Wide Web" } }
  }
}
```

```JSON
{
  "path": "/author/PaperIDs/paper",
  "author": {
    "type": "Author",
    "select": [ "DisplayAuthorName" ],
    "match": { "Name": "leslie lamport" }
  },
  "paper": {
    "type": "Paper",
    "select": [ "OriginalTitle", "CitationCount" ],
    "return": { "CitationCount": { "gt": 100 } }
  }
}
```

