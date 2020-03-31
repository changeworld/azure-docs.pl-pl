---
title: Kopiowanie bazy danych
description: Utwórz spójną transakcyjnie kopię istniejącej bazy danych SQL platformy Azure na tym samym serwerze lub innym serwerze.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 02/24/2020
ms.openlocfilehash: 7e4744627cfd08874e07bb126df048ea3e644f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473748"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Kopiowanie spójnej transakcyjnie kopii bazy danych SQL platformy Azure

Usługa Azure SQL Database udostępnia kilka metod tworzenia spójnej transakcyjnie kopii istniejącej bazy danych SQL platformy Azure[(pojedynczej bazy danych)](sql-database-single-database.md)na tym samym serwerze lub innym serwerze. Bazę danych SQL można skopiować przy użyciu portalu Azure, programu PowerShell lub T-SQL.

## <a name="overview"></a>Omówienie

Kopia bazy danych jest migawką źródłowej bazy danych na czas żądania kopiowania. Można wybrać ten sam serwer lub inny serwer. Ponadto można zachować jego warstwy usług i rozmiar obliczeń lub użyć innego rozmiaru obliczeń w tej samej warstwie usługi (wersja). Po zakończeniu kopiowania staje się w pełni funkcjonalną, niezależną bazą danych. W tym momencie można uaktualnić lub obniżyć go do dowolnej wersji. Logowania, użytkowników i uprawnień mogą być zarządzane niezależnie. Kopia jest tworzona przy użyciu technologii replikacji geograficznej, a po zakończeniu wysiewu łącze replikacji geograficznej zostanie automatycznie zakończone. Wszystkie wymagania dotyczące korzystania z replikacji geograficznej dotyczą operacji kopiowania bazy danych. Szczegółowe informacje [można znaleźć w ekrepcie aktywnej replikacji geograficznej.](sql-database-active-geo-replication.md)

> [!NOTE]
> [Automatyczne kopie zapasowe bazy danych](sql-database-automated-backups.md) są używane podczas tworzenia kopii bazy danych.

## <a name="logins-in-the-database-copy"></a>Logowania w kopii bazy danych

Podczas kopiowania bazy danych do tego samego serwera bazy danych SQL, te same logowania mogą być używane w obu bazach danych. Podmiot zabezpieczeń używany do kopiowania bazy danych staje się właścicielem bazy danych w nowej bazie danych. 

Podczas kopiowania bazy danych do innego serwera bazy danych SQL podmiot zabezpieczeń, który zainicjował operację kopiowania na serwerze docelowym staje się właścicielem nowej bazy danych. 

Niezależnie od serwera docelowego wszyscy użytkownicy bazy danych, ich uprawnienia i identyfikatory zabezpieczeń (IDENTYFIKATORY) są kopiowane do kopii bazy danych. Korzystanie z [zamkniętych użytkowników bazy danych](sql-database-manage-logins.md) w celu uzyskania dostępu do danych gwarantuje, że skopiowana baza danych ma te same poświadczenia użytkownika, dzięki czemu po zakończeniu kopiowania można natychmiast uzyskać do niej dostęp przy użyciu tych samych poświadczeń.

