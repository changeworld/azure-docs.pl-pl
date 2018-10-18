---
title: Optymalizowanie pamięci podręcznej Gen2 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować Gen2 pamięci podręcznej w witrynie Azure portal.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: how-to
ms.component: monitor and tune
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 1d366850bc886dc48afc59ffaf0958b39314ebb1
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385536"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Jak monitorować Gen2 pamięci podręcznej
Architektura magazynu Gen2 automatycznie tworzy warstwy segmentów najczęściej poszukiwanych magazynu kolumn w pamięci podręcznej znajdującej się na NVMe oparte na dyskach SSD, przeznaczona dla magazynów danych Gen2. Większa wydajność jest wykonywane zapytania pobierają segmenty, które są znajdującej się w pamięci podręcznej. W tym artykule opisano sposób monitorowania i rozwiązywanie problemów z wydajnością wolnych zapytań, ustalając, czy obciążenie optymalnie korzystanie z pamięci podręcznej Gen2.  
## <a name="troubleshoot-using-the-azure-portal"></a>Rozwiązywanie problemów przy użyciu witryny Azure portal
Aby wyświetlić metryki pamięci podręcznej Gen2 rozwiązywać problemy z wydajnością zapytań, można użyć usługi Azure Monitor. Najpierw przejdź do witryny Azure portal i kliknij Monitor:

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

Wybierz przycisk metryki, a następnie wypełnij **subskrypcji**, **zasobów** **grupy**, **typ zasobu**, i **zasobów Nazwa** magazynu danych.

Czy kluczowych metryk do rozwiązywania problemów z pamięci podręcznej Gen2 **procent liczby trafień pamięci podręcznej** i **pamięć podręczna używana wartość procentowa**. Konfigurowanie usługi Azure wykresu metryki, aby wyświetlić te dwie metryki.

![Metryki pamięci podręcznej](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Pamięć podręczna trafień i używana wartość procentowa
Tabela poniżej opisano scenariusze, w oparciu o wartości metryk pamięci podręcznej:

|                                | **Procent trafień wysokiej pamięci podręcznej** | **Procent trafień w pamięci podręcznej niski** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Wysoki procent pamięć podręczna w użyciu** |          Scenariusz 1           |          Scenariusz 2          |
| **Niska pamięć podręczna używana wartość procentowa**  |          Scenariusz 3           |          Scenariusz 4          |

**Scenariusz 1:** są optymalnie przy użyciu pamięci podręcznej. [Rozwiązywanie problemów z](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) innych obszarów, które mogą spowalniać zapytania.

**Scenariusz 2:** bieżącego zestawu danych roboczych nie mieści się w pamięci podręcznej, co powoduje, że niskiej wartości procentowej ze względu na fizyczne odczyty trafień w pamięci podręcznej. Rozważ skalowanie w górę poziomu wydajności i ponownie uruchom obciążenia, aby wypełnić pamięć podręczną.

**Scenariusz 3:** jest prawdopodobne, że zapytanie działa wolno, z przyczyn niezwiązanych ze sobą w pamięci podręcznej. [Rozwiązywanie problemów z](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) innych obszarów, które mogą spowalniać zapytania. Możesz też rozważyć [skalowanie w dół wystąpienia](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) Aby zmniejszyć rozmiar pamięci podręcznej w celu obniżenia kosztów. 

**Scenariusz 4:** miał zimnych pamięci podręcznej, która może być powód, dlaczego zapytania została powolne. Należy rozważyć ponowne uruchomienie zapytania jako zestaw danych roboczych powinny teraz być w buforowane. 

**Ważne: Procent trafień w pamięci podręcznej, lub wartość procentowa pamięć podręczna w użyciu nie jest aktualizacji po ponownym uruchomieniu obciążenie, zestaw roboczy może już być przechowywanych w pamięci. Uwaga tylko klastrowanego magazynu kolumn, które tabele są buforowane.**

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat dostosowywania wydajności kwerendy ogólne, zobacz [monitorowania wykonywanych zapytań](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor#monitor-query-execution).


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
