---
title: Konfiguruj parametry usługi w usłudze Azure Database for MySQL
description: W tym artykule opisano jak skonfigurować parametry usługi w usłudze Azure Database for MySQL za pomocą narzędzia wiersza polecenia wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: b0d7bbdc3e1dcad6f6cecb57b15e2e5df6b3fd28
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60525425"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Dostosowywanie parametrów konfiguracji serwera przy użyciu wiersza polecenia platformy Azure
Można wyświetlać, pokazać i zaktualizować parametry konfiguracji dla serwera Azure Database for MySQL przy użyciu wiersza polecenia platformy Azure, narzędzie wiersza polecenia platformy Azure. Podzbiór aparatu konfiguracji jest narażony na poziomie serwera i może być modyfikowany. 

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków w tym przewodniku, potrzebne są:
- [Serwer Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Interfejs wiersza polecenia Azure](/cli/azure/install-azure-cli) narzędzie wiersza polecenia lub użyj usługi Azure Cloud Shell w przeglądarce.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Lista parametrów konfiguracji serwera dla usługi Azure Database dla serwera MySQL
Aby wyświetlić listę wszystkich parametrów można modyfikować w serwera i ich wartości, należy uruchomić [az mysql server configuration list](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) polecenia.

Możesz wyświetlić listę parametrów konfiguracji serwera dla serwera **mydemoserver.mysql.database.azure.com** w ramach grupy zasobów **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Aby definicję każdego z wymienionych parametrów, zobacz sekcję MySQL na [zmiennych systemowych serwera](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Pokaż szczegóły parametrów konfiguracji serwera
Aby wyświetlić szczegółowe informacje dotyczące parametru określonej konfiguracji dla serwera, uruchom [az mysql server configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) polecenia.

Ten przykład przedstawia szczegółowe informacje o **powolne\_zapytania\_dziennika** parametru konfiguracji serwera dla serwera **mydemoserver.mysql.database.azure.com** wgrupiezasobów**myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Modyfikowanie wartości parametru konfiguracji serwera
Można również zmodyfikować wartości parametru niektórych serwera konfiguracji, która aktualizuje podstawowej wartości konfiguracji dla aparatu serwera MySQL. Aby zaktualizować konfigurację, użyj [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) polecenia. 

Można zaktualizować **powolne\_zapytania\_dziennika** parametru konfiguracji serwera serwera **mydemoserver.mysql.database.azure.com** w ramach grupy zasobów  **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Jeśli chcesz zresetować wartość parametru konfiguracji, Pomiń opcjonalnego `--value` parametr i usługa stosuje się wartością domyślną. W powyższym przykładzie to powiadomienie będzie wyglądać:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Ten kod ustawia **powolne\_zapytania\_dziennika** konfiguracji, wartość domyślna **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Praca z parametr strefy czasowej

### <a name="populating-the-time-zone-tables"></a>Wypełnianie tabel strefy czasowej

Tabele strefę czasową na serwerze można wypełnić przez wywołanie metody `az_load_timezone` przechowywane procedury przy użyciu narzędzia wiersza polecenia MySQL lub połączenia aplikacji MySQL Workbench.

> [!NOTE]
> Jeśli używasz `az_load_timezone` polecenia za pomocą aplikacji MySQL Workbench, konieczne może być pierwszym Wyłącz tryb awaryjny aktualizacji przy użyciu `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Aby wyświetlić wartości dostępne strefy czasowej, uruchom następujące polecenie:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ustawienia globalne poziomu strefy czasowej

Globalne poziomu strefy czasowej można ustawić za pomocą [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) polecenia.

Następujące polecenie aktualizacji **czasu\_strefy** parametru konfiguracji serwera serwera **mydemoserver.mysql.database.azure.com** w ramach grupy zasobów  **myresourcegroup** do **USA / Pacyfik**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Ustawienie strefy czasowej z poziomu sesji

Sesji można ustawić poziomu strefy czasowej, uruchamiając `SET time_zone` polecenia za pomocą narzędzia wiersza polecenia MySQL lub połączenia aplikacji MySQL Workbench. Poniższy przykład ustawia strefę czasową **USA / Pacyfik** strefy czasowej.  

```sql
SET time_zone = 'US/Pacific';
```

Zapoznaj się z dokumentacją MySQL dla [funkcji daty i godziny](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Kolejne kroki

- Jak skonfigurować [parametry serwera w witrynie Azure portal](howto-server-parameters.md)