Jeśli używasz logowania na poziomie serwera do dostępu do danych i kopiowania bazy danych na inny serwer, dostęp oparty na logowaniu może nie działać. Może się tak zdarzyć, ponieważ logowania nie istnieją na serwerze docelowym lub ponieważ ich hasła i identyfikatory zabezpieczeń (IDENTYFIKATORY) są różne. Aby dowiedzieć się więcej na temat zarządzania logowaniami podczas kopiowania bazy danych na inny serwer bazy danych SQL, zobacz [Jak zarządzać zabezpieczeniami bazy danych SQL platformy Azure po porodzie](sql-database-geo-replication-security-config.md). Po zakończeniu operacji kopiowania na inny serwer i przed ponownej mapowania innych użytkowników, tylko logowania skojarzonego z właścicielem bazy danych lub administrator serwera można zalogować się do skopiowanej bazy danych. Aby rozwiązać problemy związane z logowaniem i ustanowić dostęp do danych po zakończeniu operacji kopiowania, zobacz [Rozwiązywanie problemów z logowaniem](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Kopiowanie bazy danych przy użyciu portalu Azure

Aby skopiować bazę danych przy użyciu witryny Azure Portal, otwórz stronę bazy danych, a następnie kliknij przycisk **Kopiuj**.

   ![Kopiowanie bazy danych](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell-or-azure-cli"></a>Kopiowanie bazy danych przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure

Aby skopiować bazę danych, należy użyć następujących przykładów.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

W programie PowerShell należy użyć polecenia cmdlet [New-AzSqlDatabaseCopy.](/powershell/module/az.sql/new-azsqldatabasecopy)

> [!IMPORTANT]
> Moduł programu PowerShell Azure Resource Manager (RM) jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Moduł AzureRM będzie nadal otrzymywać poprawki błędów co najmniej do grudnia 2020.  Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji na temat ich zgodności, zobacz [Przedstawianie nowego modułu Az programu Azure PowerShell.](/powershell/azure/new-azureps-module-az)

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

Kopia bazy danych jest operacją asynchronicznego, ale docelowa baza danych jest tworzona natychmiast po zaakceptowaniu żądania. Jeśli chcesz anulować operację kopiowania w toku, upuść docelową bazę danych przy użyciu polecenia cmdlet [Remove-AzSqlDatabase.](/powershell/module/az.sql/new-azsqldatabase)

Aby uzyskać pełny przykładowy skrypt programu PowerShell, zobacz [Kopiowanie bazy danych na nowy serwer](scripts/sql-database-copy-database-to-new-server-powershell.md).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

Kopia bazy danych jest operacją asynchronicznego, ale docelowa baza danych jest tworzona natychmiast po zaakceptowaniu żądania. Jeśli musisz anulować operację kopiowania w trakcie, upuść docelową bazę danych za pomocą polecenia [az sql db delete.](/cli/azure/sql/db#az-sql-db-delete)

* * *

## <a name="rbac-roles-to-manage-database-copy"></a>Role RBAC do zarządzania kopiowaniem bazy danych

Aby utworzyć kopię bazy danych, musisz być w następujących rolach

- Właściciel subskrypcji lub
- Rola współautora programu SQL Server lub
- Rola niestandardowa w źródłowych i docelowych bazach danych z następującymi uprawnieniami:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Aby anulować kopię bazy danych, musisz być w następujących rolach

- Właściciel subskrypcji lub
- Rola współautora programu SQL Server lub
- Rola niestandardowa w źródłowych i docelowych bazach danych z następującymi uprawnieniami:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Aby zarządzać kopiowaniem bazy danych przy użyciu witryny Azure portal, potrzebne będą również następujące uprawnienia:

   Zasoby firmy Microsoft/subskrypcje/zasoby/odczytywać Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/operationstatuses/read

Jeśli chcesz zobaczyć operacje w ramach wdrożeń w grupie zasobów w portalu, operacje w wielu dostawców zasobów, w tym operacji SQL, trzeba będzie te dodatkowe role RBAC:

   Zasoby firmy Microsoft/subskrypcje/grupy zasobów/wdrożenia/operacje/odczytywać Zasoby/subskrypcje firmy Microsoft/grupy zasobów/wdrożenia/operationstatuses/read

## <a name="copy-a-database-by-using-transact-sql"></a>Kopiowanie bazy danych przy użyciu usługi Transact-SQL

Zaloguj się do głównej bazy danych za pomocą logowania administratora serwera lub logowania, który utworzył bazę danych, którą chcesz skopiować. Aby kopia bazy danych powiodła się, logowania, które `dbmanager` nie są administratorem serwera, muszą być członkami roli. Aby uzyskać więcej informacji na temat logowania i łączenia się z serwerem, zobacz [Zarządzanie loginami](sql-database-manage-logins.md).

Rozpocznij kopiowanie źródłowej bazy danych za pomocą [programu CREATE DATABASE ... AS COPY instrukcji.](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#copy-a-database) Instrukcja T-SQL jest kontynuowana do czasu zakończenia operacji kopiowania bazy danych.

> [!NOTE]
> Zakończenie instrukcji T-SQL nie kończy operacji kopiowania bazy danych. Aby zakończyć operację, upuść docelową bazę danych.
>

### <a name="copy-a-sql-database-to-the-same-server"></a>Kopiowanie bazy danych SQL na ten sam serwer

Zaloguj się do głównej bazy danych za pomocą logowania administratora serwera lub logowania, który utworzył bazę danych, którą chcesz skopiować. Aby kopiowanie bazy danych powiodło się, logowania, które `dbmanager` nie są administratorem serwera, muszą być członkami roli.

To polecenie kopiuje bazę danych Database1 do nowej bazy danych o nazwie Database2 na tym samym serwerze. W zależności od rozmiaru bazy danych operacja kopiowania może zająć trochę czasu.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>Kopiowanie bazy danych SQL na inny serwer

Zaloguj się do głównej bazy danych serwera docelowego, na którym ma zostać utworzona nowa baza danych. Użyj logowania, który ma taką samą nazwę i hasło jak właściciel bazy danych źródłowej bazy danych na serwerze źródłowym. Login na serwerze docelowym musi być `dbmanager` również członkiem roli lub być logowaniem administratora serwera.

To polecenie kopiuje bazę danych Database1 na serwerze1 do nowej bazy danych o nazwie Database2 na serwerze2. W zależności od rozmiaru bazy danych operacja kopiowania może zająć trochę czasu.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Zapory obu serwerów muszą być skonfigurowane tak, aby zezwalały na połączenie przychodzące z adresu IP klienta wystawiającego bazę danych T-SQL CREATE ... AS COPY POLECENIA.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Kopiowanie bazy danych SQL do innej subskrypcji

Można użyć kroków w sekcji [Kopiowanie bazy danych SQL do innego serwera,](#copy-a-sql-database-to-a-different-server) aby skopiować bazę danych na serwer bazy danych SQL w innej subskrypcji przy użyciu języka T-SQL. Upewnij się, że używasz logowania, który ma taką samą nazwę i hasło jak właściciel bazy danych źródłowej bazy danych. Ponadto logowanie musi być członkiem `dbmanager` roli lub administratorem serwera, zarówno na serwerach źródłowych, jak i docelowych.

> [!NOTE]
> [Portal Azure,](https://portal.azure.com)PowerShell i narzędzia platformy Azure CLI nie obsługują kopiowania bazy danych do innej subskrypcji.

### <a name="monitor-the-progress-of-the-copying-operation"></a>Monitorowanie postępu operacji kopiowania

Monitoruj proces kopiowania, odpytując widoki [sys.databases](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys.dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)i [sys.dm_operation_status.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) Podczas kopiowania jest w toku, **kolumna state_desc** widoku sys.databases dla nowej bazy danych jest ustawiona na **KOPIOWANIE**.

* Jeśli kopiowanie nie powiedzie się, **kolumna state_desc** widoku sys.databases dla nowej bazy danych jest **ustawiona**na PODEJRZANY . Wykonaj instrukcję DROP w nowej bazie danych i spróbuj ponownie później.
* Jeśli kopiowanie zakończy się pomyślnie, **kolumna state_desc** widoku sys.databases dla nowej bazy danych jest ustawiona na **ONLINE**. Kopiowanie zostało zakończone, a nowa baza danych jest zwykłą bazą danych, którą można zmienić niezależnie od źródłowej bazy danych.

> [!NOTE]
> Jeśli zdecydujesz się anulować kopiowanie, gdy jest w toku, należy wykonać [drop database](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) instrukcji w nowej bazie danych.

> [!IMPORTANT]
> Jeśli trzeba utworzyć kopię z celu usługi znacznie mniejsze niż źródło, docelowa baza danych może nie mieć wystarczających zasobów, aby zakończyć proces wysiewu i może spowodować, że operaacja kopiowania zakończy się niepowodzeniem. W tym scenariuszu użyj żądania przywracania geograficznego, aby utworzyć kopię na innym serwerze i/lub innym regionie. Zobacz [Odzyskiwanie bazy danych SQL platformy Azure przy użyciu kopii zapasowych bazy danych,](sql-database-recovery-using-backups.md#geo-restore) aby uzyskać więcej informaion.

## <a name="resolve-logins"></a>Rozwiązywanie problemów z logowaniem

Po nowej bazie danych jest w trybie online na serwerze docelowym, użyj [instrukcji ALTER USER,](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current) aby ponownie mapować użytkowników z nowej bazy danych do logowania na serwerze docelowym. Aby rozwiązać problem osieroconych użytkowników, zobacz [Rozwiązywanie problemów z osieroconych użytkowników](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server). Zobacz też [Jak zarządzać zabezpieczeniami bazy danych SQL platformy Azure po porodzie](sql-database-geo-replication-security-config.md).

Wszyscy użytkownicy w nowej bazie danych zachowują uprawnienia, które mieli w źródłowej bazie danych. Użytkownik, który zainicjował kopię bazy danych staje się właścicielem bazy danych nowej bazy danych. Po pomyślnym skopiowaniu i przed innych użytkowników są mapowane ponownie, tylko właściciel bazy danych można zalogować się do nowej bazy danych.

Aby dowiedzieć się więcej na temat zarządzania użytkownikami i loginami podczas kopiowania bazy danych na inny serwer bazy danych SQL, zobacz [Jak zarządzać zabezpieczeniami bazy danych SQL platformy Azure po porodzie](sql-database-geo-replication-security-config.md).

## <a name="database-copy-errors"></a>Błędy kopiowania bazy danych

Podczas kopiowania bazy danych w bazie danych SQL azure można napotkać następujące błędy. Więcej informacji znajdziesz w artykule [Kopiowanie bazy danych usługi Azure SQL Database](sql-database-copy.md).

| Kod błędu | Ważność | Opis |
| ---:| ---:|:--- |
| 40635 |16 |Klient z adresem IP "%.&#x2a;ls" jest tymczasowo wyłączony. |
| 40637 |16 |Tworzenie kopii bazy danych jest obecnie wyłączone. |
| 40561 |16 |Kopiowanie bazy danych nie powiodło się. Źródło lub docelowa baza danych nie istnieje. |
| 40562 |16 |Kopiowanie bazy danych nie powiodło się. Baza danych źródłowych została porzucona. |
| 40563 |16 |Kopiowanie bazy danych nie powiodło się. Docelowa baza danych została porzucona. |
| 40564 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Upuść docelową bazę danych i spróbuj ponownie. |
| 40565 |16 |Kopiowanie bazy danych nie powiodło się. Nie więcej niż 1 jednoczesna kopia bazy danych z tego samego źródła jest dozwolone. Upuść docelową bazę danych i spróbuj ponownie później. |
| 40566 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Upuść docelową bazę danych i spróbuj ponownie. |
| 40567 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Upuść docelową bazę danych i spróbuj ponownie. |
| 40568 |16 |Kopiowanie bazy danych nie powiodło się. Źródłowa baza danych stała się niedostępna. Upuść docelową bazę danych i spróbuj ponownie. |
| 40569 |16 |Kopiowanie bazy danych nie powiodło się. Docelowa baza danych stała się niedostępna. Upuść docelową bazę danych i spróbuj ponownie. |
| 40570 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Upuść docelową bazę danych i spróbuj ponownie później. |
| 40571 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Upuść docelową bazę danych i spróbuj ponownie później. |

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat logowania, zobacz [Zarządzanie loginami](sql-database-manage-logins.md) i [jak zarządzać zabezpieczeniami bazy danych SQL platformy Azure po porodzie](sql-database-geo-replication-security-config.md).
- Aby wyeksportować bazę danych, zobacz [Eksportowanie bazy danych do bacpac](sql-database-export.md).
