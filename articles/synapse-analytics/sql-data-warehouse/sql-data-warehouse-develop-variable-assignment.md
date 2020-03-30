---
title: Przypisywanie zmiennych
description: Porady dotyczące przypisywania zmiennych T-SQL w usłudze Azure SQL Data Warehouse do tworzenia rozwiązań.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0adcd9bdf92b7ec649b7d91ca0e655fc006b3549
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351660"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Przypisywanie zmiennych w magazynie danych SQL usługi Azure

Porady dotyczące przypisywania zmiennych T-SQL w usłudze Azure SQL Data Warehouse do tworzenia rozwiązań.

## <a name="setting-variables-with-declare"></a>Ustawianie zmiennych za pomocą DECLARE

Zmienne w magazynie danych SQL `DECLARE` są `SET` ustawiane przy użyciu instrukcji lub instrukcji. Inicjowanie zmiennych za pomocą declare jest jednym z najbardziej elastycznych sposobów ustawiania wartości zmiennej w magazynie danych SQL.

```sql
DECLARE @v  int = 0
;
```

Można również użyć DECLARE, aby ustawić więcej niż jedną zmienną naraz. Nie można użyć SELECT lub UPDATE, aby wykonać następujące czynności:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Nie można zainicjować i użyć zmiennej w tej samej instrukcji DECLARE. Aby zilustrować punkt, poniższy **not** przykład nie @p1 jest dozwolone, ponieważ jest inicjowany i używany w tej samej instrukcji DECLARE. Poniższy przykład zawiera błąd.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Ustawianie wartości za pomocą SET

SET jest powszechną metodą ustawiania pojedynczej zmiennej.

Następujące instrukcje są wszystkie prawidłowe sposoby, aby ustawić zmienną z SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Za pomocą SET można ustawić tylko jedną zmienną naraz. Jednakże podmioty podmiotów łączenia są dopuszczalne.

## <a name="limitations"></a>Ograniczenia

Nie można użyć update dla przypisania zmiennych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](sql-data-warehouse-overview-develop.md).
