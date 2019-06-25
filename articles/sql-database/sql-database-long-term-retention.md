---
title: Store kopie zapasowe bazy danych SQL platformy Azure przez maksymalnie 10 lat | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Azure SQL Database obsługuje przechowywania tworzenia pełnych kopii zapasowych dla maksymalnie 10 lat.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 05/18/2019
ms.openlocfilehash: 6549892bfd04065bf83ab50fa5f5b439c35c4238
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190542"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Store kopie zapasowe bazy danych SQL platformy Azure przez maksymalnie 10 lat

Wiele aplikacji ma prawnych, zgodności lub innych firm do celów, które można przechowywać kopie zapasowe bazy danych dłużej niż 7 – 35 dni, dostarczone przez usługę Azure SQL Database wymagają [automatyczne tworzenie kopii zapasowych](sql-database-automated-backups.md). Funkcja długoterminowego przechowywania (od lewej do prawej), można przechowywać określonego SQL pełne kopie zapasowe bazy danych w [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) magazynu obiektów blob do 10 lat. Następnie możesz przywrócić dowolną kopię zapasową jako nową bazę danych.

> [!NOTE]
> Od lewej do prawej, można włączyć dla pojedynczych i puli baz danych. Go nie są jeszcze dostępne dla wystąpienia bazy danych w wystąpieniach zarządzanych. Można użyć zadania agenta programu SQL, aby zaplanować [kopie zapasowe bazy danych tylko do kopiowania](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) zamiast od lewej do prawej dłużej niż 35 dni.
> 

## <a name="how-sql-database-long-term-retention-works"></a>Jak działa długoterminowe przechowywanie bazy danych SQL

Długoterminowe przechowywanie kopii zapasowych (LTR) wykorzystuje pełne kopie zapasowe, które są [automatycznie utworzone](sql-database-automated-backups.md) włączyć punkt przywracania (Odzyskiwanie). Jeśli skonfigurowano zasadę od lewej do prawej, te kopie zapasowe są kopiowane do różnych obiektów blob w celu przechowywania długoterminowego. Operacja kopiowania jest zadanie w tle, które ma wpływ na wydajność na obciążenie bazy danych. Tworzenie kopii zapasowych LTR są zachowywane przez okres czasu ustawionego na podstawie zasad od lewej do prawej. Można również określić częstotliwość tworzenia kopii zapasowych LTR zasad pisowni LTR dla każdej bazy danych SQL. Aby umożliwić tej elastyczności można zdefiniować zasady przy użyciu kombinacji cztery parametry: tygodniowe przechowywanie kopii zapasowej (W) miesięczne przechowywanie kopii zapasowej (M), roczne przechowywanie kopii zapasowej (Y) i tydzień roku (WeekOfYear). W przypadku określenia W, jedna kopia zapasowa każdego tygodnia zostanie skopiowany do magazynu długoterminowego. Jeśli określisz M, jedna kopia zapasowa w pierwszym tygodniu każdego miesiąca zostaną skopiowane do magazynu długoterminowego. Jeśli określisz Y, jedna kopia zapasowa w ciągu tygodnia określony przez WeekOfYear zostaną skopiowane do magazynu długoterminowego. Każdej kopii zapasowej będą przechowywane w pamięci nieulotnej przez czas określony przez te parametry. Każda zmiana zasad pisowni LTR ma zastosowanie do przyszłych kopii zapasowych. Na przykład jeśli określony WeekOfYear znajduje się w przeszłości, gdy zasada jest skonfigurowana, pierwsza kopia zapasowa od lewej do prawej zostanie utworzony następny rok. 

Przykłady zasad od lewej do prawej:

-  W=0, M=0, Y=5, WeekOfYear=3

   Trzeci pełna kopia zapasowa każdego roku będą przechowywane przez pięć lat.
   
- W=0, M=3, Y=0

   Pierwsza pełna kopia zapasowa każdego miesiąca, będą przechowywane w ciągu trzech miesięcy.

- W=12, M=0, Y=0

   Każdy pełnej cotygodniowej kopii zapasowej zostaną zachowane dla 12 tygodni.

