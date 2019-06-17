---
title: Konfigurowanie i dostęp do dzienników serwera for PostgreSQL — pojedynczy serwer przy użyciu wiersza polecenia platformy Azure
description: W tym artykule opisano sposób konfigurowania i dostęp do dzienników serwera w usłudze Azure Database for PostgreSQL — pojedynczy serwer przy użyciu wiersza polecenia wiersza polecenia platformy Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4702db31ffeb15481584b9638f5be1aa640ff39e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067209"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurowanie i dostęp do dzienników serwera przy użyciu wiersza polecenia platformy Azure
Możesz pobrać dzienniki błędów serwera PostgreSQL za pomocą interfejsu wiersza polecenia (CLI platformy Azure). Jednak dostęp do dzienników transakcji nie jest obsługiwane. 

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków w tym przewodniku, potrzebne są:
- [Usługa Azure Database for postgresql w warstwie serwera](quickstart-create-server-database-azure-cli.md)
- [Wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) narzędzie wiersza polecenia lub programu Azure Cloud Shell w przeglądarce

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Można skonfigurować serwera dostępu do dzienników zapytań i dzienniki błędów. Dzienniki błędów może zawierać informacje odkurzający auto, połączenia i punktu kontrolnego.
1. Włącz rejestrowanie.
2. Aby włączyć rejestrowanie zapytań, zaktualizuj **dziennika\_instrukcji** i **dziennika\_min\_czas trwania\_instrukcji**.
3. Zaktualizuj okres przechowywania.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie parametrów konfiguracji serwera](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Lista dzienników
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
