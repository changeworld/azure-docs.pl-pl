---
title: Przechowywanie Azure SQL Database kopii zapasowych przez maksymalnie 10 lat
description: Dowiedz się, jak Azure SQL Database obsługuje przechowywanie pełnych kopii zapasowych bazy danych przez maksymalnie 10 lat.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: 5d6f0797802a622ada1916752bc35c1bae2cde9f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689518"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Przechowywanie Azure SQL Database kopii zapasowych przez maksymalnie 10 lat

Wiele aplikacji ma przepisy prawne, zgodność lub inne cele biznesowe, które wymagają zachowania kopii zapasowych bazy danych ponad 7-35 dni udostępnionych przez Azure SQL Database [Automatyczne kopie zapasowe](sql-database-automated-backups.md). Za pomocą funkcji długoterminowego przechowywania (LTR) można przechowywać określone kopie zapasowe bazy danych SQL w usłudze [GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) BLOB Storage przez maksymalnie 10 lat. Następnie możesz przywrócić dowolną kopię zapasową jako nową bazę danych.

> [!NOTE]
> Można włączyć funkcję LTR dla jednej bazy danych i puli. Nie jest jeszcze dostępna dla baz danych wystąpień w zarządzanych wystąpieniach. Zadań programu SQL Agent można użyć do zaplanowania [kopii zapasowych bazy danych tylko do kopiowania](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) jako alternatywy dla listy odwołującej więcej niż 35 dni.
> 

## <a name="how-sql-database-long-term-retention-works"></a>Jak działa długoterminowe przechowywanie danych SQL Database

Długoterminowe przechowywanie kopii zapasowych (LTR) wykorzystuje pełne kopie zapasowe bazy danych, które są [tworzone automatycznie](sql-database-automated-backups.md) , aby włączyć przywracanie do punktu w czasie (kopie). Jeśli skonfigurowano zasady LTR, te kopie zapasowe są kopiowane do różnych obiektów BLOB w celu przechowywania długoterminowego. Operacja kopiowania to zadanie w tle, które nie ma wpływu na wydajność bazy danych. Kopie zapasowe LTR są zachowywane przez pewien czas ustawiony przez zasady LTR. Zasady LTR dla każdej bazy danych SQL mogą również określać częstotliwość tworzenia kopii zapasowych LTR. Aby zapewnić elastyczność, można zdefiniować zasady przy użyciu kombinacji czterech parametrów: cotygodniowe przechowywanie kopii zapasowych (W), miesięczne przechowywanie kopii zapasowych (M), roczne przechowywanie kopii zapasowych (Y) i tydzień roku (WeekOfYear). Jeśli określisz W, jedna kopia zapasowa co tydzień zostanie skopiowana do magazynu długoterminowego. Jeśli określisz pozycję M, jedna kopia zapasowa w pierwszym tygodniu każdego miesiąca zostanie skopiowana do magazynu długoterminowego. Jeśli określisz wartość Y, jedna kopia zapasowa w tygodniu określona przez WeekOfYear zostanie skopiowana do magazynu długoterminowego. Poszczególne kopie zapasowe będą przechowywane w magazynie długoterminowym przez okres określony przez te parametry. Wszelkie zmiany zasad odliczenia odnoszą się do przyszłych kopii zapasowych. Na przykład jeśli określona WeekOfYear jest w przeszłości podczas konfigurowania zasad, pierwsza kopia zapasowa LTR zostanie utworzona w następnym roku. 

Przykłady zasad LTR:

-  W = 0, M = 0, Y = 5, WeekOfYear = 3

   Trzecia pełna kopia zapasowa każdego roku będzie utrzymywana przez pięć lat.
   
- W = 0, M = 3, Y = 0

   Pierwsza pełna kopia zapasowa każdego miesiąca będzie utrzymywana przez trzy miesiące.

- W = 12, M = 0, Y = 0

   Każde cotygodniowe pełne kopie zapasowe będą przechowywane przez 12 tygodni.

