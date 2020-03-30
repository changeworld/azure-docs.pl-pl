---
title: Zarządzanie regułami zapory — azure cli — usługa Azure Database for MariaDB
description: W tym artykule opisano sposób tworzenia reguł zapory usługi Azure Database dla mariadb przy użyciu wiersza polecenia interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure CLI i zarządzania nimi.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 898b65f07140bca04bd97ff7314b01920b783914
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530635"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Tworzenie reguł zapory usługi Azure Database dla mariadb i zarządzanie nimi przy użyciu interfejsu wiersza polecenia platformy Azure
Reguły zapory na poziomie serwera mogą służyć do zarządzania dostępem do usługi Azure Database for MariaDB Server z określonego adresu IP lub zakresu adresów IP. Korzystając z wygodnych poleceń interfejsu wiersza polecenia platformy Azure, można tworzyć, aktualizować, usuwać, wyświetlać i wyświetlać reguły zapory w celu zarządzania serwerem. Aby zapoznać się z omówieniem zapór usługi Azure Database dla mariadb, zobacz [Reguły zapory serwera MariaDB usługi Azure Database.](./concepts-firewall-rules.md)

Reguły sieci wirtualnej (VNet) mogą być również używane do bezpiecznego dostępu do serwera. Dowiedz się więcej o [tworzeniu punktów końcowych i regułach usługi sieci wirtualnej i zarządzaniu nimi przy użyciu interfejsu wiersza polecenia platformy Azure](howto-manage-vnet-cli.md).

