---
title: Klauzula FROM w usłudze Azure Cosmos DB
description: Dowiedz się więcej o składni SQL i przykład dla klauzuli FROM dla usługi Azure Cosmos DB. W tym artykule przedstawiono również przykłady wyników zakresu i uzyskać elementy podrzędne przy użyciu from klauzuli.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 3939594064b63c567720378b9d316acca64d3266
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587689"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Klauzula FROM w usłudze Azure Cosmos DB

Klauzula`FROM <from_specification>`FROM ( ) jest opcjonalna, chyba że źródło jest filtrowane lub przewidywane w dalszej części kwerendy. Kwerenda, `SELECT * FROM Families` taka jak wylicza dla całego `Families` kontenera. Można również użyć specjalnego identyfikatora ROOT dla kontenera zamiast używać nazwy kontenera.

Klauzula FROM wymusza następujące reguły na kwerendę:

* Kontener może mieć alias, taki jak `SELECT f.id FROM Families AS f` lub po prostu `SELECT f.id FROM Families f`. Oto `f` alias dla `Families`. AS jest opcjonalnym słowem kluczowym [aliasu](sql-query-aliasing.md) identyfikatora.  

* Po aliasie oryginalna nazwa źródła nie może być powiązana. Na przykład `SELECT Families.id FROM Families f` jest syntaktycznie nieprawidłowy, ponieważ identyfikator `Families` został aliasowany i nie można go już rozpoznać.  

* Wszystkie właściwości, do których istnieje odwołanie, muszą być w pełni kwalifikowane, aby uniknąć niejednoznacznych powiązań w przypadku braku ścisłego przestrzegania schematu. Na przykład `SELECT id FROM Families f` jest syntaktycznie nieprawidłowe, ponieważ właściwość `id` nie jest powiązana.

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
  
  Określa źródło danych z aliasem lub bez niego. Jeśli alias nie zostanie określony, zostanie on `<container_expression>` wywnioskowany z następujących reguł:  
  
  -  Jeśli wyrażenie jest container_name, container_name będzie używany jako alias.  
  
  -  Jeśli wyrażenie `<container_expression>`jest , a następnie property_name, a następnie property_name będzie używany jako alias. Jeśli wyrażenie jest container_name, container_name będzie używany jako alias.  
  
- Jako`input_alias`  
  
  Określa, że `input_alias` jest to zestaw wartości zwracanych przez podstawowe wyrażenie kontenera.  
 
- `input_alias`Cala  
  
  Określa, że `input_alias` powinien reprezentować zestaw wartości uzyskanych przez iteracji na wszystkich elementów tablicy każdej tablicy zwracanych przez wyrażenie kontenera źródłowego. Każda wartość zwracana przez podstawowe wyrażenie kontenera, która nie jest tablicą, jest ignorowana.  
  
- `<container_expression>`  
  
  Określa wyrażenie kontenera, które ma być używane do pobierania dokumentów.  
  
- `ROOT`  
  
  Określa, że dokument powinien być pobierany z domyślnego, aktualnie połączonego kontenera.  
  
- `container_name`  
  
  Określa, że dokument powinien być pobierany z podanego kontenera. Nazwa kontenera musi być zgodna z nazwą kontenera aktualnie połączonego.  
  
- `input_alias`  
  
  Określa, że dokument powinien być pobierany z innego źródła zdefiniowanego przez podany alias.  
  
- `<container_expression> '.' property_name`  
  
  Określa, że dokument powinien być pobierany przez dostęp do `property_name` właściwości.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Określa, że dokument powinien być pobierany przez dostęp do `property_name` właściwości lub array_index elementu tablicy dla wszystkich dokumentów pobranych przez określone wyrażenie kontenera.  
  
## <a name="remarks"></a>Uwagi
  
Wszystkie aliasy dostarczone lub wywnioskowane w `<from_source>(`s) muszą być unikatowe. Property_name składni `<container_expression>.`jest taka sama `<container_expression>' ['"property_name"']'`jak . Jednak ta ostatnia składnia może być używana, jeśli nazwa właściwości zawiera znak niebędący identyfikatorem.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Obsługa brakujących właściwości, brakujących elementów tablicy i niezdefiniowanych wartości
  
Jeśli wyrażenie kontenera uzyskuje dostęp do właściwości lub elementów tablicy i ta wartość nie istnieje, ta wartość zostanie zignorowana i nie będzie dalej przetwarzana.  
  
### <a name="container-expression-context-scoping"></a>Zakres kontekstu wyrażenia kontenera  
  
Wyrażenie kontenera może mieć zakres kontenera lub zakres dokumentu:  
  
-   Wyrażenie jest o zakresie kontenera, jeśli źródłem źródłowego wyrażenia `container_name`kontenera jest ROOT lub . Takie wyrażenie reprezentuje zestaw dokumentów pobranych bezpośrednio z kontenera i nie jest zależne od przetwarzania innych wyrażeń kontenera.  
  
-   Wyrażenie jest o zakresie dokumentu, jeśli podstawowe źródło `input_alias` wyrażenia kontenera jest wprowadzany wcześniej w kwerendzie. Takie wyrażenie reprezentuje zestaw dokumentów uzyskanych przez ocenę wyrażenia kontenera w zakresie każdego dokumentu należącego do zestawu skojarzonego z kontenerem aliasów.  Wynikowy zestaw będzie unii zestawów uzyskanych przez ocenę wyrażenia kontenera dla każdego z dokumentów w zestawie źródłowym. 

## <a name="examples"></a>Przykłady

### <a name="get-subitems-by-using-the-from-clause"></a>Pobierz podwykonań przy użyciu klauzuli FROM

Klauzula FROM może zmniejszyć źródło do mniejszego podzbioru. Aby wyliczyć tylko poddrzewo w każdym elemencie, podroz może stać się źródłem, jak pokazano w poniższym przykładzie:

```sql
    SELECT *
    FROM Families.children
```

Wyniki są następujące:

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

Poprzednia kwerenda użyła tablicy jako źródła, ale można również użyć obiektu jako źródła. Kwerenda uwzględnia wszystkie prawidłowe, zdefiniowane JSON wartość w źródle do włączenia w wynik. Poniższy przykład `Families` można wykluczyć, że `address.state` nie mają wartości.

```sql
    SELECT *
    FROM Families.address.state
```

Wyniki są następujące:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie](sql-query-getting-started.md)
- [Klauzula SELECT](sql-query-select.md)
- [Klauzula WHERE](sql-query-where.md)
