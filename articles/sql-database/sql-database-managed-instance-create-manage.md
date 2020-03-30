---
title: Odwołanie do interfejsu API zarządzania dla wystąpienia zarządzanego
description: Dowiedz się więcej o tworzeniu wystąpień zarządzanych bazy danych SQL i zarządzaniu nimi.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 713217a933c646cc4d04759f5697bbc0312827ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268862"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Odwołanie do zarządzanego interfejsu API dla wystąpień zarządzanych usługi Azure SQL Database

Wystąpienia zarządzanej bazy danych SQL usługi Azure i zarządzanie nimi można tworzyć za pomocą portalu Azure, programu PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST i transact-SQL. W tym artykule można znaleźć omówienie funkcji i interfejsu API, których można użyć do tworzenia i konfigurowania wystąpienia zarządzanego.

## <a name="azure-portal-create-a-managed-instance"></a>Witryna Azure portal: tworzenie wystąpienia zarządzanego

Aby uzyskać przewodnik Szybki start pokazujący sposób tworzenia wystąpienia zarządzanego usługi Azure SQL Database, zobacz [Szybki start: Tworzenie wystąpienia zarządzanego bazy danych Azure SQL](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: tworzenie wystąpień zarządzanych i zarządzanie nimi

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne.

Aby utworzyć wystąpienia zarządzane za pomocą programu Azure PowerShell i zarządzać nimi, należy użyć następujących poleceń cmdlet programu PowerShell. Jeśli chcesz zainstalować lub uaktualnić program PowerShell, zobacz [Instalowanie modułu programu Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> W przypadku przykładowych skryptów programu PowerShell zobacz [Skrypt szybkiego startu: Tworzenie wystąpienia zarządzanego programu Azure SQL przy użyciu biblioteki programu PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Polecenie cmdlet | Opis |
| --- | --- |
|[Nowy-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Tworzy wystąpienie zarządzane bazy danych SQL platformy Azure |
|[Get-AzSqlInstance (Nieumieja)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Zwraca informacje o wystąpieniu zarządzanym usługi Azure SQL|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Ustawia właściwości wystąpienia zarządzanego usługi Azure SQL Database|
|[Usuń-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Usuwa wystąpienie zarządzanej bazy danych usługi Azure SQL|
|[Nowa baza danych AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Tworzy bazę danych wystąpienia zarządzanego bazy danych usługi Azure SQL Database|
|[Baza danych Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Zwraca informacje o bazie danych wystąpienia zarządzanego sql usługi Azure|
|[Usuń-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Usuwa bazę danych wystąpienia zarządzanej bazy danych usługi Azure SQL|
|[Przywróć-AzSqlInstanceBaż danych](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Przywraca bazę danych wystąpienia zarządzanej bazy danych usługi Azure SQL|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Narzędzie CLI platformy Azure: tworzenie wystąpień zarządzanych i zarządzanie nimi

Aby utworzyć wystąpienia zarządzane i zarządzać nimi za pomocą [interfejsu wiersza polecenia platformy Azure CLI,](/cli/azure)należy użyć następujących poleceń [wystąpienia zarządzanego sql interfejsu wiersza polecenia platformy Azure.](/cli/azure/sql/mi) Używaj usługi [Cloud Shell](/azure/cloud-shell/overview), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows.

> [!TIP]
> Aby uzyskać szybki start interfejsu wiersza polecenia platformy Azure, zobacz [Praca z wystąpieniem zarządzanym sql przy użyciu interfejsu wiersza polecenia platformy Azure](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Polecenie cmdlet | Opis |
| --- | --- |
|[az sql mi tworzenie](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Tworzy wystąpienie zarządzane|
|[az sql mi lista](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Wyświetla listę dostępnych wystąpień zarządzanych|
|[az sql mi pokaż](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Uzyskaj szczegółowe informacje o wystąpieniu zarządzanym|
|[az sql mi aktualizacja](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Aktualizuje wystąpienie zarządzane|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Usuwa wystąpienie zarządzane|
|[az sql midb tworzenie](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Tworzy zarządzaną bazę danych|
|[az sql midb lista](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Wyświetla listę dostępnych zarządzanych baz danych|
|[az sql midb przywracanie](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Przywracanie zarządzanej bazy danych|
|[az sql midb usunąć](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Usuwa zarządzaną bazę danych|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: tworzenie baz danych wystąpień i zarządzanie nimi

Aby utworzyć bazę danych wystąpień i zarządzać nią po utworzeniu wystąpienia zarządzanego, należy użyć następujących poleceń T-SQL. Te polecenia można wydać za pomocą portalu Azure, [programu SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), usługi Azure Data [Studio.](https://docs.microsoft.com/sql/azure-data-studio/what-is) [Visual Studio Code](https://code.visualstudio.com/docs)lub inny program, który może łączyć się z serwerem usługi Azure SQL Database i przekazywać polecenia Transact-SQL.

> [!TIP]
> Aby uzyskać przewodnik szybki start pokazujący, że musisz skonfigurować wystąpienie zarządzane i połączyć się z nim przy użyciu programu SQL Server Management Studio w systemie Microsoft Windows, zobacz [Szybki start: Konfigurowanie maszyny Wirtualnej platformy Azure w celu nawiązania połączenia z wystąpieniem zarządzanym](sql-database-managed-instance-configure-vm.md) bazy danych SQL azure i [szybkim uruchomieniem: Konfigurowanie połączenia typu "punkt-lokacja" z wystąpieniem zarządzanym bazy danych SQL platformy Azure z lokalnego](sql-database-managed-instance-configure-p2s.md)wystąpienia .
> [!IMPORTANT]
> Nie można utworzyć ani usunąć wystąpienia zarządzanego przy użyciu funkcji Transact-SQL.

| Polecenie | Opis |
| --- | --- |
|[TWORZENIE BAZY DANYCH](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Tworzy nową bazę danych wystąpienia zarządzanego. Aby utworzyć nową bazę danych, musisz być połączony z główną bazą danych.|
| [ZMIEŃ BAZĘ DANYCH](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Modyfikuje bazę danych wystąpienia zarządzanego sql platformy Azure.|

## <a name="rest-api-create-and-manage-managed-instances"></a>INTERFEJS API REST: tworzenie wystąpień zarządzanych i zarządzanie nimi

Aby utworzyć wystąpienia zarządzane i zarządzać nimi, użyj tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Wystąpienia zarządzane — tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Tworzy lub aktualizuje wystąpienie zarządzane.|
|[Wystąpienia zarządzane — usuwanie](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Usuwa wystąpienie zarządzane.|
|[Wystąpienia zarządzane — pobierz](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Pobiera wystąpienie zarządzane.|
|[Wystąpienia zarządzane — lista](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Zwraca listę wystąpień zarządzanych w ramach subskrypcji.|
|[Wystąpienia zarządzane — lista według grupy zasobów](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Zwraca listę wystąpień zarządzanych w grupie zasobów.|
|[Wystąpienia zarządzane — aktualizacja](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Aktualizuje wystąpienie zarządzane.|

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o migrowaniu bazy danych programu SQL Server na platformę Azure, zobacz [Migrowanie do bazy danych SQL Azure](sql-database-single-database-migrate.md).
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](sql-database-features.md).
