---
title: Konfigurowanie parametrów serwera — narzędzie interfejsu wiersza polecenia platformy Azure — usługa Azure Database dla bazy mariadb
description: W tym artykule opisano sposób konfigurowania parametrów usługi w usłudze Azure Database dla mariadb przy użyciu narzędzia wiersza polecenia interfejsu wiersza interfejsu wiersza interfejsu wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 3ba06ea592d51eedbe827e1ab6418f65722d579c
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632305"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Dostosowywanie parametrów konfiguracji serwera przy użyciu interfejsu wiersza polecenia platformy Azure
Można wyświetlić, wyświetlić i zaktualizować parametry konfiguracji dla serwera usługi Azure Database dla MariaDB przy użyciu narzędzia interfejsu wiersza polecenia platformy Azure, narzędzia wiersza polecenia platformy Azure. Podzbiór konfiguracji aparatu jest narażony na poziomie serwera i może być modyfikowany.

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebujesz:
- [Usługa Azure Database dla serwera MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Narzędzie wiersza polecenia interfejsu wiersza interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub użyj usługi Azure Cloud Shell w przeglądarce.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Lista parametrów konfiguracji serwera dla usługi Azure Database dla serwera MariaDB
Aby wyświetlić listę wszystkich modyfikowalnych parametrów na serwerze i ich wartości, uruchom polecenie [listy konfiguracji serwera az mariadb.](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list)

Można wyświetlić parametry konfiguracji serwera dla serwera **mydemoserver.mariadb.database.azure.com** w ramach grupy zasobów **myresourcegroup**.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Definicja każdego z wymienionych parametrów znajduje się w sekcji MariaDB reference na temat [zmiennych systemowych serwera](https://mariadb.com/kb/en/library/server-system-variables/).

## <a name="show-server-configuration-parameter-details"></a>Pokaż szczegóły parametru konfiguracji serwera
Aby wyświetlić szczegółowe informacje o określonym parametrze konfiguracji serwera, uruchom polecenie [show konfiguracji serwera az mariadb.](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show)

W tym przykładzie przedstawiono szczegóły parametru konfiguracji serwera **\_\_dziennika kwerend powolnych** dla **mydemoserver.mariadb.database.azure.com** serwera w ramach grupy zasobów **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modyfikowanie wartości parametru konfiguracji serwera
Można również zmodyfikować wartość określonego parametru konfiguracji serwera, który aktualizuje podstawową wartość konfiguracji dla aparatu serwera MariaDB. Aby zaktualizować konfigurację, należy użyć polecenia [zestaw konfiguracji serwera az mariadb.](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) 

Aby zaktualizować parametr konfiguracji serwera **\_\_dziennika kwerend powolnych** **serwera mydemoserver.mariadb.database.azure.com** w ramach grupy zasobów **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Jeśli chcesz zresetować wartość parametru konfiguracji, pomiń parametr opcjonalny, `--value` a usługa zastosuje wartość domyślną. W powyższym przykładzie wygląda na to:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Ten kod resetuje konfigurację **\_\_dziennika kwerendy powolnej** do wartości domyślnej **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Praca z parametrem strefy czasowej

### <a name="populating-the-time-zone-tables"></a>Wypełnianie tabel stref czasowych

Tabele stref czasowych na serwerze `mysql.az_load_timezone` można wypełnić, wywołując procedurę składowaną za pomocą narzędzia, takiego jak wiersz polecenia MariaDB lub MariaDB Workbench.

> [!NOTE]
> Jeśli `mysql.az_load_timezone` polecenie jest uruchomione z programu MariaDB Workbench, może być `SET SQL_SAFE_UPDATES=0;`konieczne wyłączenie trybu bezpiecznej aktualizacji przy użyciu programu .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Należy ponownie uruchomić serwer, aby upewnić się, że tabele stref czasowych są poprawnie wypełnione. Aby ponownie uruchomić serwer, użyj [witryny Azure portal](howto-restart-server-portal.md) lub [interfejsu wiersza polecenia](howto-restart-server-cli.md).

Aby wyświetlić dostępne wartości stref czasowych, uruchom następujące polecenie:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ustawianie strefy czasowej poziomu globalnego

Strefę czasową poziomu globalnego można ustawić za pomocą polecenia [az mariadb configuration set.](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set)

Następujące polecenie aktualizuje parametr konfiguracji serwera **strefy\_czasowej** serwera **mydemoserver.mariadb.database.azure.com** w ramach grupy zasobów **myresourcegroup** do **US/Pacific**.

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Ustawianie strefy czasowej poziomu sesji

Strefę czasową poziomu sesji można `SET time_zone` ustawić, uruchamiając polecenie za pomocą narzędzia, takiego jak wiersz polecenia MariaDB lub MariaDB Workbench. Poniższy przykład ustawia strefę czasową na strefę czasową **USA/Pacyfiku.**  

```sql
SET time_zone = 'US/Pacific';
```

Zapoznaj się z dokumentacją MariaDB dotyczącą [funkcji daty i godziny](https://mariadb.com/kb/en/library/date-time-functions/).

## <a name="next-steps"></a>Następne kroki

- Jak skonfigurować [parametry serwera w witrynie Azure portal](howto-server-parameters.md)
