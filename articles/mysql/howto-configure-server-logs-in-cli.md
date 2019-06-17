---
title: Dzienniki wolnych zapytań dostępu w usłudze Azure Database for MySQL przy użyciu wiersza polecenia platformy Azure
description: W tym artykule opisano, jak dostęp do dzienników wolnych zapytań w usłudze Azure Database for MySQL przy użyciu wiersza polecenia platformy Azure.
author: andrela
ms.author: ajlam
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/219
ms.openlocfilehash: 740dbce579fba6347b1a7f2cfc6bcae40d3503ab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052710"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurowanie i dostęp do dzienników wolnych zapytań przy użyciu wiersza polecenia platformy Azure
Azure Database for MySQL — dzienniki wolnych zapytań można pobrać przy użyciu wiersza polecenia platformy Azure, narzędzie wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków w tym przewodniku, potrzebne są:
- [Azure Database dla serwera MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub usługi Azure Cloud Shell w przeglądarce

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Można skonfigurować serwer do dostępu do dzienników wolnych zapytań MySQL, wykonując następujące czynności:
1. Włącz rejestrowanie wolnych zapytań, ustawiając **powolne\_zapytania\_dziennika** parametru na wartość ON.
2. Dostosuj inne parametry, takie jak **długie\_zapytania\_czasu** i **dziennika\_powolne\_administratora\_instrukcji**.

Aby dowiedzieć się, jak można ustawić wartości tych parametrów, za pomocą wiersza polecenia platformy Azure, zobacz [jak konfigurować parametry serwera](howto-configure-server-parameters-using-cli.md).

Na przykład następujące polecenie interfejsu wiersza polecenia platformy włącza w dzienniku wolnych zapytań, ustawia długi czas wykonywania zapytania na 10 sekund i następnie wyłącza rejestrowanie instrukcji powolne administratora. Na koniec Wyświetla listę opcji konfiguracji do przejrzenia.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Dzienniki listy dla usługi Azure Database dla serwera MySQL
Aby wyświetlić listę plików dziennika dostępnych wolnych zapytań serwera, uruchom [az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) polecenia.

Możesz wyświetlić listę plików dziennika dla serwera **mydemoserver.mysql.database.azure.com** w grupie zasobów **myresourcegroup**. Następnie przekierowanie do listy plików dziennika do pliku tekstowego o nazwie **dziennika\_pliki\_lista.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Pobierz dzienniki z serwera
Za pomocą [Pobierz az mysql server-logs](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) polecenia, możesz pobrać osobnych plikach dziennika dla serwera. 

Skorzystaj z następującego przykładu, aby pobrać określonego pliku dziennika dla serwera **mydemoserver.mysql.database.azure.com** w grupie zasobów **myresourcegroup** do środowiska lokalnego.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [wolnych zapytań dzienników w usłudze Azure Database for MySQL](concepts-server-logs.md).
