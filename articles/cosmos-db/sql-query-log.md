---
title: Logowanie w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o funkcji rejestrowania w systemie SQL w Azure Cosmos DB, aby zwrócić logarytm naturalny określonego wyrażenia liczbowego
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ae7812670da836efa326b9224547e4d1b64374c2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873288"
---
# <a name="log-azure-cosmos-db"></a>Dziennik (Azure Cosmos DB)
 Zwraca logarytm naturalny z określonego wyrażenia liczbowego.  
  
## <a name="syntax"></a>Składnia
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   To wyrażenie liczbowe.  
  
*base*  
   Opcjonalny argument liczbowy, który ustawia podstawa logarytmu.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="remarks"></a>Uwagi
  
  Domyślnie LOG() Zwraca logarytm naturalny. Podstawa logarytmu można zmienić z inną wartością za pomocą opcjonalnego parametru podstawowej.  
  
  Logarytm naturalny to logarytm o podstawie **e**, gdzie **e** jest stałą nieracjonalnej w przybliżeniu równa 2.718281828.  
  
  Logarytm naturalny wykładniczą liczby jest to liczba sam: LOG (EXP (n)) = n. I wykładniczą logarytm naturalny liczby jest to liczba sam: EXP (dziennik (n)) = n.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład deklaruje zmienną i zwraca wartość logarytmu określonej zmiennej (10).  
  
```sql
SELECT LOG(10) AS log  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 Poniższy przykład oblicza `LOG` dla wykładnika liczby.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
