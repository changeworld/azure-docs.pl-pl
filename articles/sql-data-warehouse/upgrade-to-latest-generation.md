---
title: Uaktualnianie do najnowszej generacji usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Uaktualnij usługi Azure SQL Data Warehouse do najnowszej generacji architektura sprzętu i magazynowania na platformie Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/26/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 173846e4828228bdc51fc42858e0c6c9b00cafd6
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242794"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Optymalizowanie wydajności przez zmianę warstwy dla usługi SQL Data Warehouse
Uaktualnij usługi Azure SQL Data Warehouse do najnowszej generacji architektura sprzętu i magazynowania na platformie Azure.

## <a name="why-upgrade"></a>Dlaczego warto wykonać uaktualnienie?
Użytkownik może teraz można bezproblemowo przeprowadzić uaktualnienie do warstwy SQL dane magazynu obliczenia zoptymalizowane pod kątem Gen2 w witrynie Azure portal. Uaktualnianie jest zalecane w przypadku magazynu obliczenia zoptymalizowane pod kątem Gen1 warstwy danych. Przeprowadzając uaktualnienie, można użyć najnowszej generacji sprzętowych platformy Azure i rozszerzone architektury magazynu. Możesz korzystać z zalet zwiększyć wydajność, skalowalności i nieograniczony magazyn kolumnowy. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>Dotyczy
To uaktualnienie dotyczy magazyny danych warstwy obliczeń Gen1 zoptymalizowane pod kątem.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Przed rozpoczęciem
> [!NOTE]
> Jeśli istniejący magazyn danych warstwy obliczeń Gen1 zoptymalizowane pod kątem nie jest dostępne w regionie, w których jest dostępna w warstwie obliczenia zoptymalizowane pod kątem Gen2, możesz to zrobić [geoprzywracanie](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) za pomocą programu PowerShell obsługiwany region.
>
> 

1. Jeśli w magazynie danych warstwy obliczeń Gen1 zoptymalizowane pod kątem uaktualnienia jest wstrzymana, [hurtowni danych wznowić](pause-and-resume-compute-portal.md).

2. Należy przygotować za kilka minut przestoju. 

3. Zidentyfikować wszelkie odwołania do kodu do obliczenia zoptymalizowane pod kątem Gen1 poziomów wydajności i zmodyfikuj je do ich równoważne poziomu wydajności obliczenia zoptymalizowane pod kątem Gen2. Poniżej przedstawiono dwa przykłady, o których należy zaktualizować odwołania do kodu przed uaktualnieniem:

   Oryginalne polecenie Gen1 programu PowerShell:

   ```powershell
   Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Zmodyfikowane w celu:

   ```powershell
   Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE] 
   > -RequestedServiceObjectiveName "DW300" jest zmieniana na - RequestedServiceObjectiveName "DW300**c**"
   >

   Oryginalne polecenie Gen1 T-SQL:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Zmodyfikowane w celu:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ; 
   ```
    > [!NOTE] 
    > SERVICE_OBJECTIVE = "DW300" jest zmieniana na SERVICE_OBJECTIVE = "DW300**c**"



## <a name="start-the-upgrade"></a>Uruchom operację uaktualniania

1. Przejdź do usługi obliczenia zoptymalizowane pod kątem Gen1 danych warstwy magazynu w witrynie Azure portal i wybierz polecenie **uaktualnienie do Gen2** karty na karcie zadania:  ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)
    
    > [!NOTE]
    > Jeśli nie widzisz **uaktualnienie do Gen2** karty, na karcie zadania, do typu Twojej subskrypcji jest ograniczona w bieżącym regionie.
    > [Wyślij bilet pomocy technicznej](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) można pobrać listy dozwolonych Twojej subskrypcji.

2. Domyślnie **wybierz poziom wydajności sugerowane** dla magazynu danych na podstawie Twój bieżący poziom wydajności w warstwie obliczenia zoptymalizowane pod kątem Gen1 przy użyciu mapowania poniżej:

   | Zoptymalizowane pod kątem Gen1 warstwa wystąpień obliczeniowych | Zoptymalizowane pod kątem Gen2 warstwa wystąpień obliczeniowych |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

3. Upewnij się, że obciążenie zostało zakończone przed uaktualnieniem uruchomiona i w stanie spoczynku. Wystąpi przestój przez kilka minut, zanim magazyn danych jest online jako magazyn danych warstwy obliczenia zoptymalizowane pod kątem Gen2. **Kliknij przycisk Uaktualnij**:

   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Monitorowanie uaktualnienia** , sprawdzając jego stan w witrynie Azure portal:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)

   Pierwszym krokiem procesu uaktualniania przechodzi przez operację skalowania ("Uaktualnianie — tryb Offline") gdzie wszystkie sesje zostaną skasowane i zostanie porzucony połączeń. 

   Drugi etap procesu uaktualniania jest migracja danych ("Uaktualnianie — Tryb Online"). Migracja danych jest strumieniem online proces w tle, który powoli przesuwa ze starego Architektura magazynu danych kolumnowych do nowej architektury magazynu korzystanie z lokalnej pamięci podręcznej dysków SSD. W tym czasie magazyn danych będzie w trybie online wykonywania zapytań i ładowania. Wszystkie dane będą dostępne dla zapytań, niezależnie od tego, czy ma została zmigrowana, czy nie. Migracja danych odbywa się stawki różne w zależności od tego, czy rozmiar danych, poziom wydajności i liczbę segmentów magazynu kolumn. 

5. **Zalecenie opcjonalne:** Aby przyspieszyć proces w tle migracji danych, możesz od razu wymusić przenoszenia danych, uruchamiając [Alter Index rebuild](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index) dla wszystkich tabel magazynu kolumn głównej będzie wykonywana kwerenda na większą klasę poziomu usługi i zasobów. Ta operacja jest **offline** w porównaniu do proces tła strumieniem, który może zająć godzin w zależności od liczby i rozmiarów tabel; jednak migracja danych będzie znacznie szybsza, bo gdzie następnie mogą w pełni korzystać nowej architektury magazynu rozszerzonego po zakończeniu za pomocą grupy wierszy wysokiej jakości. 

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
