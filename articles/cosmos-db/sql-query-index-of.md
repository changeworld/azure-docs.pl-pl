---
title: INDEX_OF w języku zapytań usługi Azure Cosmos DB
description: Dowiedz się więcej o INDEX_OF funkcji systemu SQL w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 50e489fdf57398d486b07944782ecbb3fd1d6a43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71350992"
---
# <a name="index_of-azure-cosmos-db"></a>INDEX_OF (Usługa Azure Cosmos DB)
 Zwraca pozycję początkową pierwszego wystąpienia drugiego ciągu wyrażenia w ramach pierwszego określonego wyrażenia ciągu lub wartość -1, jeśli ciąg nie zostanie znaleziony.  
  
## <a name="syntax"></a>Składnia
  
```sql
INDEX_OF(<str_expr1>, <str_expr2> [, <numeric_expr>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Jest wyrażeniem ciągu, które ma być przeszukiwane.  
  
*str_expr2*  
   Jest wyrażeniem ciągu do wyszukania.  

*numeric_expr* Opcjonalne wyrażenie liczbowe, które ustawia pozycję, w której rozpocznie się wyszukiwanie. Pierwsza pozycja w *str_expr1* wynosi 0. 
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca indeks różnych podciągów wewnątrz "abc".  
  
```sql
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"i1": 0, "i2": 1, "i3": -1}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje ciągów usługi Azure Cosmos DB](sql-query-string-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
