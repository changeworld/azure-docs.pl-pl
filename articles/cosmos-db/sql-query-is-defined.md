---
title: IS_DEFINED w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemu SQL IS_DEFINED w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a5fcaf52d8e9e6b942a95f0b0c43f3f654c5d5d2
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303855"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED (Azure Cosmos DB)
 Zwraca wartość logiczną wskazującą, do właściwości przypisano wartość.  
  
## <a name="syntax"></a>Składnia
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*wyrażenie*  
   To dowolne wyrażenie.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład sprawdza obecność właściwość w ramach określonego dokumentu JSON. Pierwsza zwraca wartość true, ponieważ "a" jest obecny, ale druga zwraca wartość false, ponieważ nie istnieje "b".  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Następne kroki

- [Funkcje sprawdzania typu Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
