---
title: Składnia poleceń wyszukiwania JSON w Academic Knowledge API | Dokumentacja firmy Microsoft
description: Więcej informacji na temat składni wyszukiwania JSON używanego w Academic Knowledge API w kognitywnych usług firmy Microsoft.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: a4b9cf535dae60258d71c43bba6f9eec1444bd41
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346861"
---
# <a name="json-search-syntax"></a>Składnia poleceń wyszukiwania JSON

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

Nazwy węzłów w ścieżce zapytania (_v0, v1..._ ) identyfikatorami węzła, które może być przywoływany w obiekcie zapytania; nazwy krawędzi (_e0, e1,..._ ) w ścieżce reprezentują typy odpowiednich krawędzi. Możemy użyć gwiazdki _*_ jako nazwa węzła lub Microsoft edge (z wyjątkiem węzła początkowego, które muszą mieć) Aby zadeklarować że istnieją nie ograniczenia dotyczące takiego elementu. Na przykład ścieżka zapytania `/v0/*/v1/e1/*/` pobiera ścieżki z programu graph bez ograniczania typu granicy _(v0 v1)_. W tym samym czasie zapytanie nie ma ograniczeń na komputerze docelowym (ostatni węzeł) ścieżki albo.

Jeśli ścieżka zawiera tylko jeden węzeł, powiedz _v0_, po prostu zwraca wszystkie jednostki, które spełniają ograniczenia. Obiekt ograniczenia dotyczą węzeł początkowy jest wywoływana *Uruchamianie obiektu Query*, którego specyfikacji znajduje się w następujący sposób.

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

Jeśli ścieżka zawiera więcej niż tylko węzeł początkowy, procesor zapytań wykona przechodzenie wykresu, zgodnie ze wzorcem określonej ścieżki. Po dostarczeniu do węzła, akcje przechodzenie wybrany użytkownik zostanie wyzwolony, oznacza to, czy zatrzyma się w bieżącym węźle i powrócić do eksplorowania wykresu w dalszym ciągu. Jeśli nie określono żadnej akcji przechodzenie, zostaną wykonane akcje domyślne. Węzeł pośredni domyślne działanie jest nadal Eksploruj wykresu. W przypadku ostatniego węzła ścieżki domyślne działanie jest zatrzymanie i powrót. Obiekt ograniczenia, który określa akcje przechodzenie jest nazywany *obiekt akcji przechodzenie*. Jego specyfikację znajduje się w następujący sposób:

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

Treść POST *json* zapytanie wyszukiwania musi zawierać co najmniej *ścieżki* wzorca. Obiekty akcji przechodzenia są opcjonalne. Poniżej przedstawiono dwa przykłady.

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

