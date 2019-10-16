---
title: Klauzula SELECT w Azure Cosmos DB
description: Dowiedz się więcej na temat klauzuli SELECT języka SQL dla Azure Cosmos DB. Użyj programu SQL jako Azure Cosmos DB języka zapytań JSON.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: b90fc6f1f50ec2ea75619188cca36f78061f28df
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326787"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Klauzula SELECT w Azure Cosmos DB

Każde zapytanie składa się z klauzuli SELECT i opcjonalnych klauzul from i [WHERE](sql-query-where.md) , zgodnie [ze](sql-query-from.md) standardami ANSI języka SQL. Typowo, źródło w klauzuli FROM jest wyliczane i klauzula WHERE stosuje filtr dla źródła, aby pobrać podzestaw elementów JSON. Klauzula SELECT następnie projektuje żądane wartości JSON na liście wyboru.

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

  Właściwości lub wartości, które mają zostać wybrane dla zestawu wyników.  
  
- `'*'`  

  Określa, że wartość powinna być pobierana bez wprowadzania żadnych zmian. W przypadku, gdy przetworzona wartość jest obiektem, zostaną pobrane wszystkie właściwości.  
  
- `<object_property_list>`  
  
  Określa listę właściwości, które mają zostać pobrane. Każda zwrócona wartość będzie obiektem ze wskazanymi właściwościami.  
  
- `VALUE`  

  Określa, że wartość JSON ma być pobierana zamiast pełnego obiektu JSON. W przeciwieństwie do `<property_list>` nie otacza przewidywanej wartości w obiekcie.  
 
- `DISTINCT`
  
  Określa, że duplikaty właściwości rzutowane powinny zostać usunięte.  

- `<scalar_expression>`  

  Wyrażenie reprezentujące wartość, która ma zostać obliczona. Szczegóły można znaleźć w sekcji [wyrażenia skalarne](sql-query-scalar-expressions.md) .  

## <a name="remarks"></a>Uwagi

Składnia `SELECT *` jest prawidłowa tylko wtedy, gdy klauzula FROM deklaruje dokładnie jeden alias. `SELECT *` zawiera projekcję tożsamości, która może być przydatna, jeśli nie jest wymagana projekcja. SELECT * jest prawidłowy tylko wtedy, gdy klauzula FROM jest określona i wprowadza tylko pojedyncze źródło danych wejściowych.  
  
Zarówno `SELECT <select_list>`, jak i `SELECT *` są "sacharoza" i mogą być alternatywnie wyrażone przy użyciu prostych instrukcji SELECT, jak pokazano poniżej.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   jest równoważne:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   jest równoważne:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Przykłady

Poniższy przykład zapytania SELECT zwraca `address` z `Families`, którego `id` dopasowuje `AndersenFamily`:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są następujące:

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
Można uzyskać dostęp do właściwości przy użyciu operatora właściwości cytowane []. Na przykład elementy `SELECT c.grade` i `SELECT c["grade"]` są równoważne. Ta składnia jest przydatna do ucieczki właściwości, która zawiera spacje, znaki specjalne lub ma taką samą nazwę jak słowo kluczowe SQL lub słowo zastrzeżone.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Właściwości zagnieżdżone

W poniższym przykładzie przedstawiono projekty dwóch zagnieżdżonych właściwości, `f.address.state` i `f.address.city`.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są następujące:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```
### <a name="json-expressions"></a>Wyrażenia JSON

Projekcja obsługuje również wyrażenia JSON, jak pokazano w następującym przykładzie:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są następujące:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

W poprzednim przykładzie klauzula SELECT musi utworzyć obiekt JSON, a ponieważ przykład nie zawiera klucza, klauzula używa niejawnej nazwy zmiennej argumentu `$1`. Następujące zapytanie zwraca dwie niejawne zmienne argumentów: `$1` i `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są następujące:

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

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzula WHERE](sql-query-where.md)