- W = 6, M = 12, Y = 10, WeekOfYear = 16

   Każde cotygodniowe pełne kopie zapasowe będą przechowywane przez sześć tygodni. Oprócz pierwszej pełnej kopii zapasowej każdego miesiąca, która będzie przechowywana przez 12 miesięcy. Z wyjątkiem pełnej kopii zapasowej wykonanej w szesnastym tygodniu roku, która będzie przechowywana przez 10 lat. 

W poniższej tabeli przedstawiono erze i okres ważności długoterminowych kopii zapasowych dla następujących zasad:

W = 12 tygodni (84 dni), M = 12 miesięcy (365 dni), Y = 10 lat (3650 dni), WeekOfYear = 15 (tydzień po 15 kwietnia)

   ![przykład ltr](./media/sql-database-long-term-retention/ltr-example.png)



Jeśli zmodyfikujesz powyższe zasady i ustawisz wartość z = 0 (bez cotygodniowych kopii zapasowych), erze kopii zapasowych zmienią się tak, jak pokazano w powyższej tabeli według wyróżnionych dat. Ilość miejsca do magazynowania wymagana do zachowania tych kopii zapasowych zostanie odpowiednio zredukowana. 

> [!IMPORTANT]
> Chronometraż poszczególnych kopii zapasowych w odróżnieniu jest kontrolowany przez Azure SQL Database. Nie można ręcznie utworzyć kopii zapasowej LTR ani kontrolować chronometrażu tworzenia kopii zapasowej. Po skonfigurowaniu zasad LTR może upłynąć do 7 dni, zanim pierwsza kopia zapasowa LTR zostanie wyświetlona na liście dostępnych kopii zapasowych.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Replikacja geograficzna i długoterminowe przechowywanie kopii zapasowych

Jeśli używasz aktywnej replikacji geograficznej lub grup trybu failover jako rozwiązania do ciągłej ciągłości biznesowej, należy przygotować się do przejścia do trybu failover i skonfigurować te same zasady LTR w pomocniczej bazie danych geograficznych. Koszt magazynu LTR nie zostanie zwiększony, ponieważ kopie zapasowe nie są generowane na podstawie serwerów pomocniczych. Tylko wtedy, gdy pomocniczy staną się podstawowe, tworzone są kopie zapasowe. Zapewnia to nieprzerwaną generację kopii zapasowych w przypadku wyzwolenia trybu failover, a podstawowy przenosi do regionu pomocniczego. 

> [!NOTE]
> Gdy pierwotna podstawowa baza danych odzyska się z awarii, która spowodowała przejście w tryb failover, stanie się nowym serwerem pomocniczym. W związku z tym tworzenie kopii zapasowej nie zostanie wznowione, a istniejące zasady LTR nie zaczną obowiązywać, dopóki nie staną się pierwotne. 

## <a name="configure-long-term-backup-retention"></a>Konfigurowanie długoterminowego przechowywania kopii zapasowych

Aby dowiedzieć się, jak skonfigurować przechowywanie długoterminowe przy użyciu Azure Portal lub programu PowerShell, zobacz [Zarządzanie usługą Azure SQL Database długoterminowe przechowywanie kopii zapasowych](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Przywracanie bazy danych z kopii zapasowej LTR

Aby przywrócić bazę danych z magazynu LTR, można wybrać określoną kopię zapasową w oparciu o sygnaturę czasową. Bazę danych można przywrócić na dowolnym istniejącym serwerze w ramach tej samej subskrypcji, w której znajduje się oryginalna baza danych. Aby dowiedzieć się, jak przywrócić bazę danych z kopii zapasowej LTR przy użyciu Azure Portal lub programu PowerShell, zobacz [Manage Azure SQL Database długoterminowe przechowywanie kopii zapasowych](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Następne kroki

Ponieważ kopie zapasowe bazy danych chronią dane przed przypadkowym uszkodzeniem lub usunięciem, są one istotną częścią strategii ciągłości działania i odzyskiwania po awarii. Aby dowiedzieć się więcej na temat innych SQL Database rozwiązań zapewniających ciągłość działania firmy, zobacz temat [ciągłość](sql-database-business-continuity.md)działania — Omówienie.
