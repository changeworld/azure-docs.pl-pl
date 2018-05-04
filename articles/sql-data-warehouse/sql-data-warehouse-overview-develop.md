---
title: Zasoby na potrzeby tworzenia magazynu danych na platformie Azure | Dokumentacja firmy Microsoft
description: Pojęcia dotyczące programowania, decyzji projektowych i zalecenia dotyczące technik programowania dla usługi SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: d9a272b2f43e080cd44b7179fe6f9dc55507142b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Decyzje dotyczące projektu i technik programowania dla usługi SQL Data Warehouse
Zapoznaj się za pośrednictwem tych artykułach programowanie, aby lepiej zrozumieć kluczowych decyzji projektowych, zalecenia i technik programowania dla usługi SQL Data Warehouse.

## <a name="key-design-decisions"></a>Kluczowych decyzji projektowych
Następujące artykuły Wyróżnij pojęcia i decyzje dotyczące projektu związane z opracowywaniem magazynu danych rozproszonych za pomocą usługi SQL Data Warehouse:

* [Połączenia][connections]
* [Współbieżność][concurrency]
* [Transakcje][transactions]
* [Schematy zdefiniowane przez użytkownika][user-defined schemas]
* [Dystrybucja tabeli][table distribution]
* [indeksy tabel][table indexes]
* [partycje tabeli][table partitions]
* [CTAS][CTAS]
* [Statystyki][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Zalecenia dotyczące tworzenia i technik programowania
Te artykuły wyróżnianie określonych technik programowania, wskazówki i zalecenia dotyczące Tworzenie magazynu danych SQL:

* [Procedury składowane][stored procedures]
* [labels][labels]
* [Widoki][views]
* [tabele tymczasowe][temporary tables]
* [dynamic SQL][dynamic SQL]
* [tworzenie pętli][looping]
* [Grupuj według opcje][group by options]
* [Przypisanie zmiennej][variable assignment]

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacje, zobacz [instrukcji SQL Data Warehouse T-SQL](sql-data-warehouse-reference-tsql-statements.md).

<!--Image references-->

<!--Article references-->
[concurrency]: ./resource-classes-for-workload-management.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md


<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
