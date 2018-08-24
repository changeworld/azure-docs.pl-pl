---
title: Uaktualnianie do najnowszej generacji usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Uaktualnij usługi Azure SQL Data Warehouse do najnowszej generacji architektura sprzętu i magazynowania na platformie Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/22/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 1e2993e1f4d28fd5d281ea510121686d3bc37a8c
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746966"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Optymalizowanie wydajności przez zmianę warstwy dla usługi SQL Data Warehouse
Uaktualnij usługi Azure SQL Data Warehouse do najnowszej generacji architektura sprzętu i magazynowania na platformie Azure.

## <a name="why-upgrade"></a>Dlaczego warto wykonać uaktualnienie?
Użytkownik może teraz można bezproblemowo przeprowadzić uaktualnienie do warstwy SQL dane magazynu obliczenia zoptymalizowane pod kątem Gen2 w witrynie Azure portal. Uaktualnianie jest zalecane w przypadku magazynu obliczenia zoptymalizowane pod kątem Gen1 warstwy danych. Przeprowadzając uaktualnienie, można użyć najnowszej generacji sprzętowych platformy Azure i rozszerzone architektury magazynu. Możesz korzystać z zalet zwiększyć wydajność, skalowalności i nieograniczony magazyn kolumnowy. 

## <a name="applies-to"></a>Dotyczy
To uaktualnienie dotyczy magazyny danych warstwy obliczeń Gen1 zoptymalizowane pod kątem.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Przed rozpoczęciem
> [!NOTE]
> Jeśli istniejący magazyn danych warstwy obliczeń Gen1 zoptymalizowane pod kątem nie jest dostępne w regionie, w których jest dostępna w warstwie obliczenia zoptymalizowane pod kątem Gen2, możesz to zrobić [geoprzywracanie](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) za pomocą programu PowerShell obsługiwany region.
> 
>

1. Jeśli w magazynie danych warstwy obliczeń Gen1 zoptymalizowane pod kątem uaktualnienia jest wstrzymana, [hurtowni danych wznowić](pause-and-resume-compute-portal.md).
2. Należy przygotować za kilka minut przestoju. 



## <a name="start-the-upgrade"></a>Uruchom operację uaktualniania

1. Przejdź do usługi obliczenia zoptymalizowane pod kątem Gen1 danych warstwy magazynu w witrynie Azure portal i wybierz polecenie **uaktualnienie do Gen2**: ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)

2. Domyślnie **wybierz poziom wydajności sugerowane** dla magazynu danych na podstawie Twój bieżący poziom wydajności w warstwie obliczenia zoptymalizowane pod kątem Gen1 przy użyciu mapowania poniżej:
    
   | Zoptymalizowane pod kątem Gen1 warstwa wystąpień obliczeniowych | Zoptymalizowane pod kątem Gen2 warstwa wystąpień obliczeniowych |
   | :----------------------: | :-------------------: |
   |      DW100 – DW1000      |        DW1000c        |
   |          DW1200          |        DW1500c        |
   |          DW1500          |        DW1500c        |
   |          DW2000          |        DW2000c        |
   |          DW3000          |        DW3000c        |
   |          DW6000          |        DW6000c        |

3. Upewnij się, że obciążenie zostało zakończone przed uaktualnieniem uruchomiona i w stanie spoczynku. Wystąpi przestój przez kilka minut, zanim magazyn danych jest online jako magazyn danych warstwy obliczenia zoptymalizowane pod kątem Gen2. **Kliknij przycisk Uaktualnij**. Cena warstwy wydajności warstwy obliczeń Gen2 zoptymalizowane pod kątem jest obecnie pół off w okresie zapoznawczym:
    
   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Monitorowanie uaktualnienia** , sprawdzając jego stan w witrynie Azure portal:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)
   
   Pierwszym krokiem procesu uaktualniania przechodzi przez operację skalowania ("Uaktualnianie — tryb Offline") gdzie wszystkie sesje zostaną skasowane i zostanie porzucony połączeń. 
   
   Drugi etap procesu uaktualniania jest migracja danych ("Uaktualnianie — Tryb Online"). Migracja danych jest strumieniem online proces w tle, który powoli przesuwa ze starego Architektura magazynu danych kolumnowych do nowej architektury magazynu korzystanie z lokalnej pamięci podręcznej dysków SSD. W tym czasie magazyn danych będzie w trybie online wykonywania zapytań i ładowania. Wszystkie dane będą dostępne dla zapytań, niezależnie od tego, czy ma została zmigrowana, czy nie. Migracja danych odbywa się stawki różne w zależności od tego, czy rozmiar danych, poziom wydajności i liczbę segmentów magazynu kolumn. 

5. **Opcjonalne zalecenie:** przyspiesza proces w tle migracji danych, możesz od razu wymusić przenoszenia danych, uruchamiając [Alter Index rebuild](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index) dla wszystkich tabel magazynu kolumn podstawowy może być wykonywania zapytań na większy cel poziomu usługi i klasa zasobów. Ta operacja jest **offline** w porównaniu do proces tła strumieniem, który może zająć godzin w zależności od liczby i rozmiarów tabel; jednak migracja danych będzie znacznie szybsza, bo gdzie następnie mogą w pełni korzystać nowej architektury magazynu rozszerzonego po zakończeniu za pomocą grupy wierszy wysokiej jakości. 

Następujące zapytanie generuje wymagane polecenia Alter Index Rebuild w celu przyspieszenia procesu migracji danych:

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
Magazyn danych uaktualnionego jest w trybie online. Aby móc korzystać z rozszerzonych architektury, zobacz [klasy zasobów do zarządzania obciążeniem](resource-classes-for-workload-management.md).
 
