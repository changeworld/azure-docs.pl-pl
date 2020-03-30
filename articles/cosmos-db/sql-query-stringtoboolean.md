---
title: StringToBoolean w języku kwerend usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL StringToBoolean w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ce11db91eff51e669f0917fbf34b1d560d0e9f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296545"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (usługa Azure Cosmos DB)
 Zwraca wyrażenie przetłumaczone na wartość logiczną. Jeśli nie można przetłumaczyć wyrażenia, zwraca niezdefiniowane.  
  
## <a name="syntax"></a>Składnia
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu, które ma być analizowane jako wyrażenie logiczne.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie logiczne lub niezdefiniowane.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym `StringToBoolean` przykładzie pokazano, jak zachowuje się w różnych typach. 
 
 Poniżej przedstawiono przykłady z prawidłowym wejściem.

Odstęp jest dozwolony tylko przed lub po "true"/"false".

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

Poniżej przedstawiono przykłady z nieprawidłowym wprowadzaniem danych.

 Wartości logiczne są rozróżniane i muszą być zapisywane ze wszystkimi małych liter, czyli "true" i "false".

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Tutaj znajduje się zestaw wyników.  
  
```json
[{}]
``` 

Przekazane wyrażenie będzie analizowane jako wyrażenie logiczne; te dane wejściowe nie są do oceny typu logicznego i w ten sposób zwracają niezdefiniowane.

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

Tutaj znajduje się zestaw wyników.  
  
```json
[{}]
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje ciągów usługi Azure Cosmos DB](sql-query-string-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
