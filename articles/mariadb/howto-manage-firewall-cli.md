---
title: Tworzenie i zarządzanie nimi — Azure Database dla MariaDB reguł zapory przy użyciu wiersza polecenia platformy Azure
description: W tym artykule opisano sposób tworzenia i zarządzania usługi Azure Database dla MariaDB reguł zapory przy użyciu wiersza polecenia platformy Azure jest wiersza polecenia.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 562987b953f0a8a20a917e208f43557bd768c0a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61038624"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Tworzenie i zarządzanie nimi — Azure Database dla MariaDB reguł zapory przy użyciu wiersza polecenia platformy Azure
Reguły zapory na poziomie serwera mogą służyć do zarządzania dostępem do usługi Azure Database dla serwera MariaDB z określonego adresu IP lub zakres adresów IP. Przy użyciu wygodne poleceń interfejsu wiersza polecenia platformy Azure, możesz utworzyć, zaktualizować, Usuń listę i Pokaż reguły zapory, aby zarządzać serwerem. Omówienie usługi Azure Database dla zapór MariaDB, zobacz [— Azure Database for reguły zapory serwera MariaDB](./concepts-firewall-rules.md).

Wirtualne reguł sieci wirtualnej można również bezpieczny dostęp do serwera. Dowiedz się więcej o [creating and managing Virtual Network i punktów końcowych i reguł za pomocą wiersza polecenia platformy Azure usługi](howto-manage-vnet-cli.md).

