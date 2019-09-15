---
title: Wyrażenia skalarne w Azure Cosmos DB zapytania SQL
description: Dowiedz się więcej na temat składni wyrażeń skalarnych SQL dla Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: c35ad65a584f8ee95142e9bc85a58b5b6cd99744
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003539"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Wyrażenia skalarne w Azure Cosmos DB zapytania SQL

[Klauzula SELECT](sql-query-select.md) obsługuje wyrażenia skalarne. Wyrażenie skalarne, które jest kombinacją symboli i operatorów, które mogą być obliczane w celu uzyskania pojedynczej wartości. Przykłady wyrażeń skalarnych obejmują: stałe, odwołania do właściwości, odwołania do elementów tablicy, odwołania aliasów lub wywołania funkcji. Wyrażenia skalarne mogą być łączone w wyrażenia złożone przy użyciu operatorów.

## <a name="syntax"></a>Składnia
  
```sql  
<scalar_expression> ::=  
       <constant>
     | input_alias
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>
     | <create_array_expression>  
     | (<scalar_expression>)
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```

## <a name="arguments"></a>Argumenty
  
- `<constant>`  
  
   Reprezentuje wartość stałą. Zobacz [stałe](sql-query-constants.md) sekcji, aby uzyskać szczegółowe informacje.  
  
- `input_alias`  
  
   Reprezentuje wartość zdefiniowana przez `input_alias` wprowadzona w `FROM` klauzuli.  
  Ta wartość może nie być **niezdefiniowane** —**niezdefiniowane** wartości w danych wejściowych są pomijane.  
  
- `<scalar_expression>.property_name`  
  
   Reprezentuje wartość właściwości obiektu. Jeśli właściwość nie istnieje lub właściwość jest przywoływana dla wartości, która nie jest obiektem, wyrażenie daje w wyniku **niezdefiniowaną** wartość.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Reprezentuje wartość właściwości o nazwie `property_name` lub element tablicy z indeksem `array_index` tablicy. Jeśli indeks tablicy właściwości/nie istnieje, lub odwołanie do indeksu tablicy/właściwości na wartość, która nie jest/Tablica obiektów, a następnie wyrażenie ma wartość niezdefiniowaną wartość.  
  
- `unary_operator <scalar_expression>`  
  
   Reprezentuje operatora, który jest stosowany do pojedynczej wartości. Zobacz [operatory](sql-query-operators.md) sekcji, aby uzyskać szczegółowe informacje.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Reprezentuje operatora, który jest stosowany do dwóch wartości. Zobacz [operatory](sql-query-operators.md) sekcji, aby uzyskać szczegółowe informacje.  
  
- `<scalar_function_expression>`  
  
   Reprezentuje wartość zdefiniowana przez wynik wywołania funkcji.  
  
- `udf_scalar_function`  
  
   Nazwa funkcji skalarnej zdefiniowanej przez użytkownika.  
  
- `builtin_scalar_function`  
  
   Nazwa wbudowanej funkcji skalarnej.  
  
- `<create_object_expression>`  
  
   Reprezentuje wartość uzyskaną przez utworzenie nowego obiektu przy użyciu określonych właściwości i ich wartości.  
  
- `<create_array_expression>`  
  
   Reprezentuje wartość uzyskać, tworząc nową tablicę z określonymi wartościami jako elementy  
  
- `parameter_name`  
  
   Reprezentuje wartość określona nazwa parametru. Nazwy parametrów muszą mieć pojedynczy \@ jako pierwszego znaku.  
  
## <a name="remarks"></a>Uwagi
  
  Podczas wywoływania wbudowanej funkcji skalarnej lub zdefiniowanej przez użytkownika, wszystkie argumenty muszą być zdefiniowane. Jeśli którykolwiek z argumentów jest niezdefiniowana, nie zostanie wywołana funkcja, a wynik jest niezdefiniowany.  
  
  Podczas tworzenia obiektu, dowolnej właściwości, która jest przypisana wartość niezdefiniowana zostanie pominięty i nie są objęte utworzony obiekt.  
  
  Podczas tworzenia tablicy, każda wartość elementu, który jest przypisany **niezdefiniowane** wartość zostanie pominięty i nie są objęte utworzony obiekt. To spowoduje, że następny element zdefiniowany do jego miejsce w taki sposób, że utworzona tablica nie zostaną pominięte indeksów.  

## <a name="examples"></a>Przykłady

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Wyniki są następujące:

```json
    [{
      "$1": 1.33333
    }]
```

W poniższym zapytaniu wynik wyrażenia skalarnego jest wartością logiczną:

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

Wyniki są następujące:

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do Azure Cosmos DB](introduction.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Podzapytania](sql-query-subquery.md)