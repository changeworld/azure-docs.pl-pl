---
title: GÓRNy w Azure Cosmos DB języku zapytań
description: Więcej informacji na temat funkcji systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303974"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych małych liter na wielkie litery.  

Górna Funkcja systemowa nie korzysta z indeksu. Jeśli planujesz częste porównywanie bez uwzględniania wielkości liter, Górna Funkcja systemowa może zużywać znaczną ilość jednostek RU. W takim przypadku zamiast używać górnej funkcji systemowej do normalizacji danych za każdym razem w przypadku porównań, można znormalizować wielkość liter po wstawieniu. Następnie zapytanie takie jak SELECT * FROM c, gdzie UPPER (c. Name) = "BOB", po prostu wybiera * z c, gdzie c.name = "BOB".

## <a name="syntax"></a>Składnia
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak używać `UPPER` w zapytaniu  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
