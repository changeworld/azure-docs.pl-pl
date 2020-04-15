---
title: Długoterminowe przechowywanie kopii zapasowych
description: Dowiedz się, jak usługa Azure SQL Database obsługuje przechowywanie pełnych kopii zapasowych bazy danych przez okres do 10 lat za pośrednictwem długoterminowych zasad przechowywania.
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
ms.openlocfilehash: d015eea21bcfa499d6751e024a882a7316b7f1a5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380759"
---
# <a name="azure-sql-database-long-term-retention"></a>Długoterminowa retencja usługi Azure SQL Database

Wiele aplikacji ma cele regulacyjne, zgodne z przepisami lub inne cele biznesowe, które wymagają przechowywania kopii zapasowych bazy danych po 7-35 dniach dostarczonych przez [automatyczne kopie zapasowe](sql-database-automated-backups.md)usługi Azure SQL Database. Korzystając z funkcji długoterminowego przechowywania (LTR), można przechowywać określone bazy danych SQL pełne kopie zapasowe w magazynie obiektów Blob platformy Azure z dostępem do odczytu magazynu geograficznego nadmiarowego przez okres do 10 lat. Następnie można przywrócić dowolną kopię zapasową jako nową bazę danych. Aby uzyskać więcej informacji na temat nadmiarowości usługi Azure Storage, zobacz [Nadmiarowość usługi Azure Storage](../storage/common/storage-redundancy.md). 

Przechowywanie przez długi czas można włączyć dla pojedynczych i puli baz danych i jest w ograniczonej publicznej wersji zapoznawczej dla wystąpień zarządzanych usługi Azure SQL Database. 

> [!NOTE]
> Za pomocą zadań programu SQL Agent można zaplanować [tworzenie kopii zapasowych bazy danych tylko](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) do kopiowania jako alternatywę dla LTR po upływie 35 dni.


## <a name="how-sql-database-long-term-retention-works"></a>Jak działa długoterminowe przechowywanie bazy danych SQL Database

Długoterminowe przechowywanie kopii zapasowych (LTR) wykorzystuje pełne kopie zapasowe bazy danych, które są [tworzone automatycznie,](sql-database-automated-backups.md) aby włączyć przywracanie w czasie punktu (PITR). Jeśli skonfigurowano zasady LTR, te kopie zapasowe są kopiowane do różnych obiektów blob dla długoterminowego magazynu. Kopia jest zadanie w tle, które nie ma wpływu na wydajność obciążenia bazy danych. Zasady LTR dla każdej bazy danych SQL można również określić, jak często tworzone są kopie zapasowe LTR.

Aby włączyć funkcję LTR, można zdefiniować zasadę przy użyciu kombinacji czterech parametrów: tygodniowego przechowywania kopii zapasowych (W), miesięcznego przechowywania kopii zapasowych (M), rocznego przechowywania kopii zapasowych (Y) i tygodnia roku (WeekOfYear). Jeśli określisz W, jedna kopia zapasowa co tydzień zostanie skopiowana do magazynu długoterminowego. Jeśli określisz M, pierwsza kopia zapasowa każdego miesiąca zostanie skopiowana do magazynu długoterminowego. Jeśli określisz Y, jedna kopia zapasowa w ciągu tygodnia określonego przez WeekOfYear zostanie skopiowana do magazynu długoterminowego. Jeśli określony WeekOfYear jest w przeszłości, gdy zasady są skonfigurowane, pierwsza kopia zapasowa LTR zostanie utworzona w następnym roku. Każda kopia zapasowa będzie przechowywana w długoterminowym magazynie zgodnie z parametrami zasad, które są skonfigurowane podczas tworzenia kopii zapasowej LTR.

> [!NOTE]
> Wszelkie zmiany zasad LTR mają zastosowanie tylko do przyszłych kopii zapasowych. Na przykład jeśli tygodniowe przechowywanie kopii zapasowych (W), miesięczne przechowywanie kopii zapasowych (M) lub roczne przechowywanie kopii zapasowych (Y) zostanie zmodyfikowane, nowe ustawienie przechowywania będzie miało zastosowanie tylko do nowych kopii zapasowych. Przechowywanie istniejących kopii zapasowych nie zostanie zmodyfikowane. Jeśli twoim zamiarem jest usunięcie starych kopii zapasowych LTR przed upływem okresu przechowywania, należy [ręcznie usunąć kopie zapasowe](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups).
> 

Przykłady zasad LTR:

-  W=0, M=0, Y=5, WeekOfYear=3

   Trzecia pełna kopia zapasowa każdego roku będzie przechowywana przez pięć lat.
   
- W=0, M=3, Y=0

   Pierwsza pełna kopia zapasowa każdego miesiąca będzie przechowywana przez trzy miesiące.

- W=12, M=0, Y=0

   Każda cotygodniowa pełna kopia zapasowa będzie przechowywana przez 12 tygodni.

