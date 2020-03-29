---
title: StringToNull w języku kwerendy usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL StringToNull w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e17547f88465103b61eabec04978ea806ffa2c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296443"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (usługa Azure Cosmos DB)
 Zwraca wyrażenie przetłumaczone na wartość null. Jeśli nie można przetłumaczyć wyrażenia, zwraca niezdefiniowane.  
  
## <a name="syntax"></a>Składnia
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu, które ma być analizowane jako wyrażenie null.
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie zerowe lub niezdefiniowane.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym `StringToNull` przykładzie pokazano, jak zachowuje się w różnych typach. 

Poniżej przedstawiono przykłady z prawidłowym wejściem.

 Odstęp jest dozwolony tylko przed lub po "null".

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

Poniżej przedstawiono przykłady z nieprawidłowym wprowadzaniem danych.

Null jest rozróżniana wielkość liter i musi być zapisywana ze wszystkimi małych liter, czyli "null".

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{}]
```  

Wyrażenie przekazane zostanie przeanalizowane jako wyrażenie null; te dane wejściowe nie są do oceny typu null i w związku z tym zwracają niezdefiniowane.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
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
