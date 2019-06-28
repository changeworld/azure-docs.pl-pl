---
title: SELECT — klauzula w usłudze Azure Cosmos DB
description: Dowiedz się więcej o klauzuli SQL ZAZNACZYĆ dla usługi Azure Cosmos DB. Użyj programu SQL jako języka zapytań usługi Azure Cosmos DB w formacie JSON.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 84d0212f7f212b4554b506726e027fe51f795eea
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342525"
---
# <a name="select-clause"></a>Klauzula SELECT

Każdego zapytania składa się w klauzuli SELECT i opcjonalnych [FROM](sql-query-from.md) i [gdzie](sql-query-where.md) klauzule na standardy ANSI SQL. Zazwyczaj są wyliczane źródła w klauzuli FROM a klauzuli WHERE zastosowanie filtru źródła do pobrania podzbioru w formacie JSON. Klauzula SELECT następnie projektów wymagane wartości JSON na liście wyboru.

## <a name="syntax"></a>Składnia

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
## <a name="arguments"></a>Argumenty
  
- `<select_specification>`  

  Właściwości lub wartość należy wybrać zestaw wyników.  
  
- `'*'`  

  Określa, że wartość należy pobrać bez wprowadzania żadnych zmian. W szczególności, jeśli przetworzonych wartość jest obiektem, wszystkie właściwości będą pobierane.  
  
- `<object_property_list>`  
  
  Określa listę właściwości, które mają zostać pobrane. Każdy zwrócona wartość będzie obiektu przy użyciu właściwości określonych.  
  
- `VALUE`  

  Określa, że wartość JSON mają zostać pobrane zamiast kompletnego obiektu JSON. To, w przeciwieństwie do `<property_list>` nie jest zawijany przewidywane wartości w obiekcie.  
 
- `DISTINCT`
  
  Określa, duplikaty właściwości przewidywany powinny zostać usunięte.  

- `<scalar_expression>`  

  Wyrażenie reprezentujące wartość ma zostać obliczony. Zobacz [wyrażenia skalarne](sql-query-scalar-expressions.md) sekcji, aby uzyskać szczegółowe informacje.  

## <a name="remarks"></a>Uwagi

`SELECT *` Składnia jest prawidłowa, jeśli klauzula FROM zadeklarował dokładnie jeden alias. `SELECT *` udostępnia projekcji tożsamości, które mogą być przydatne, jeśli brak projekcji nie jest konieczne. Wybierz * jest prawidłowa, jeśli klauzula FROM określono tylko i wyłącznie jedno źródło danych wejściowych.  
  
Zarówno `SELECT <select_list>` i `SELECT *` są "sugar składni" i można również wyrazić za pomocą prostych instrukcji "SELECT", jak pokazano poniżej.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   jest równoważne:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   jest równoważne:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Przykłady

Wybierz następujące zapytanie zwraca przykład `address` z `Families` którego `id` odpowiada `AndersenFamily`:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="quoted-property-accessor"></a>Metoda dostępu do właściwości w cudzysłowie
Możesz uzyskać dostęp właściwości za pomocą operatora [] właściwości w cudzysłowie. Na przykład elementy `SELECT c.grade` i `SELECT c["grade"]` są równoważne. Ta składnia jest przydatne w jako znak ucieczki dla właściwości, która zawiera spacje, znaki specjalne lub ma taką samą nazwę jak słowo kluczowe SQL lub słowo zastrzeżone.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Właściwości zagnieżdżone

Poniższy przykład projekty dwie właściwości zagnieżdżone, `f.address.state` i `f.address.city`.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```
### <a name="json-expressions"></a>Wyrażenia JSON

Projekcja obsługuje również wyrażeń JSON, jak pokazano w poniższym przykładzie:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

W powyższym przykładzie klauzuli SELECT musi utworzyć obiekt JSON, a ponieważ przykład zawiera bez klucza, klauzuli używa nazwy zmiennej niejawnego argumentu `$1`. Następujące zapytanie zwraca dwie zmienne niejawnego argumentu: `$1` i `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Klauzula WHERE](sql-query-where.md)