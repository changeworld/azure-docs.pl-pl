---
title: Uaktualnianie do najnowszej generacji usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Uaktualnij usługi Azure SQL Data Warehouse do najnowszej generacji architektura sprzętu i magazynowania na platformie Azure.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.openlocfilehash: a8bd260db7a141ce845ce7fb5b7e10f642907b82
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60310375"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Optymalizowanie wydajności przez zmianę warstwy dla usługi SQL Data Warehouse

Uaktualnij usługi Azure SQL Data Warehouse do najnowszej generacji architektura sprzętu i magazynowania na platformie Azure.

## <a name="why-upgrade"></a>Dlaczego warto wykonać uaktualnienie?

Możesz teraz rozpocząć płynnie do warstwy SQL dane magazynu obliczenia zoptymalizowane pod kątem Gen2 w witrynie Azure portal, aby uzyskać [obsługiwane regiony](gen2-migration-schedule.md#automated-schedule-and-region-availability-table). Jeśli swój region nie obsługuje uaktualniania samodzielnie, można uaktualnić do obsługiwany region lub poczekaj, aż własnym uaktualnienie ma być dostępny w Twoim regionie. Uaktualnij teraz, aby móc korzystać z najnowszej generacji sprzętowych platformy Azure i architektury magazynu rozszerzonego, tym szybciej, skalowalności i nieograniczony magazyn kolumnowy. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>Stosuje się do

To uaktualnienie dotyczy magazyny danych warstwy obliczenia zoptymalizowane pod kątem Gen1 w [obsługiwane regiony](gen2-migration-schedule.md#automated-schedule-and-region-availability-table).

## <a name="before-you-begin"></a>Przed rozpoczęciem

1. Sprawdź, czy Twoje [region](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) jest obsługiwana w przypadku GEN1 GEN2 migracji. Należy pamiętać, daty automatyczną migrację. W celu uniknięcia konfliktów z zautomatyzowanego procesu, należy zaplanować migrację ręcznie przed datą rozpoczęcia zautomatyzowanego procesu.
2. Jeśli jesteś w regionie, który nie jest jeszcze obsługiwany w dalszym ciągu Sprawdź w Twoim regionie, które mają zostać dodane lub [uaktualnienia przy użyciu funkcji przywracania](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) na obsługiwany region.
3. Jeśli swój region jest obsługiwany, [uaktualnienia w witrynie Azure portal](#upgrade-in-a-supported-region-using-the-azure-portal)
4. **Wybierz poziom wydajności sugerowane** dla magazynu danych na podstawie Twój bieżący poziom wydajności w warstwie obliczenia zoptymalizowane pod kątem Gen1 przy użyciu mapowania poniżej:

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

> [!Note]
> Poziomy wydajności sugerowane nie są to bezpośrednia konwersji. Na przykład zalecamy przejście od wartości DW600 DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Po uaktualnieniu w obsługiwanym regionie przy użyciu witryny Azure portal

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> Migracja z Gen1 Gen2 za pośrednictwem witryny Azure portal jest trwały. Nie jest to proces, wracając do Gen1.  

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Jeśli w magazynie danych warstwy obliczeń Gen1 zoptymalizowane pod kątem uaktualnienia jest wstrzymana, [hurtowni danych wznowić](pause-and-resume-compute-portal.md).

   > [!NOTE]
   > Usługa Azure SQL Data Warehouse musi być uruchomiona migrację do Gen2.

2. Należy przygotować za kilka minut przestoju. 

3. Zidentyfikować wszelkie odwołania do kodu do obliczenia zoptymalizowane pod kątem Gen1 poziomów wydajności i zmodyfikuj je do ich równoważne poziomu wydajności obliczenia zoptymalizowane pod kątem Gen2. Poniżej przedstawiono dwa przykłady, o których należy zaktualizować odwołania do kodu przed uaktualnieniem:

   Oryginalne polecenie Gen1 programu PowerShell:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Zmodyfikowane w celu:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
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

1. Przejdź do obliczenia zoptymalizowane pod kątem Gen1 warstwy magazynu danych w witrynie Azure portal. Jeśli w magazynie danych warstwy obliczeń Gen1 zoptymalizowane pod kątem uaktualnienia jest wstrzymana, [hurtowni danych wznowić](pause-and-resume-compute-portal.md). 
2. Wybierz **uaktualnienie do Gen2** karty na karcie zadania:  ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)
    
    > [!NOTE]
    > Jeśli nie widzisz **uaktualnienie do Gen2** karty, na karcie zadania, do typu Twojej subskrypcji jest ograniczona w bieżącym regionie.
    > [Wyślij bilet pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) można pobrać listy dozwolonych Twojej subskrypcji.

3. Upewnij się, że obciążenie zostało zakończone przed uaktualnieniem uruchomiona i w stanie spoczynku. Kilka minut, zanim magazyn danych jest online jako magazyn danych warstwy obliczenia zoptymalizowane pod kątem Gen2 będzie środowisko przestojów. **Wybierz opcję uaktualnienia**:

   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Monitorowanie uaktualnienia** , sprawdzając jego stan w witrynie Azure portal:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)

   Pierwszym krokiem procesu uaktualniania przechodzi przez operację skalowania ("Uaktualnianie — tryb Offline") gdzie wszystkie sesje zostaną skasowane i zostanie porzucony połączeń. 

   Drugi etap procesu uaktualniania jest migracja danych ("Uaktualnianie — Tryb Online"). Migracja danych jest strumieniem online proces w tle. Ten proces powoli przesuwa dokumentowe ze starego architektury magazynu do nowej architektury magazynu przy użyciu lokalnej pamięci podręcznej dysków SSD. W tym czasie magazyn danych będzie w trybie online wykonywania zapytań i ładowania. Twoje dane będą dostępne do wykonywania zapytań, niezależnie od tego, czy ma została zmigrowana, czy nie. Migracja danych odbywa się według stawek różne w zależności od tego, czy rozmiar danych, poziom wydajności i liczbę segmentów magazynu kolumn. 

5. **Zalecenie opcjonalne:** Po zakończeniu operacji skalowania można przyspieszyć proces migracji danych w tle. Można wymusić przenoszenia danych, uruchamiając [Alter Index rebuild](sql-data-warehouse-tables-index.md) dla wszystkich tabel magazynu kolumn głównej będzie wykonywana kwerenda na większą klasę poziomu usługi i zasobów. Ta operacja jest **offline** w porównaniu do strumieniem proces w tle, co może zająć godzin w zależności od liczby i rozmiarów tabel. Jednak po zakończeniu migracji danych będzie znacznie szybsza, bo ze względu na nowej architekturze magazyn rozszerzony za pomocą grupy wierszy wysokiej jakości.
 
> [!NOTE]
> ALTER Index rebuild jest operacją w trybie offline i tabele nie będą dostępne do momentu ukończenia ponownej kompilacji.

Następujące zapytanie generuje wymagane polecenia Alter Index Rebuild przyspiesza migrację danych:

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

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Uaktualnienie z regionu geograficznego platformy Azure przy użyciu funkcji przywracania w witrynie Azure portal

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Utwórz punkt przywracania zdefiniowanych przez użytkownika przy użyciu witryny Azure portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Przejdź do magazynu danych SQL, który chcesz utworzyć punkt przywracania dla.

3. W górnej sekcji Przegląd, wybierz **+ nowy punkt przywracania**.

    ![Nowy punkt przywracania](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)

4. Określ nazwę dla tego punktu przywracania.

    ![Nazwa punktu przywracania](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Przywracanie aktywnych lub wstrzymania bazy danych przy użyciu witryny Azure portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do magazynu danych SQL, który chcesz przywrócić z.
3. W górnej sekcji Przegląd, wybierz **przywrócić**.

    ![ Omówienie przywracania kopii zapasowych](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)

4. Wybierz opcję **punkty przywracania na automatyczne** lub **zdefiniowanych przez użytkownika punktów przywracania**.

    ![Punkty przywracania na automatyczny](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)

5. Dla punktów przywracania User-defined **wybierz punkt przywracania** lub **utworzyć nowy punkt przywracania użytkownika**. Wybierz serwer w Gen2 obsługiwanym regionie geograficznym. 

    ![Punkty przywracania zdefiniowanych przez użytkownika](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Przywróć z regionu geograficznego platformy Azure przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby odzyskać bazę danych, należy użyć [AzSqlDatabase przywracania](/powershell/module/az.sql/restore-azsqldatabase) polecenia cmdlet.

> [!NOTE]
> Można przeprowadzić przywracania geograficznego, aby Gen2! Aby to zrobić, należy określić ServiceObjectiveName Gen2 (np. DW1000**c**) jako parametr opcjonalny.

1. Otwórz program Windows PowerShell.
2. Nawiąż połączenie z kontem platformy Azure i wyświetlanie listy wszystkich subskrypcji skojarzonych z Twoim kontem.
3. Wybierz subskrypcję, która zawiera bazę danych do przywrócenia.
4. Pobierz bazę danych, którą chcesz odzyskać.
5. Utwórz żądanie odzyskiwania bazy danych, określając Gen2 ServiceObjectiveName.
6. Sprawdź stan bazy danych, przywrócić geograficznie.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Aby skonfigurować bazę danych, po ukończeniu przywracania, zobacz [konfiguracji bazy danych po odzyskaniu](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

Odzyskanej bazy danych będzie włączona funkcja TDE źródłowa baza danych jest włączona funkcja TDE.


Jeśli napotkasz jakiekolwiek problemy z magazynem danych, Utwórz [żądania pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) i odwoływać się do "Gen2 uaktualnienia" jako możliwa przyczyna.

## <a name="next-steps"></a>Kolejne kroki

Magazyn danych uaktualnionego jest w trybie online. Aby móc korzystać z rozszerzonych architektury, zobacz [klasy zasobów do zarządzania obciążeniem](resource-classes-for-workload-management.md).
