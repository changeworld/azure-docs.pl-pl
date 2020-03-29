---
title: Uzyskiwanie dostępu do wolnych dzienników zapytań — narzędzie interfejsu wiersza polecenia platformy Azure — usługa Azure Database for MySQL
description: W tym artykule opisano sposób uzyskiwania dostępu do dzienników wolnych zapytań w usłudze Azure Database dla mysql przy użyciu interfejsu wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 93840af61a69599447588be01869a20290b2db94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062479"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurowanie wolnych dzienników kwerend i uzyskiwanie do nich dostępu przy użyciu interfejsu wiersza polecenia platformy Azure
Dzienniki wolnych zapytań usługi Azure Database for MySQL można pobrać przy użyciu narzędzia wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebujesz:
- [Usługa Azure Database dla serwera MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub usługa Azure Cloud Shell w przeglądarce

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Serwer można skonfigurować tak, aby miał dostęp do dziennika wolnych zapytań MySQL, wykonując następujące czynności:
1. Włącz rejestrowanie wolnych zapytań, ustawiając parametr **dziennika kwerendy\_powolnej\_** na ON.
2. Dostosuj inne parametry, takie jak **długi\_czas zapytania\_** i **rejestrowanie\_powolnych\_instrukcji administratora\_**.

Aby dowiedzieć się, jak ustawić wartość tych parametrów za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Jak skonfigurować parametry serwera](howto-configure-server-parameters-using-cli.md).

Na przykład następujące polecenie interfejsu wiersza polecenia włącza dziennik kwerend powolnych, ustawia długi czas kwerendy na 10 sekund, a następnie wyłącza rejestrowanie instrukcji powolnego administratora. Na koniec wyświetla listę opcji konfiguracji dla przeglądu.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Listy dzienników dla usługi Azure Database dla serwera MySQL
Aby wyświetlić listę dostępnych wolnych plików dziennika zapytań dla serwera, uruchom polecenie [listy az mysql server-logs.](/cli/azure/mysql/server-logs#az-mysql-server-logs-list)

Pliki dziennika dla serwera **można wyświetlić mydemoserver.mysql.database.azure.com** w grupie zasobów **myresourcegroup**. Następnie przekieruj listę plików dziennika do pliku tekstowego o nazwie **log\_files\_list.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Pobieranie dzienników z serwera
Za pomocą polecenia [pobierania az mysql server-logs](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) można pobrać poszczególne pliki dziennika dla swojego serwera. 

Poniższy przykład służy do pobierania określonego pliku dziennika dla serwera **mydemoserver.mysql.database.azure.com** w ramach grupy zasobów **myresourcegroup** do środowiska lokalnego.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [wolnych dziennikach zapytań w usłudze Azure Database for MySQL](concepts-server-logs.md).
