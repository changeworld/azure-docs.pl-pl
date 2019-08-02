---
title: Przywracanie bazy danych Azure SQL Database z kopii zapasowej | Microsoft Docs
description: Informacje o przywracaniu do określonego momentu, które umożliwiają wycofanie Azure SQL Database do wcześniejszego punktu w czasie (do 35 dni).
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 04/30/2019
ms.openlocfilehash: 55d60ec332515fcfa3deb565a4a770027681537a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566981"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Odzyskiwanie bazy danych Azure SQL Database przy użyciu zautomatyzowanych kopii zapasowych bazy danych

Domyślnie kopie zapasowe SQL Database są przechowywane w replikowanym geograficznie magazynie obiektów BLOB (RA-GRS). Następujące opcje są dostępne do odzyskiwania bazy danych przy użyciu [zautomatyzowanych kopii zapasowych bazy danych](sql-database-automated-backups.md):

- Utwórz nową bazę danych na tym samym serwerze SQL Database odzyskanym do określonego punktu w czasie w okresie przechowywania.
- Utwórz bazę danych na tym samym serwerze SQL Database odzyskany do czasu usunięcia usuniętej bazy danych.
- Utwórz nową bazę danych na dowolnym serwerze SQL Database w tym samym regionie odzyskanym do punktu najnowszych kopii zapasowych.
- Utwórz nową bazę danych na dowolnym serwerze SQL Database w dowolnym innym regionie odzyskiwanym do punktu najnowszych zreplikowanych kopii zapasowych.

Jeśli skonfigurowano [długoterminowe przechowywanie kopii zapasowych](sql-database-long-term-retention.md), można także utworzyć nową bazę danych z dowolnej kopii zapasowej na dowolnym serwerze SQL Database.

> [!IMPORTANT]
> Nie można zastąpić istniejącej bazy danych podczas przywracania.

W przypadku korzystania z warstwy usługi w warstwie Standardowa lub Premium przywrócona baza danych wiąże się z dodatkowym kosztem magazynu w następujących warunkach:

- Przywracanie P11 – P15 do S4-S12 lub P1 – P6, jeśli maksymalny rozmiar bazy danych jest większy niż 500 GB.
- Przywrócenie P1 – P6 do S4-S12, jeśli maksymalny rozmiar bazy danych jest większy niż 250 GB.

