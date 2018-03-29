---
title: Konfigurowanie i dostępu do dzienników serwera dla PostgreSQL przy użyciu wiersza polecenia platformy Azure
description: W tym artykule opisano sposób konfigurowania i uzyskiwać dostęp do dzienników serwera w bazie danych Azure dla PostgreSQL przy użyciu wiersza polecenia wiersza polecenia platformy Azure.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 951dcca562c08698b4ce4528d005fc91152ea337
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurowanie i dostęp do dzienników serwera przy użyciu wiersza polecenia platformy Azure
Dzienniki błędów serwera PostgreSQL można pobrać przy użyciu interfejsu wiersza polecenia (Azure CLI). Jednak nie jest obsługiwany dostęp do dzienników transakcji. 

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków opisanych ten przewodnik, potrzebne są:
- [Azure bazy danych dla serwera PostgreSQL](quickstart-create-server-database-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli) narzędzia wiersza polecenia lub powłoki chmury Azure w przeglądarce

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Konfigurowanie rejestrowania w bazie danych Azure dla PostgreSQL
Można skonfigurować serwera dostępu do dzienników kwerend i dzienników błędów. Dzienniki błędów mogą mieć informacje automatycznego próżni, połączenia i punktu kontrolnego.
1. Należy włączyć rejestrowanie.
2. Aby włączyć rejestrowanie zapytań, należy zaktualizować **dziennika\_instrukcji** i **dziennika\_min\_czas trwania\_instrukcji**.
3. Okres przechowywania aktualizacji.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie parametry konfiguracji serwera](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Lista dzienników bazy danych Azure PostgreSQL serwera
Aby wyświetlić pliki dziennika dostępne na serwerze, uruchom [az postgres dzienniki serwera listy](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) polecenia.

Można wyświetlić listę plików dziennika dla serwera **mydemoserver.postgres.database.azure.com** w grupie zasobów **myresourcegroup**. Następnie przekierowanie do listy plików dziennika w pliku tekstowym o nazwie **dziennika\_pliki\_lista.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Pobierz dzienniki lokalnie z serwera
Z [Pobierz dzienniki serwera az postgres](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) polecenia, można pobrać osobnych plikach dziennika dla serwera. 

Skorzystaj z następującego przykładu, aby pobrać określonego pliku dziennika dla serwera **mydemoserver.postgres.database.azure.com** w grupie zasobów **myresourcegroup** do środowiska lokalnego.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Kolejne kroki
- Aby dowiedzieć się więcej na temat dzienniki serwera, zobacz [dzienników serwera w bazie danych Azure PostgreSQL](concepts-server-logs.md).
- Aby uzyskać więcej informacji na temat parametrów serwera, zobacz [dostosować parametry konfiguracji serwera przy użyciu interfejsu wiersza polecenia Azure](howto-configure-server-parameters-using-cli.md).
