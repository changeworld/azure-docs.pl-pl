---
title: Skonfiguruj parametry usługi w bazie danych Azure dla programu MySQL
description: W tym artykule opisano sposób konfigurowania parametrów usługi w bazie danych Azure dla programu MySQL za pomocą narzędzia wiersza polecenia wiersza polecenia platformy Azure.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 9caf6b164f2433ab3c1b701554f562211cc2de80
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Dostosuj parametry konfiguracji serwera przy użyciu wiersza polecenia platformy Azure
Można wyświetlić listę, wyświetlić i zaktualizować parametry konfiguracji bazy danych Azure MySQL serwera przy użyciu wiersza polecenia platformy Azure, narzędzie wiersza polecenia platformy Azure. Podzbiór aparatu konfiguracji jest narażony na poziomie serwera i może być modyfikowany. 

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków opisanych ten przewodnik, potrzebne są:
- [Baza danych Azure dla serwera MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli) narzędzia wiersza polecenia lub użyj powłoki chmury Azure w przeglądarce.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Lista parametrów konfiguracji serwera dla bazy danych Azure MySQL serwera
Aby wyświetlić listę wszystkich parametrów można modyfikować w serwerze i ich wartości, należy uruchomić [listy konfiguracji serwerów mysql az](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) polecenia.

Można wyświetlić parametry konfiguracji serwera dla serwera **mydemoserver.mysql.database.azure.com** w grupie zasobów **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Dla definicji każdej z wymienionych parametrów, zobacz sekcję odwołanie MySQL na [zmienne systemowe serwera](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Pokaż szczegóły parametru konfiguracji serwera
Aby wyświetlić szczegółowe informacje dotyczące parametru określoną konfigurację serwera, uruchom [az mysql serwera konfiguracji Pokaż](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show) polecenia.

W tym przykładzie przedstawiono szczegóły **powolne\_zapytania\_dziennika** parametru konfiguracji serwera dla serwera **mydemoserver.mysql.database.azure.com** wgrupiezasobów**myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Zmodyfikuj wartość parametru konfiguracji serwera
Można również zmodyfikować wartość niektórych serwera parametru konfiguracji, która aktualizuje odpowiednia wartość konfiguracji dla aparatu serwera MySQL. Aby zaktualizować konfigurację, użyj [zestawu konfiguracji serwera mysql az](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) polecenia. 

Aby zaktualizować **powolne\_zapytania\_dziennika** parametru konfiguracji serwera serwera **mydemoserver.mysql.database.azure.com** w grupie zasobów  **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Jeśli chcesz zresetować wartość parametru konfiguracji, Pomiń parametr opcjonalny `--value` parametr, a usługa ma zastosowanie wartości domyślnej. Na przykład powyżej wyglądałyby tak jak:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Ten kod resetuje **powolne\_zapytania\_dziennika** konfiguracji przy użyciu wartości domyślnej **OFF**. 

## <a name="next-steps"></a>Kolejne kroki
- Jak skonfigurować [parametry serwera w portalu Azure](howto-server-parameters.md)