---
title: Uzyskiwanie dostępu do dzienników serwera w Azure Database for MariaDB przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym artykule opisano sposób uzyskiwania dostępu do dzienników serwera w Azure Database for MariaDB przy użyciu narzędzia wiersza polecenia platformy Azure.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: ffc724ef5133ee25643a966d2b6d8448a4c3a920
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72023616"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurowanie i uzyskiwanie dostępu do dzienników serwera przy użyciu interfejsu wiersza polecenia platformy Azure
Dzienniki serwera Azure Database for MariaDB można pobrać przy użyciu interfejsu wiersza polecenia platformy Azure, narzędzia wiersza poleceń platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- [Serwer Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub Azure Cloud Shell w przeglądarce

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Konfigurowanie rejestrowania dla Azure Database for MariaDB
Serwer można skonfigurować tak, aby mógł uzyskać dostęp do dziennika wolnych zapytań MariaDB, wykonując następujące czynności:
1. Włącz rejestrowanie, ustawiając parametr **"wolno @ no__t-1query @ no__t-2log** " na wartość włączone.
2. Dostosuj inne parametry, takie jak **Long @ no__t-1query @ no__t-2time** i **log @ no__t-4slow @ no__t-5admin @ no__t-6statements**.

Aby dowiedzieć się, jak ustawić wartości tych parametrów za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [How to configure Server Parameters](howto-configure-server-parameters-cli.md).

Na przykład następujące polecenie interfejsu wiersza polecenia włącza dziennik wolnych zapytań, ustawia długi czas zapytania na 10 sekund, a następnie wyłącza rejestrowanie powolnej instrukcji administratora. Na koniec wyświetla listę opcji konfiguracji dla przeglądu.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Wyświetlanie listy dzienników dla Azure Database for MariaDB Server
Aby wyświetlić listę dostępnych wolnych plików dziennika zapytań dla serwera, uruchom polecenie [AZ MariaDB Server-Logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) .

Możesz wyświetlić listę plików dziennika dla serwera **mydemoserver.MariaDB.Database.Azure.com** w **obszarze Grupa zasobów**. Następnie należy skierować listę plików dziennika do pliku tekstowego o nazwie **log @ no__t-1files\_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Pobierz dzienniki z serwera
Za pomocą polecenia [AZ MariaDB Server-Logs Download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) można pobrać pojedyncze pliki dziennika dla serwera.

Użyj poniższego przykładu, aby pobrać określony plik dziennika dla serwera **mydemoserver.MariaDB.Database.Azure.com** w **obszarze Grupa zasobów zasobu do** środowiska lokalnego.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [wolnych dziennikach zapytań w Azure Database for MariaDB](concepts-server-logs.md).