## <a name="prerequisites"></a>Wymagania wstępne
* [Zainstaluj interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
* [Usługa Azure Database dla serwera i bazy danych MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Polecenia reguł zapory:
Polecenie **az mariadb server firewall-rule** jest używane z interfejsu wiersza polecenia platformy Azure do tworzenia, usuwania, listy, pokazywalenia i aktualizowania reguł zapory.

Polecenia:
- **tworzenie:** Tworzenie reguły zapory serwera usługi Azure MariaDB.
- **delete**: Usuń regułę zapory serwera Usługi Azure MariaDB.
- **lista**: Lista reguł zapory serwera usługi Azure MariaDB.
- **pokaż:** Pokaż szczegóły reguły zapory serwera Usługi Azure MariaDB.
- **aktualizacja**: Zaktualizuj regułę zapory serwera usługi Azure MariaDB.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Zaloguj się na platformie Azure i wystaw swoją usługę Azure Database dla serwerów MariaDB
Bezpiecznie połącz interfejs wiersza polecenia platformy Azure z kontem platformy Azure przy użyciu polecenia **az login.**

1. Z wiersza polecenia uruchom następujące polecenie:
   ```azurecli
   az login
   ```
   To polecenie wyprowadza kod do użycia w następnym kroku.

2. Użyj przeglądarki internetowej, aby [https://aka.ms/devicelogin](https://aka.ms/devicelogin)otworzyć stronę , a następnie wprowadź kod.

3. W wierszu polecenia zaloguj się przy użyciu poświadczeń platformy Azure.

4. Po autoryzacji logowania w konsoli zostanie wydrukowana lista subskrypcji. Skopiuj identyfikator żądanej subskrypcji, aby ustawić bieżącą subskrypcję do użycia. Użyj polecenia [az account set.](/cli/azure/account#az-account-set)
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Lista usług Azure Baz danych dla serwerów MariaDB dla subskrypcji i grupy zasobów, jeśli nie masz pewności co do nazw. Użyj polecenia [az mariadb server list.](/cli/azure/mariadb/server#az-mariadb-server-list)

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Zanotuj atrybut name w aukcji, który należy określić serwer MariaDB do pracy. W razie potrzeby potwierdź szczegóły dla tego serwera i używając atrybutu nazwa, aby upewnić się, że jest poprawny. Użyj polecenia [az mariadb server show.](/cli/azure/mariadb/server#az-mariadb-server-show)

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Lista reguł zapory w usłudze Azure Database dla serwera MariaDB Server 
Używając nazwy serwera i nazwy grupy zasobów, wyświetl listę istniejących reguł zapory serwera na serwerze. Użyj polecenia [listy zapory serwera az mariadb.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list)  Należy zauważyć, że atrybut nazwa serwera jest określony w przełączniku **--server,** a nie w przełączniku **--name.** 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Dane wyjściowe zawierają listę reguł, jeśli istnieją, w formacie JSON (domyślnie). Można użyć przełącznika **tabeli --output,** aby wyprowadzić wyniki w bardziej czytelnym formacie tabeli.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Tworzenie reguły zapory w usłudze Azure Database dla serwera MariaDB Server
Korzystając z nazwy serwera usługi Azure MariaDB i nazwy grupy zasobów, utwórz nową regułę zapory na serwerze. Użyj polecenia [tworzenia zapory serwera az mariadb.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) Podaj nazwę reguły, a także początkowy adres IP i początkowy adres IP (aby zapewnić dostęp do zakresu adresów IP) dla reguły.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Aby zezwolić na dostęp dla jednego adresu IP, podaj ten sam adres IP co początkowy adres IP i początkowy adres IP, jak w tym przykładzie.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Aby zezwolić aplikacjom z adresów IP platformy Azure na łączenie się z usługą Azure Database dla serwera MariaDB, podaj adres IP 0.0.0.0 jako początkowy adres IP i końcowy adres IP, jak w tym przykładzie.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

Po sukcesie każde polecenie wyjścia create zawiera listę szczegółów utworzonej reguły zapory w formacie JSON (domyślnie). Jeśli występuje błąd, dane wyjściowe wyświetla tekst komunikatu o błędzie zamiast tego.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Aktualizowanie reguły zapory na serwerze usługi Azure Database dla mariadb 
Korzystając z nazwy serwera usługi Azure MariaDB i nazwy grupy zasobów, zaktualizuj istniejącą regułę zapory na serwerze. Użyj polecenia [aktualizacji zapory serwera az mariadb.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update) Podaj nazwę istniejącej reguły zapory jako dane wejściowe, a także początkowe atrybuty IP i end IP do aktualizacji.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Po sukcesie dane wyjściowe polecenia zawierają szczegółowe informacje o zaktualizowanej regule zapory w formacie JSON (domyślnie). Jeśli występuje błąd, dane wyjściowe wyświetla tekst komunikatu o błędzie zamiast tego.

> [!NOTE]
> Jeśli reguła zapory nie istnieje, reguła jest tworzona przez polecenie aktualizacji.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Pokaż szczegóły reguły zapory w usłudze Azure Database for MariaDB Server
Korzystając z nazwy serwera usługi Azure MariaDB i nazwy grupy zasobów, pokaż szczegóły istniejącej reguły zapory z serwera. Użyj polecenia [az mariadb server firewall show.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show) Podaj nazwę istniejącej reguły zapory jako dane wejściowe.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Po powodzenie, dane wyjściowe polecenia wyświetla szczegóły reguły zapory, które zostały określone, w formacie JSON (domyślnie). Jeśli występuje błąd, dane wyjściowe wyświetla tekst komunikatu o błędzie zamiast tego.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Usuwanie reguły zapory w usłudze Azure Database dla serwera MariaDB Server
Korzystając z nazwy serwera usługi Azure MariaDB i nazwy grupy zasobów, usuń istniejącą regułę zapory z serwera. Użyj polecenia [usuwania zapory serwera az mariadb.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete) Podaj nazwę istniejącej reguły zapory.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Po sukcesie nie ma wyjścia. Po niepowodzeniu zostanie wyświetlony komunikat o błędzie.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [regułach zapory usługi Azure Database for MariaDB Server](./concepts-firewall-rules.md).
- [Tworzenie reguł zapory usługi Azure Database dla mariadb i zarządzanie nimi przy użyciu portalu Azure](./howto-manage-firewall-portal.md).
- Dalsze bezpieczne dostęp do serwera przez [tworzenie i zarządzanie punktami końcowymi usługi sieci wirtualnej i regułami przy użyciu interfejsu wiersza polecenia platformy Azure](howto-manage-vnet-cli.md).