## <a name="prerequisites"></a>Wymagania wstępne
* [Zainstaluj interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
* [— Azure Database dla serwera MariaDB i bazy danych](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Polecenia reguły zapory:
**Az mariadb reguły zapory serwera-** polecenia jest używany z wiersza polecenia platformy Azure do tworzenie, usuwanie, listy, wyświetlanie i aktualizowanie reguł zapory.

Polecenia:
- **Utwórz**: Tworzenie reguły zapory serwera MariaDB platformy Azure.
- **Usuń**: Usuń regułę zapory serwera MariaDB platformy Azure.
- **Lista**: Lista reguł zapory serwera MariaDB platformy Azure.
- **Pokaż**: Pokaż szczegóły serwera Azure MariaDB reguły zapory.
- **Aktualizacja**: Aktualizuj reguły zapory serwera MariaDB platformy Azure.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Logowanie do platformy Azure i wyświetlać listę usługi Azure Database dla serwerów MariaDB
Bezpiecznie łączyć z wiersza polecenia platformy Azure przy użyciu konta platformy Azure przy użyciu **az login** polecenia.

1. Z wiersza polecenia Uruchom następujące polecenie:
   ```azurecli
   az login
   ```
   To polecenie generuje kod do użycia w następnym kroku.

2. Użyj przeglądarki sieci web, aby otworzyć stronę [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin), a następnie wprowadź kod.

3. W wierszu Zaloguj się przy użyciu swoich poświadczeń platformy Azure.

4. Po autoryzacji Twoje dane logowania listę subskrypcji jest drukowany w konsoli. Skopiuj identyfikator odpowiedniej subskrypcji, aby ustawić bieżącą subskrypcję do użycia. Użyj [zestaw konta az](/cli/azure/account#az-account-set) polecenia.
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Lista baz danych Azure dla serwerów MariaDB dla Twojej subskrypcji i grupie zasobów, w razie wątpliwości nazw. Użyj [listy serwerów mariadb az](/cli/azure/mariadb/server#az-mariadb-server-list) polecenia.

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Należy pamiętać, atrybut nazwy na liście, należy określić serwer MariaDB pracować nad. Jeśli to konieczne, Potwierdź szczegóły dla tego serwera, a przy użyciu nazwy atrybutu, aby upewnić się, że jest prawidłowa. Użyj [az mariadb server show](/cli/azure/mariadb/server#az-mariadb-server-show) polecenia.

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Lista reguł zapory w usłudze Azure Database dla serwera MariaDB 
Za pomocą nazwy serwera i nazwy grupy zasobów, Utwórz listę istniejących reguł zapory serwera na serwerze. Użyj [listy zapory serwera mariadb az](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list) polecenia.  Należy zauważyć, że nazwa serwera jest określony w **— serwer** Przełącz a nie w **— nazwa** przełącznika. 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Dane wyjściowe wyświetla listę reguł, jeśli formatowania w formacie JSON (domyślnie). Możesz użyć **— tabela wyjściowa** przełącznika w danych wyjściowych wyników w bardziej czytelnym formacie tabeli.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Tworzenie reguły zapory w usłudze Azure Database dla serwera MariaDB
Za pomocą Azure MariaDB nazwę serwera i nazwę grupy zasobów Utwórz nową regułę zapory na serwerze. Użyj [tworzenie zapory serwera mariadb az](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) polecenia. Podaj nazwę reguły, a także ustawiony początkowy adres IP i końcowy adres IP (w celu zapewnienia dostępu do zakresu adresów IP) dla tej reguły.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Aby zezwolić na dostęp dla pojedynczego adresu IP, należy podać ten sam adres IP jako początkowy adres IP i końcowy adres IP, jak w poniższym przykładzie.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Aby umożliwić aplikacjom z adresów IP platformy Azure, nawiązać połączenia z usługi Azure Database dla serwera MariaDB, podaj adres IP 0.0.0.0 jako początkowy adres IP i końcowy adres IP, jak w poniższym przykładzie.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

W razie powodzenia tworzone polecenia, które dane wyjściowe Wyświetla szczegóły reguły zapory, które zostały utworzone w formacie JSON (domyślnie). W przypadku awarii dane wyjściowe zawierają tekst komunikatu o błędzie zamiast tego.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Aktualizuj reguły zapory w usłudze Azure Database dla serwera MariaDB 
Za pomocą Azure MariaDB nazwę serwera i nazwę grupy zasobów, zaktualizuj istniejącą regułę zapory na serwerze. Użyj [aktualizacji zapory serwera mariadb az](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update) polecenia. Podaj nazwę istniejącej reguły zapory jako dane wejściowe, a także początek adresów IP i końcowy atrybutów adresu IP do aktualizacji.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
W razie powodzenia dane wyjściowe polecenia Wyświetla szczegóły reguły zapory, które zostały zaktualizowane w formacie JSON (domyślnie). W przypadku awarii dane wyjściowe zawierają tekst komunikatu o błędzie zamiast tego.

> [!NOTE]
> Jeśli nie ma reguły zapory, tworzona jest reguła za pomocą polecenia update.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Pokaż zapory szczegóły reguły w usłudze Azure Database dla serwera MariaDB
Za pomocą Azure MariaDB nazwę serwera i nazwę grupy zasobów, wyświetlić istniejące zapory szczegóły reguły z serwera. Użyj [Pokaż zapory serwera mariadb az](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show) polecenia. Podaj nazwę istniejącej reguły zapory jako dane wejściowe.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
W razie powodzenia dane wyjściowe polecenia Wyświetla szczegóły reguły zapory, które zostały określone w formacie JSON (domyślnie). W przypadku awarii dane wyjściowe zawierają tekst komunikatu o błędzie zamiast tego.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Usuwanie reguły zapory w usłudze Azure Database dla serwera MariaDB
Przy użyciu Azure MariaDB nazwę serwera i nazwę grupy zasobów, usuń istniejącą regułę zapory z serwera. Użyj [Usuń zapory serwera mariadb az](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete) polecenia. Podaj nazwę istniejącej reguły zapory.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
W razie powodzenia nie ma żadnych danych wyjściowych. W przypadku awarii Wyświetla tekst komunikatu o błędzie.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedzieć się więcej o [— Azure Database for reguły zapory serwera MariaDB](./concepts-firewall-rules.md).
- [Tworzenie i zarządzanie nimi — Azure Database dla MariaDB reguł zapory przy użyciu witryny Azure portal](./howto-manage-firewall-portal.md).
- Dodatkowo zabezpieczyć dostęp do serwera przez [creating and managing Virtual Network i punktów końcowych i reguł za pomocą wiersza polecenia platformy Azure usługi](howto-manage-vnet-cli.md).
