---
title: Przypisz zmiennych w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące przypisywania zmiennych języka T-SQL w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 62c4273a02e02aff268a96e1b13483088ba33f87
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861681"
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
