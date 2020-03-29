---
title: Wyrażenia skalarne w kwerendach SQL usługi Azure Cosmos DB
description: Dowiedz się więcej o składni sql wyrażenia skalarnego dla usługi Azure Cosmos DB. W tym artykule opisano również sposób łączenia wyrażeń skalarnych w wyrażenia złożone przy użyciu operatorów.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: f8c98915ad3b682af00492acc7bc51672ec874a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870738"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Wyrażenia skalarne w kwerendach SQL usługi Azure Cosmos DB

[Klauzula SELECT](sql-query-select.md) obsługuje wyrażenia skalarne. Wyrażenie skalarne jest kombinacją symboli i operatorów, które mogą być oceniane w celu uzyskania pojedynczej wartości. Przykłady wyrażeń skalarnych obejmują: stałe, odwołania do właściwości, odwołania do elementów tablicy, odwołania do aliasów lub wywołania funkcji. Wyrażenia skalarne można łączyć w złożone wyrażenia przy użyciu operatorów.

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
  
   Reprezentuje wartość stałą. Zobacz [sekcję Stałe,](sql-query-constants.md) aby uzyskać szczegółowe informacje.  
  
- `input_alias`  
  
   Reprezentuje wartość zdefiniowaną `input_alias` przez `FROM` wprowadzone w klauzuli.  
  Ta wartość jest gwarantowana nie **jest niezdefiniowana** —**niezdefiniowane** wartości we danych wejściowych są pomijane.  
  
- `<scalar_expression>.property_name`  
  
   Reprezentuje wartość właściwości obiektu. Jeśli właściwość nie istnieje lub właściwość odwołuje się do wartości, która nie jest obiektem, wyrażenie ocenia wartość **niezdefiniowanej.**  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Reprezentuje wartość właściwości z `property_name` nazwą lub elementem tablicy z indeksem `array_index` tablicy. Jeśli indeks właściwości/tablicy nie istnieje lub odwołuje się do indeksu właściwości/tablicy na wartość, która nie jest obiektem/tablicą, wyrażenie ocenia wartość niezdefiniowanej.  
  
- `unary_operator <scalar_expression>`  
  
   Reprezentuje operator, który jest stosowany do pojedynczej wartości. Szczegółowe informacje można znaleźć w sekcji [Operatorzy.](sql-query-operators.md)  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Reprezentuje operator, który jest stosowany do dwóch wartości. Szczegółowe informacje można znaleźć w sekcji [Operatorzy.](sql-query-operators.md)  
  
- `<scalar_function_expression>`  
  
   Reprezentuje wartość zdefiniowaną przez wynik wywołania funkcji.  
  
- `udf_scalar_function`  
  
   Nazwa zdefiniowanej przez użytkownika funkcji skalarnej.  
  
- `builtin_scalar_function`  
  
   Nazwa wbudowanej funkcji skalarnej.  
  
- `<create_object_expression>`  
  
   Reprezentuje wartość uzyskaną przez utworzenie nowego obiektu o określonych właściwościach i ich wartościach.  
  
- `<create_array_expression>`  
  
   Reprezentuje wartość uzyskaną przez utworzenie nowej tablicy z określonymi wartościami jako elementami  
  
- `parameter_name`  
  
   Reprezentuje wartość określonej nazwy parametru. Nazwy parametrów muszą \@ mieć pojedynczy jako pierwszy znak.  
  
## <a name="remarks"></a>Uwagi
  
  Podczas wywoływania wbudowanej lub zdefiniowanej przez użytkownika funkcji skalarnej należy zdefiniować wszystkie argumenty. Jeśli którykolwiek z argumentów jest niezdefiniowany, funkcja nie zostanie wywołana, a wynik nie zostanie zdefiniowany.  
  
  Podczas tworzenia obiektu każda właściwość, która jest przypisana niezdefiniowana wartość zostanie pominięta i nie zostanie uwzględniona w utworzonym obiekcie.  
  
  Podczas tworzenia tablicy każda wartość elementu, która jest **przypisana niezdefiniowana** wartość zostanie pominięta i nie uwzględnione w utworzonym obiekcie. Spowoduje to, że następny zdefiniowany element zajmie jego miejsce w taki sposób, że utworzona tablica nie będzie pomijane indeksy.  

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

W następującej kwerendzie wynikiem wyrażenia skalarnego jest wartość logiczna:

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

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Podkwerend](sql-query-subquery.md)