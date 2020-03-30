---
title: EXP w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL Exponent (EXP) w usłudze Azure Cosmos DB w celu zwrócenia wykładniczej wartości określonego wyrażenia liczbowego
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 76d614264124e1ce4138663b702ff6d899b3aa4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873322"
---
# <a name="exp-azure-cosmos-db"></a>EXP (usługa Azure Cosmos DB)
 Zwraca wykładniczą wartość określonego wyrażenia liczbowego.  
  
## <a name="syntax"></a>Składnia
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Jest wyrażeniem liczbowym.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="remarks"></a>Uwagi
  
  Stała **e** (2,718281...), jest podstawą logarytmów naturalnych.  
  
  Wykładnikiem liczby jest stała **e** podniesiona do potęgi liczby. Na przykład EXP(1.0) = e^1.0 = 2.71828182845905 i EXP(10) = e^10 = 22026.4657948067.  
  
  Wykładniczym logarytmu naturalnego liczby jest sama liczba: EXP (LOG (n)) = n. A logarytmem naturalnym wykładniczej liczby jest sama liczba: LOG (EXP (n)) = n.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład deklaruje zmienną i zwraca wartość wykładniczą określonej zmiennej (10).  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 Poniższy przykład zwraca wykładniczą wartość logarytmu naturalnego 20 i logarytm naturalny wykładniczego 20. Ponieważ te funkcje są odwrotne funkcje siebie nawzajem, zwracana wartość z zaokrągleniem dla matematyki zmiennoprzecinkowej w obu przypadkach jest 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Usługi Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
