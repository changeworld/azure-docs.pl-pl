---
title: Konfigurowanie parametrów — usługa Azure Database for PostgreSQL — single server
description: W tym artykule opisano sposób konfigurowania parametrów postgres w usłudze Azure Database dla postgreSQL — pojedynczy serwer przy użyciu interfejsu wiersza polecenia platformy Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 4e029428a3709bacdbcd50a6ac3714e730377242
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763627"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Dostosowywanie parametrów konfiguracji serwera dla usługi Azure Database for PostgreSQL — pojedynczy serwer przy użyciu interfejsu wiersza polecenia platformy Azure
Można wyświetlić, wyświetlić i zaktualizować parametry konfiguracji dla serwera PostgreSQL usługi Azure przy użyciu interfejsu wiersza polecenia (Azure CLI). Podzbiór konfiguracji aparatu jest narażony na poziomie serwera i może być modyfikowany. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebujesz:
- Tworzenie usługi Azure Database dla serwera i bazy danych PostgreSQL przez następujące [Tworzenie bazy danych platformy Azure dla postgreSQL](quickstart-create-server-database-azure-cli.md)
- Zainstaluj interfejs wiersza polecenia interfejsu wiersza interfejsu wiersza interfejsu [wiersza platformy Azure](/cli/azure/install-azure-cli) na komputerze lub użyj usługi Azure Cloud [Shell](../cloud-shell/overview.md) w witrynie Azure portal przy użyciu przeglądarki.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Lista parametrów konfiguracji serwera dla usługi Azure Database dla serwera PostgreSQL
Aby wyświetlić listę wszystkich modyfikowalnych parametrów na serwerze i ich wartości, uruchom polecenie [listy konfiguracji serwera az postgres.](/cli/azure/postgres/server/configuration)

Można wyświetlić parametry konfiguracji serwera dla serwera **mydemoserver.postgres.database.azure.com** w ramach grupy zasobów **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Pokaż szczegóły parametru konfiguracji serwera
Aby wyświetlić szczegółowe informacje o określonym parametrze konfiguracji serwera, uruchom polecenie [show konfiguracji serwera az postgres.](/cli/azure/postgres/server/configuration)

W tym przykładzie przedstawiono szczegóły parametru konfiguracji serwera **wiadomości\_min\_dziennika** dla **mydemoserver.postgres.database.azure.com** serwera w ramach grupy zasobów **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Modyfikowanie wartości parametru konfiguracji serwera
Można również zmodyfikować wartość określonego parametru konfiguracji serwera, który aktualizuje podstawową wartość konfiguracji dla aparatu serwera PostgreSQL. Aby zaktualizować konfigurację, należy użyć polecenia [zestawu konfiguracji serwera az postgres.](/cli/azure/postgres/server/configuration) 

Aby zaktualizować parametr konfiguracji serwera **wiadomości min\_\_dziennika** serwera **mydemoserver.postgres.database.azure.com** w ramach grupy zasobów **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Jeśli chcesz zresetować wartość parametru konfiguracji, po prostu należy `--value` pominąć parametr opcjonalny, a usługa zastosuje wartość domyślną. W powyższym przykładzie będzie wyglądać następująco:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
To polecenie resetuje konfigurację **komunikatów\_dziennika min\_** do wartości domyślnej **OSTRZEŻENIE**. Aby uzyskać więcej informacji na temat konfiguracji serwera i dopuszczalnych wartości, zobacz Dokumentacja PostgreSQL dotycząca [konfiguracji serwera](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się, jak ponownie uruchomić serwer](howto-restart-server-cli.md)
- Aby skonfigurować dzienniki serwera i uzyskiwać do nich dostęp, zobacz [Dzienniki serwera w bazie danych platformy Azure dla postgreSQL](concepts-server-logs.md)
