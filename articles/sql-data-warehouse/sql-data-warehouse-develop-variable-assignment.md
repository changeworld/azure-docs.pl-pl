---
title: Przypisz zmiennych w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące przypisywania zmiennych T-SQL w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 012bc76950f212d69d26607c666e878b22015e70
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Przypisywanie zmiennych w magazynie danych SQL Azure
Porady dotyczące przypisywania zmiennych T-SQL w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.

## <a name="setting-variables-with-declare"></a>Ustawienia zmiennych z DECLARE
Zmienne w usłudze SQL Data Warehouse są ustawiane przy użyciu `DECLARE` instrukcji lub `SET` instrukcji. Inicjowanie zmiennych z DECLARE jest jednym z najbardziej elastycznym sposoby ustawiania wartości zmiennej w usłudze SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

Umożliwia także DECLARE można ustawić więcej niż jedną zmienną w czasie. Wybierz lub aktualizacji nie można używać do następujących:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Nie można zainicjować i użyć zmiennej w tej samej instrukcji DECLARE. Aby zilustrować punkt, poniższy przykład jest **nie** dozwolone od @p1 jest zainicjowany i używany w tej samej instrukcji DECLARE. Poniższy przykład zwraca błąd.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Ustawienie wartości z zestawu
ZESTAW jest powszechnie używaną metodą do ustawiania zmiennej.

Poniższe instrukcje są poprawnymi sposób ustawiania zmiennej z ZESTAWEM:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Z zestawu jednej zmiennej można ustawić tylko w czasie. Złożone operatory są jednak dopuszczalną wartość.

## <a name="limitations"></a>Ograniczenia
Wybierz lub aktualizacji nie można użyć zmiennej przypisania.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej porad programistycznych, zobacz [omówienie tworzenia](sql-data-warehouse-overview-develop.md).

