---
title: Uaktualnienie do najnowszej generacji
description: Uaktualnij pulę SQL usługi Azure Synapse Analytics do najnowszej generacji architektury sprzętu i magazynu platformy Azure.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 3299aa8ed85cff5c29d043d30aac08db45ffe5d4
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632270"
---
# <a name="optimize-performance-by-upgrading-azure-synapse-analytics-sql-pool"></a>Optymalizacja wydajności przez uaktualnienie puli SQL usługi Azure Synapse Analytics

Uaktualnij pulę SQL do najnowszej generacji architektury sprzętu i magazynu platformy Azure.

## <a name="why-upgrade"></a>Dlaczego warto uaktualnić?

Teraz można bezproblemowo uaktualnić do puli SQL obliczeń zoptymalizowane gen2 warstwy w witrynie Azure portal dla [obsługiwanych regionów](gen2-migration-schedule.md#automated-schedule-and-region-availability-table). Jeśli region nie obsługuje samodzielnego uaktualniania, można uaktualnić do obsługiwanego regionu lub czekać na samomodernacji, aby być dostępne w twoim regionie. Uaktualnij teraz, aby korzystać z najnowszej generacji sprzętu platformy Azure i ulepszonej architektury pamięci masowej, w tym szybszej wydajności, większej skalowalności i nieograniczonej przestrzeni dyskowej kolumnowej.

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

> [!IMPORTANT]
> To uaktualnienie dotyczy pul SQL warstwy gen1 zoptymalizowanej pod kątem obliczeń w [obsługiwanych regionach.](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)

## <a name="before-you-begin"></a>Przed rozpoczęciem

1. Sprawdź, czy twój [region](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) jest obsługiwany dla migracji GEN1 do GEN2. Zanotuj daty migracji automatycznej. Aby uniknąć konfliktów z procesem automatycznym, zaplanuj migrację ręczną przed datą rozpoczęcia procesu automatycznego.
2. Jeśli znajdujesz się w regionie, który nie jest jeszcze obsługiwany, kontynuuj sprawdzanie, czy twój region ma zostać dodany lub [uaktualniony przy użyciu przywracania](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) do obsługiwanego regionu.
3. Jeśli twój region jest obsługiwany, [uaktualnij za pośrednictwem witryny Azure portal](#upgrade-in-a-supported-region-using-the-azure-portal)
4. **Wybierz sugerowany poziom wydajności** dla puli SQL na podstawie bieżącego poziomu wydajności w warstwie Zoptymalizowana pod kątem obliczeń Gen1 przy użyciu poniższego mapowania:

   | Warstwa Zoptymalizowana do obliczeń Gen1 | Warstwa Zoptymalizowana do obliczeń Gen2 |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200 (dw200)            |           DW200c            |
   |            DW300 (dw300)            |           DW300c            |
   |            DW400 ( DW400 )            |           DW400c            |
   |            DW500            |           DW500c.            |
   |            DW600 ( DW600 )            |           DW500c.            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!NOTE]
> Sugerowane poziomy skuteczności nie są konwersją bezpośrednią. Na przykład zaleca się przejście z DW600 do DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Uaktualnianie w obsługiwanym regionie przy użyciu portalu Azure

- Migracja z gen1 do gen2 za pośrednictwem witryny Azure portal jest stała. Nie ma procesu powrotu do Gen1.
- Pula SQL musi być uruchomiona w celu migracji do gen2

### <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Zaloguj się do [Portalu Azure](https://portal.azure.com/).
- Upewnij się, że pula SQL jest uruchomiona — musi być migracja do gen2

### <a name="powershell-upgrade-commands"></a>Polecenia uaktualniania programu PowerShell

1. Jeśli pula SQL warstwy gen1 zoptymalizowana pod kątem obliczeń do uaktualnienia zostanie wstrzymana, [wznowić pulę SQL](pause-and-resume-compute-portal.md).

2. Przygotuj się na kilka minut przestoju.

3. Identyfikować wszelkie odwołania do kodu do obliczeń zoptymalizowane gen1 poziomów wydajności i zmodyfikować je do ich równoważnego poziomu wydajności zoptymalizowane pod kątem obliczeń Gen2. Poniżej znajdują się dwa przykłady, gdzie należy zaktualizować odwołania do kodu przed uaktualnieniem:

   Oryginalne polecenie Programu PowerShell w programie Gen1:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Zmodyfikowano do:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE]
   > -RequestedServiceObjectiveName "DW300" został zmieniony na - RequestedServiceObjectiveName "DW300**c"**
   >

   Oryginalne polecenie Gen1 T-SQL:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Zmodyfikowano do:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ;
   ```

   > [!NOTE]
   > SERVICE_OBJECTIVE = 'DW300' jest zmieniany na SERVICE_OBJECTIVE = 'DW300**c**'

## <a name="start-the-upgrade"></a>Rozpocznij uaktualnienie

1. Przejdź do puli SQL zoptymalizowanej pod kątem obliczeń Gen1 w witrynie Azure portal. Jeśli pula SQL warstwy gen1 zoptymalizowana pod kątem obliczeń do uaktualnienia zostanie wstrzymana, [wznowić pulę SQL](pause-and-resume-compute-portal.md).
2. Wybierz **pozycję Uaktualnij do** karty ![Gen2 na karcie Zadania: Upgrade_1](./media/upgrade-to-latest-generation/upgrade-to-gen2-1.png)

   > [!NOTE]
   > Jeśli karta **Uaktualnienie do gen2** nie jest widoczna na karcie Zadania, typ subskrypcji jest ograniczony w bieżącym regionie.
   > [Prześlij zgłoszenie pomocy technicznej,](sql-data-warehouse-get-started-create-support-ticket.md) aby uzyskać białą listę subskrypcji.

3. Przed uaktualnieniem upewnij się, że obciążenie zostało ukończone i zakończone. Będziesz doświadczać przestojów przez kilka minut, zanim pula SQL powróci do trybu online jako pula SQL warstwy gen2 zoptymalizowana pod kątem obliczeń. **Wybierz uaktualnienie:**

   ![Upgrade_2](./media/upgrade-to-latest-generation/upgrade-to-gen2-2.png)

4. **Monitoruj uaktualnienie,** sprawdzając stan w witrynie Azure portal:

   ![Ulepszenie3](./media/upgrade-to-latest-generation/upgrade-to-gen2-3.png)

   Pierwszy krok procesu uaktualniania przechodzi przez operację skalowania ("Uaktualnianie — offline"), gdzie wszystkie sesje zostaną zabite, a połączenia zostaną przerwane.

   Drugim etapem procesu uaktualniania jest migracja danych ("Uaktualnianie — online"). Migracja danych jest procesem w tle w tle. Ten proces powoli przenosi dane kolumnowe ze starej architektury magazynu do nowej architektury magazynu przy użyciu lokalnej pamięci podręcznej SSD. W tym czasie pula SQL będzie w trybie online do wykonywania zapytań i ładowania. Dane będą dostępne do kwerendy niezależnie od tego, czy zostały zmigrowane, czy nie. Migracja danych odbywa się z różnoliszowymi szybkościami w zależności od rozmiaru danych, poziomu wydajności i liczby segmentów magazynu kolumn.

5. **Zalecenie opcjonalne:** Po zakończeniu operacji skalowania można przyspieszyć proces migracji danych w tle. Można wymusić przenoszenie danych, uruchamiając [alter index odbudować](sql-data-warehouse-tables-index.md) na wszystkich tabelach magazynu kolumn podstawowych, które będą kwerendy w większym SLO i klasy zasobów. Ta operacja jest **w trybie offline** w porównaniu do procesu tła, który może potrwać wiele godzin, aby zakończyć w zależności od liczby i rozmiarów tabel. Jednak po zakończeniu migracji danych będzie znacznie szybsze ze względu na nową architekturę rozszerzonej pamięci masowej z wysokiej jakości grup wierszy.

> [!NOTE]
> Alter Index rebuild jest operacją w trybie offline i tabele nie będą dostępne, dopóki przebudowa nie zostanie zakończona.

Następująca kwerenda generuje wymagane polecenia przebudowywać alter index w celu przyspieszenia migracji danych:

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

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Uaktualnianie z regionu geograficznego platformy Azure przy użyciu przywracania za pośrednictwem witryny Azure portal

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Tworzenie punktu przywracania zdefiniowanego przez użytkownika przy użyciu portalu Azure

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

2. Przejdź do puli SQL, dla której chcesz utworzyć punkt przywracania.

3. U góry sekcji Przegląd wybierz pozycję **+Nowy punkt przywracania**.

    ![Nowy punkt przywracania](./media/upgrade-to-latest-generation/creating_restore_point_0.png)

4. Określ nazwę punktu przywracania.

    ![Nazwa punktu przywracania](./media/upgrade-to-latest-generation/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Przywracanie aktywnej lub wstrzymanej bazy danych przy użyciu witryny Azure Portal

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Przejdź do puli SQL, z której chcesz przywrócić.
3. U góry sekcji Przegląd wybierz pozycję **Przywróć**.

    ![ Omówienie przywracania kopii zapasowych](./media/upgrade-to-latest-generation/restoring_0.png)

4. Wybierz **punkty automatycznego przywracania** lub **zdefiniowane przez użytkownika punkty przywracania**. W przypadku punktów przywracania zdefiniowanych przez użytkownika **wybierz zdefiniowany przez użytkownika punkt przywracania** lub **Utwórz nowy punkt przywracania zdefiniowany przez użytkownika**. W przypadku serwera wybierz pozycję **Utwórz nowy** i wybierz serwer w obsługiwanym regionie geograficznym Gen2.

    ![Automatyczne punkty przywracania](./media/upgrade-to-latest-generation/restoring_1.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Przywracanie z regionu geograficznego platformy Azure przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby odzyskać bazę danych, należy użyć polecenia cmdlet [Restore-AzSqlDatabase.](/powershell/module/az.sql/restore-azsqldatabase)

> [!NOTE]
> Można wykonać geo-przywracanie do Gen2! W tym celu należy określić nazwę usługi Gen2 ServiceObjectiveName**c**(np.

1. Otwórz program Windows PowerShell.
2. Połącz się ze swoim kontem platformy Azure i wymień wszystkie subskrypcje skojarzone z Twoim kontem.
3. Wybierz subskrypcję zawierającą bazę danych, która ma zostać przywrócona.
4. Pobierz bazę danych, którą chcesz odzyskać.
5. Utwórz żądanie odzyskiwania dla bazy danych, określając nazwę usługi Gen2 ServiceObjectiveName.
6. Sprawdź stan bazy danych przywróconych geograficznie.

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
> Aby skonfigurować bazę danych po zakończeniu przywracania, zobacz [Konfigurowanie bazy danych po odzyskaniu](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

Odzyskana baza danych będzie włączona TDE, jeśli źródłowa baza danych jest włączona TDE.

Jeśli wystąpią jakiekolwiek problemy z puli SQL, utwórz [żądanie pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) i odwołanie "Gen2 upgrade" jako możliwą przyczynę.

## <a name="next-steps"></a>Następne kroki

Uaktualniona pula SQL jest w trybie online. Aby skorzystać z ulepszonej architektury, zobacz [Klasy zasobów do zarządzania obciążeniem](resource-classes-for-workload-management.md).
