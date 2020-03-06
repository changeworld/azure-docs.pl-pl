---
title: Logowanie w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o funkcji rejestrowania w systemie SQL w Azure Cosmos DB, aby zwrócić logarytm naturalny określonego wyrażenia liczbowego
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9213ef03f383dec7109652246411fac154b4a7f9
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302512"
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
  
*opiera*  
   Opcjonalny argument liczbowy, który ustawia podstawa logarytmu.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="remarks"></a>Uwagi
  
  Domyślnie LOG() Zwraca logarytm naturalny. Podstawa logarytmu można zmienić z inną wartością za pomocą opcjonalnego parametru podstawowej.  
  
  Logarytm naturalny to logarytm o podstawie **e**, gdzie **e** jest stałą o wartości poniżej 2,718281828.  
  
  Logarytm naturalny wykładniczą liczby jest to liczba sam: LOG (EXP (n)) = n. I wykładniczą logarytm naturalny liczby jest to liczba sam: EXP (dziennik (n)) = n.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład deklaruje zmienną i zwraca wartość logarytmu określonej zmiennej (10).  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 Poniższy przykład oblicza `LOG` dla wykładnika liczby.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
