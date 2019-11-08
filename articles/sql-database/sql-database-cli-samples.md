---
title: Przykłady skryptów interfejsu wiersza polecenia platformy Azure
description: Przykłady skryptów interfejsu wiersza polecenia platformy Azure służących do tworzenia serwerów, pul elastycznych, baz danych i zapór usługi Azure SQL Database oraz zarządzania nimi.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 023651d61f891c5a73eee234a14306add838bf98
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821842"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla bazy danych Azure SQL Database

Bazę danych Azure SQL Database można skonfigurować za pomocą <a href="/cli/azure">wiersza polecenia platformy Azure</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="single-database--elastic-poolstabsingle-database"></a>[Jedna baza danych & pule elastyczne](#tab/single-database)

Poniższa tabela zawiera linki do przykładów skryptów interfejsu wiersza polecenia platformy Azure SQL Database.

| |  |
|---|---|
|**Tworzenie pojedynczej bazy danych i elastycznej puli**||
| [Tworzenie pojedynczej bazy danych i konfigurowanie reguły zapory](scripts/sql-database-create-and-configure-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Ten przykładowy skrypt interfejsu wiersza polecenia tworzy pojedynczą bazę danych Azure SQL Database i konfiguruje regułę zapory na poziomie serwera. |
| [Tworzenie pul elastycznych i przenoszenie baz danych w puli](scripts/sql-database-move-database-between-pools-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Ten przykładowy skrypt interfejsu wiersza polecenia tworzy elastyczne pule SQL, przenosi bazy danych Azure SQL Database w puli i zmienia rozmiary obliczeniowe.|
|**Skalowanie pojedynczej bazy danych i puli elastycznej**||
| [Skalowanie pojedynczej bazy danych](scripts/sql-database-monitor-and-scale-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Ten przykładowy skrypt interfejsu wiersza polecenia skaluje pojedynczą bazę danych Azure SQL Database do innego rozmiaru obliczeniowego po utworzeniu zapytania o informacje na temat rozmiaru bazy danych. |
| [Skalowanie puli elastycznej](scripts/sql-database-scale-pool-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Ten przykładowy skrypt interfejsu wiersza polecenia skaluje pulę elastyczną SQL do innego rozmiaru obliczeniowego.  |
|**Grupy trybu failover**||
| [Dodaj pojedynczą bazę danych do grupy trybu failover](scripts/sql-database-add-single-db-to-failover-group-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Ten skrypt interfejsu wiersza polecenia tworzy bazę danych i grupę trybu failover, dodaje bazę danych do grupy trybu failover i testuje tryb failover na serwerze pomocniczym.|
|||

Dowiedz się więcej o [interfejsie API interfejsu wiersza platformy Azure pojedynczej bazy danych](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

## <a name="managed-instancetabmanaged-instance"></a>[Wystąpienie zarządzane](#tab/managed-instance)

Poniższa tabela zawiera linki do przykładów skryptów interfejsu wiersza polecenia platformy Azure dla wystąpienia zarządzanego usługi Azure SQL Database.

| |  |
|---|---|
| [Tworzenie wystąpienia zarządzanego](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/) | Ten skrypt interfejsu wiersza polecenia ilustruje sposób tworzenia wystąpienia zarządzanego. |
| [Aktualizowanie wystąpienia zarządzanego](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/) | Ten skrypt interfejsu wiersza polecenia ilustruje sposób aktualizowania wystąpienia zarządzanego. |
| [Przenoszenie bazy danych do innego wystąpienia zarządzanego](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) | Ten skrypt interfejsu wiersza polecenia ilustruje przywracanie kopii zapasowej bazy danych z jednego wystąpienia do innego. |
|||

Dowiedz się więcej o [interfejsie API interfejsu wiersza polecenia platformy Azure wystąpienia zarządzanego](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances) i znajdź [tutaj dodatkowe przykłady](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

---