Dodatkowy koszt jest icurred, gdy maksymalny rozmiar przywróconej bazy danych jest większy niż ilość miejsca do magazynowania dołączona do warstwy usługi i poziomu wydajności docelowej bazy danych. Dodatkowy magazyn, który został zainicjowany powyżej uwzględnionej kwoty, jest obciążany opłatami dodatkowymi. Szczegóły cennika dodatkowego magazynu znajdują się na [stronie cennika SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Jeśli rzeczywista ilość zajętego miejsca jest mniejsza niż ilość dostępnego magazynu, możesz uniknąć tego dodatkowego kosztu, ustawiając wartość w polu Maksymalny rozmiar bazy danych na uwzględnioną kwotę.

> [!NOTE]
> [Automatyczne kopie zapasowe bazy danych](sql-database-automated-backups.md) są używane podczas tworzenia [kopii bazy danych](sql-database-copy.md).

## <a name="recovery-time"></a>Czas odzyskiwania

Czas odzyskiwania służący do przywracania bazy danych przy użyciu zautomatyzowanych kopii zapasowych bazy danych ma wpływ kilka czynników:

- Rozmiar bazy danych.
- Rozmiar obliczeń bazy danych
- Liczba użytych dzienników transakcji
- Ilość działań, które muszą być odtwarzane w celu odzyskania do punktu przywracania
- Przepustowość sieci, jeśli przywracanie jest w innym regionie
- Liczba współbieżnych żądań przywrócenia przetwarzanych w regionie docelowym

W przypadku dużej i/lub bardzo aktywnej bazy danych Przywracanie może potrwać kilka godzin. Jeśli w regionie występuje długotrwała awaria, istnieje możliwość, że wiele żądań przywracania geograficznego jest przetwarzanych przez inne regiony. W przypadku wielu żądań czas odzyskiwania może wzrosnąć w przypadku baz danych w tym regionie. Większość operacji przywracania bazy danych jest przeprowadzana w czasie krótszym niż 12 godzin.

W przypadku pojedynczej subskrypcji istnieją ograniczenia liczby współbieżnych żądań przywracania.  Te ograniczenia mają zastosowanie do dowolnej kombinacji przywracania do punktu w czasie, a następnie przywracania geograficznego i przywracania z kopii zapasowej przechowywania długoterminowego.

| | **Maksymalna liczba przetwarzanych żądań współbieżnych** | **Maksymalna liczba przesyłanych żądań współbieżnych** |
| :--- | --: | --: |
|Pojedyncza baza danych (na subskrypcję)|10|60|
|Pula elastyczna (na pulę)|4|200|
||||

Obecnie nie ma wbudowanej metody przywracania całego serwera. [Azure SQL Database: Pełny skrypt odzyskiwania](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) serwera to przykład, w jaki sposób można wykonać to zadanie.

> [!IMPORTANT]
> Aby odzyskać przy użyciu zautomatyzowanych kopii zapasowych, musisz być członkiem roli współautor SQL Server w subskrypcji lub być właścicielem subskrypcji — zobacz [RBAC: Built-in roles](../role-based-access-control/built-in-roles.md) (Kontrola dostępu oparta na rolach: role wbudowane). Odzyskiwanie można przeprowadzić za pomocą witryny Azure Portal, programu PowerShell lub interfejsu API REST. Nie można używać języka Transact-SQL.

## <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

Do wcześniejszego punktu w czasie za pomocą Azure Portal, [programu PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databases)można przywrócić autonomiczną bazę danych w puli lub wystąpieniu. Żądanie może określać dowolną warstwę usług lub rozmiar obliczeń dla przywróconej bazy danych. Upewnij się, że na serwerze, na którym jest przywracana baza danych, jest wystarczająca ilość zasobów. Po zakończeniu Nowa baza danych zostanie utworzona na tym samym serwerze, na którym znajduje się oryginalna baza danych. Przywrócona baza danych będzie naliczana według normalnych stawek na podstawie jej warstwy usług i rozmiaru obliczeniowego. Nie są naliczane opłaty do momentu zakończenia przywracania bazy danych.

Na ogół przywracana jest baza danych do wcześniejszego punktu na potrzeby odzyskiwania. Przywróconą bazę danych można traktować jako zamiennik do oryginalnej bazy danych lub użyć jej jako źródła danych, aby zaktualizować oryginalną bazę danych.

- **Zastąpienie bazy danych**

  Jeśli przywrócona baza danych ma zastąpić oryginalną bazę danych, należy określić rozmiar i warstwę usługi oryginalnej bazy danych. Następnie można zmienić nazwę oryginalnej bazy danych i nadać przywróconą bazę danych oryginalną nazwę za pomocą polecenia [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) w języku T-SQL.

- **Odzyskiwanie danych**

  Jeśli planujesz pobrać dane z przywróconej bazy danych w celu odzyskania sprawności po błędzie użytkownika lub aplikacji, musisz napisać i wykonać skrypt odzyskiwania danych, który wyodrębnia dane z przywróconej bazy danych i ma zastosowanie do oryginalnej bazy danych. Mimo że operacja przywracania może zająć dużo czasu, przywracanie bazy danych jest widoczne na liście baz danych w ramach procesu przywracania. Jeśli baza danych zostanie usunięta podczas przywracania, operacja przywracania zostanie anulowana i nie zostanie naliczona opłata za bazę danych, która nie ukończyła przywracania.

Aby odzyskać pojedynczą, pulę lub wystąpienie bazy danych do punktu w czasie za pomocą Azure Portal, Otwórz stronę bazy danych i kliknij przycisk **Przywróć** na pasku narzędzi.

![Przywracanie do punktu w czasie](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> Aby programowo przywrócić bazę danych z kopii zapasowej, zobacz programowe [wykonywanie odzyskiwania przy użyciu zautomatyzowanych kopii zapasowych](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="deleted-database-restore"></a>Przywracanie usuniętej bazy danych

Usuniętą bazę danych można przywrócić do czasu usunięcia lub do wcześniejszego punktu w czasie na tym samym serwerze SQL Database przy użyciu Azure Portal, [programu PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)lub [REST (createmode = Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Usuniętą [bazę danych można przywrócić w wystąpieniu zarządzanym przy użyciu programu PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). 

> [!TIP]
> Przykładowy skrypt programu PowerShell przedstawiający sposób przywracania usuniętej bazy danych znajduje się w temacie [przywracanie bazy danych SQL przy użyciu programu PowerShell](scripts/sql-database-restore-database-powershell.md).
> [!IMPORTANT]
> W przypadku usunięcia wystąpienia serwera Azure SQL Database wszystkie jego bazy danych również zostaną usunięte i nie będzie można go odzyskać. Obecnie nie jest obsługiwane przywracanie usuniętego serwera.

### <a name="deleted-database-restore-using-the-azure-portal"></a>Przywracanie bazy danych zostało usunięte przy użyciu Azure Portal

Aby odzyskać usuniętą bazę danych przy użyciu Azure Portal, Otwórz stronę serwera i w obszarze operacje kliknij pozycję **usunięte bazy danych**.

![usunięte-Database-Restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![usunięte-Database-Restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> Aby programowo przywrócić usuniętą bazę danych, zobacz programowe [wykonywanie odzyskiwania przy użyciu zautomatyzowanych kopii zapasowych](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="geo-restore"></a>Przywracanie geograficzne

Bazę danych SQL można przywrócić na dowolnym serwerze w dowolnym regionie świadczenia usługi Azure z najnowszych kopii zapasowych replikowanych geograficznie. Przywracanie geograficzne używa kopii zapasowej replikowanej geograficznie jako źródła. Można je żądać nawet wtedy, gdy baza danych lub Datacenter są niedostępne z powodu przestoju.

Przywracanie geograficzne jest domyślną opcją odzyskiwania, gdy baza danych jest niedostępna z powodu zdarzenia w regionie hostingu. Bazę danych można przywrócić na serwerze w dowolnym innym regionie. Istnieje opóźnienie między wykonaniem kopii zapasowej a replikacją geograficzną do obiektu blob platformy Azure w innym regionie. W związku z tym przywrócona baza danych może należeć do jednej godziny w bazie danych orignal. Na poniższej ilustracji przedstawiono przywracanie bazy danych z ostatniej dostępnej kopii zapasowej w innym regionie.

![Przywracanie geograficzne](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Przykładowy skrypt programu PowerShell przedstawiający sposób wykonywania przywracania geograficznego znajduje się w sekcji [przywracanie bazy danych SQL przy użyciu programu PowerShell](scripts/sql-database-restore-database-powershell.md).

Przywracanie do punktu w czasie na poziomie geograficznym nie jest obecnie obsługiwane. Przywracanie do punktu w czasie można wykonać tylko w podstawowej bazie danych. Aby uzyskać szczegółowe informacje o używaniu przywracania geograficznego w celu odzyskania sprawności przed awarią, zobacz [odzyskiwanie po awarii](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Przywracanie geograficzne to najbardziej podstawowe rozwiązanie do odzyskiwania po awarii dostępne w SQL Database. Polega ona na automatycznym tworzeniu kopii zapasowych replikowanych geograficznie z celem punktu odzyskiwania = 1 godzina i szacowanym czasie odzyskiwania wynoszącym maksymalnie 12 godzin. Nie gwarantuje to, że region docelowy będzie miał zdolność do przywracania baz danych po ourage regionalnym, ponieważ prawdopodobnie zostanie osiągnięty gwałtowny wzrost popytu. W przypadku aplikacji niekrytycznych dla firm korzystających z stosunkowo małych baz danych Przywracanie geograficzne jest odpowiednim rozwiązaniem odzyskiwania po awarii. W przypadku aplikacji o znaczeniu krytycznym, które korzystają z dużych baz danych i muszą zapewnić ciągłość działania, należy używać [grup autotrybu failover](sql-database-auto-failover-group.md). Oferuje znacznie mniejszy cel punktu odzyskiwania i RTO, a pojemność jest zawsze gwarantowana. Aby uzyskać więcej informacji o opcjach ciągłości biznesowej, zobacz [Omówienie ciągłości działania](sql-database-business-continuity.md).

### <a name="geo-restore-using-the-azure-portal"></a>Przywracanie geograficzne przy użyciu Azure Portal

Aby przywrócić bazę danych do lokalizacji geograficznej w ramach [okresu przechowywania modelu opartego na](sql-database-service-tiers-dtu.md) jednostkach DTU lub [okresu przechowywania modelu opartego na rdzeń wirtualny](sql-database-service-tiers-vcore.md) przy użyciu Azure Portal, Otwórz stronę bazy danych SQL, a następnie kliknij przycisk **Dodaj**. W polu tekstowym **Wybierz źródło** wybierz pozycję **kopia zapasowa**. Określ kopię zapasową, z której ma zostać wykonane odzyskiwanie w regionie i na wybranym przez siebie serwerze.

> [!Note]
> Przywracanie geograficzne przy użyciu Azure Portal nie jest dostępne w wystąpieniu zarządzanym. Zamiast tego użyj programu PowerShell.

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Programowe wykonywanie odzyskiwania przy użyciu zautomatyzowanych kopii zapasowych

Jak opisano wcześniej, oprócz Azure Portal, odzyskiwanie bazy danych można wykonywać programowo przy użyciu Azure PowerShell lub interfejsu API REST. W poniższych tabelach opisano zestaw dostępnych poleceń.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

- Aby przywrócić bazę danych autonomiczną lub w puli, zobacz [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Polecenia cmdlet | Opis |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Pobiera co najmniej jedną bazę danych. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Pobiera usuniętą bazę danych, którą można przywrócić. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Pobiera geograficznie nadmiarową kopię zapasową bazy danych. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Przywraca bazę danych SQL. |

  > [!TIP]
  > Przykładowy skrypt programu PowerShell przedstawiający sposób wykonywania przywracania bazy danych do punktu w czasie, patrz [przywracanie bazy danych SQL przy użyciu programu PowerShell](scripts/sql-database-restore-database-powershell.md).

- Aby przywrócić bazę danych wystąpienia zarządzanego, zobacz [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Polecenia cmdlet | Opis |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Pobiera co najmniej jedno wystąpienie zarządzane. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Pobiera bazy danych wystąpienia. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Przywraca bazę danych wystąpienia. |

### <a name="rest-api"></a>Interfejs API REST

Aby przywrócić pojedynczą lub w puli bazę danych przy użyciu interfejsu API REST:

| interfejs API | Opis |
| --- | --- |
| [REST (createmode = Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Przywraca bazę danych |
| [Pobieranie lub aktualizowanie stanu bazy danych](https://docs.microsoft.com/rest/api/sql/operations) |Zwraca stan podczas operacji przywracania |

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

- Aby przywrócić pojedynczą lub w puli bazę danych przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [AZ SQL DB Restore](/cli/azure/sql/db#az-sql-db-restore).
- Aby przywrócić wystąpienie zarządzane przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [AZ SQL MidB Restore](/cli/azure/sql/midb#az-sql-midb-restore)

## <a name="summary"></a>Podsumowanie

Automatyczne kopie zapasowe chronią bazy danych przed błędami użytkowników i aplikacji, przypadkowym usunięciem bazy danych i długotrwałym przestojem. Ta wbudowana funkcja jest dostępna dla wszystkich warstw usług i rozmiarów obliczeniowych.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem i scenariuszami ciągłości działania, zobacz temat ciągłość działania [— Omówienie](sql-database-business-continuity.md).
- Aby dowiedzieć się więcej o Azure SQL Database zautomatyzowanych kopii zapasowych, zobacz [SQL Database zautomatyzowane kopie zapasowe](sql-database-automated-backups.md).
- Aby dowiedzieć się więcej na temat przechowywania długoterminowego, zobacz [długoterminowe przechowywanie](sql-database-long-term-retention.md)danych.
- Aby dowiedzieć się więcej na temat szybszych opcji odzyskiwania, zobacz [aktywne replikacje geograficzne](sql-database-active-geo-replication.md) lub [grupy autopracy awaryjnej](sql-database-auto-failover-group.md).
