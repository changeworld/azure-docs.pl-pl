---
title: Funkcje sprawdzania typów w języku kwerend usługi Azure Cosmos DB
description: Dowiedz się więcej o sprawdzaniu funkcji systemu SQL w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349077"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Funkcje sprawdzania typów (usługa Azure Cosmos DB)

Funkcje sprawdzania typu umożliwiają sprawdzenie typu wyrażenia w kwerendzie SQL. Za pomocą funkcji sprawdzania typu można określić typy właściwości w elementach w locie, gdy są one zmienne lub nieznane. 

## <a name="functions"></a>Funkcje

Oto tabela obsługiwanych wbudowanych funkcji sprawdzania typu:

Następujące funkcje obsługują sprawdzanie typu względem wartości wejściowych, a każda z nich zwraca wartość logiczną.  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[IS_NULL](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>Następne kroki

- [Funkcje systemowe Usługi Azure Cosmos DB](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Funkcje zdefiniowane przez użytkownika](sql-query-udfs.md)
- [Agregacje](sql-query-aggregates.md)
