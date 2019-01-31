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
ms.date: 01/25/2019
ms.openlocfilehash: 3d6010df64c4e3c75bd05e2eb9828c07cf3fb342
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471389"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Store kopie zapasowe bazy danych SQL platformy Azure przez maksymalnie 10 lat

Wiele aplikacji ma prawnych, zgodności lub innych firm do celów, które można przechowywać kopie zapasowe bazy danych dłużej niż 7 – 35 dni, dostarczone przez usługę Azure SQL Database wymagają [automatyczne tworzenie kopii zapasowych](sql-database-automated-backups.md). Funkcja długoterminowego przechowywania (od lewej do prawej), można przechowywać określonego SQL pełne kopie zapasowe bazy danych w [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) magazynu obiektów blob do 10 lat. Jakiejkolwiek kopii zapasowej można przywrócić jako nową bazę danych.

> [!NOTE]
> Od lewej do prawej, które mogą być włączone dla autonomicznych i puli baz danych. Go nie są jeszcze dostępne dla wystąpienia bazy danych w wystąpieniach zarządzanych. Można użyć zadania agenta programu SQL, aby zaplanować [kopie zapasowe bazy danych tylko do kopiowania](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) zamiast od lewej do prawej dłużej niż 35 dni.
> 

## <a name="how-sql-database-long-term-retention-works"></a>Jak działa długoterminowe przechowywanie bazy danych SQL

Długoterminowe przechowywanie kopii zapasowych (LTR) wykorzystuje pełne kopie zapasowe, które są [automatycznie utworzone](sql-database-automated-backups.md) włączyć punkt przywracania (Odzyskiwanie). Te kopie zapasowe są kopiowane do innego magazynu obiektów blob, jeśli skonfigurowano zasady od lewej do prawej.
Można skonfigurować zasady LTR dla każdej bazy danych SQL i określ, jak często należy skopiować kopie zapasowe w obiektach blob magazynu długoterminowego. Aby umożliwić tej elastyczności można zdefiniować zasady przy użyciu kombinacji cztery parametry: tygodniowe przechowywanie kopii zapasowej (W) miesięczne przechowywanie kopii zapasowej (M), roczne przechowywanie kopii zapasowej (Y) i tydzień roku (WeekOfYear). W przypadku określenia W, jedna kopia zapasowa każdego tygodnia zostanie skopiowany do magazynu długoterminowego. Jeśli określisz M, jedna kopia zapasowa w pierwszym tygodniu każdego miesiąca zostaną skopiowane do magazynu długoterminowego. Jeśli określisz Y, jedna kopia zapasowa w ciągu tygodnia określony przez WeekOfYear zostaną skopiowane do magazynu długoterminowego. Każdej kopii zapasowej będą przechowywane w pamięci nieulotnej przez czas określony przez te parametry. 

Przykłady:

-  W=0, M=0, Y=5, WeekOfYear=3

   3 pełna kopia zapasowa każdego roku będą przechowywane przez 5 lat.
- W=0, M=3, Y=0

   Pierwsza pełna kopia zapasowa każdego miesiąca, będą przechowywane w ciągu 3 miesięcy.

- W=12, M=0, Y=0

   Każdy pełnej cotygodniowej kopii zapasowej zostaną zachowane dla 12 tygodni.

- W = 6, WeekOfYear M = 12, Y = 10 = 16

   Każdy pełnej cotygodniowej kopii zapasowej zostaną zachowane dla 6 tygodni. Z wyjątkiem pierwsza pełna kopia zapasowa każdego miesiąca, której będą przechowywane przez 12 miesięcy. Oprócz pełnej kopii zapasowej, wykonywanych na 16 tydzień roku, której będą przechowywane przez okres 10 lat. 

W poniższej tabeli przedstawiono tempa i wygaśnięcia długoterminowe kopie zapasowe następujących zasad:

W = 12 tygodni (84 dni), M = rok (365 dni), Y = 10 lat (3650 dni), WeekOfYear = 15 (tydzień po 15 kwietnia)

   ![przykład od lewej do prawej](./media/sql-database-long-term-retention/ltr-example.png)


 
Jeśli zostały do modyfikowania powyższych zasad, a zestaw W = 0 (nie cotygodniowych kopii zapasowych), tempo kopii zapasowych zmienią się jako przedstawione w powyższej tabeli przez wyróżnione daty. Wielkość magazynu potrzebnych do zapewnienia te kopie zapasowe zredukuje odpowiednio. 

> [!NOTE]
1. Kopiuje od lewej do prawej są tworzone przez usługę Azure storage, więc proces kopiowania nie wpływu na wydajność w istniejącej bazie danych.
2. Zasada ma zastosowanie do przyszłych kopii zapasowych. Na przykład Jeśli określony WeekOfYear znajduje się w przeszłości, gdy zasada jest skonfigurowana, pierwsza kopia zapasowa od lewej do prawej zostanie utworzony następny rok. 
3. Aby przywrócić bazę danych z magazynu od lewej do prawej, możesz wybrać określonej kopii zapasowej, oparte na jego sygnatury czasowej.   Można przywrócić bazy danych do dowolnego istniejącego serwera w ramach tej samej subskrypcji co oryginalna baza danych. 
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Replikacja geograficzna i długoterminowego przechowywania kopii zapasowych

Jeśli używasz grup usługi active replikacji geograficznej i trybu failover jako rozwiązania ciągłości biznesowej, należy przygotować się do ewentualnego przejścia w tryb failover i skonfigurować te same zasady LTR na pomocniczej geograficznej bazy danych. To nie spowoduje zwiększenia kosztów magazynu od lewej do prawej zgodnie z kopii zapasowych nie są generowane na podstawie pomocnicze bazy danych. Tylko wtedy, gdy pomocnicza staje się podstawowym zostaną utworzone kopie zapasowe. W ten sposób, w przypadku wyzwolenia trybu failover gwarantuje-przerwane generowania kopii zapasowych LTR podstawowego powoduje przeniesienie do regionu pomocniczego. 

> [!NOTE]
Po odzyskaniu sprawności po awarii, powodującą, że jej w tryb failover oryginalnej podstawowej bazy danych stanie się nowym serwerem pomocniczym. W związku z tym tworzenia kopii zapasowej nie zostanie wznowione, a istniejące zasady LTR nie zostanie zastosowana aż przyjmie postać podstawowego. 
> 

## <a name="configure-long-term-backup-retention"></a>Konfigurowanie długoterminowego przechowywania kopii zapasowych

Aby dowiedzieć się, jak skonfigurować długoterminowe przechowywanie w witrynie Azure portal lub przy użyciu programu PowerShell, zobacz [Konfigurowanie długoterminowego przechowywania kopii zapasowych](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Kolejne kroki

Ponieważ kopie zapasowe bazy danych ochronę danych przed przypadkowym uszkodzeniem lub usunięcia, są one integralną część wszelkie ciągłość prowadzenia działalności biznesowej i strategii odzyskiwania po awarii. Aby dowiedzieć się więcej o innych rozwiązaniach ciągłość prowadzenia działalności biznesowej bazy danych SQL, zobacz [omówienie ciągłości działania](sql-database-business-continuity.md).
