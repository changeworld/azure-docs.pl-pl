---
title: Przywróć bazę danych Azure SQL database z kopii zapasowej | Dokumentacja firmy Microsoft
description: Więcej informacji na temat przywracania w momencie, która umożliwia przywracanie usługi Azure SQL Database do wcześniejszego punktu w czasie (do 35 dni).
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
ms.date: 04/30/2019
ms.openlocfilehash: 47bf59adb33f3685b31430c652b31880d383833e
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232649"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Odzyskiwanie bazy danych Azure SQL za pomocą bazy danych automatycznych kopii zapasowych

Domyślnie kopie zapasowe bazy danych SQL są przechowywane w georeplikowanym magazynie obiektów blob (RA-GRS). Poniższe opcje są dostępne dla bazy danych odzyskiwania przy użyciu [automatyczne kopie zapasowe bazy danych](sql-database-automated-backups.md):

- Utwórz nową bazę danych na tym samym serwerze bazy danych SQL odzyskiwane do określonego punktu w czasie w okresie przechowywania.
- Tworzenie bazy danych na tym samym serwerze bazy danych SQL odzyskiwane do czasu usunięcia dla usuniętej bazy danych.
- Utwórz nową bazę danych na dowolnym serwerze bazy danych SQL, w tym samym regionie przywrócona do stanu najnowszej kopii zapasowych.
- Utwórz nową bazę danych na dowolnym serwerze bazy danych SQL w innym regionie przywrócona do stanu najnowszych replikowane kopie zapasowe.

Jeśli skonfigurowano [kopii zapasowych, długoterminowe przechowywanie](sql-database-long-term-retention.md), można również utworzyć nową bazę danych z dowolnej kopii zapasowej od lewej do prawej, na dowolnym serwerze bazy danych SQL.

> [!IMPORTANT]
> Nie można zastąpić istniejącą bazę danych podczas przywracania.

Korzystając z warstwy usługi Standard lub Premium, przywróconej bazy danych wiąże się koszt dodatkowego magazynu w następujących warunkach:

- Przywracanie P11 – P15 S4-S12 lub P1 – P6, jeśli maksymalnego rozmiaru bazy danych jest większa niż 500 GB.
- Przywracanie P1 – P6 do S4-S12, jeśli maksymalnego rozmiaru bazy danych jest większa niż 250 GB.

Dodatkowy koszt jest icurred, gdy maksymalny rozmiar przywróconej bazy danych jest większe niż wielkość magazynu oferowanego w pakiecie z docelowej bazy danych usługi warstwy i poziomu wydajności. Dodatkowego magazynu aprowizowanego ponad uwzględnioną kwotę jest rozliczany według dodatkowych. Aby uzyskać szczegóły dodatkowego magazynu, zobacz [SQL Database, cennik](https://azure.microsoft.com/pricing/details/sql-database/). Rzeczywista ilość zajętego miejsca jest mniejsza niż wielkość magazynu oferowanego w pakiecie, można uniknąć, to dodatkowych kosztów, ustawiając rozmiar maksymalny bazy danych uwzględnioną kwotę.

> [!NOTE]
> [Automatyczne kopie zapasowe bazy danych](sql-database-automated-backups.md) są używane podczas tworzenia [kopiowanie bazy danych](sql-database-copy.md).

## <a name="recovery-time"></a>Godzina odzyskiwania

Czas odzyskiwania, aby przywrócić bazę danych przy użyciu kopii zapasowych automatycznych bazy danych ma wpływ na kilka czynników:

- Rozmiar bazy danych
- Rozmiar obliczeń bazy danych
- Liczba zaangażowanych dzienniki transakcji
- Zmniejszenia liczby działań, który ma być powtórzone odzyskiwanie do punktu przywracania
- Przepustowość sieci, jeśli przywracania jest w innym regionie
- Liczba żądań współbieżnych przywracania przetwarzanych w regionie docelowym

Dla dużych i/lub bardzo aktywni bazy danych przywracania może zająć kilka godzin. Jeśli region jest długotrwały Przestój, jest możliwe, że istnieją dużą liczbę żądań przywracania geograficznego przetwarzanych przez inne regiony. W przypadku dużej liczby żądań, czas odzyskiwania może się zwiększyć dla baz danych, w tym regionie. Większość bazy danych przywraca pełną w mniej niż 12 godzin.

W przypadku pojedynczej subskrypcji istnieją ograniczenia dotyczące liczby żądań współbieżnych przywracania.  Te ograniczenia dotyczą dowolnej kombinacji punktu w czasie, przywracanie, przywracanie geograficzne i przywraca z długoterminowe przechowywanie kopii zapasowej):

