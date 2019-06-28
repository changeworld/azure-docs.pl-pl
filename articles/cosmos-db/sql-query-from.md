---
title: Klauzuli FROM w usłudze Azure Cosmos DB
description: Dowiedz się więcej o klauzuli SQL FROM dla usługi Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: tisande
ms.openlocfilehash: 6bc93569dc9a0405ec3a8dfd719c89ede01df84d
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342906"
---
# <a name="from-clause"></a>FROM — klauzula

ŹRÓDŁOWA (`FROM <from_specification>`) klauzula jest opcjonalne, jeśli źródło jest filtrowana lub przewidywany później w zapytaniu. Zapytanie, takich jak `SELECT * FROM Families` wylicza w całym `Families` kontenera. Umożliwia także specjalny identyfikator GŁÓWNYM w kontenerze, zamiast nazwy kontenera.

Klauzula FROM wymusza następujące zasady na zapytanie:

* Kontener może mieć alias, taki jak `SELECT f.id FROM Families AS f` lub po prostu `SELECT f.id FROM Families f`. W tym miejscu `f` jest aliasem dla `Families`. PONIEWAŻ jest opcjonalne słowo kluczowe, aby [alias](sql-query-aliasing.md) identyfikator.  

* Jeden raz, ponieważ oryginalna nazwa źródła nie może być powiązany. Na przykład `SELECT Families.id FROM Families f` ma nieprawidłową składnię ponieważ identyfikator `Families` jest aliasem i nie można rozpoznać dłużej.  

* Wszystkie właściwości odwołania musi być w pełni kwalifikowana uniknąć niejednoznaczne powiązań w przypadku braku zgodności ścisłego schematu. Na przykład `SELECT id FROM Families f` ma nieprawidłową składnię ponieważ właściwość `id` nie jest powiązany.

## <a name="syntax"></a>Składnia
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>Argumenty
  
- `<from_source>`  
  
  Określa źródło danych, z lub bez aliasu. Jeśli nie określono aliasu, zostanie wywnioskowany z `<container_expression>` za pomocą następujących reguł:  
  
  -  Jeśli wyrażenie ma container_name, container_name będzie używany jako alias.  
  
  -  Jeśli wyrażenie ma `<container_expression>`, property_name, a następnie property_name zostanie użyta jako alias. Jeśli wyrażenie ma container_name, container_name będzie używany jako alias.  
  
- AS `input_alias`  
  
  Określa, że `input_alias` to zbiór wartości zwróconych z podstawowego wyrażenia kontenera.  
 
- `input_alias` INDIE  
  
  Określa, że `input_alias` powinny reprezentować zbiór wartości uzyskane przez Iterowanie wszystkie elementy tablicy każdej macierzy zwracanym przez wyrażenie bazowego kontenera. Każda wartość zwracana przez bazowego wyrażenie kontenera, który nie jest tablicą jest ignorowany.  
  
- `<container_expression>`  
  
  Określa wyrażenie kontenera, który ma być używany do pobierania dokumentów.  
  
- `ROOT`  
  
  Określa, że dany dokument powinny zostać pobrane z domyślnego kontenera aktualnie połączonych.  
  
- `container_name`  
  
  Określa, że w tym dokumencie mają zostać pobrane z podanego kontenera. Nazwa kontenera musi odpowiadać nazwie kontenera połączone obecnie z usługą.  
  
- `input_alias`  
  
  Określa, że w tym dokumencie mają zostać pobrane ze źródła, zdefiniowane przez podany alias.  
  
- `<container_expression> '.' property_`  
  
  Określa dokumentu powinny zostać pobrane, uzyskując dostęp do `property_name` właściwości lub indeks_tablicy elementu tablicy, dla wszystkich dokumentów, pobierane przez określone wyrażenie kontenera.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Określa dokumentu powinny zostać pobrane, uzyskując dostęp do `property_name` właściwości lub indeks_tablicy elementu tablicy, dla wszystkich dokumentów, pobierane przez określone wyrażenie kontenera.  
  
## <a name="remarks"></a>Uwagi
  
Wszystkie aliasy podany lub wnioskowany w `<from_source>(`s) musi być unikatowa. Składnia `<container_expression>.`property_name jest taka sama jak `<container_expression>' ['"property_name"']'`. Jednak te ostatnie składni można użyć, jeśli nazwa właściwości zawiera znak — identyfikator.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Obsługa brakujących elementów tablicy i wartości niezdefiniowanego nie ma właściwości
  
Wyrażenie kontenerów uzyskuje dostęp do właściwości lub elementów tablicy i wartości nie istnieje, ta wartość będą ignorowane i nie przetwarzane dalszych.  
  
### <a name="container-expression-context-scoping"></a>Wyznaczanie zakresu kontekście wyrażenia kontenera  
  
Wyrażenie kontener może być należące do kontenera lub zakresu w dokumencie:  
  
-   Wyrażenie jest kontenerów należące do zakresu, jeśli bazowe źródło wyrażenia kontenera jest albo głównego lub `container_name`. Takie wyrażenie reprezentuje zestaw dokumenty pobierane z kontenera bezpośrednio, a nie jest zależna od przetwarzania innych wyrażeń kontenera.  
  
-   Wyrażenie jest dokument należące do zakresu, jeśli bazowe źródło wyrażenia kontenera jest `input_alias` wprowadzone wcześniej w zapytaniu. Takie wyrażenie reprezentuje zestaw dokumentów uzyskanych przez obliczenie wyrażenia kontenera w zakresie każdego dokumentu należących do zestawu skojarzonych z danym kontenerem aliasem.  Wynikowy zestaw będzie Unii zestawów uzyskany w wyniku obliczenia wyrażenia kontenera dla poszczególnych dokumentów w podstawowym zestawie. 

## <a name="examples"></a>Przykłady

### <a name="get-subitems-by-using-the-from-clause"></a>Pobierz elementy podrzędne przy użyciu klauzuli FROM

Klauzula FROM może zmniejszyć źródła utworzenie mniejszego podzestawu. Aby wyliczyć tylko poddrzewo w każdym elemencie, subroot może stać się źródła, jak pokazano w poniższym przykładzie:

```sql
    SELECT *
    FROM Families.children
```

Wyniki są:

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Poprzednie Zapytanie używane tablicę jako źródła, ale obiekt jest również używany jako źródło. Zapytanie uwzględnia wszystkie wartości JSON prawidłowe, definicja źródła do włączenia w wyniku. Poniższy przykład, można wyłączyć `Families` nie mają `address.state` wartość.

```sql
    SELECT *
    FROM Families.address.state
```

Wyniki są:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Klauzula SELECT](sql-query-select.md)
- [Klauzula WHERE](sql-query-where.md)