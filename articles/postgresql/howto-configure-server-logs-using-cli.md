---
title: Zarządzanie dziennikami — interfejsu wiersza polecenia platformy Azure — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób konfigurowania dzienników serwera (.log) w usłudze Azure Database dla postgreSQL — pojedynczy serwer przy użyciu interfejsu wiersza polecenia platformy Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: be679be91d49516bd2f6c672eb53640cfad2ae2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763576"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurowanie dzienników serwera i uzyskiwanie do nich dostępu przy użyciu interfejsu wiersza polecenia platformy Azure
Dzienniki błędów serwera PostgreSQL można pobrać przy użyciu interfejsu wiersza polecenia (Azure CLI). Jednak dostęp do dzienników transakcji nie jest obsługiwany. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebujesz:
- [Usługa Azure Database dla serwera PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Narzędzie wiersza polecenia [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub usługa Azure Cloud Shell w przeglądarce

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Serwer można skonfigurować tak, aby miał dostęp do dzienników zapytań i dzienników błędów. Dzienniki błędów mogą mieć informacje o automatycznym odkurzaniu, połączeniu i punkcie kontrolnym.
1. Włącz rejestrowanie.
2. Aby włączyć rejestrowanie kwerend, należy zaktualizować **instrukcję\_dziennika** i **instrukcję czasu\_trwania\_\_dziennika min**.
3. Zaktualizuj okres przechowywania.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie parametrów konfiguracji serwera](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Dzienniki listy
Aby wyświetlić listę dostępnych plików dziennika dla serwera, uruchom polecenie [listy dzienników serwera az postgres.](/cli/azure/postgres/server-logs)

Pliki dziennika dla serwera **można wyświetlić mydemoserver.postgres.database.azure.com** w grupie zasobów **myresourcegroup**. Następnie przekieruj listę plików dziennika do pliku tekstowego o nazwie **log\_files\_list.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Pobieranie dzienników lokalnie z serwera
Za pomocą polecenia [pobierania dzienników serwera az postgres](/cli/azure/postgres/server-logs) można pobrać poszczególne pliki dziennika dla swojego serwera. 

Poniższy przykład służy do pobierania określonego pliku dziennika dla serwera **mydemoserver.postgres.database.azure.com** w ramach grupy zasobów **myresourcegroup** do środowiska lokalnego.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Następne kroki
- Aby dowiedzieć się więcej o dziennikach serwera, zobacz [Dzienniki serwera w usłudze Azure Database for PostgreSQL](concepts-server-logs.md).
- Aby uzyskać więcej informacji na temat parametrów serwera, zobacz [Dostosowywanie parametrów konfiguracji serwera przy użyciu interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).