| | **Maksymalna liczba jednoczesnych żądań przetwarzanych** | **Maksymalna liczba jednoczesnych żądań przesyłane** |
| :--- | --: | --: |
|Pojedynczą bazę danych (na subskrypcję)|10|60|
|Puli elastycznej (na pulę)|4|200|
||||

Obecnie nie istnieje wbudowana metoda Przywracanie całego serwera. [Usługi Azure SQL Database: Pełne odzyskiwanie serwera](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) skrypt znajduje się przykład jak można wykonać tego zadania.

> [!IMPORTANT]
> Aby odzyskać za pomocą automatycznych kopii zapasowych, musi być członkiem roli Współautor serwera SQL Server w ramach subskrypcji lub być właścicielem subskrypcji — zobacz [RBAC: Built-in roles](../role-based-access-control/built-in-roles.md) (Kontrola dostępu oparta na rolach: role wbudowane). Odzyskiwanie można przeprowadzić za pomocą witryny Azure Portal, programu PowerShell lub interfejsu API REST. Nie można używać języka Transact-SQL.

## <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

Możesz przywracać autonomicznej puli lub wystąpienie bazy danych do wcześniejszego punktu w czasie za pomocą witryny Azure portal [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase), lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databases). Żądania można określić dowolną warstwę usługi lub obliczenia rozmiaru dla przywróconej bazy danych. Upewnij się, że masz wystarczającą liczbą zasobów na serwerze, na którym odbywa się przywracanie bazy danych. Po wykonaniu tych czynności, na tym samym serwerze co oryginalna baza danych zostanie utworzona nowa baza danych. Przywrócona baza danych jest naliczana normalna stawka na podstawie jego warstwy usług i rozmiaru obliczeń. Do czasu ukończenia przywracania bazy danych nie są naliczane opłaty.

Ogólnie przywrócić bazę danych do wcześniejszego punktu w celach recovery. Można traktować przywróconej bazy danych jako mogą zastąpić oryginalnej bazy danych lub go użyć jako źródła danych do aktualizacji oryginalnej bazy danych.

- **Zastępowania bazy danych**

  Przywrócona baza danych jest przeznaczony jako zamiennika dla oryginalnej bazy danych, należy określić rozmiar obliczeń orinal bazy danych i warstwy usług. Można zmienić nazwy oryginalnej bazy danych i zapewniają przywróconej bazy danych do oryginalnej nazwy przy użyciu [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) polecenia języka T-SQL.

- **Odzyskiwanie danych**

  Jeśli planowane jest do pobierania danych z przywróconej bazy danych, aby odzyskać sprawność po błędzie użytkownika lub aplikacji, musisz zapisu i wykonywania skryptu odzyskiwania danych, wyodrębnia dane z przywróconej bazy danych, która ma zastosowanie do oryginalnej bazy danych. Mimo, że operacja przywracania może zająć dużo czasu, przywracania bazy danych jest widoczny na liście baz danych w całym procesie przywracania. Jeśli usuniesz bazę danych podczas przywracania operacji przywracania zostanie anulowane i nie opłata wyniesie bazą danych, która nie została ukończona, przywracania.

Do odzyskania pojedynczej puli lub wystąpienie bazy danych do punktu w czasie za pomocą witryny Azure portal, otwórz stronę bazy danych i kliknij przycisk **przywrócić** na pasku narzędzi.

