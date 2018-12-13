---
title: Dokumentacja interfejsu API zarządzania dla wystąpienia zarządzanego Azure SQL Database | Dokumentacja firmy Microsoft
description: Więcej informacji na temat tworzenia i zarządzania nimi w wystąpieniach zarządzanych bazy danych SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/12/2018
ms.openlocfilehash: 7fb6917e129c015536143a707fd2a89fc5423a99
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323084"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Dokumentacja interfejsu API zarządzanego w wystąpieniach zarządzanych bazy danych SQL Azure

Można tworzyć i zarządzać usługi Azure SQL wystąpieniach zarządzanych bazy danych przy użyciu witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST i języka Transact-SQL. W tym artykule można znaleźć Przegląd funkcji i interfejsu API, który umożliwia tworzenie i konfigurowanie wystąpienia zarządzanego.

## <a name="azure-portal-create-a-managed-instance"></a>Witryna Azure portal: Tworzenie wystąpienia zarządzanego

Aby uzyskać szybki start omawiający Tworzenie wystąpienia zarządzanego bazy danych SQL Azure, zobacz [Szybki Start: Tworzenie wystąpienia zarządzanego Azure SQL Database](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-a-managed-instance"></a>Program PowerShell: Tworzenie i zarządzanie nimi wystąpienia zarządzanego

Aby utworzyć i zarządzać nimi, Azure SQL server, baz danych i zapory za pomocą programu Azure PowerShell, użyj następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić programu PowerShell, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

> [!TIP]
> W przypadku skryptów przykład programu PowerShell, zobacz [skryptu szybki start: Tworzenie wystąpienia zarządzanego Azure SQL przy użyciu programu PowerShell biblioteki](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Polecenie cmdlet | Opis |
| --- | --- |
|[Nowe AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlinstance)|Tworzy wystąpienie zarządzane usługi Azure SQL Database |
|[Get-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/Get-AzureRmSqlInstance)|Zwraca informacje na temat wystąpienia zarządzanego Azure SQL|
|[Zestaw AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/Set-AzureRmSqlInstance)|Ustawia właściwości dla wystąpienia zarządzanego bazy danych SQL Azure|
|[Usuń AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/Remove-AzureRmSqlInstance)|Usuwa wystąpienie zarządzane bazy danych Azure SQL|

## <a name="azure-cli-manage-logical-servers-and-databases"></a>Interfejs wiersza polecenia platformy Azure: Zarządzanie serwerów logicznych i baz danych

Tworzenie i zarządzanie nimi, Azure SQL server, baz danych i zapory za pomocą [wiersza polecenia platformy Azure](/cli/azure), należy użyć następującego [wystąpienia zarządzanego SQL interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/mi) poleceń. Używaj usługi [Cloud Shell](/azure/cloud-shell/overview), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows.

> [!TIP]
> W przewodniku Szybki Start wiersza polecenia platformy Azure, zobacz [Praca z wystąpienia zarządzanego SQL przy użyciu wiersza polecenia platformy Azure](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).
>

| Polecenie cmdlet | Opis |
| --- | --- |
|[Tworzenie wystąpienia zarządzanego sql az](https://docs.microsoft.com/cli/azure/sql/db#az-sql-mi-create) |Tworzy wystąpienie zarządzane|
|[AZ sql mi listy](https://docs.microsoft.com/cli/azure/sql/db#az-sql-mi-list)|Wyświetla dostępne wystąpienia zarządzane|
|[Pokaż mi sql az](/cli/azure/sql/db#az-sql-mi-show)|Uzyskaj szczegółowe informacje dla wystąpienia zarządzanego|
|[Aktualizacja wystąpienia zarządzanego sql az](/cli/azure/sql/db#az-sql-mi-update)|Aktualizuje wystąpienia zarządzanego|
|[Usuwanie wystąpienia zarządzanego sql az](/cli/azure/sql/db#az-sql-mi-delete)|Usuwa wystąpienia zarządzanego|

## <a name="transact-sql-manage-logical-servers-and-databases"></a>Języka Transact-SQL: Zarządzanie serwerów logicznych i baz danych

Tworzenie i zarządzanie nimi bazy danych Azure SQL Database Managed Instance, po utworzeniu wystąpienia zarządzanego, użyj następujących poleceń języka T-SQL. Można wydać tych poleceń w witrynie Azure portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [usługi Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is). [Visual Studio Code](https://code.visualstudio.com/docs), lub innego programu, który mogą połączyć się z serwerem usługi Azure SQL Database i przekazać polecenia języka Transact-SQL.

> [!TIP]
> Przewodniki Szybki Start przedstawiający, należy skonfigurować i nawiązać połączenie z wystąpienia zarządzanego przy użyciu programu SQL Server Management Studio na Windows firmy Microsoft można znaleźć [Szybki Start: Konfigurowanie maszyny Wirtualnej platformy Azure, aby nawiązać połączenie z wystąpienia usługi Azure SQL Database Managed](sql-database-managed-instance-configure-vm.md) i [Szybki Start: Konfigurowanie połączenia typu punkt lokacja do wystąpienia usługi Azure SQL Database Managed ze środowiska lokalnego](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Nie można utworzyć ani usunąć wystąpienia zarządzanego przy użyciu języka Transact-SQL.

| Polecenie | Opis |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Tworzy nową bazę danych wystąpienia zarządzanego. Musisz mieć połączenie z główną bazą danych, aby utworzyć nową bazę danych.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Modyfikuje wystąpienia zarządzanego Azure SQL database.|

## <a name="rest-api-manage-logical-servers-and-databases"></a>INTERFEJS API REST: Zarządzanie serwerów logicznych i baz danych

Tworzenie i zarządzanie nimi wystąpienia zarządzanego Azure SQL Database, należy użyć tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Zarządzane wystąpienia — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Tworzy lub aktualizuje wystąpienia zarządzanego.|
|[Usuwanie wystąpienia zarządzanego —](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Usuwa wystąpienia zarządzanego.|
|[Wystąpienia zarządzane — Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Pobiera wystąpienie zarządzane.|
|[Zarządzane wystąpienia — lista](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Zwraca listę wystąpień zarządzanych w ramach subskrypcji.|
|[Zarządzane wystąpienia — listy według grupy zasobów](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Zwraca listę wystąpień zarządzanych w grupie zasobów.|
|[Wystąpienia zarządzane — aktualizacja](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Aktualizuje wystąpienia zarządzanego.|

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o migracji bazy danych programu SQL Server na platformie Azure, zobacz [migracja do usługi Azure SQL Database](sql-database-cloud-migrate.md).
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](sql-database-features.md).
