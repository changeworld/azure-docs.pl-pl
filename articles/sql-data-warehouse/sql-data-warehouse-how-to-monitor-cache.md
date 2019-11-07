---
title: Optymalizowanie pamięci podręcznej Gen2
description: Dowiedz się, jak monitorować pamięć podręczną Gen2 przy użyciu Azure Portal.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b33f7cedca4ef130eefa28c1dbaaedd82d11a9e4
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645764"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Jak monitorować pamięć podręczną Gen2
Architektura magazynu Gen2 automatycznie warstwuje najczęściej badane segmenty magazynu kolumn w pamięci podręcznej na podstawie dysków SSD opartych na interfejsie NVMe, które są przeznaczone dla Gen2 magazynów danych. Zwiększenie wydajności jest realizowane, gdy zapytania pobierają segmenty znajdujące się w pamięci podręcznej. W tym artykule opisano sposób monitorowania i rozwiązywania problemów z niską wydajnością zapytań przez określenie, czy obciążenie jest optymalnie używane w pamięci podręcznej Gen2.  
## <a name="troubleshoot-using-the-azure-portal"></a>Rozwiązywanie problemów przy użyciu Azure Portal
Za pomocą Azure Monitor można wyświetlać metryki pamięci podręcznej Gen2 w celu rozwiązywania problemów z wydajnością zapytań. Najpierw przejdź do Azure Portal i kliknij przycisk Monitoruj:

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

Wybierz przycisk metryki i wypełnij pola **subskrypcja**, Grupa **zasobów**, **Typ zasobu**i **nazwa zasobu** magazynu danych.

Kluczowe metryki dotyczące rozwiązywania problemów z pamięcią podręczną Gen2 są **procentami trafień pamięci podręcznej** i **procentowo używane**. Skonfiguruj wykres metryki platformy Azure, aby wyświetlić te dwie metryki.

![Metryki pamięci podręcznej](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Procent trafień i użycia pamięci podręcznej
W poniższej macierzy opisano scenariusze oparte na wartościach metryk pamięci podręcznej:

|                                | **Wysoki procent trafień w pamięci podręcznej** | **Procent trafień niskiego poziomu pamięci podręcznej** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Procent użycia dużej pamięci podręcznej** |          Scenariusz 1           |          Scenariusz 2          |
| **Procent użycia niskiej pamięci podręcznej**  |          Scenariusz 3           |          Scenariusz 4          |

**Scenariusz 1:** Optymalnie korzystasz z pamięci podręcznej. [Rozwiązywanie problemów z](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) innymi obszarami, które mogą spowalniać zapytania.

**Scenariusz 2:** Bieżący roboczy zestaw danych nie mieści się w pamięci podręcznej, co powoduje niską wartość procentową trafień pamięci podręcznej z powodu fizycznych operacji odczytu. Rozważ skalowanie w górę poziomu wydajności i ponowne uruchomienie obciążenia w celu wypełnienia pamięci podręcznej.

**Scenariusz 3:** Prawdopodobnie zapytanie działa wolno ze względu na przyczyny niezwiązane z pamięcią podręczną. [Rozwiązywanie problemów z](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) innymi obszarami, które mogą spowalniać zapytania. Możesz również rozważyć [skalowanie wystąpienia](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) , aby zmniejszyć rozmiar pamięci podręcznej w celu oszczędności kosztów. 

**Scenariusz 4:** Masz zimną pamięć podręczną, która może być przyczyną powolnego działania zapytania. Rozważ ponowne uruchomienie zapytania, ponieważ roboczy zestaw danych powinien teraz znajdować się w pamięci podręcznej. 

**Ważne: Jeśli procent trafień pamięci podręcznej lub procent użycia pamięci podręcznej nie jest aktualizowany po rozpoczęciu obciążenia, zestaw roboczy może już znajdować się w pamięci. Uwaga tylko klastrowane tabele magazynu kolumn są buforowane.**

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat ogólnego dostrajania wydajności zapytań, zobacz [monitorowanie wykonywania zapytań](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor#monitor-query-execution).


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: https://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
