---
title: Uaktualnij do najnowszej generacji Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Magazyn danych SQL Azure należy uaktualnić do najnowszej generacji Azure architektura sprzętu i magazynowania.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 8ca8156d98932e0e7313375cadc1ace2a3088881
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Optymalizowanie wydajności przez zmianę warstwy dla usługi SQL Data Warehouse
Magazyn danych SQL Azure należy uaktualnić do najnowszej generacji Azure architektura sprzętu i magazynowania.

## <a name="why-upgrade"></a>Dlaczego uaktualnienie?
Można teraz bezproblemowo uaktualnić do Gen2 magazynu danych SQL w portalu Azure. Jeśli masz magazyn danych Gen1 uaktualniania jest zalecane. Uaktualniając, można użyć najnowszej generacji Azure sprzętu i magazynu architektura rozszerzona. Można korzystać z większą wydajność, skalowalności i nieograniczony magazyn kolumnowym. 

## <a name="applies-to"></a>Dotyczy
To uaktualnienie dotyczy Gen1 hurtowni danych.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Przed rozpoczęciem
> [!NOTE]
> Jeśli istniejącego magazynu danych Gen1 nie znajduje się w regionie, w którym Gen2 jest dostępna, możesz [geograficznie — po ukończeniu przywracania Gen2](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) za pomocą programu PowerShell do obsługiwanym regionie.
> 
>

1. Jeśli w magazynie danych Gen1 do uaktualnienia jest wstrzymana, [hurtowni danych wznowić](pause-and-resume-compute-portal.md).
2. Należy przygotować kilka minut przestoju. 



## <a name="start-the-upgrade"></a>Uruchom operację uaktualniania

1. Przejdź do Twojej Gen1 danych magazynu w portalu Azure i wybierz polecenie **uaktualnienia do Gen2**: ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)

2. Domyślnie **wybierz poziom wydajności sugerowane** dla magazynu danych na podstawie aktualny poziom wydajności na Gen1 przy użyciu mapowania poniżej:
    
| Gen1 | Gen2 |
| :----------------------: | :-------------------: |
|      DW100 – DW1000      |        DW1000c        |
|          DW1200          |        DW1500c        |
|          DW1500          |        DW1500c        |
|          DW2000          |        DW2000c        |
|          DW3000          |        DW3000c        |
|          DW6000          |        DW6000c        |


3. Upewnij się, że obciążenie została ukończona uruchomiona i stanie spoczynku przed uaktualnieniem. Wystąpi przestój kilka minut przed powrotem do trybu online jako Gen2 Hurtownia danych magazynu danych. **Kliknij przycisk Uaktualnij,**. Cena warstwę wydajności Gen2 jest obecnie połowie — wyłączone okresie używania wersji zapoznawczej:
    
    ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Monitorowanie uaktualnienia** sprawdzając stan w portalu Azure:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)
   
   Pierwszym krokiem procesu uaktualniania przechodzi przez operację skalowania ("Uaktualnianie — w trybie Offline") gdzie wszystkie sesje zostaną skasowane i połączenia zostaną usunięte. 
   
   Drugi etap procesu uaktualniania jest migracji danych ("Uaktualnianie - Online"). Migracja danych jest strumieniem online proces w tle, co wolno przenosi kolumnowy danych z architektury starego magazynu, do korzystania z lokalnej pamięci podręcznej SSD Architektura magazynu. W tym czasie będzie online wykonywania kwerend i ładowania magazynu danych. Wszystkie dane będą dostępne dla zapytań niezależnie od tego, czy został już zmigrowany, czy nie. Migracja danych odbywa się z szybkością różne w zależności od tego, że rozmiar danych, poziom wydajności i liczby segmentów magazynu kolumn. 

5. **Opcjonalne zalecenie:** aby przyspieszyć proces migracji danych w tle, zaleca się natychmiast wymusić przenoszenia danych, uruchamiając [Alter Index rebuild](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index) dla wszystkich tabel magazynu kolumn w większych SLO i zasobów Klasa. Ta operacja jest w trybie offline w porównaniu do strumieniem proces w tle; jednak migracji danych będzie znacznie szybsza, bo gdzie następnie mogą w pełni korzystać nowej architektury magazynu rozszerzonego raz wraz z rowgroups wysokiej jakości. 

To następujące zapytanie generuje wymagane polecenia Alter Index Rebuild aby przyspieszyć proces migracji danych:

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON [' 
       + Schema_name(tbl.schema_id) + '].[' 
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE 
                                                         WHEN ( 
                                                     (SELECT Count(*) 
                                                      FROM   sys.partitions 
                                                             part2 
                                                      WHERE  part2.index_id 
                                                             = idx.index_id 
                                                             AND 
                                                     idx.object_id = 
                                                     part2.object_id) 
                                                     > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              ELSE '' 
                                                       END ) + '; SELECT ''[' + 
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' + 
              Object_name(idx.object_id) + '] ' + ( 
              CASE 
                WHEN ( (SELECT Count(*) 
                        FROM   sys.partitions 
                               part2 
                        WHERE 
                     part2.index_id = 
                     idx.index_id 
                     AND idx.object_id 
                         = part2.object_id) > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              + ' completed'';' 
              ELSE ' completed'';' 
                                                    END ) 
FROM   sys.indexes idx 
       INNER JOIN sys.tables tbl 
               ON idx.object_id = tbl.object_id 
       LEFT OUTER JOIN sys.partitions part 
                    ON idx.index_id = part.index_id 
                       AND idx.object_id = part.object_id 
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE'; 
```



## <a name="next-steps"></a>Kolejne kroki
Magazyn danych uaktualnionego jest w trybie online. Aby móc korzystać z rozszerzonego architektury, zobacz [klasy zasobów do zarządzania obciążenia](resource-classes-for-workload-management.md).
 