![punkt w czasie przywracania](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> Programowe Przywracanie bazy danych z kopii zapasowej, zobacz [programowe wykonywanie odzyskiwania za pomocą automatycznych kopii zapasowych](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="deleted-database-restore"></a>Przywracanie usuniętej bazy danych

Można przywrócić usuniętą bazę danych do czasu usunięcia lub wcześniejszego punktu w czasie na tym samym serwerze bazy danych SQL przy użyciu portalu Azure [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase), lub [REST (createMode = przywracanie)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Możesz [Przywracanie usuniętej bazy danych w wystąpieniu zarządzanym przy użyciu programu PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). 

> [!TIP]
> Aby uzyskać przykładowy skrypt programu PowerShell przedstawiająca sposób przywrócić usuniętą bazę danych, zobacz [przywrócić bazę danych SQL przy użyciu programu PowerShell](scripts/sql-database-restore-database-powershell.md).
> [!IMPORTANT]
> Jeśli usuniesz wystąpienia serwera usługi Azure SQL Database, jego baz danych również zostaną usunięte i nie można go odzyskać. Obecnie nie jest obsługiwane dla przywracanie usuniętych serwera.

### <a name="deleted-database-restore-using-the-azure-portal"></a>Przywracanie usuniętej bazy danych przy użyciu witryny Azure portal

Aby odzyskać usuniętą bazę danych przy użyciu witryny Azure portal, otwórz stronę na serwerze i w obszarze operacji, kliknij przycisk **usuniętych baz danych**.

![usunięte database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> Aby programowo przywrócić usuniętą bazę danych, zobacz [programowe wykonywanie odzyskiwania za pomocą automatycznych kopii zapasowych](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="geo-restore"></a>Przywracanie geograficzne

Możesz przywrócić bazę danych SQL na dowolnym serwerze w dowolnym regionie systemu Azure z najnowszej kopii zapasowych replikacją geograficzną. Funkcja przywracania geograficznego używa replikacji geograficznej kopii zapasowej jako źródła. Można żądać, nawet jeśli bazy danych lub centrum danych jest niedostępna z powodu awarii.

Funkcja przywracania geograficznego jest domyślną opcję odzyskiwania, gdy baza danych jest niedostępna z powodu zdarzenia w regionie hostingu. Możesz przywrócić bazy danych na serwerze w każdym innym regionie. Występuje opóźnienie między podczas tworzenia kopii zapasowej i jego replikacją geograficzną na platformie Azure blob w innym regionie. W rezultacie przywróconej bazy danych może być maksymalnie jedną godzinę za orignal bazy danych. Poniższa ilustracja przedstawia przywracania bazy danych z ostatnią dostępną kopią zapasową w innym regionie.

![Funkcja przywracania geograficznego](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Aby uzyskać przykładowy skrypt programu PowerShell przedstawiająca sposób wykonać przywracanie geograficzne, zobacz [przywrócić bazę danych SQL przy użyciu programu PowerShell](scripts/sql-database-restore-database-powershell.md).

W momencie przywracania na pomocnicza geograficzna nie jest obecnie obsługiwane. Przywracanie do punktu w czasie jest możliwe tylko w głównej bazie danych. Aby uzyskać szczegółowe informacje na temat przy użyciu przywracania geograficznego odzyskiwania sprawności po awarii, zobacz [odzyskiwanie sprawności po awarii](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Funkcja przywracania geograficznego jest najbardziej podstawowym rozwiązanie odzyskiwania po awarii dostępne w usłudze SQL Database. Opiera się na tworzone automatycznie kopii zapasowych z replikacją geograficzną za pomocą cel punktu odzyskiwania = 1 godziny i szacowany czas odzyskiwania do 12 godzin. Gwarantuje to, że region docelowy pojemności przywrócić baz danych po regionalnym ourage, ponieważ prawdopodobnie gwałtownego wzrostu zapotrzebowania. Dla firm innych niż aplikacja o krytycznym znaczeniu korzystanie z baz danych stosunkowo mały funkcja przywracania geograficznego jest rozwiązanie do odzyskiwania po awarii odpowiednie. Firmowe kluczowych aplikacji używanie dużych baz danych, które należy zapewnić ciągłość biznesową, należy użyć [automatyczny tryb failover grupy](sql-database-auto-failover-group.md). Oferuje znacznie niższe RPO i RTO i pojemność zawsze jest gwarantowana. Aby uzyskać więcej informacji na temat opcji ciągłości biznesowej, zobacz [Przegląd ciągłości](sql-database-business-continuity.md).

### <a name="geo-restore-using-the-azure-portal"></a>Funkcja przywracania geograficznego w witrynie Azure portal

Do przywracania geograficznego w bazie danych podczas jego [okres przechowywania modelu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) lub [okres przechowywania model oparty na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md) przy użyciu witryny Azure portal, otwórz stronę bazy danych SQL, a następnie kliknij przycisk **Dodaj** . W **wybierz źródło** pola tekstowego, wybierz **kopii zapasowej**. Określ kopię zapasową, z którego można przeprowadzić operację odzyskiwania, w regionie, jak i na serwerze wybranym.

> [!Note]
> Funkcja przywracania geograficznego w witrynie Azure portal nie jest dostępny w wystąpieniu zarządzanym. Zamiast tego użyj programu PowerShell.

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Programowe odzyskiwanie systemu za pomocą automatycznych kopii zapasowych

Jak już wspomniano, oprócz witryny Azure portal odzyskiwanie bazy danych można wykonać programowo przy użyciu programu Azure PowerShell lub interfejsu API REST. W poniższych tabelach opisano zestaw poleceń dostępnych.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

- Aby przywrócić autonomiczny lub baza danych w puli, zobacz [AzSqlDatabase przywracania](/powershell/module/az.sql/restore-azsqldatabase).

  | Polecenie cmdlet | Opis |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Pobiera co najmniej jedną bazę danych. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Pobiera usuniętą bazę danych, którą można przywrócić. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Pobiera geograficznie nadmiarową kopię zapasową bazy danych. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Przywraca bazę danych SQL. |

  > [!TIP]
  > Aby uzyskać przykładowy skrypt programu PowerShell przedstawiający sposób wykonywania w momencie przywracania bazy danych, zobacz [przywrócić bazę danych SQL przy użyciu programu PowerShell](scripts/sql-database-restore-database-powershell.md).

- Aby przywrócić bazę danych wystąpienia zarządzanego, zobacz [AzSqlInstanceDatabase przywracania](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Polecenie cmdlet | Opis |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Pobiera co najmniej jednego wystąpienia zarządzanego. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Pobiera wystąpienie bazy danych. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Przywraca bazę danych wystąpienia. |

### <a name="rest-api"></a>Interfejs API REST

Aby przywrócić bazę pojedynczego lub w puli przy użyciu interfejsu API REST:

| Interfejs API | Opis |
| --- | --- |
| [REST (createMode = odzyskiwanie)](https://docs.microsoft.com/rest/api/sql/databases) |Przywraca bazę danych |
| [Pobierz Utwórz lub zaktualizuj stan bazy danych](https://docs.microsoft.com/rest/api/sql/operations) |Zwraca stan podczas operacji przywracania |

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

- Aby przywrócić bazę pojedynczego lub w puli przy użyciu wiersza polecenia platformy Azure, zobacz [az sql db restore](/cli/azure/sql/db#az-sql-db-restore).
- Aby przywrócić wystąpienia zarządzanego przy użyciu wiersza polecenia platformy Azure, zobacz [az sql fragment przywracania](/cli/azure/sql/midb#az-sql-midb-restore)

## <a name="summary"></a>Podsumowanie

Automatyczne kopie zapasowe chronić baz danych użytkownika i błędy aplikacji, usuwać przypadkowym bazy danych, lub długotrwały przestojów. Ta wbudowana funkcja jest dostępna dla wszystkich warstw usług i rozmiarów wystąpień obliczeniowych.

## <a name="next-steps"></a>Kolejne kroki

- Omówienie ciągłości działania i scenariuszach można znaleźć [omówienie ciągłości działania](sql-database-business-continuity.md).
- Aby dowiedzieć się więcej na temat usługi Azure SQL Database automatyczne kopie zapasowe, zobacz [bazy danych SQL, automatyczne kopie zapasowe](sql-database-automated-backups.md).
- Aby dowiedzieć się więcej o długoterminowym przechowywaniu, zobacz [długoterminowego przechowywania](sql-database-long-term-retention.md).
- Aby dowiedzieć się o szybsze opcje odzyskiwania, zobacz [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md) lub [automatyczny tryb failover grupy](sql-database-auto-failover-group.md).
