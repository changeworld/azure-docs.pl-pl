---
title: ODWRÓĆ w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL REVERSE w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a22e1c8a5f4350bd2f966ee48f96368c648a4a1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302172"
---
# <a name="reverse-azure-cosmos-db"></a>ODWROTNIE (Usługa Azure Cosmos DB)
 Zwraca wartość ciągu w odwrotnej kolejności.  
  
## <a name="syntax"></a>Składnia
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie `REVERSE` pokazano, jak używać w kwerendzie.  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje ciągów usługi Azure Cosmos DB](sql-query-string-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
