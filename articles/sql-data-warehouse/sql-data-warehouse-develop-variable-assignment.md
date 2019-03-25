---
title: Przypisz zmiennych w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące przypisywania zmiennych języka T-SQL w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: ee97f7e5cda8b954fb697f73746e416d88d38c2d
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401688"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Przypisywanie zmiennych w usłudze Azure SQL Data Warehouse

Porady dotyczące przypisywania zmiennych języka T-SQL w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.

## <a name="setting-variables-with-declare"></a>Ustawianie zmiennych z DECLARE

Zmienne w usłudze SQL Data Warehouse są ustawiane przy użyciu `DECLARE` instrukcji lub `SET` instrukcji. Inicjowanie zmiennych o DECLARE, jest najbardziej elastyczny sposób ustawić wartość zmiennej w usłudze SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

DECLARE umożliwia również ustawić więcej niż jedną zmienną w czasie. Wybierz lub aktualizacji nie można używać, wykonaj następujące czynności:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Nie można zainicjować i użyć zmiennej w tej samej instrukcji DECLARE. Aby zilustrować ideę, poniższy przykład jest **nie** dozwolona, ponieważ @p1 jest inicjowany i używane w tej samej instrukcji DECLARE. Poniższy przykład powoduje błąd.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Ustawianie wartości przy użyciu zestawu

ZESTAW to typowe metody do ustawiania zmiennej.

Poniższe instrukcje są wszystkie prawidłowe metody, aby ustawić zmienną przy użyciu zestawu:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Jednej zmiennej w danym momencie można ustawić tylko przy użyciu zestawu. Jednak złożone operatory są dozwolone.

## <a name="limitations"></a>Ograniczenia

Przypisanie zmiennej nie można użyć aktualizacji.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej porad programistycznych, zobacz [omówienie programowania w usłudze](sql-data-warehouse-overview-develop.md).