- W = 6, WeekOfYear M = 12, Y = 10 = 16

   Każdy pełnej cotygodniowej kopii zapasowej zostaną zachowane w sześć tygodni. Z wyjątkiem pierwsza pełna kopia zapasowa każdego miesiąca, której będą przechowywane przez 12 miesięcy. Oprócz pełnej kopii zapasowej, wykonywanych na 16 tydzień roku, której będą przechowywane przez okres 10 lat. 

W poniższej tabeli przedstawiono tempa i wygaśnięcia długoterminowe kopie zapasowe następujących zasad:

W = 12 tygodni (84 dni), M = rok (365 dni), Y = 10 lat (3650 dni), WeekOfYear = 15 (tydzień po 15 kwietnia)

   ![przykład od lewej do prawej](./media/sql-database-long-term-retention/ltr-example.png)



Jeśli zmodyfikujesz powyższych zasad, a zestaw W = 0 (nie cotygodniowych kopii zapasowych), tempo kopii zapasowych spowoduje to zmianę pokazano w powyższej tabeli przez wyróżnione daty. Wielkość magazynu potrzebnych do zapewnienia te kopie zapasowe zredukuje odpowiednio. 

> [!IMPORTANT]
> Chronometraż poszczególnych kopii zapasowych LTR jest kontrolowany przez usługę Azure SQL Database. Nie można ręcznie utworzyć kopię zapasową od lewej do prawej lub kontrolować termin tworzenia kopii zapasowej. Po skonfigurowaniu zasad od lewej do prawej, może potrwać maksymalnie 7 dni przed pierwszym kopii zapasowych LTR pojawią się na liście dostępnych kopii zapasowych.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Replikacja geograficzna i długoterminowego przechowywania kopii zapasowych

Jeśli używasz aktywnej replikacji geograficznej lub grupy trybu failover jako rozwiązania ciągłości biznesowej, należy przygotować się do ewentualnego przejścia w tryb failover i skonfigurować te same zasady LTR na pomocniczej geograficznej bazy danych. Nie spowoduje zwiększenia kosztów magazynu od lewej do prawej, jak kopie zapasowe nie są generowane na podstawie pomocnicze bazy danych. Tylko wtedy, gdy pomocnicza staje się podstawowym zostaną utworzone kopie zapasowe. Po wyzwoleniu przełączenie w tryb failover i podstawowy przechodzi do regionu pomocniczego, zapewnia Generowanie kopii zapasowych LTR — przerwana. 

> [!NOTE]
> Po odzyskaniu sprawności po awarii, które spowodowało przełączenie w tryb failover oryginalnej podstawowej bazy danych stanie się nowym serwerem pomocniczym. W związku z tym tworzenia kopii zapasowej nie zostanie wznowione, a istniejące zasady LTR nie zostanie zastosowana aż przyjmie postać podstawowego. 

## <a name="configure-long-term-backup-retention"></a>Konfigurowanie długoterminowego przechowywania kopii zapasowych

Aby dowiedzieć się, jak skonfigurować długoterminowe przechowywanie przy użyciu witryny Azure portal lub programu PowerShell, zobacz [długoterminowego przechowywania kopii zapasowych zarządzanie usługą Azure SQL Database](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Przywracanie bazy danych z kopii zapasowych LTR

Aby przywrócić bazę danych z magazynu od lewej do prawej, możesz wybrać określonej kopii zapasowej, oparte na jego sygnatury czasowej. Można przywrócić bazy danych do dowolnego istniejącego serwera w ramach tej samej subskrypcji co oryginalna baza danych. Aby dowiedzieć się, jak przywrócić bazę danych z kopii zapasowej od lewej do prawej, przy użyciu witryny Azure portal lub programu PowerShell, zobacz [długoterminowego przechowywania kopii zapasowych zarządzanie usługą Azure SQL Database](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Kolejne kroki

Ponieważ kopie zapasowe bazy danych ochronę danych przed przypadkowym uszkodzeniem lub usunięcia, są one integralną część wszelkie ciągłość prowadzenia działalności biznesowej i strategii odzyskiwania po awarii. Aby dowiedzieć się więcej o innych rozwiązaniach ciągłość prowadzenia działalności biznesowej bazy danych SQL, zobacz [omówienie ciągłości działania](sql-database-business-continuity.md).
