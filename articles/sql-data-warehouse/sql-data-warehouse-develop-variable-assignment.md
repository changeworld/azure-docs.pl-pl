---
title: Przypisz zmiennych w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące przypisywania zmiennych języka T-SQL w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: cf6d63c8cf67e42eed2ca52bfd0d0a3f9b0e10b1
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43302088"
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
Przypisanie zmiennej nie można używać SELECT lub UPDATE.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej porad programistycznych, zobacz [omówienie programowania w usłudze](sql-data-warehouse-overview-develop.md).

