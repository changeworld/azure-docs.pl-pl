---
title: Dzienniki serwera dostępu w usłudze Azure Database dla serwera MariaDB przy użyciu wiersza polecenia platformy Azure
description: W tym artykule opisano, jak dostęp do dzienników serwera w usłudze Azure Database dla serwera MariaDB przy użyciu narzędzia wiersza polecenia wiersza polecenia platformy Azure.
services: mariadb
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/10/2018
ms.openlocfilehash: b9ae07b88164a830598db791d61b77a6abb7b1df
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2018
ms.locfileid: "51516219"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurowanie i dostęp do dzienników serwera przy użyciu wiersza polecenia platformy Azure
Azure Database for dzienniki serwera MariaDB można pobrać przy użyciu wiersza polecenia platformy Azure, narzędzie wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków w tym przewodniku, potrzebne są:
- [Azure Database dla serwera MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub usługi Azure Cloud Shell w przeglądarce

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Konfigurowanie rejestrowania dla usługi Azure Database dla serwera MariaDB
Można skonfigurować serwer do dostępu do dzienników wolnych zapytań MariaDB, wykonując następujące czynności:
1. Włącz rejestrowanie, ustawiając **powolne\_zapytania\_dziennika** parametru na wartość ON.
2. Dostosuj inne parametry, takie jak **długie\_zapytania\_czasu** i **dziennika\_powolne\_administratora\_instrukcji**.

Aby dowiedzieć się, jak można ustawić wartości tych parametrów, za pomocą wiersza polecenia platformy Azure, zobacz [jak konfigurować parametry serwera](howto-configure-server-parameters-cli.md).

Na przykład następujące polecenie interfejsu wiersza polecenia platformy włącza w dzienniku wolnych zapytań, ustawia długi czas wykonywania zapytania na 10 sekund i następnie wyłącza rejestrowanie instrukcji powolne administratora. Na koniec Wyświetla listę opcji konfiguracji do przejrzenia.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Dzienniki listy dla usługi Azure Database dla serwera MariaDB
Aby wyświetlić listę plików dziennika dostępne na serwerze, uruchom [az mariadb server-logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) polecenia.

Możesz wyświetlić listę plików dziennika dla serwera **mydemoserver.mariadb.database.azure.com** w grupie zasobów **myresourcegroup**. Następnie przekierowanie do listy plików dziennika do pliku tekstowego o nazwie **dziennika\_pliki\_lista.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Pobierz dzienniki z serwera
Za pomocą [pobrać dzienniki serwera az mariadb](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) polecenia, możesz pobrać osobnych plikach dziennika dla serwera.

Skorzystaj z następującego przykładu, aby pobrać określonego pliku dziennika dla serwera **mydemoserver.mariadb.database.azure.com** w grupie zasobów **myresourcegroup** do środowiska lokalnego.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [dzienników serwera w usłudze Azure Database dla serwera MariaDB](concepts-server-logs.md).
