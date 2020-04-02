---
title: Konfigurowanie parametrów serwera — narzędzie interfejsu wiersza polecenia platformy Azure — usługa Azure Database for MySQL
description: W tym artykule opisano sposób konfigurowania parametrów usługi w usłudze Azure Database dla mysql przy użyciu narzędzia wiersza polecenia interfejsu wiersza interfejsu wiersza interfejsu wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: ca5f80e57f90e4dd26ac2e4a175998ff3de2c102
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546437"
---
# <a name="customize-server-parameters-by-using-azure-cli"></a>Dostosowywanie parametrów serwera przy użyciu interfejsu wiersza polecenia platformy Azure
Można wyświetlić, wyświetlić i zaktualizować parametry konfiguracji dla usługi Azure Database dla serwera MySQL przy użyciu narzędzia azure cli, narzędzia wiersza polecenia platformy Azure. Podzbiór konfiguracji aparatu jest narażony na poziomie serwera i może być modyfikowany. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebujesz:
- [Usługa Azure Database dla serwera MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- Narzędzie wiersza polecenia interfejsu wiersza interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub użyj usługi Azure Cloud Shell w przeglądarce.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Lista parametrów konfiguracji serwera dla usługi Azure Database dla serwera MySQL
Aby wyświetlić listę wszystkich modyfikowalnych parametrów na serwerze i ich wartości, uruchom polecenie [listy konfiguracji serwera az mysql.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list)

Można wyświetlić parametry konfiguracji serwera dla serwera **mydemoserver.mysql.database.azure.com** w ramach grupy zasobów **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Definicja każdego z wymienionych parametrów znajduje się w sekcji Odwołania do MySQL dotyczące [zmiennych systemowych serwera](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Pokaż szczegóły parametru konfiguracji serwera
Aby wyświetlić szczegółowe informacje o określonym parametrze konfiguracji serwera, uruchom polecenie [show konfiguracji serwera az mysql.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show)

W tym przykładzie przedstawiono szczegóły parametru konfiguracji serwera **\_\_dziennika kwerend powolnych** dla **mydemoserver.mysql.database.azure.com** serwera w ramach grupy zasobów **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Modyfikowanie wartości parametru konfiguracji serwera
Można również zmodyfikować wartość określonego parametru konfiguracji serwera, który aktualizuje podstawową wartość konfiguracji dla aparatu serwera MySQL. Aby zaktualizować konfigurację, użyj polecenia [az mysql server configuration set.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) 

Aby zaktualizować parametr konfiguracji serwera **\_\_dziennika kwerend powolnych** **serwera mydemoserver.mysql.database.azure.com** w ramach grupy zasobów **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Jeśli chcesz zresetować wartość parametru konfiguracji, pomiń parametr opcjonalny, `--value` a usługa zastosuje wartość domyślną. W powyższym przykładzie wygląda na to:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Ten kod resetuje konfigurację **\_\_dziennika kwerendy powolnej** do wartości domyślnej **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Praca z parametrem strefy czasowej

### <a name="populating-the-time-zone-tables"></a>Wypełnianie tabel stref czasowych

Tabele stref czasowych na serwerze `mysql.az_load_timezone` można wypełnić, wywołując procedurę składowaną za pomocą narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench.

> [!NOTE]
> Jeśli `mysql.az_load_timezone` używasz polecenia z mysql workbench, może być konieczne wyłączenie `SET SQL_SAFE_UPDATES=0;`trybu bezpiecznej aktualizacji przy użyciu .

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

Globalną strefę czasową poziomu można ustawić za pomocą polecenia [az mysql server configuration set.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set)

Następujące polecenie aktualizuje parametr konfiguracji serwera **strefy\_czasowej** serwera **mydemoserver.mysql.database.azure.com** w ramach grupy zasobów **myresourcegroup** do **US/Pacific**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Ustawianie strefy czasowej poziomu sesji

Strefę czasową poziomu sesji można `SET time_zone` ustawić, uruchamiając polecenie za pomocą narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench. Poniższy przykład ustawia strefę czasową na strefę czasową **USA/Pacyfiku.**  

```sql
SET time_zone = 'US/Pacific';
```

Zapoznaj się z dokumentacją MySQL dotyczącą [funkcji daty i godziny](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Następne kroki

- Jak skonfigurować [parametry serwera w witrynie Azure portal](howto-server-parameters.md)