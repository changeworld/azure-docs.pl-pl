---
title: Dokumentacja interfejsu API zarządzania dla wystąpienia zarządzanego Azure SQL Database | Dokumentacja firmy Microsoft
description: Więcej informacji na temat tworzenia i zarządzania nimi w wystąpieniach zarządzanych bazy danych SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 6362084c11ce7aa9078823758700239694162765
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59359155"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Dokumentacja interfejsu API zarządzanego w wystąpieniach zarządzanych bazy danych SQL Azure

Można tworzyć i zarządzać usługi Azure SQL wystąpieniach zarządzanych bazy danych przy użyciu witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST i języka Transact-SQL. W tym artykule można znaleźć Przegląd funkcji i interfejsu API, który umożliwia tworzenie i konfigurowanie wystąpienia zarządzanego.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: Tworzenie wystąpienia zarządzanego

Aby uzyskać szybki start omawiający Tworzenie wystąpienia zarządzanego bazy danych SQL Azure, zobacz [Szybki Start: Tworzenie wystąpienia zarządzanego Azure SQL Database](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-managed-instances"></a>Program PowerShell: Tworzenie i zarządzanie nimi wystąpienia zarządzanego

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

Tworzenie i zarządzanie nimi wystąpień zarządzanych przy użyciu programu Azure PowerShell, użyj następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić programu PowerShell, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> W przypadku skryptów przykład programu PowerShell, zobacz [skryptu szybki start: Tworzenie wystąpienia zarządzanego Azure SQL przy użyciu programu PowerShell biblioteki](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Polecenie cmdlet | Opis |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Tworzy wystąpienie zarządzane usługi Azure SQL Database |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Zwraca informacje na temat wystąpienia zarządzanego Azure SQL|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Ustawia właściwości dla wystąpienia zarządzanego bazy danych SQL Azure|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Usuwa wystąpienie zarządzane bazy danych Azure SQL|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Tworzy bazę danych Azure SQL Database Managed Instance|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Zwraca informacje na temat wystąpienia zarządzanego Azure SQL database|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Usuwa bazę danych Azure SQL Managed Instance bazy danych|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Przywraca bazę danych Azure SQL Managed Instance bazy danych|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Interfejs wiersza polecenia platformy Azure: Tworzenie i zarządzanie nimi wystąpienia zarządzanego

Tworzenie i zarządzanie nimi wystąpienia zarządzanego z [wiersza polecenia platformy Azure](/cli/azure), należy użyć następującego [wystąpienia zarządzanego SQL interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/mi) poleceń. Używaj usługi [Cloud Shell](/azure/cloud-shell/overview), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows.

> [!TIP]
> W przewodniku Szybki Start wiersza polecenia platformy Azure, zobacz [Praca z wystąpienia zarządzanego SQL przy użyciu wiersza polecenia platformy Azure](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Polecenie cmdlet | Opis |
| --- | --- |
|[Tworzenie wystąpienia zarządzanego sql az](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Tworzy wystąpienie zarządzane|
|[AZ sql mi listy](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Wyświetla dostępne wystąpienia zarządzane|
|[Pokaż mi sql az](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Uzyskaj szczegółowe informacje dla wystąpienia zarządzanego|
|[Aktualizacja wystąpienia zarządzanego sql az](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Aktualizuje wystąpienia zarządzanego|
|[Usuwanie wystąpienia zarządzanego sql az](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Usuwa wystąpienia zarządzanego|
|[Utwórz fragment sql az](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Utworzenie zarządzanej bazy danych|
|[AZ sql fragment listy](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Dostępne listy zarządzane bazy danych|
|[AZ sql fragment przywracania](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Przywracanie zarządzanej bazy danych|
|[AZ sql fragment delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Usuwa zarządzanej bazy danych|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: Tworzenie i zarządzanie nimi wystąpienia bazy danych

Tworzenie i zarządzanie nimi wystąpienia bazy danych, po utworzeniu wystąpienia zarządzanego, użyj następujących poleceń języka T-SQL. Można wydać tych poleceń w witrynie Azure portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [usługi Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is). [Visual Studio Code](https://code.visualstudio.com/docs), lub innego programu, który mogą połączyć się z serwerem usługi Azure SQL Database i przekazać polecenia języka Transact-SQL.

> [!TIP]
> Przewodniki Szybki Start przedstawiający, należy skonfigurować i nawiązać połączenie z wystąpienia zarządzanego przy użyciu programu SQL Server Management Studio na Windows firmy Microsoft można znaleźć [Szybki Start: konfigurowanie maszyny wirtualnej platformy Azure w celu nawiązania połączenia z wystąpieniem zarządzanym usługi Azure SQL Database](sql-database-managed-instance-configure-vm.md) i [Szybki start: konfigurowanie połączenia punkt-lokacja z wystąpieniem zarządzanym usługi Azure SQL Database ze środowiska lokalnego](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Nie można utworzyć ani usunąć wystąpienia zarządzanego przy użyciu języka Transact-SQL.

| Polecenie | Opis |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Tworzy nową bazę danych wystąpienia zarządzanego. Musisz mieć połączenie z główną bazą danych, aby utworzyć nową bazę danych.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Modyfikuje wystąpienia zarządzanego Azure SQL database.|

## <a name="rest-api-create-and-manage-managed-instances"></a>INTERFEJS API REST: Tworzenie i zarządzanie nimi wystąpienia zarządzanego

Tworzenie i zarządzanie nimi wystąpienia zarządzane przez usługę, użyj tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Zarządzane wystąpienia — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Tworzy lub aktualizuje wystąpienia zarządzanego.|
|[Usuwanie wystąpienia zarządzanego —](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Usuwa wystąpienia zarządzanego.|
|[Wystąpienia zarządzane — Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Pobiera wystąpienie zarządzane.|
|[Zarządzane wystąpienia — lista](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Zwraca listę wystąpień zarządzanych w ramach subskrypcji.|
|[Zarządzane wystąpienia — listy według grupy zasobów](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Zwraca listę wystąpień zarządzanych w grupie zasobów.|
|[Wystąpienia zarządzane — aktualizacja](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Aktualizuje wystąpienia zarządzanego.|

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o migracji bazy danych programu SQL Server na platformie Azure, zobacz [migracja do usługi Azure SQL Database](sql-database-single-database-migrate.md).
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](sql-database-features.md).
