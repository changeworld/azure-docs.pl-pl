---
title: Konfigurowanie i dostęp do dzienników serwera PostgreSQL za pomocą wiersza polecenia platformy Azure
description: W tym artykule opisano sposób konfigurowania i dostęp do dzienników serwera w usłudze Azure Database for PostgreSQL przy użyciu wiersza polecenia wiersza polecenia platformy Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 3ca77c1ffa5f5a3f384009299701f4aa674baf59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60421196"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurowanie i dostęp do dzienników serwera przy użyciu wiersza polecenia platformy Azure
Możesz pobrać dzienniki błędów serwera PostgreSQL za pomocą interfejsu wiersza polecenia (CLI platformy Azure). Jednak dostęp do dzienników transakcji nie jest obsługiwane. 

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków w tym przewodniku, potrzebne są:
- [Usługa Azure Database for postgresql w warstwie serwera](quickstart-create-server-database-azure-cli.md)
- [Wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) narzędzie wiersza polecenia lub programu Azure Cloud Shell w przeglądarce

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Konfigurowanie rejestrowania dla usługi Azure Database for PostgreSQL
Można skonfigurować serwera dostępu do dzienników zapytań i dzienniki błędów. Dzienniki błędów może zawierać informacje odkurzający auto, połączenia i punktu kontrolnego.
1. Włącz rejestrowanie.
2. Aby włączyć rejestrowanie zapytań, zaktualizuj **dziennika\_instrukcji** i **dziennika\_min\_czas trwania\_instrukcji**.
3. Zaktualizuj okres przechowywania.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie parametrów konfiguracji serwera](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Dzienniki listy dla usługi Azure Database dla serwera PostgreSQL
Aby wyświetlić listę plików dziennika dostępne na serwerze, uruchom [az postgres server-logs list](/cli/azure/postgres/server-logs) polecenia.

Możesz wyświetlić listę plików dziennika dla serwera **mydemoserver.postgres.database.azure.com** w grupie zasobów **myresourcegroup**. Następnie przekierowanie do listy plików dziennika do pliku tekstowego o nazwie **dziennika\_pliki\_lista.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Pobierz dzienniki lokalnie z serwera
Za pomocą [Pobierz az postgres server-logs](/cli/azure/postgres/server-logs) polecenia, możesz pobrać osobnych plikach dziennika dla serwera. 

Skorzystaj z następującego przykładu, aby pobrać określonego pliku dziennika dla serwera **mydemoserver.postgres.database.azure.com** w grupie zasobów **myresourcegroup** do środowiska lokalnego.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Kolejne kroki
- Aby dowiedzieć się więcej na temat dzienników serwerów, zobacz [dzienników serwera w usłudze Azure Database for postgresql w warstwie](concepts-server-logs.md).
- Aby uzyskać więcej informacji na temat parametrów serwera, zobacz [Dostosowywanie parametrów konfiguracji serwera przy użyciu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).
