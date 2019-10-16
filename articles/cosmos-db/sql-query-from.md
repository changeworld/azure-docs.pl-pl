---
title: Klauzula FROM w Azure Cosmos DB
description: Dowiedz się więcej na temat klauzuli SQL FROM dla Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: tisande
ms.openlocfilehash: 79bb17277a041f71c095ed724737012f9501f16f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326991"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Klauzula FROM w Azure Cosmos DB

Klauzula FROM (`FROM <from_specification>`) jest opcjonalna, chyba że źródło zostanie odfiltrowane lub zaprojektować w dalszej części zapytania. Zapytanie, takie jak `SELECT * FROM Families`, wylicza cały kontener `Families`. Zamiast używać nazwy kontenera, można także użyć głównego identyfikatora dla kontenera.

Klauzula FROM wymusza następujące reguły dla każdego zapytania:

* Kontener może mieć alias, taki jak `SELECT f.id FROM Families AS f` lub po prostu `SELECT f.id FROM Families f`. Tutaj `f` jest aliasem dla `Families`. JAKO opcjonalne słowo kluczowe [aliasu](sql-query-aliasing.md) identyfikatora.  

* Po usunięciu aliasu oryginalna nazwa źródła nie może być powiązana. Na przykład `SELECT Families.id FROM Families f` jest syntaktycznie nieprawidłowy, ponieważ identyfikator `Families` ma alias i nie można go rozpoznać.  

* Wszystkie właściwości, do których istnieją odwołania, muszą być w pełni kwalifikowane, aby uniknąć niejednoznacznych powiązań w przypadku braku ścisłego przestrzegania schematu. Na przykład `SELECT id FROM Families f` jest syntaktycznie nieprawidłowa, ponieważ właściwość `id` nie jest powiązana.

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
  
  Określa źródło danych z aliasem lub bez niego. Jeśli alias nie zostanie określony, zostanie wywnioskowany na podstawie `<container_expression>` przy użyciu następujących reguł:  
  
  -  Jeśli wyrażenie jest container_name, container_name będzie używany jako alias.  
  
  -  Jeśli wyrażenie jest `<container_expression>`, a następnie property_name, PROPERTY_NAME zostanie użyte jako alias. Jeśli wyrażenie jest container_name, container_name będzie używany jako alias.  
  
- JAKO `input_alias`  
  
  Określa, że `input_alias` to zbiór wartości zwracanych przez bazowe wyrażenie kontenera.  
 
- `input_alias` w  
  
  Określa, że `input_alias` powinien reprezentować zestaw wartości uzyskanych przez iterację wszystkich elementów tablicy każdej tablicy zwracanej przez bazowe wyrażenie kontenera. Każda wartość zwrócona przez wyrażenie kontenera bazowego, który nie jest tablicą, jest ignorowana.  
  
- `<container_expression>`  
  
  Określa wyrażenie kontenera, które ma być używane do pobierania dokumentów.  
  
- `ROOT`  
  
  Określa, że dokument powinien być pobrany z domyślnego, połączonego kontenera.  
  
- `container_name`  
  
  Określa, że dokument powinien być pobrany z podanego kontenera. Nazwa kontenera musi być zgodna z nazwą kontenera, z którym jest aktualnie połączona.  
  
- `input_alias`  
  
  Określa, że dokument ma zostać pobrany z innego źródła zdefiniowanego przez podany alias.  
  
- `<container_expression> '.' property_`  
  
  Określa, że dokument ma być pobierany przez uzyskanie dostępu do właściwości `property_name` lub elementu tablicy array_index dla wszystkich dokumentów pobranych przez określone wyrażenie kontenera.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Określa, że dokument ma być pobierany przez uzyskanie dostępu do właściwości `property_name` lub elementu tablicy array_index dla wszystkich dokumentów pobranych przez określone wyrażenie kontenera.  
  
## <a name="remarks"></a>Uwagi
  
Wszystkie aliasy dostarczone lub wywnioskowane w `<from_source>(`s) muszą być unikatowe. Składnia `<container_expression>.`property_name jest taka sama jak `<container_expression>' ['"property_name"']'`. Jednakże Ostatnia składnia może być użyta, jeśli nazwa właściwości zawiera znak inny niż identyfikator.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Obsługa brakujących właściwości, brak elementów tablicy i niezdefiniowane wartości
  
Jeśli wyrażenie kontenera uzyskuje dostęp do właściwości lub elementów tablicy i ta wartość nie istnieje, ta wartość zostanie zignorowana i nie będzie można jej przetworzyć.  
  
### <a name="container-expression-context-scoping"></a>Określanie zakresu kontekstu wyrażenia kontenera  
  
Wyrażenie kontenera może być w zakresie kontenera lub w zakresie dokumentu:  
  
-   Wyrażenie jest w zakresie kontenera, jeśli bazowe Źródło wyrażenia kontenera jest elementem głównym lub `container_name`. Takie wyrażenie reprezentuje zestaw dokumentów pobranych bezpośrednio z kontenera i nie zależy od przetwarzania innych wyrażeń kontenera.  
  
-   Wyrażenie jest w zakresie dokumentu, jeśli bazowe Źródło wyrażenia kontenera jest `input_alias` wprowadzone wcześniej w zapytaniu. Takie wyrażenie reprezentuje zestaw dokumentów uzyskanych przez ocenę wyrażenia kontenera w zakresie każdego dokumentu należącego do zestawu skojarzonego z kontenerem aliasów.  Wynikiem zestawu będzie związek zestawów uzyskanych przez obliczenie wyrażenia kontenera dla każdego z dokumentów w zestawie bazowym. 

## <a name="examples"></a>Przykłady

### <a name="get-subitems-by-using-the-from-clause"></a>Pobierz elementy podelementowe za pomocą klauzuli FROM

Klauzula FROM może zmniejszyć źródło do mniejszego podzestawu. Aby wyliczyć tylko poddrzewo dla każdego elementu, jego element główny może stać się źródłem, jak pokazano w następującym przykładzie:

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

Poprzednie zapytanie używało tablicy jako źródła, ale można również użyć obiektu jako źródła. Zapytanie uwzględnia wszystkie prawidłowe, zdefiniowane wartości JSON w źródle do uwzględnienia w wyniku. W poniższym przykładzie wykluczono `Families`, które nie mają wartości `address.state`.

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

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [SELECT — klauzula](sql-query-select.md)
- [Klauzula WHERE](sql-query-where.md)