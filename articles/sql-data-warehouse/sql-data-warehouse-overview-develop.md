---
title: Zasoby używane do tworzenia magazynu danych na platformie Azure | Dokumentacja firmy Microsoft
description: Pojęcia dotyczące programowania, decyzji projektowych, zalecenia i technik kodowania dla usługi SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 613bcb05dab993989a2ae00b71fef95794953ab8
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65850731"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Decyzje dotyczące projektu i technik kodowania dla usługi SQL Data Warehouse
Zapoznaj się za pośrednictwem tych artykuły dotyczące programowania, aby lepiej zrozumieć kluczowych decyzji projektowych, zalecenia i technik kodowania dla usługi SQL Data Warehouse.

## <a name="key-design-decisions"></a>Kluczowych decyzji projektowych
Następujące artykuły, wyróżnij pojęć i decyzje projektowe dotyczące tworzenia magazynu danych rozproszonych przy użyciu SQL Data Warehouse:

* [Połączenia][connections]
* [Współbieżność][concurrency]
* [Transakcje][transactions]
* [schematy definiowane przez użytkownika][user-defined schemas]
* [dystrybucji tabel][table distribution]
* [indeksy tabel][table indexes]
* [Partycje tabel][table partitions]
* [CTAS][CTAS]
* [statystyki][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Zalecenia dotyczące rozwoju i technik kodowania
Te artykuły wyróżnienia określonych technik kodowania, wskazówki i zalecenia dotyczące tworzenia usługi SQL Data Warehouse:

* [Procedury składowane][stored procedures]
* [labels][labels]
* [Widoki][views]
* [Tabele tymczasowe][temporary tables]
* [dynamic SQL][dynamic SQL]
* [tworzenie pętli][looping]
* [Grupuj według opcji][group by options]
* [Przypisanie zmiennej][variable assignment]

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacje, zobacz [instrukcje SQL Data Warehouse T-SQL](sql-data-warehouse-reference-tsql-statements.md).

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
