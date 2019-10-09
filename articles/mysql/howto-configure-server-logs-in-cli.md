---
title: Uzyskiwanie dostępu do dzienników wolnych zapytań w Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym artykule opisano, jak uzyskać dostęp do dzienników wolnych zapytań w Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 0ab4162d11642ec7df53040bd744711002227497
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030639"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurowanie i uzyskiwanie dostępu do dzienników wolnych zapytań za pomocą interfejsu wiersza polecenia platformy Azure
Możesz pobrać Azure Database for MySQL dzienników wolnych zapytań za pomocą interfejsu wiersza polecenia platformy Azure, narzędzia wiersza poleceń platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- [Serwer Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub Azure Cloud Shell w przeglądarce

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Serwer można skonfigurować tak, aby mógł uzyskać dostęp do dziennika wolnych zapytań programu MySQL, wykonując następujące czynności:
1. Włącz rejestrowanie wolnych zapytań, ustawiając parametr **"wolno @ no__t-1query @ no__t-2log** " na wartość włączone.
2. Dostosuj inne parametry, takie jak **Long @ no__t-1query @ no__t-2time** i **log @ no__t-4slow @ no__t-5admin @ no__t-6statements**.

Aby dowiedzieć się, jak ustawić wartości tych parametrów za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [How to configure Server Parameters](howto-configure-server-parameters-using-cli.md).

Na przykład następujące polecenie interfejsu wiersza polecenia włącza dziennik wolnych zapytań, ustawia długi czas zapytania na 10 sekund, a następnie wyłącza rejestrowanie powolnej instrukcji administratora. Na koniec wyświetla listę opcji konfiguracji dla przeglądu.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Wyświetlanie listy dzienników dla Azure Database for MySQL Server
Aby wyświetlić listę dostępnych wolnych plików dziennika zapytań dla serwera, uruchom polecenie [AZ MySQL Server-Logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) .

Możesz wyświetlić listę plików dziennika dla serwera **mydemoserver.MySQL.Database.Azure.com** w **obszarze Grupa zasobów**. Następnie należy skierować listę plików dziennika do pliku tekstowego o nazwie **log @ no__t-1files\_list.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Pobierz dzienniki z serwera
Za pomocą polecenia [AZ MySQL Server-Logs Download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) można pobrać pojedyncze pliki dziennika dla serwera. 

Użyj poniższego przykładu, aby pobrać określony plik dziennika dla serwera **mydemoserver.MySQL.Database.Azure.com** w **obszarze Grupa zasobów zasobu do** środowiska lokalnego.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [wolnych dziennikach zapytań w Azure Database for MySQL](concepts-server-logs.md).
