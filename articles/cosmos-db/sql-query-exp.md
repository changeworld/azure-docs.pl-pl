---
title: Azure Cosmos DB języka zapytań
description: Dowiedz się więcej na temat funkcji systemowej języka SQL (EXP) w Azure Cosmos DB, aby zwrócić wartość wykładniczą określonego wyrażenia liczbowego
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 76d614264124e1ce4138663b702ff6d899b3aa4e
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873322"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
 Zwraca wartość określonego wyrażenia liczbowego.  
  
## <a name="syntax"></a>Składnia
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   To wyrażenie liczbowe.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość wyrażenia liczbowego.  
  
## <a name="remarks"></a>Uwagi
  
  Stała **e** (2.718281...), jest podstawą logarytmy naturalne.  
  
  Wykładnik potęgi liczby jest stałą **e** podniesioną do potęgi liczby. Na przykład EXP(1.0) = e ^ 1.0 = 2.71828182845905 i EXP(10) = e ^ 10 = 22026.4657948067.  
  
  Jest to liczba wykładniczą logarytm naturalny liczby sam: EXP (dziennik (n)) = n. A logarytm naturalny wykładniczą liczby jest liczbą sam: LOG (EXP (n)) = n.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład deklaruje zmienną i zwraca wartość określonej zmiennej (10).  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 Poniższy przykład zwraca wartość natural logarithm 20 i wykładniczą 20 logarytm naturalny. Ponieważ te funkcje są funkcje odwrotne od siebie, wartość zwracana z zaokrąglaniem do zmiennoprzecinkowych matematyczne punktu w obu przypadkach wynosi 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
