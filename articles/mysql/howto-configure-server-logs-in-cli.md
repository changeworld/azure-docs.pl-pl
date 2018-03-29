---
title: Dostęp do dzienników serwera w bazie danych Azure dla programu MySQL przy użyciu wiersza polecenia platformy Azure
description: W tym artykule opisano sposób dostępu do dzienników serwera w bazie danych Azure dla programu MySQL za pomocą narzędzia wiersza polecenia wiersza polecenia platformy Azure.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 037f631b3b23e66f1ed54106cc5be14a809b2e22
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurowanie i dostęp do dzienników serwera przy użyciu wiersza polecenia platformy Azure
Za pomocą interfejsu wiersza polecenia Azure, narzędzie wiersza polecenia platformy Azure, możesz pobrać bazy danych Azure do dzienników serwera MySQL.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków opisanych ten przewodnik, potrzebne są:
- [Azure bazy danych MySQL serwera](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli) lub powłoki chmury Azure w przeglądarce

## <a name="configure-logging-for-azure-database-for-mysql"></a>Konfigurowanie rejestrowania w bazie danych Azure dla programu MySQL
Można skonfigurować serwer do dostępu do dziennika powolne zapytania MySQL, wykonując następujące czynności:
1. Należy włączyć rejestrowanie przez ustawienie **powolne\_zapytania\_dziennika** parametru na wartość on.
2. Dostosuj innych parametrów, takich jak **długi\_zapytania\_czasu** i **dziennika\_powolne\_admin\_instrukcje**.

Aby dowiedzieć się, jak można ustawić wartości tych parametrów, za pomocą interfejsu wiersza polecenia Azure, zobacz [sposobu konfigurowania parametrów serwera](howto-configure-server-parameters-using-cli.md). 

Na przykład następujące polecenia interfejsu wiersza polecenia powoduje włączenie dziennika powolne zapytania, ustawia czas długiej kwerendy do 10 sekund i następnie wyłącza rejestrowanie instrukcji powolne administratora. Ponadto zawiera listę opcji konfiguracji dla zapoznania się z nimi.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Lista dzienników bazy danych Azure MySQL serwera
Aby wyświetlić pliki dziennika dostępne na serwerze, uruchom [az mysql dzienniki serwera listy](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) polecenia.

Można wyświetlić listę plików dziennika dla serwera **mydemoserver.mysql.database.azure.com** w grupie zasobów **myresourcegroup**. Następnie przekierowanie do listy plików dziennika w pliku tekstowym o nazwie **dziennika\_pliki\_lista.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Pobierz dzienniki z serwera
Z [Pobierz dzienniki serwera az mysql](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) polecenia, można pobrać osobnych plikach dziennika dla serwera. 

Skorzystaj z następującego przykładu, aby pobrać określonego pliku dziennika dla serwera **mydemoserver.mysql.database.azure.com** w grupie zasobów **myresourcegroup** do środowiska lokalnego.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [dzienników serwera w bazie danych Azure dla programu MySQL](concepts-server-logs.md).
