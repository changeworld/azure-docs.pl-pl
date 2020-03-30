---
title: Przywracanie bazy danych z kopii zapasowej
description: Dowiedz się więcej o przywracaniu punktu w czasie, który umożliwia wycofanie bazy danych SQL platformy Azure do 35 dni.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: b98331a9cdb359aeefac5db1546f3a15b54010ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268745"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>Odzyskiwanie bazy danych SQL platformy Azure przy użyciu automatycznych kopii zapasowych bazy danych

Domyślnie kopie zapasowe usługi Azure SQL Database są przechowywane w magazynie obiektów blob replikowanych geograficznie (typ magazynu RA-GRS). Następujące opcje są dostępne do odzyskiwania bazy danych przy użyciu [automatycznych kopii zapasowych bazy danych](sql-database-automated-backups.md). Możesz:

- Utwórz nową bazę danych na tym samym serwerze bazy danych SQL, odzyskaną do określonego punktu w czasie w okresie przechowywania.
- Utwórz bazę danych na tym samym serwerze bazy danych SQL, odzyskane do czasu usunięcia dla usuniętej bazy danych.
- Utwórz nową bazę danych na dowolnym serwerze bazy danych SQL w tym samym regionie, odzyskane do punktu najnowszych kopii zapasowych.
- Utwórz nową bazę danych na dowolnym serwerze bazy danych SQL w dowolnym innym regionie, odzyskaną do punktu najnowszych replikowanych kopii zapasowych.

Jeśli skonfigurowano [tworzenie kopii zapasowych długoterminowego przechowywania,](sql-database-long-term-retention.md)można również utworzyć nową bazę danych z dowolnej długoterminowej kopii zapasowej przechowywania na dowolnym serwerze bazy danych SQL.

> [!IMPORTANT]
> Nie można zastąpić istniejącej bazy danych podczas przywracania.

Gdy używasz warstwy usługi Standardowa lub Premium, przywracanie bazy danych może ponieść dodatkowe koszty magazynowania. Dodatkowy koszt jest ponoszony, gdy maksymalny rozmiar przywróconej bazy danych jest większa niż ilość miejsca do magazynowania w zestawie z warstwą usług i poziomem wydajności docelowej bazy danych. Szczegółowe informacje o cenach dodatkowego magazynu można znaleźć na [stronie cennika bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/). Jeśli rzeczywista ilość używanego miejsca jest mniejsza niż ilość magazynu, można uniknąć tego dodatkowego kosztu, ustawiając maksymalny rozmiar bazy danych na uwzględniony.

## <a name="recovery-time"></a>Czas regeneracji

Na czas odzyskiwania, aby przywrócić bazę danych przy użyciu automatycznych kopii zapasowych bazy danych ma wpływ na kilka czynników:

- Rozmiar bazy danych.
- Rozmiar obliczeń bazy danych.
- Liczba dzienników transakcji zaangażowanych.
- Ilość działania, które muszą zostać odtwoczone, aby odzyskać do punktu przywracania.
- Przepustowość sieci, jeśli przywracanie jest w innym regionie.
- Liczba równoczesnych żądań przywracania przetwarzanych w regionie docelowym.

W przypadku dużej lub bardzo aktywnej bazy danych przywracanie może potrwać kilka godzin. Jeśli występuje długotrwała awaria w regionie, jest możliwe, że duża liczba żądań przywracania geograficznego zostanie zainicjowana w celu odzyskania po awarii. Gdy istnieje wiele żądań, czas odzyskiwania dla poszczególnych baz danych może zwiększyć. Większość przywraca bazy danych kończy się w mniej niż 12 godzin.

W przypadku pojedynczej subskrypcji istnieją ograniczenia dotyczące liczby równoczesnych żądań przywracania. Ograniczenia te mają zastosowanie do dowolnej kombinacji przywracania punktu w czasie, przywracania geograficznego i przywraca z długoterminowej kopii zapasowej przechowywania.

| | **Maksymalnie liczba przetwarzanych równoczesnych żądań** | **Maksymalnie liczba równoczesnych żądań przesyłanych** |
| :--- | --: | --: |
|Pojedyncza baza danych (na subskrypcję)|10|60|
|Basen elastyczny (na basen)|4|200|
||||

