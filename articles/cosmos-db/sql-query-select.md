---
title: Klauzula SELECT w usłudze Azure Cosmos DB
description: Dowiedz się więcej o klauzuli SQL SELECT dla usługi Azure Cosmos DB. Użyj języka SQL jako języka zapytań JSON usługi Azure Cosmos DB.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 013ebdcdbac41825c10a1362f73ab4c94052400d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469939"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Klauzula SELECT w usłudze Azure Cosmos DB

Każda kwerenda składa się z klauzul select i opcjonalnych klauzul [FROM](sql-query-from.md) i [WHERE,](sql-query-where.md) zgodnie ze standardami SQL ANSI. Zazwyczaj źródło w klauzuli FROM jest wyliczona, a klauzula WHERE stosuje filtr w źródle, aby pobrać podzbiór elementów JSON. Select Klauzula następnie projekty żądane wartości JSON na liście select.

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

  Właściwości lub wartość, która ma być wybrana dla zestawu wyników.  
  
- `'*'`  

  Określa, że wartość powinna być pobierana bez wprowadzania jakichkolwiek zmian. W szczególności, jeśli przetworzona wartość jest obiektem, zostaną pobrane wszystkie właściwości.  
  
- `<object_property_list>`  
  
  Określa listę właściwości, które mają zostać pobrane. Każda zwrócona wartość będzie obiektem z określonymi właściwościami.  
  
- `VALUE`  

  Określa, że wartość JSON powinna być pobierana zamiast pełnego obiektu JSON. To, `<property_list>` w przeciwieństwie do nie zawija prognozowaną wartość w obiekcie.  
 
- `DISTINCT`
  
  Określa, że duplikaty wyświetlanych właściwości powinny zostać usunięte.  

- `<scalar_expression>`  

  Wyrażenie reprezentujące wartość, która ma być obliczona. Zobacz [sekcję wyrażeń skalarnych,](sql-query-scalar-expressions.md) aby uzyskać szczegółowe informacje.  

## <a name="remarks"></a>Uwagi

Składnia jest prawidłowa `SELECT *` tylko wtedy, gdy klauzula FROM zadeklarowała dokładnie jeden alias. `SELECT *`zapewnia projekcję tożsamości, która może być przydatna, jeśli nie jest potrzebna projekcja. SELECT * jest prawidłowy tylko wtedy, gdy klauzula FROM jest określona i wprowadzona tylko jedno źródło wejściowe.  
  
Oba `SELECT <select_list>` `SELECT *` i są "cukrem składniowym" i mogą być alternatywnie wyrażone za pomocą prostych instrukcji SELECT, jak pokazano poniżej.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   odpowiada:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   odpowiada:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Przykłady

Następujący przykład kwerendy `address` `Families` SELECT `id` `AndersenFamily`zwraca, z którego dopasowań:

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

### <a name="quoted-property-accessor"></a>Metoda dostępu do właściwości cytowanej
Dostęp do właściwości można uzyskać za pomocą operatora właściwości cudzysłowu []. Na przykład elementy `SELECT c.grade` i `SELECT c["grade"]` są równoważne. Ta składnia jest przydatna do ucieczki z właściwości, która zawiera spacje, znaki specjalne lub ma taką samą nazwę jak słowo kluczowe SQL lub słowo zastrzeżone.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Właściwości zagnieżdżone

W poniższym przykładzie przedstawiono `f.address.state` `f.address.city`dwie właściwości zagnieżdżone i .

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

Projekcja obsługuje również wyrażenia JSON, jak pokazano w poniższym przykładzie:

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

W poprzednim przykładzie select klauzuli musi utworzyć obiekt JSON, a ponieważ próbka nie zawiera klucza, klauzula używa nazwy `$1`zmiennej argumentu niejawnego . Następująca kwerenda zwraca dwie `$1` niejawne zmienne argumentów: i `$2`.

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
## <a name="reserved-keywords-and-special-characters"></a>Zastrzeżone słowa kluczowe i znaki specjalne

Jeśli dane zawierają właściwości o takich samych nazwach jak zastrzeżone słowa kluczowe, takie jak "order" lub "Group", zapytania dotyczące tych dokumentów spowoduje błędy składni. Należy jawnie dołączyć właściwość w `[]` znaku, aby pomyślnie uruchomić kwerendę.

Oto na przykład dokument z właściwością `order` o `price($)` nazwie i właściwością zawierającą znaki specjalne:

```json
{
  "id": "AndersenFamily",
  "order": [
     {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
     }
  ],
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Po uruchomieniu kwerendy, która `order` zawiera `price($)` właściwość lub właściwość, pojawi się błąd składni.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```
```sql
SELECT * FROM c where c.order.price($) > 50
```
Rezultatem jest:

`
Syntax error, incorrect syntax near 'order'
`

Należy przepisać te same zapytania, jak poniżej:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie](sql-query-getting-started.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzula WHERE](sql-query-where.md)
