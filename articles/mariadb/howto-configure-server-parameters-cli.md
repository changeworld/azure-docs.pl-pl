---
title: Konfigurowanie parametrów serwera — interfejs wiersza polecenia platformy Azure — Azure Database for MariaDB
description: W tym artykule opisano sposób konfigurowania parametrów usługi w Azure Database for MariaDB przy użyciu narzędzia wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 6616bd8172e9bc049a6e0e2c687390197de2f391
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767317"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Dostosowywanie parametrów konfiguracji serwera za pomocą interfejsu wiersza polecenia platformy Azure
Można wyświetlić, wyświetlić i zaktualizować parametry konfiguracji dla serwera Azure Database for MariaDB przy użyciu interfejsu wiersza polecenia platformy Azure, narzędzia wiersza poleceń platformy Azure. Podzestaw konfiguracji aparatu jest uwidoczniony na poziomie serwera i można go modyfikować.

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- [Serwer Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Narzędzie wiersza polecenia [platformy Azure](/cli/azure/install-azure-cli) lub użyj Azure Cloud Shell w przeglądarce.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Wyświetlanie listy parametrów konfiguracji serwera dla Azure Database for MariaDB Server
Aby wyświetlić listę wszystkich modyfikowalnych parametrów na serwerze i ich wartości, uruchom polecenie [AZ MariaDB Server Configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) .

Dla serwera **mydemoserver.MariaDB.Database.Azure.com** można wyświetlić listę parametrów konfiguracji serwera w **obszarze Grupa zasobów**.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Aby zapoznać się z definicją każdego z wymienionych parametrów, zobacz sekcję Referencja MariaDB na stronie [zmienne systemowe serwera](https://mariadb.com/kb/en/library/server-system-variables/).

## <a name="show-server-configuration-parameter-details"></a>Pokaż szczegóły parametru konfiguracji serwera
Aby wyświetlić szczegóły dotyczące określonego parametru konfiguracji dla serwera, uruchom polecenie [AZ MariaDB Server Configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) .

W tym przykładzie przedstawiono szczegółowe informacje o **powolnych\_zapytań\_** konfiguracji serwera dzienników dla serwera **mydemoserver.MariaDB.Database.Azure.com** w obszarze Grupa zasobów **.**
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modyfikowanie wartości parametru konfiguracji serwera
Można również zmodyfikować wartość określonego parametru konfiguracji serwera, który aktualizuje podstawową wartość konfiguracyjną dla aparatu serwera MariaDB. Aby zaktualizować konfigurację, użyj polecenia [AZ MariaDB Server Configuration Set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) . 

Aby zaktualizować parametr konfiguracyjny **powolnej\_kwerendy\_dziennik** konfiguracji serwera **mydemoserver.MariaDB.Database.Azure.com** w obszarze Grupa zasobów **.**
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Jeśli chcesz zresetować wartość parametru konfiguracji, Pomiń opcjonalny parametr `--value`, a usługa zastosuje wartość domyślną. W powyższym przykładzie będzie wyglądać następująco:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Ten kod resetuje **powolne\_kwerendy\_konfiguracji dziennika** do wartości **domyślnej.** 

## <a name="working-with-the-time-zone-parameter"></a>Praca z parametrem strefy czasowej

### <a name="populating-the-time-zone-tables"></a>Wypełnianie tabel strefy czasowej

Tabele strefy czasowej na serwerze można wypełnić przez wywołanie procedury składowanej `az_load_timezone` za pomocą narzędzia, takiego jak MariaDB Command line lub MariaDB Workbench.

> [!NOTE]
> Jeśli uruchamiasz polecenie `az_load_timezone` z MariaDB Workbench, może być konieczne wyłączenie bezpiecznego trybu aktualizacji przy użyciu `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Aby wyświetlić dostępne wartości strefy czasowej, uruchom następujące polecenie:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ustawianie strefy czasowej na poziomie globalnym

Strefę czasową na poziomie globalnym można ustawić za pomocą polecenia [AZ MariaDB Server Configuration Set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) .

Następujące polecenie aktualizuje **czas\_** parametr **konfiguracji serwera strefy w obszarze Grupa** **zasobów zasobu do** **USA/Pacyfik**.

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Ustawianie strefy czasowej na poziomie sesji

Strefę czasową na poziomie sesji można ustawić, uruchamiając polecenie `SET time_zone` za pomocą narzędzia, takiego jak MariaDB Command line lub MariaDB Workbench. W poniższym przykładzie ustawiono strefę czasową dla strefy czasowej **USA/Pacyfiku** .  

```sql
SET time_zone = 'US/Pacific';
```

Zapoznaj się z dokumentacją MariaDB dla [funkcji daty i godziny](https://mariadb.com/kb/en/library/date-time-functions/).

## <a name="next-steps"></a>Następne kroki

- Jak skonfigurować [parametry serwera w Azure Portal](howto-server-parameters.md)
