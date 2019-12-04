---
title: Dostęp do dzienników wolnych zapytań — interfejs wiersza polecenia platformy Azure — Azure Database for MariaDB
description: W tym artykule opisano, jak uzyskać dostęp do wolnych dzienników w Azure Database for MariaDB przy użyciu narzędzia wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 32e73835732538813f90de5cb737429373c3762a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767385"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurowanie i uzyskiwanie dostępu do dzienników wolnych zapytań za pomocą interfejsu wiersza polecenia platformy Azure
Możesz pobrać Azure Database for MariaDB dzienników wolnych zapytań za pomocą interfejsu wiersza polecenia platformy Azure, narzędzia wiersza poleceń platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- [Serwer Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub Azure Cloud Shell w przeglądarce

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Konfigurowanie rejestrowania dla Azure Database for MariaDB
Serwer można skonfigurować tak, aby mógł uzyskać dostęp do dziennika wolnych zapytań MariaDB, wykonując następujące czynności:
1. Włącz rejestrowanie, ustawiając wartość parametru **dziennika powolnej\_kwerendy\_log** na wartość włączone.
2. Dostosuj inne parametry, takie jak **long\_query\_Time** i **log\_wolno\_administracyjnych\_instrukcji**.

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

Możesz wyświetlić listę plików dziennika dla serwera **mydemoserver.MariaDB.Database.Azure.com** w **obszarze Grupa zasobów**. Następnie należy skierować listę plików dziennika do pliku tekstowego o nazwie **log\_files\_list. txt**.
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
