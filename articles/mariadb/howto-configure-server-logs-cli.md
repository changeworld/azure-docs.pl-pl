---
title: Dostęp do wolnych dzienników zapytań — interfejsu wiersza polecenia platformy Azure — usługa Azure Database dla bazy mariadb
description: W tym artykule opisano sposób uzyskiwania dostępu do powolnych dzienników w usłudze Azure Database dla MariaDB przy użyciu narzędzia wiersza polecenia interfejsu wiersza polecenia platformy Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: f33a02ff0e287c135a7d63277cf3d8d3c0cd13d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527660"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurowanie wolnych dzienników kwerend i uzyskiwanie do nich dostępu przy użyciu interfejsu wiersza polecenia platformy Azure
Możesz pobrać dzienniki wolnych zapytań usługi Azure Database dla MariaDB przy użyciu narzędzia azure cli, narzędzia wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebujesz:
- [Usługa Azure Database dla serwera MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub usługa Azure Cloud Shell w przeglądarce

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Konfigurowanie rejestrowania dla usługi Azure Database dla bazy danych MariaDB
Serwer można skonfigurować tak, aby miał dostęp do dziennika kwerend wolnych MariaDB, wykonując następujące kroki:
1. Włącz rejestrowanie, ustawiając parametr **dziennika kwerendy\_powolnej\_** na ON.
2. Dostosuj inne parametry, takie jak **długi\_czas zapytania\_** i **rejestrowanie\_powolnych\_instrukcji administratora\_**.

Aby dowiedzieć się, jak ustawić wartość tych parametrów za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Jak skonfigurować parametry serwera](howto-configure-server-parameters-cli.md).

Na przykład następujące polecenie interfejsu wiersza polecenia włącza dziennik kwerend powolnych, ustawia długi czas kwerendy na 10 sekund, a następnie wyłącza rejestrowanie instrukcji powolnego administratora. Na koniec wyświetla listę opcji konfiguracji dla przeglądu.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Listy dzienników dla usługi Azure Database dla serwera MariaDB
Aby wyświetlić listę dostępnych wolnych plików dziennika zapytań dla serwera, uruchom polecenie [listy dzienników serwera az mariadb.](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list)

Pliki dziennika dla serwera **można wyświetlić mydemoserver.mariadb.database.azure.com** w grupie zasobów **myresourcegroup**. Następnie przekieruj listę plików dziennika do pliku tekstowego o nazwie **log\_files\_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Pobieranie dzienników z serwera
Za pomocą polecenia [pobierania az mariadb server-logs](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) można pobrać poszczególne pliki dziennika dla swojego serwera.

Poniższy przykład służy do pobierania określonego pliku dziennika dla serwera **mydemoserver.mariadb.database.azure.com** w ramach grupy zasobów **myresourcegroup** do środowiska lokalnego.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [wolnych dziennikach zapytań w usłudze Azure Database for MariaDB](concepts-server-logs.md).
