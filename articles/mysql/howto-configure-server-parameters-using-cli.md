---
title: Konfigurowanie parametrów usługi — Azure Database for MySQL
description: W tym artykule opisano sposób konfigurowania parametrów usługi w Azure Database for MySQL przy użyciu narzędzia wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: a107c5130968ca960036d7e0f948cf6ea5d209a8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350334"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Dostosowywanie parametrów konfiguracji serwera za pomocą interfejsu wiersza polecenia platformy Azure
Można wyświetlić, wyświetlić i zaktualizować parametry konfiguracji dla serwera Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure, narzędzia wiersza poleceń platformy Azure. Podzestaw konfiguracji aparatu jest uwidoczniony na poziomie serwera i można go modyfikować. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- [Serwer Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- Narzędzie wiersza polecenia [platformy Azure](/cli/azure/install-azure-cli) lub użyj Azure Cloud Shell w przeglądarce.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Wyświetlanie listy parametrów konfiguracji serwera dla Azure Database for MySQL Server
Aby wyświetlić listę wszystkich modyfikowalnych parametrów na serwerze i ich wartości, uruchom polecenie [AZ MySQL Server Configuration list](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) .

Dla serwera **mydemoserver.MySQL.Database.Azure.com** można wyświetlić listę parametrów konfiguracji serwera w **obszarze Grupa zasobów**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Aby zapoznać się z definicją każdego z wymienionych parametrów, zobacz sekcję informacje dotyczące programu MySQL w artykule [zmienne systemowe serwera](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Pokaż szczegóły parametru konfiguracji serwera
Aby wyświetlić szczegóły dotyczące określonego parametru konfiguracji dla serwera, uruchom polecenie [AZ MySQL Server Configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) .

W tym przykładzie przedstawiono szczegółowe informacje o **powolnych\_zapytań\_** konfiguracji serwera dzienników dla serwera **mydemoserver.MySQL.Database.Azure.com** w obszarze Grupa zasobów **.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Modyfikowanie wartości parametru konfiguracji serwera
Można również zmodyfikować wartość określonego parametru konfiguracji serwera, który aktualizuje podstawową wartość konfiguracyjną dla aparatu serwera MySQL. Aby zaktualizować konfigurację, użyj polecenia [AZ MySQL Server Configuration Set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) . 

Aby zaktualizować parametr konfiguracyjny **powolnej\_kwerendy\_dziennik** konfiguracji serwera **mydemoserver.MySQL.Database.Azure.com** w obszarze Grupa zasobów **.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Jeśli chcesz zresetować wartość parametru konfiguracji, Pomiń opcjonalny parametr `--value`, a usługa zastosuje wartość domyślną. W powyższym przykładzie będzie wyglądać następująco:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Ten kod resetuje **powolne\_kwerendy\_konfiguracji dziennika** do wartości **domyślnej.** 

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

Strefę czasową na poziomie globalnym można ustawić za pomocą polecenia [AZ MySQL Server Configuration Set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) .

Następujące polecenie aktualizuje **czas\_** parametr **konfiguracji serwera strefy w obszarze Grupa** **zasobów zasobu do** **USA/Pacyfik**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Ustawienie strefy czasowej z poziomu sesji

Sesji można ustawić poziomu strefy czasowej, uruchamiając `SET time_zone` polecenia za pomocą narzędzia wiersza polecenia MySQL lub połączenia aplikacji MySQL Workbench. Poniższy przykład ustawia strefę czasową **USA / Pacyfik** strefy czasowej.  

```sql
SET time_zone = 'US/Pacific';
```

Zapoznaj się z dokumentacją MySQL dla [funkcji daty i godziny](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Następne kroki

- Jak skonfigurować [parametry serwera w Azure Portal](howto-server-parameters.md)