Nie ma wbudowanej metody przywracania całego serwera. Aby uzyskać przykład wykonania tego zadania, zobacz [Azure SQL Database: Full Server Recovery](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Aby odzyskać za pomocą automatycznych kopii zapasowych, należy być członkiem roli współautora programu SQL Server w subskrypcji lub być właścicielem subskrypcji. Aby uzyskać więcej informacji, zobacz [RBAC: Wbudowane role](../role-based-access-control/built-in-roles.md). Można odzyskać za pomocą witryny Azure portal, powershell lub interfejsu API REST. Nie można używać funkcji Transact-SQL.

## <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

Bazę danych autonomiczną, zaręczoną lub wystąpienią można przywrócić do wcześniejszego punktu w czasie przy użyciu portalu Azure, [programu PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databases). Żądanie można określić dowolną warstwę usług lub rozmiar obliczeń dla przywróconej bazy danych. Upewnij się, że masz wystarczające zasoby na serwerze, na którym przywracasz bazę danych. Po zakończeniu przywracania tworzy nową bazę danych na tym samym serwerze co oryginalna baza danych. Przywrócona baza danych jest naliczana według normalnych stawek, na podstawie jej warstwy usług i rozmiaru obliczeń. Nie ponosisz opłat, dopóki przywracanie bazy danych nie zostanie ukończone.

Zazwyczaj przywracasz bazę danych do wcześniejszego punktu do celów odzyskiwania. Przywróconą bazę danych można traktować jako zamiennik oryginalnej bazy danych lub użyć jej jako źródła danych do zaktualizowania oryginalnej bazy danych.

- **Wymiana bazy danych**

  Jeśli zamierzasz przywróconej bazy danych do zastąpienia oryginalnej bazy danych, należy określić rozmiar obliczeń oryginalnej bazy danych i warstwy usług. Następnie można zmienić nazwę oryginalnej bazy danych i nadać przywróconej bazie danych oryginalną nazwę przy użyciu polecenia [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) w języku T-SQL.

- **Odzyskiwanie danych**

  Jeśli planujesz pobrać dane z przywróconej bazy danych, aby odzyskać od użytkownika lub błąd aplikacji, należy napisać i wykonać skrypt odzyskiwania danych, który wyodrębnia dane z przywróconej bazy danych i stosuje się do oryginalnej bazy danych. Chociaż operacja przywracania może zająć dużo czasu, aby zakończyć, przywracanie bazy danych jest widoczny na liście bazy danych w całym procesie przywracania. Jeśli usuniesz bazę danych podczas przywracania, operacja przywracania zostanie anulowana i nie zostanie naliczona opłata za bazę danych, która nie została ukończona przywracania.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Przywracanie w czasie przy użyciu witryny Azure portal

Można odzyskać pojedynczą bazę danych SQL lub bazy danych wystąpienia do punktu w czasie z bloku przeglądu bazy danych, które chcesz przywrócić w witrynie Azure portal.

#### <a name="single-azure-sql-database"></a>Pojedyncza baza danych SQL platformy Azure

Aby odzyskać pojedynczą lub braną bazę danych do punktu w czasie przy użyciu witryny Azure portal, otwórz stronę przeglądu bazy danych i wybierz **pozycję Przywróć** na pasku narzędzi. Wybierz źródło kopii zapasowej i wybierz punkt kopii zapasowej typu punkt w czasie, z którego zostanie utworzona nowa baza danych. 

  ![Zrzut ekranu przedstawiający opcje przywracania bazy danych](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Baza danych wystąpienia zarządzanego

Aby odzyskać bazę danych wystąpienia zarządzanego do punktu w czasie przy użyciu witryny Azure Portal, otwórz stronę przeglądu bazy danych i wybierz **pozycję Przywróć** na pasku narzędzi. Wybierz punkt kopii zapasowej punktu, z którego zostanie utworzona nowa baza danych. 

  ![Zrzut ekranu przedstawiający opcje przywracania bazy danych](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Aby programowo przywrócić bazę danych z kopii zapasowej, zobacz [Programowo wykonywanie odzyskiwania przy użyciu automatycznych kopii zapasowych](sql-database-recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Przywracanie usuniętej bazy danych

Można przywrócić usuniętą bazę danych do czasu usunięcia lub wcześniejszego punktu w czasie na tym samym serwerze bazy danych SQL lub tym samym wystąpieniu zarządzanym. Można to osiągnąć za pośrednictwem witryny Azure Portal, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)lub [REST (createMode=Restore).](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) Przywracanie usuniętej bazy danych przez utworzenie nowej bazy danych z kopii zapasowej.

> [!IMPORTANT]
> Jeśli usuniesz serwer bazy danych SQL azure lub wystąpienie zarządzane, wszystkie jego bazy danych są również usuwane i nie można odzyskać. Nie można przywrócić usuniętego serwera lub wystąpienia zarządzanego.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Przywracanie usuniętej bazy danych przy użyciu portalu Azure

Przywracanie usuniętych baz danych z witryny Azure Portal z serwera i zasobu wystąpienia.

#### <a name="single-azure-sql-database"></a>Pojedyncza baza danych SQL platformy Azure

Aby odzyskać pojedynczą lub populkowany usunięty plik danych do czasu usuwania za pomocą witryny Azure Portal, otwórz stronę przeglądu serwera i wybierz pozycję **Usunięte bazy danych**. Wybierz usuniętą bazę danych, którą chcesz przywrócić, i wpisz nazwę nowej bazy danych, która zostanie utworzona przy przywróceniu danych z kopii zapasowej.

  ![Zrzut ekranu przedstawiający przywracanie usuniętej bazy danych SQL platformy Azure](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Baza danych wystąpienia zarządzanego

Aby odzyskać zarządzaną bazę danych za pomocą portalu Azure, otwórz stronę przeglądu wystąpienia zarządzanego i wybierz pozycję **Usunięte bazy danych**. Wybierz usuniętą bazę danych, którą chcesz przywrócić, i wpisz nazwę nowej bazy danych, która zostanie utworzona przy przywróceniu danych z kopii zapasowej.

  ![Zrzut ekranu przedstawiający przywracanie usuniętej bazy danych wystąpień SQL platformy Azure](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Przywracanie usuniętej bazy danych przy użyciu programu PowerShell

Użyj następujących przykładowych skryptów, aby przywrócić usuniętą bazę danych dla usługi Azure SQL Database i wystąpienie zarządzane przy użyciu programu PowerShell.

#### <a name="single-azure-sql-database"></a>Pojedyncza baza danych SQL platformy Azure

Aby uzyskać przykładowy skrypt programu PowerShell przedstawiający sposób przywracania usuniętej bazy danych SQL platformy Azure, zobacz [Przywracanie bazy danych SQL przy użyciu programu PowerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Baza danych wystąpienia zarządzanego

W przypadku przykładowego skryptu programu PowerShell przedstawiającego sposób przywracania usuniętej bazy danych wystąpień zobacz [Przywracanie usuniętej bazy danych w wystąpieniu zarządzanym przy użyciu programu PowerShell](sql-database-managed-instance-point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> Aby programowo przywrócić usuniętą bazę danych, zobacz [Programowo wykonywanie odzyskiwania przy użyciu automatycznych kopii zapasowych](sql-database-recovery-using-backups.md).

## <a name="geo-restore"></a>Przywracanie geograficzne

Można przywrócić bazę danych SQL na dowolnym serwerze w dowolnym regionie platformy Azure z najnowszych kopii zapasowych replikowanych geograficznie. Przywracanie geograficzne używa replikowanej geograficznie kopii zapasowej jako źródła. Można zażądać przywracania geograficznego, nawet jeśli baza danych lub centrum danych jest niedostępna z powodu awarii.

Przywracanie geograficzne jest domyślną opcją odzyskiwania, gdy baza danych jest niedostępna z powodu zdarzenia w regionie hostingu. Bazę danych można przywrócić na serwerze w dowolnym innym regionie. Występuje opóźnienie między podczas wykonywania kopii zapasowej i gdy jest replikowana geograficznie do obiektu blob platformy Azure w innym regionie. W rezultacie przywrócona baza danych może być maksymalnie jedną godzinę za oryginalną bazą danych. Na poniższej ilustracji przedstawiono przywracanie bazy danych z ostatniej dostępnej kopii zapasowej w innym regionie.

![Grafika przywracania geograficznego](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Przywracanie geograficzne przy użyciu witryny Azure portal

W witrynie Azure Portal tworzysz nową bazę danych pojedynczych lub zarządzanych wystąpień i wybierasz dostępną kopię zapasową przywracania geograficznego. Nowo utworzona baza danych zawiera dane kopii zapasowej przywrócone geograficznie.

#### <a name="single-azure-sql-database"></a>Pojedyncza baza danych SQL platformy Azure

Aby przywrócić geograficznie pojedynczą bazę danych SQL z witryny Azure portal w wybranym regionie i serwerze, wykonaj następujące kroki:

1. Z **pulpitu nawigacyjnego**wybierz pozycję Dodaj pozycję **Utwórz** > **bazę danych SQL**. Na karcie **Podstawy** wprowadź wymagane informacje.
2. Wybierz **opcję Dodatkowe ustawienia**.
3. W obszarze **Korzystanie z istniejących danych**wybierz pozycję Kopia **zapasowa**.
4. W obszarze **Kopia zapasowa**wybierz kopię zapasową z listy dostępnych kopii zapasowych przywracania geograficznego.

    ![Zrzut ekranu przedstawiający opcje tworzenia bazy danych SQL](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Ukończ proces tworzenia nowej bazy danych z kopii zapasowej. Podczas tworzenia pojedynczej bazy danych SQL platformy Azure zawiera przywróconą kopię zapasową przywracania geograficznego.

#### <a name="managed-instance-database"></a>Baza danych wystąpienia zarządzanego

Aby przywrócić geograficznie bazę danych wystąpienia zarządzanego z witryny Azure portal do istniejącego wystąpienia zarządzanego w wybranym regionie, wybierz wystąpienie zarządzane, w którym chcesz przywrócić bazę danych. Wykonaj następujące kroki:

1. Wybierz **pozycję Nowa baza danych**.
2. Wpisz żądaną nazwę bazy danych.
3. W obszarze **Używanie istniejących danych**wybierz pozycję Kopia **zapasowa**.
4. Wybierz kopię zapasową z listy dostępnych kopii zapasowych przywracania geograficznego.

    ![Zrzut ekranu przedstawiający nowe opcje bazy danych](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Ukończ proces tworzenia nowej bazy danych. Podczas tworzenia bazy danych wystąpienia zawiera ona przywróconą kopię zapasową przywracania geograficznego.

### <a name="geo-restore-by-using-powershell"></a>Przywracanie geograficzne przy użyciu programu PowerShell

#### <a name="single-azure-sql-database"></a>Pojedyncza baza danych SQL platformy Azure

Aby uzyskać skrypt programu PowerShell, który pokazuje, jak wykonać przywracanie geograficzne dla pojedynczej bazy danych SQL, zobacz [Przywracanie pojedynczej bazy danych usługi Azure SQL do wcześniejszego punktu w czasie.](scripts/sql-database-restore-database-powershell.md)

#### <a name="managed-instance-database"></a>Baza danych wystąpienia zarządzanego

Aby uzyskać skrypt programu PowerShell, który pokazuje, jak przeprowadzić przywracanie geograficzne bazy danych wystąpienia zarządzanego, zobacz [Przywracanie bazy danych wystąpienia zarządzanego do innego regionu](scripts/sql-managed-instance-restore-geo-backup.md)za pomocą programu PowerShell.

### <a name="geo-restore-considerations"></a>Zagadnienia dotyczące przywracania geograficznego

Nie można wykonać przywracania punktu w czasie w pomocniczej bazie danych. Można to zrobić tylko w podstawowej bazie danych. Aby uzyskać szczegółowe informacje na temat używania przywracania geograficznego do odzyskiwania po awarii, zobacz [Odzyskiwanie po awarii](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Przywracanie geograficzne jest najbardziej podstawowym rozwiązaniem odzyskiwania po awarii dostępne w bazie danych SQL. Opiera się na automatycznie utworzonych kopiach zapasowych replikowanych geograficznie z celem punktu odzyskiwania (RPO) równym 1 godzinie i szacowanemu czasowi odzyskiwania do 12 godzin. Nie gwarantuje, że region docelowy będzie miał możliwość przywracania baz danych po awarii regionalnej, ponieważ prawdopodobny jest gwałtowny wzrost popytu. Jeśli aplikacja używa stosunkowo małych baz danych i nie jest krytyczna dla firmy, przywracanie geograficzne jest odpowiednim rozwiązaniem odzyskiwania po awarii. W przypadku aplikacji o znaczeniu krytycznym dla firmy, które wymagają dużych baz danych i muszą zapewnić ciągłość działania, należy użyć [grup auto-trybu failover.](sql-database-auto-failover-group.md) Oferuje znacznie niższy cel RPO i czas odzyskiwania, a pojemność jest zawsze gwarantowana. Aby uzyskać więcej informacji na temat wyborów dotyczących ciągłości działania, zobacz [Omówienie ciągłości działania](sql-database-business-continuity.md).

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>Programowo wykonujące odzyskiwanie przy użyciu automatycznych kopii zapasowych

Można również użyć programu Azure PowerShell lub interfejsu API REST do odzyskiwania. W poniższych tabelach opisano zestaw dostępnych poleceń.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i azurerm moduły są w dużym stopniu identyczne.

#### <a name="single-azure-sql-database"></a>Pojedyncza baza danych SQL platformy Azure

Aby przywrócić autonomiczną lub pulchną bazę danych, zobacz [Przywracanie-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Polecenie cmdlet | Opis |
  | --- | --- |
  | [Baza danych Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Pobiera co najmniej jedną bazę danych. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Pobiera usuniętą bazę danych, którą można przywrócić. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Pobiera geograficznie nadmiarową kopię zapasową bazy danych. |
  | [Przywracanie-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Przywraca bazę danych SQL. |

  > [!TIP]
  > Przykładowy skrypt programu PowerShell, który pokazuje, jak wykonać przywracanie bazy danych w czasie, zobacz [Przywracanie bazy danych SQL przy użyciu programu PowerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Baza danych wystąpienia zarządzanego

Aby przywrócić bazę danych wystąpienia zarządzanego, zobacz [Przywracanie-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Polecenie cmdlet | Opis |
  | --- | --- |
  | [Get-AzSqlInstance (Nieumieja)](/powershell/module/az.sql/get-azsqlinstance) |Pobiera co najmniej jedno wystąpienie zarządzane. |
  | [Baza danych Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstancedatabase) | Pobiera bazę danych wystąpienia. |
  | [Przywróć-AzSqlInstanceBaż danych](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Przywraca bazę danych wystąpienia. |

### <a name="rest-api"></a>Interfejs API REST

Aby przywrócić pojedynczą lub zarętą bazę danych przy użyciu interfejsu API REST:

| interfejs API | Opis |
| --- | --- |
| [REST (createMode=Odzyskiwanie)](https://docs.microsoft.com/rest/api/sql/databases) |Przywraca bazę danych. |
| [Pobierz stan tworzenia lub aktualizowania bazy danych](https://docs.microsoft.com/rest/api/sql/operations) |Zwraca stan podczas operacji przywracania. |

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

#### <a name="single-azure-sql-database"></a>Pojedyncza baza danych SQL platformy Azure

Aby przywrócić pojedynczą lub zgromadzone bazy danych przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [az sql db restore](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="managed-instance-database"></a>Baza danych wystąpienia zarządzanego

Aby przywrócić bazę danych wystąpienia zarządzanego przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [az sql midb restore](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Podsumowanie

Automatyczne kopie zapasowe chronią bazy danych przed błędami użytkowników i aplikacji, przypadkowym usunięciem bazy danych i długotrwałymi awariami. Ta wbudowana funkcja jest dostępna dla wszystkich warstw usług i rozmiarów obliczeń.

## <a name="next-steps"></a>Następne kroki

- [Omówienie ciągłości działania](sql-database-business-continuity.md)
- [Automatyczne kopie zapasowe bazy danych SQL](sql-database-automated-backups.md)
- [Długoterminowe przechowywanie](sql-database-long-term-retention.md)
- Aby dowiedzieć się więcej o szybszych opcjach odzyskiwania, zobacz [Aktywne replikowanie geograficzne](sql-database-active-geo-replication.md) lub [Grupy auto-trybu failover](sql-database-auto-failover-group.md).
