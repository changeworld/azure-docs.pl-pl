---
title: Klauzula SELECT w Azure Cosmos DB
description: Dowiedz się więcej na temat klauzuli SELECT języka SQL dla Azure Cosmos DB. Użyj programu SQL jako Azure Cosmos DB języka zapytań JSON.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: d34b1c39d9789409dc365cd4cf07fdc3d5a780fd
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003515"
---
# <a name="select-clause"></a>SELECT — klauzula

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

  Właściwości lub wartość należy wybrać zestaw wyników.  
  
- `'*'`  

  Określa, że wartość należy pobrać bez wprowadzania żadnych zmian. W szczególności, jeśli przetworzonych wartość jest obiektem, wszystkie właściwości będą pobierane.  
  
- `<object_property_list>`  
  
  Określa listę właściwości, które mają zostać pobrane. Każdy zwrócona wartość będzie obiektu przy użyciu właściwości określonych.  
  
- `VALUE`  

  Określa, że wartość JSON mają zostać pobrane zamiast kompletnego obiektu JSON. To, w przeciwieństwie do `<property_list>` nie jest zawijany przewidywane wartości w obiekcie.  
 
- `DISTINCT`
  
  Określa, że duplikaty właściwości rzutowane powinny zostać usunięte.  

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

Poniższy przykład `address` zapytania SELECT zwraca, z `Families` którego `id` pasuje: `AndersenFamily`

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

W poniższym przykładzie przedstawiono projekty dwóch zagnieżdżonych `f.address.state` właściwości `f.address.city`i.

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

W poprzednim przykładzie klauzula SELECT musi utworzyć obiekt JSON, a ponieważ przykład nie zawiera klucza, klauzula używa nazwy `$1`zmiennej argumentu niejawnego. Następujące zapytanie zwraca dwie niejawne zmienne argumentów `$1` : `$2`i.

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