---
title: ZALOGUJ się w języku kwerendy usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu LOG SQL w usłudze Azure Cosmos DB w celu zwrócenia logarytmu naturalnego określonego wyrażenia liczbowego
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9213ef03f383dec7109652246411fac154b4a7f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302512"
---
# <a name="log-azure-cosmos-db"></a>DZIENNIK (Usługa Azure Cosmos DB)
 Zwraca logarytm naturalny określonego wyrażenia liczbowego.  
  
## <a name="syntax"></a>Składnia
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Jest wyrażeniem liczbowym.  
  
*base*  
   Opcjonalny argument numeryczny, który ustawia podstawę dla logarytmu.  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="remarks"></a>Uwagi
  
  Domyślnie funkcja LOG() zwraca logarytm naturalny. Podstawę logarytmu można zmienić na inną wartość przy użyciu opcjonalnego parametru podstawowego.  
  
  Logarytm naturalny jest logarytmem do podstawy **e**, gdzie **e** jest stałą irracjonalną w przybliżeniu równą 2,718281828.  
  
  Logarytm naturalny wykładniczej liczby jest samą liczbą: LOG( EXP( n ) ) = n. A wykładniczym logarytmem naturalnym liczby jest sama liczba: EXP( LOG( n ) ) = n.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład deklaruje zmienną i zwraca wartość logarytmu określonej zmiennej (10).  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 Poniższy przykład oblicza `LOG` wykładnik liczby.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Usługi Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