- W=6, M=12, Y=10, WeekOfYear=16

   Każda cotygodniowa pełna kopia zapasowa będzie przechowywana przez sześć tygodni. Z wyjątkiem pierwszej pełnej kopii zapasowej każdego miesiąca, która będzie przechowywana przez 12 miesięcy. Z wyjątkiem pełnej kopii zapasowej wykonanej 16 tydzień roku, która będzie przechowywana przez 10 lat. 

W poniższej tabeli przedstawiono rytm i wygaśnięcie długoterminowych kopii zapasowych dla następujących zasad:

W=12 tygodni (84 dni), M=12 miesięcy (365 dni), Y=10 lat (3650 dni), WeekOfYear=15 (tydzień po 15 kwietnia)

   ![przykład ltr](./media/sql-database-long-term-retention/ltr-example.png)



Jeśli zmodyfikujesz powyższe zasady i ustawisz W=0 (bez cotygodniowych kopii zapasowych), rytm kopii zapasowych zmieni się zgodnie z powyższą tabelą o podświetlone daty. Ilość magazynu potrzebna do przechowywania tych kopii zapasowych zmniejszyłaby się odpowiednio. 

> [!IMPORTANT]
> Czas poszczególnych kopii zapasowych LTR jest kontrolowany przez usługę Azure SQL Database. Nie można ręcznie utworzyć kopii zapasowej LTR ani kontrolować czasu tworzenia kopii zapasowej. Po skonfigurowaniu zasad LTR może upłynąć do 7 dni, zanim pierwsza kopia zapasowa LTR pojawi się na liście dostępnych kopii zapasowych.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Replikacja geograficzna i długoterminowe przechowywanie kopii zapasowych

Jeśli używasz aktywnej replikacji geograficznej lub grup pracy awaryjnej jako rozwiązania ciągłości biznesowej, należy przygotować się na ewentualne tryb failover i skonfigurować te same zasady LTR w bazie danych geo pomocniczej. Koszt magazynu LTR nie wzrośnie, ponieważ kopie zapasowe nie są generowane z pomocniczych. Tylko wtedy, gdy pomocniczy staje się podstawowym kopie zapasowe zostaną utworzone. Zapewnia nie przerwane generowanie kopii zapasowych LTR po wyzwoleniu pracy awaryjnej i przeniesienie podstawowej do regionu pomocniczego. 

> [!NOTE]
> Gdy oryginalna podstawowa baza danych odzyskuje z awarii, która spowodowała przełącze w błąd, stanie się nowym pomocniczym. W związku z tym tworzenie kopii zapasowej nie zostanie wznowione i istniejące zasady LTR nie zacznie obowiązywać, dopóki nie stanie się podstawowym ponownie. 

## <a name="managed-instance-support"></a>Obsługa wystąpienia zarządzanego

Korzystanie z długoterminowego przechowywania kopii zapasowych za pomocą wystąpień zarządzanych usługi Azure SQL Database ma następujące ograniczenia:

- **Ograniczona publiczna wersja zapoznawcza** — ta wersja zapoznawcza jest dostępna tylko dla subskrypcji EA i CSP i jest zależna od ograniczonej dostępności.  
- [**Tylko program PowerShell**](sql-database-managed-instance-long-term-backup-retention-configure.md) — obecnie nie ma obsługi portalu Azure. LTR musi być włączony przy użyciu programu PowerShell. 

Aby zażądać rejestracji, utwórz [bilet pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/) w temacie pomocy technicznej Kopia **zapasowa, przywracanie i ciągłość działania / Długoterminowe przechowywanie kopii zapasowych**.


## <a name="configure-long-term-backup-retention"></a>Konfigurowanie długoterminowego przechowywania kopii zapasowych

Aby dowiedzieć się, jak skonfigurować długoterminowe przechowywanie przy użyciu portalu Azure lub programu PowerShell, zobacz [Zarządzanie długoterminową przechowywaniem kopii zapasowych usługi Azure SQL Database.](sql-database-long-term-backup-retention-configure.md)

## <a name="restore-database-from-ltr-backup"></a>Przywracanie bazy danych z kopii zapasowej LTR

Aby przywrócić bazę danych z magazynu LTR, można wybrać określoną kopię zapasową na podstawie jej sygnatury czasowej. Bazy danych można przywrócić do dowolnego istniejącego serwera w ramach tej samej subskrypcji jako oryginalnej bazy danych. Aby dowiedzieć się, jak przywrócić bazę danych z kopii zapasowej LTR przy użyciu portalu Azure lub programu PowerShell, zobacz [Zarządzanie długoterminową przechowywaniem kopii zapasowych usługi Azure SQL Database.](sql-database-long-term-backup-retention-configure.md)

## <a name="next-steps"></a>Następne kroki

Ponieważ kopie zapasowe bazy danych chronią dane przed przypadkowym uszkodzeniem lub usunięciem, są one istotną częścią każdej strategii ciągłości biznesowej i odzyskiwania po awarii. Aby dowiedzieć się więcej o innych rozwiązaniach ciągłości biznesowej bazy danych SQL, zobacz [Omówienie ciągłości biznesowej](sql-database-business-continuity.md).
