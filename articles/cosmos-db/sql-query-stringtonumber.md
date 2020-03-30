---
title: StringToNumber w języku kwerendy usługi Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemu SQL StringToNumber w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5ca8d0c4a6d244823dda6f0f79a3cf5c743a12a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296426"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (usługa Azure Cosmos DB)
 Zwraca wyrażenie przetłumaczone na liczbę. Jeśli nie można przetłumaczyć wyrażenia, zwraca niezdefiniowane.  
  
## <a name="syntax"></a>Składnia
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu, które ma być analizowane jako wyrażenie liczby JSON. Liczby w JSON muszą być liczbą całkowitą lub zmiennoprzecinkiem. Aby uzyskać szczegółowe informacje na temat formatu JSON, zobacz [json.org](https://json.org/)  
  
## <a name="return-types"></a>Typy zwrotów
  
  Zwraca wyrażenie Liczba lub niezdefiniowane.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym `StringToNumber` przykładzie pokazano, jak zachowuje się w różnych typach. 

Odstęp jest dozwolony tylko przed lub po Numer.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

W JSON prawidłowa liczba musi być liczbą całkowitą lub zmiennoprzecinkiem.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
{{}}
```  

Przekazane wyrażenie będzie analizowane jako wyrażenie number; te dane wejściowe nie są do oceny typu Number i w związku z tym zwracają niezdefiniowane. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
{{}}
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemu nie będzie korzystać z indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje ciągów usługi Azure Cosmos DB](sql-query-string-functions.md)
- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
