---
title: Tworzenie i zarządzanie nimi Azure bazy danych MySQL reguł zapory przy użyciu wiersza polecenia platformy Azure
description: W tym artykule opisano sposób tworzenia i zarządzania nimi Azure bazy danych MySQL reguł zapory przy użyciu wiersza polecenia wiersza polecenia platformy Azure.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 1bb60a1d6f14f0dc81047a2ffdd514724954fb14
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266061"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Tworzenie i zarządzanie nimi Azure bazy danych MySQL reguł zapory przy użyciu wiersza polecenia platformy Azure
Reguły zapory poziomu serwera umożliwiają administratorom zarządzanie dostępem do bazy danych Azure MySQL serwera z określonego adresu IP lub zakresu adresów IP. Za pomocą wygodny poleceń interfejsu wiersza polecenia Azure, możesz utworzyć, zaktualizować, Usuń listę i Pokaż reguły zapory do zarządzania serwerem. Omówienie bazy danych Azure dla zapór MySQL, zobacz [bazą danych Azure dla reguł zapory serwera MySQL](./concepts-firewall-rules.md)

## <a name="prerequisites"></a>Wymagania wstępne
* [Zainstaluj interfejs wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).
* [Azure bazy danych MySQL serwera i bazy danych](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Polecenia reguły zapory:
**Az mysql reguły zapory serwera-** polecenie służy do tworzenia, usuwania, listy, Pokaż i zaktualizować reguł zapory z wiersza polecenia platformy Azure.

Polecenia:
- **Utwórz**: Tworzenie reguły zapory serwera Azure MySQL.
- **Usuń**: Usuń regułę zapory serwera Azure MySQL.
- **Lista**: lista reguł zapory serwera Azure MySQL.
- **Pokaż**: Pokaż szczegóły serwera Azure MySQL reguły zapory.
- **Zaktualizuj**: aktualizowanie reguły zapory serwera Azure MySQL.

## <a name="log-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Lista bazy danych Azure, serwerów MySQL i logowanie do platformy Azure
Bezpieczne łączenie z wiersza polecenia platformy Azure z Twoim kontem platformy Azure przy użyciu **logowania az** polecenia.

1. W wierszu polecenia Uruchom następujące polecenie:
```azurecli
az login
```
To polecenie generuje kod, aby użyć w następnym kroku.

2. Aby otworzyć stronę przy użyciu przeglądarki sieci web [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin), a następnie wprowadź kod.

3. Po wyświetleniu monitu zaloguj się przy użyciu swoich poświadczeń platformy Azure.

4. Po logowanie jest autoryzowany, listę subskrypcji jest drukowany w konsoli. Skopiuj identyfikator żądanego subskrypcji można ustawić bieżącej subskrypcji do użycia. Użyj [skonfigurowane konto az](/cli/azure/account#az_account_set) polecenia.
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Lista baz danych Azure, serwerów MySQL dla Twojej subskrypcji i grupie zasobów, jeśli nie wiesz o nazwach. Użyj [listy serwerów mysql az](/cli/azure/mysql/server#az_mysql_server_list) polecenia.

   ```azurecli-interactive
   az mysql server list --resource-group myresourcegroup
   ```

   Należy zwrócić uwagę atrybutu nazwy w liście, należy określić serwer MySQL pracować nad. Jeśli to konieczne, Potwierdź szczegóły dla tego serwera i przy użyciu atrybutu nazwy, aby upewnić się, że jest poprawny. Użyj [Pokaż serwera mysql az](/cli/azure/mysql/server#az_mysql_server_show) polecenia.

   ```azurecli-interactive
   az mysql server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Lista reguł zapory w bazie danych Azure MySQL serwera 
Przy użyciu nazwy serwera i nazwa grupy zasobów, Wyświetl listę istniejących reguł zapory serwera na serwerze. Użyj [listy zapory serwera mysql az](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_list) polecenia.  Należy zauważyć, że nazwa serwera jest określony w **— serwer** przełącznik, a nie w **— nazwa** przełącznika. 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Dane wyjściowe wymieniono reguły, jeśli istnieje, w formacie JSON formatu (domyślnie). Można użyć **--tabeli wyników** przełącznik, aby wyświetlić wyników w bardziej czytelnym formacie tabeli.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Tworzenie reguły zapory w bazie danych Azure MySQL serwera
Przy użyciu nazwy serwera Azure MySQL i nazwę grupy zasobów, Utwórz nową regułę zapory na serwerze. Użyj [utworzyć Zapora serwera mysql az](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create) polecenia. Podaj nazwę reguły, a także IP rozpoczęcia i zakończenia IP (w celu zapewnienia dostępu do zakresu adresów IP) reguły.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Aby zezwolić na dostęp dla jednego adresu IP, należy podać ten sam adres IP jako Start IP i End IP, jak w poniższym przykładzie.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Aby umożliwić aplikacjom z adresów IP platformy Azure do nawiązania połączenia bazy danych Azure, aby serwer MySQL, podaj adres IP 0.0.0.0 jako Start IP i End IP, jak w poniższym przykładzie.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

Po sukces utworzyć każdego polecenia, które dane wyjściowe Wyświetla szczegóły reguły zapory, które zostały utworzone w formacie JSON (domyślnie). W przypadku awarii, dane wyjściowe zawierają tekst komunikatu o błędzie zamiast tego.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Aktualizuj reguły zapory dla serwera MySQL w bazie danych Azure 
Przy użyciu nazwy serwera Azure MySQL i nazwę grupy zasobów, zaktualizuj istniejącą regułę zapory na serwerze. Użyj [aktualizacja zapory serwera mysql az](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_update) polecenia. Podaj nazwę istniejącej reguły zapory jako dane wejściowe, a także początek atrybutów IP adresów IP i końcowy do aktualizacji.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Na sukces dane wyjściowe polecenia Wyświetla szczegóły reguły zapory, które zostały zaktualizowane w formacie JSON (domyślnie). W przypadku awarii, dane wyjściowe zawierają tekst komunikatu o błędzie zamiast tego.

> [!NOTE]
> Jeśli reguły zapory nie istnieje, tworzona jest reguła za pomocą polecenia aktualizacji.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Pokaż zapory szczegóły reguły w bazie danych Azure dla serwera MySQL
Przy użyciu nazwy serwera Azure MySQL i nazwę grupy zasobów, Pokaż szczegóły reguły z serwera istniejącą zapory. Użyj [az mysql Serwer zapory Pokaż](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_show) polecenia. Podaj nazwę istniejącej reguły zapory jako dane wejściowe.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Na sukces dane wyjściowe polecenia Wyświetla szczegóły reguły zapory, który został określony, w formacie JSON (domyślnie). W przypadku awarii, dane wyjściowe zawierają tekst komunikatu o błędzie zamiast tego.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Usuwanie reguły zapory w bazie danych Azure MySQL serwera
Przy użyciu nazwy serwera Azure MySQL i nazwę grupy zasobów, usuń istniejącą regułę zapory z serwera. Użyj [usunąć Zapora serwera mysql az](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_delete) polecenia. Podaj nazwę istniejącej reguły zapory.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Na sukces nie ma żadnych danych wyjściowych. W przypadku awarii Wyświetla tekst komunikatu o błędzie.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedzieć się więcej o [bazą danych Azure dla reguł zapory serwera MySQL](./concepts-firewall-rules.md).
- [Tworzenie i zarządzanie nimi Azure bazy danych MySQL reguł zapory przy użyciu portalu Azure](./howto-manage-firewall-using-portal.md).
