---
title: Przypisywanie zmiennych
description: W tym artykule znajdziesz podstawowe wskazówki dotyczące przypisywania zmiennych T-SQL w puli SQL.
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
ms.openlocfilehash: 2dcf706ea59657abc2718a69e59191604dc2849d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633408"
---
# <a name="assign-variables-in-synapse-sql-pool"></a>Przypisywanie zmiennych w puli SQL Synapse

W tym artykule znajdziesz podstawowe wskazówki dotyczące przypisywania zmiennych T-SQL w puli SQL.

## <a name="set-variables-with-declare"></a>Ustawianie zmiennych za pomocą DECLARE

Zmienne w puli SQL `DECLARE` są ustawiane przy użyciu instrukcji lub `SET` instrukcji. Inicjowanie zmiennych za pomocą declare jest jednym z najbardziej elastycznych sposobów ustawiania wartości zmiennej w puli SQL.

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

Nie można zainicjować i użyć zmiennej w tej samej instrukcji DECLARE. Aby zilustrować punkt, poniższy **not** przykład nie @p1 jest dozwolone, ponieważ jest inicjowany i używany w tej samej instrukcji DECLARE. W związku z tym następujący przykład zawiera błąd:

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>Ustawianie wartości za pomocą zestawu

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

Nie można użyć UPDATE do przypisania zmiennych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](sql-data-warehouse-overview-develop.md).
