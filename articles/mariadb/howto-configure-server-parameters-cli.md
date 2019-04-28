---
title: Konfiguruj parametry usługi w usłudze Azure Database dla serwera MariaDB
description: W tym artykule opisano sposób konfigurowania parametrów usługi w usłudze Azure Database dla serwera MariaDB, za pomocą narzędzia wiersza polecenia wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 4e0bf45f1c67a5e07d6ed632f6560d094b673c0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61040058"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Dostosowywanie parametrów konfiguracji serwera przy użyciu wiersza polecenia platformy Azure
Można wyświetlać, pokazać i zaktualizować parametry konfiguracji dla usługi Azure Database dla serwera MariaDB przy użyciu wiersza polecenia platformy Azure, narzędzie wiersza polecenia platformy Azure. Podzbiór aparatu konfiguracji jest narażony na poziomie serwera i może być modyfikowany.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków w tym przewodniku, potrzebne są:
- [Azure Database dla serwera MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Interfejs wiersza polecenia Azure](/cli/azure/install-azure-cli) narzędzie wiersza polecenia lub użyj usługi Azure Cloud Shell w przeglądarce.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Lista parametrów konfiguracji serwera dla usługi Azure Database dla serwera MariaDB
Aby wyświetlić listę wszystkich parametrów można modyfikować w serwera i ich wartości, należy uruchomić [listy konfiguracji serwera mariadb az](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) polecenia.

Możesz wyświetlić listę parametrów konfiguracji serwera dla serwera **mydemoserver.mariadb.database.azure.com** w ramach grupy zasobów **myresourcegroup**.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Dla definicji każdego z wymienionych parametrów, zobacz sekcję MariaDB na [zmiennych systemowych serwera](https://mariadb.com/kb/en/library/server-system-variables/).

## <a name="show-server-configuration-parameter-details"></a>Pokaż szczegóły parametrów konfiguracji serwera
Aby wyświetlić szczegółowe informacje dotyczące parametru określonej konfiguracji dla serwera, uruchom [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) polecenia.

Ten przykład przedstawia szczegółowe informacje o **powolne\_zapytania\_dziennika** parametru konfiguracji serwera dla serwera **mydemoserver.mariadb.database.azure.com** w ramach grupy zasobów **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modyfikowanie wartości parametru konfiguracji serwera
Można również zmodyfikować wartości parametru niektórych serwera konfiguracji, która aktualizuje podstawowej wartości konfiguracji dla aparatu serwera MariaDB. Aby zaktualizować konfigurację, użyj [zestawu konfiguracji serwera mariadb az](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) polecenia. 

Można zaktualizować **powolne\_zapytania\_dziennika** parametru konfiguracji serwera serwera **mydemoserver.mariadb.database.azure.com** w ramach grupy zasobów  **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Jeśli chcesz zresetować wartość parametru konfiguracji, Pomiń opcjonalnego `--value` parametr i usługa stosuje się wartością domyślną. W powyższym przykładzie to powiadomienie będzie wyglądać:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Ten kod ustawia **powolne\_zapytania\_dziennika** konfiguracji, wartość domyślna **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Praca z parametr strefy czasowej

### <a name="populating-the-time-zone-tables"></a>Wypełnianie tabel strefy czasowej

Tabele strefę czasową na serwerze można wypełnić przez wywołanie metody `az_load_timezone` przechowywane procedury przy użyciu narzędzia wiersza polecenia MariaDB lub MariaDB aplikacji Workbench.

> [!NOTE]
> Jeśli używasz `az_load_timezone` polecenia z aplikacji MariaDB Workbench, konieczne może być pierwszym Wyłącz tryb awaryjny aktualizacji przy użyciu `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Aby wyświetlić wartości dostępne strefy czasowej, uruchom następujące polecenie:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ustawienia globalne poziomu strefy czasowej

Globalne poziomu strefy czasowej można ustawić za pomocą [zestawu konfiguracji serwera mariadb az](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) polecenia.

Następujące polecenie aktualizacji **czasu\_strefy** parametru konfiguracji serwera serwera **mydemoserver.mariadb.database.azure.com** w ramach grupy zasobów  **myresourcegroup** do **USA / Pacyfik**.

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Ustawienie strefy czasowej z poziomu sesji

Sesji można ustawić poziomu strefy czasowej, uruchamiając `SET time_zone` polecenia za pomocą narzędzia wiersza polecenia MariaDB lub MariaDB aplikacji Workbench. Poniższy przykład ustawia strefę czasową **USA / Pacyfik** strefy czasowej.  

```sql
SET time_zone = 'US/Pacific';
```

Zapoznaj się z dokumentacją MariaDB dla [funkcji daty i godziny](https://mariadb.com/kb/en/library/date-time-functions/).

## <a name="next-steps"></a>Kolejne kroki

- Jak skonfigurować [parametry serwera w witrynie Azure portal](howto-server-parameters.md)
