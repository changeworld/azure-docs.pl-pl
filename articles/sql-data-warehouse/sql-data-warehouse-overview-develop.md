---
title: Zasoby służące do tworzenia magazynu danych na platformie Azure
description: Koncepcje dotyczące programowania, decyzje projektowe, zalecenia i techniki kodowania dla SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: a6cb7937bdd5dea9eb1a48b2b350db9077431fe0
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645618"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Decyzje projektowe i techniki kodowania dla SQL Data Warehouse
Zapoznaj się z tymi artykułami programistycznymi, aby lepiej zrozumieć najważniejsze decyzje dotyczące projektowania, zalecenia i techniki kodowania dla SQL Data Warehouse.

## <a name="key-design-decisions"></a>Najważniejsze decyzje projektowe
W poniższych artykułach omówiono koncepcje i decyzje projektowe dotyczące tworzenia rozproszonego magazynu danych przy użyciu SQL Data Warehouse:

* [Licznik][connections]
* [współbieżności][concurrency]
* [Akcja][transactions]
* [schematy zdefiniowane przez użytkownika][user-defined schemas]
* [Dystrybucja tabel][table distribution]
* [indeksy tabeli][table indexes]
* [partycje tabeli][table partitions]
* [CTAS][CTAS]
* [statystyki][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Zalecenia dotyczące programowania i techniki kodowania
W tych artykułach wyróżniono konkretne techniki kodowania, porady i zalecenia dotyczące tworzenia SQL Data Warehouse:

* [procedury składowane][stored procedures]
* [Etykieta][labels]
* [Widoki][views]
* [tabele tymczasowe][temporary tables]
* [dynamiczny SQL][dynamic SQL]
* [pętli][looping]
* [Grupuj według opcji][group by options]
* [przypisanie zmiennej][variable assignment]

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz [SQL Data Warehouse instrukcji języka T-SQL](sql-data-warehouse-reference-tsql-statements.md).

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
