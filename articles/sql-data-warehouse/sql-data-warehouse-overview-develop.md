---
title: Zasoby służące do opracowywania magazynu danych w usłudze Azure Synapse Analytics
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
ms.openlocfilehash: 0f8967eab1be8351c50035d8afa784aa7afd79e1
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153319"
---
# <a name="design-decisions-and-coding-techniques-for-a-data-warehouse-in-azure-synapse-analytics"></a>Projektowanie decyzji i technik kodowania dla hurtowni danych w usłudze Azure Synapse Analytics 
 W tym artykule znajdziesz dodatkowe zasoby, które pomogą lepiej zrozumieć najważniejsze decyzje projektowe, zalecenia i techniki kodowania dla magazynu danych w usłudze Azure Synapse.

## <a name="key-design-decisions"></a>Najważniejsze decyzje projektowe
W poniższych artykułach wyróżniono koncepcje i decyzje projektowe dotyczące tworzenia rozproszonego magazynu danych przy użyciu funkcji analizy SQL w usłudze Azure Synapse:

* [Licznik](sql-data-warehouse-connect-overview.md)
* [współbieżności](resource-classes-for-workload-management.md)
* [Akcja](sql-data-warehouse-develop-transactions.md)
* [schematy zdefiniowane przez użytkownika](sql-data-warehouse-develop-user-defined-schemas.md)
* [Dystrybucja tabel](sql-data-warehouse-tables-distribute.md)
* [indeksy tabeli](sql-data-warehouse-tables-index.md)
* [partycje tabeli](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [statystyki](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Zalecenia dotyczące programowania i techniki kodowania
W poniższych artykułach przedstawiono techniki kodowania, porady i zalecenia dotyczące tworzenia hurtowni danych przy użyciu usługi SQL Analytics:

* [procedury składowane](sql-data-warehouse-develop-stored-procedures.md)
* [Etykieta](sql-data-warehouse-develop-label.md)
* [Widoki](sql-data-warehouse-develop-views.md)
* [tabele tymczasowe](sql-data-warehouse-tables-temporary.md)
* [dynamiczny SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [pętli](sql-data-warehouse-develop-loops.md)
* [Grupuj według opcji](sql-data-warehouse-develop-group-by-options.md)
* [przypisanie zmiennej](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz [instrukcje języka T-SQL](sql-data-warehouse-reference-tsql-statements.md).
