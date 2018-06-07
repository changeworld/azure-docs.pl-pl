---
title: Przechowywanie kopii zapasowych bazy danych SQL Azure przez maksymalnie 10 lat | Dokumentacja firmy Microsoft
description: Dowiedz się, jak baza danych SQL Azure obsługuje przechowywania kopii zapasowych pełnej bazy danych do 10 lat.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: b2f3c454ba84c7b892096cc42dcbe2706ab6159f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648296"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Przechowywanie kopii zapasowych bazy danych SQL Azure przez maksymalnie 10 lat

Wiele aplikacji ma przepisów prawnych, zgodności lub innych firm celów, które wymagają przechowywania kopii zapasowych bazy danych poza 7 35 dni pochodzącymi z bazy danych SQL Azure [automatycznego tworzenia kopii zapasowych](sql-database-automated-backups.md). Dzięki funkcji długoterminowego przechowywania (od lewej do prawej), można przechowywać określony SQL pełne kopie zapasowe bazy danych w [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) magazynu obiektów blob do 10 lat. Jakiejkolwiek kopii zapasowej można przywrócić jako nową bazę danych.

> [!IMPORTANT]
> Długoterminowego przechowywania jest obecnie w przeglądzie. Istniejące kopie zapasowe przechowywane w magazynie usługi Azure Recovery Services jako część poprzedniej wersji zapoznawczej tej funkcji są migrowane do magazynu SQL Azure.<!-- and available in the following regions: Australia East, Australia Southeast, Brazil South, Central US, East Asia, East US, East US 2, India Central, India South, Japan East, Japan West, North Central US, North Europe, South Central US, Southeast Asia, West Europe, and West US.-->
>

## <a name="how-sql-database-long-term-retention-works"></a>Jak działa długoterminowego przechowywania bazy danych SQL

Wykorzystuje długoterminowego przechowywania kopii zapasowych [automatycznego tworzenia kopii zapasowej bazy danych SQL](sql-database-automated-backups.md) utworzone przyjmuje do chwili przywrócenia (PITR). Można skonfigurować zasady przechowywania długoterminowego dla każdej bazy danych SQL i określ, jak często należy skopiować kopii zapasowych do magazynu długoterminowego. Aby umożliwić tym elastyczność można zdefiniować zasady przy użyciu kombinacji cztery parametry: przechowywania kopii zapasowych co tydzień (W), miesięczne przechowywania kopii zapasowych (M), corocznych przechowywania kopii zapasowych (Y) i tydzień roku (WeekOfYear). Jeśli określisz W, jedna kopia zapasowa co tydzień będą kopiowane do magazynu długoterminowego. Jeśli określisz M jedną kopię zapasową w pierwszym tygodniu każdego miesiąca będą kopiowane do magazynu długoterminowego. Jeśli określisz Y jedną kopię zapasową w tygodniu, określony przez WeekOfYear zostaną skopiowane do magazynu długoterminowego. Każdej kopii zapasowej będą przechowywane w pamięci nieulotnej przez czas określony przez te parametry. 

Przykłady:

-  W=0, M=0, Y=5, WeekOfYear=3

   3 pełna kopia zapasowa każdego roku zostaną zachowane dla 5 lat.

- W=0, M=3, Y=0

   Pierwsza pełna kopia zapasowa każdego miesiąca będą przechowywane przez 3 miesiące.

- W=12, M=0, Y=0

   Każdy co tydzień pełnej kopii zapasowej zostaną zachowane dla 12 tygodni.

- W = 6, M = 12, Y = 10 WeekOfYear = 16

   Każdy co tydzień pełnej kopii zapasowej zostaną zachowane dla 6 tygodni. Z wyjątkiem pierwsza pełna kopia zapasowa każdego miesiąca, które będą przechowywane przez 12 miesięcy. Z wyjątkiem pełną kopię zapasową wykonaną w 16 tydzień roku, które będą znajdować się na 10 lat. 

W poniższej tabeli przedstawiono okresach i wygaśnięcia długoterminowe kopie zapasowe następujących zasad:

W = 12 tygodni (84 dni), M = 12 miesięcy (365 dni), Y = 10 lat (3650 dni), WeekOfYear = 15 (tydzień po 15 kwietnia)

   ![przykład od lewej do prawej](./media/sql-database-long-term-retention/ltr-example.png)


 
Jeśli można zmodyfikować zasady powyżej i zestawu W = 0 (nie cotygodniowe kopie zapasowe), okresach kopii zapasowych zmieniłby jako przedstawiono w powyższej tabeli dat zaznaczony. W związku z tym ograniczy wielkość pamięci masowej niezbędne, aby zachować te kopie zapasowe. 

> [!NOTE]
1. Kopie od lewej do prawej są tworzone przez usługę Azure storage, więc proces kopiowania nie ma wpływu wydajności na istniejącej bazy danych.
2. Stosuje zasady do kolejnych kopii zapasowych. Na przykład Jeśli jest określony WeekOfYear w przeszłości, gdy zasada jest skonfigurowana, pierwszy od lewej do prawej zostanie utworzona kopia następnym roku. 
3. Aby przywrócić bazę danych z magazynu od lewej do prawej, można wybrać określonego oparte na jego sygnatura czasowa kopii zapasowej.   Można przywrócić bazy danych do dowolnego istniejącego serwera w tej samej subskrypcji co oryginalnej bazy danych. 
> 

## <a name="configure-long-term-backup-retention"></a>Konfigurowanie długoterminowego przechowywania kopii zapasowych

Aby dowiedzieć się, jak skonfigurować długoterminowego przechowywania przy użyciu portalu Azure lub programu PowerShell, zobacz [skonfigurować długoterminowego przechowywania kopii zapasowych](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Kolejne kroki

Ponieważ kopie zapasowe bazy danych ochronę danych przed przypadkowym uszkodzenia lub usunięcia, są one integralną część wszelkie ciągłość prowadzenia działalności biznesowej i strategii odzyskiwania po awarii. Aby dowiedzieć się więcej o innych rozwiązań ciągłość prowadzenia działalności biznesowej bazy danych SQL, zobacz [omówienie ciągłości działalności biznesowej](sql-database-business-continuity.md).
