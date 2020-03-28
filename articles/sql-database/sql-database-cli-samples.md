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
ms.openlocfilehash: 459a5ea69e11a8614c572f68fce039b6cabbb1ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061712"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla bazy danych Azure SQL Database

Bazę danych Azure SQL Database można skonfigurować za pomocą <a href="/cli/azure">wiersza polecenia platformy Azure</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

# <a name="single-database--elastic-pools"></a>[Pojedyncza baza danych & pulach elastycznych](#tab/single-database)

| | |
|---|---|
|**Tworzenie pojedynczej bazy danych i elastycznej puli**||
| [Tworzenie pojedynczej bazy danych i konfigurowanie reguły zapory](scripts/sql-database-create-and-configure-database-cli.md) | Tworzy bazę danych SQL platformy Azure i konfiguruje regułę zapory na poziomie serwera. |
| [Tworzenie pul elastycznych i przenoszenie baz danych w puli](scripts/sql-database-move-database-between-pools-cli.md) | Tworzy pule elastyczne SQL, przenosi połączone bazy danych SQL platformy Azure i zmienia rozmiary obliczeń. |
|**Skalowanie pojedynczej bazy danych i puli elastycznej**||
| [Skalowanie pojedynczej bazy danych](scripts/sql-database-monitor-and-scale-database-cli.md) | Skaluje bazę danych SQL platformy Azure do innego rozmiaru obliczeń po querying informacji o rozmiarze bazy danych. |
| [Skalowanie puli elastycznej](scripts/sql-database-scale-pool-cli.md) | Skaluje pulę elastyczną SQL do innego rozmiaru obliczeń. |
|**Konfigurowanie replikacji geograficznej i trybu failover**||
| [Dodawanie pojedynczej bazy danych do grupy trybu failover](scripts/sql-database-add-single-db-to-failover-group-cli.md)| Tworzy bazę danych i grupę trybu failover, dodaje bazę danych do grupy trybu failover, a następnie testuje przegrupowanie w pracy awaryjnej na serwerze pomocniczym. |
| [Konfigurowanie grupy trybu failover dla puli elastycznej](scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Tworzy bazę danych, dodaje ją do puli elastycznej, dodaje pulę elastyczną do grupy trybu failover, a następnie testuje przełączenie w błąd do serwera pomocniczego. |
| [Konfigurowanie pojedynczej bazy danych i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Konfiguruje aktywną replikację geograficzną dla bazy danych SQL platformy Azure i przejdzie ją do repliki pomocniczej. |
| [Konfigurowanie bazy danych w puli i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Konfiguruje aktywną replikację geograficzną dla bazy danych SQL platformy Azure w puli elastycznej SQL, a następnie przebłaja ją do repliki pomocniczej. |
| **Inspekcja i wykrywanie zagrożeń** |
| [Konfigurowanie inspekcji i wykrywanie zagrożeń](scripts/sql-database-auditing-and-threat-detection-cli.md)| Konfiguruje zasady inspekcji i wykrywania zagrożeń dla bazy danych SQL platformy Azure. |
| **Tworzenie kopii zapasowych, przywracanie, kopiowanie i importowanie bazy danych**||
| [Tworzenie kopii zapasowych bazy danych](scripts/sql-database-backup-database-cli.md)| Kopie zapasowe bazy danych SQL platformy Azure w kopii zapasowej magazynu platformy Azure. |
| [Przywracanie bazy danych](scripts/sql-database-restore-database-cli.md)| Przywraca bazę danych SQL platformy Azure z geograficznie nadmiarowej kopii zapasowej i przywraca usuniętą bazę danych SQL platformy Azure do najnowszej kopii zapasowej. |
| [Kopiowanie bazy danych na nowy serwer](scripts/sql-database-copy-database-to-new-server-cli.md) | Tworzy kopię istniejącej bazy danych SQL platformy Azure w nowym serwerze SQL platformy Azure. |
| [Importowanie bazy danych z pliku BACPAC](scripts/sql-database-import-from-bacpac-cli.md)| Importuje bazę danych do serwera SQL platformy Azure z pliku *bacpac.* |
|||

Dowiedz się więcej o [interfejsie API interfejsu wiersza polecenia platformy Azure dla pojedynczej bazy danych.](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)

# <a name="managed-instance"></a>[Wystąpienie zarządzane](#tab/managed-instance)

Poniższa tabela zawiera linki do przykładów skryptów interfejsu wiersza polecenia platformy Azure dla wystąpienia zarządzanego usługi Azure SQL Database.

| | |
|---|---|
| **Tworzenie wystąpienia zarządzanego**||
| [Tworzenie wystąpienia zarządzanego](scripts/sql-database-create-configure-managed-instance-cli.md)| Tworzy wystąpienie zarządzane. |
| **Konfigurowanie funkcji Transparent Data Encryption (TDE)**||
| [Zarządzanie przezroczystym szyfrowaniem danych w wystąpieniu zarządzanym przy użyciu usługi Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Konfiguruje przezroczyste szyfrowanie danych (TDE) w wystąpieniu zarządzanym sql platformy Azure przy użyciu usługi Azure Key Vault w różnych kluczowych scenariuszach. |
|**Konfigurowanie grupy trybu failover**||
| [Konfigurowanie grupy trybu failover dla wystąpienia zarządzanego](scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Tworzy dwa wystąpienia zarządzane, dodaje je do grupy trybu failover, a następnie testuje przegrupowanie w pełnienia pracy awaryjnej z podstawowego wystąpienia zarządzanego do pomocniczego wystąpienia zarządzanego. |
|||

Dodatkowe przykłady wystąpienia zarządzanego można znaleźć w przykładach [tworzenia,](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/) [aktualizowania,](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/) [przenoszenia bazy danych](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/), [pracy ze](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) skryptami.

Dowiedz się więcej o [interfejsie API interfejsu wiersza polecenia platformy Azure.](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances)

---
