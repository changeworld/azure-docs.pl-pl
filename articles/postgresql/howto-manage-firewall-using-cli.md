---
title: Zarządzanie regułami zapory — azure cli — Usługa Azure Database for PostgreSQL — single server
description: W tym artykule opisano sposób tworzenia reguł zapory i zarządzania nimi w usłudze Azure Database for PostgreSQL — pojedynczy serwer przy użyciu wiersza polecenia interfejsu wiersza polecenia interfejsu wiersza interfejsu wiersza polecenia platformy Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4af0fb288961689fb051bab8091c838f793cfcc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74765651"
---
# <a name="create-and-manage-firewall-rules-in-azure-database-for-postgresql---single-server-using-azure-cli"></a>Tworzenie reguł zapory w usłudze Azure Database dla postgreSQL i zarządzanie nimi — pojedynczy serwer przy użyciu interfejsu wiersza polecenia platformy Azure
Reguły zapory na poziomie serwera mogą służyć do zarządzania dostępem do usługi Azure Database dla serwera PostgreSQL z określonego adresu IP lub zakresu adresów IP. Korzystając z wygodnych poleceń interfejsu wiersza polecenia platformy Azure, można tworzyć, aktualizować, usuwać, wyświetlać i wyświetlać reguły zapory w celu zarządzania serwerem. Aby zapoznać się z omówieniem reguł zapory usługi Azure Database for PostgreSQL, zobacz [Reguły zapory usługi Azure Database for PostgreSQL Server](concepts-firewall-rules.md).

Reguły sieci wirtualnej (VNet) mogą być również używane do bezpiecznego dostępu do serwera. Dowiedz się więcej o [tworzeniu punktów końcowych i regułach usługi sieci wirtualnej i zarządzaniu nimi przy użyciu interfejsu wiersza polecenia platformy Azure](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebujesz:
- Zainstaluj narzędzie wiersza polecenia interfejsu wiersza interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub użyj usługi Azure Cloud Shell w przeglądarce.
- [Usługa Azure Database dla serwera i bazy danych PostgreSQL](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Konfigurowanie reguł zapory dla usługi Azure Database dla postgreSQL
Polecenia [az postgres serwera firewall-rule](/cli/azure/postgres/server/firewall-rule) służą do konfigurowania reguł zapory.

## <a name="list-firewall-rules"></a>Lista reguł zapory 
Aby wyświetlić listę istniejących reguł zapory serwera, uruchom polecenie [listy listy zapór serwera postgres az postgres.](/cli/azure/postgres/server/firewall-rule)
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Dane wyjściowe domyślnie zawierają listę reguł zapory w formacie JSON. Przełącznika można `--output table` używać do bardziej czytelnego formatu tabeli jako wyjścia.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Tworzenie reguły zapory
Aby utworzyć nową regułę zapory na serwerze, uruchom polecenie utwórz polecenie [utwórz regułę firewalla serwera az postgres.](/cli/azure/postgres/server/firewall-rule) 

```
To allow access to a singular IP address, provide the same address in the `--start-ip-address` and `--end-ip-address`, as in this example, replacing the IP shown here with your specific IP.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Aby zezwolić aplikacjom z adresów IP platformy Azure na łączenie się z usługą Azure Database dla serwera PostgreSQL, podaj adres IP 0.0.0.0 jako początkowy adres IP i końcowy adres IP, jak w tym przykładzie.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

Po sukcesie dane wyjściowe polecenia zawierają szczegółowe informacje o utworzonej regule zapory, która jest domyślnie tworzona w formacie JSON. Jeśli występuje błąd, dane wyjściowe wyświetla komunikat o błędzie zamiast tego.

## <a name="update-firewall-rule"></a>Aktualizuj regułę zapory 
Zaktualizuj istniejącą regułę zapory na serwerze za pomocą polecenia [aktualizacji reguły zapory serwera postgres az postgres.](/cli/azure/postgres/server/firewall-rule) Podaj nazwę istniejącej reguły zapory jako dane wejściowe oraz początkowe atrybuty IP i end IP do aktualizacji.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
Po sukcesie dane wyjściowe polecenia zawierają szczegółowe informacje o zaktualizowanej regule zapory, która jest domyślnie aktualizowana w formacie JSON. Jeśli występuje błąd, dane wyjściowe wyświetla komunikat o błędzie zamiast tego.
> [!NOTE]
> Jeśli reguła zapory nie istnieje, zostanie utworzona przez polecenie aktualizacji.

## <a name="show-firewall-rule-details"></a>Pokaż szczegóły reguły zapory
Szczegóły istniejącej reguły zapory na poziomie serwera można również wyświetlić, uruchamiając polecenie [az postgres server firewall-rule show.](/cli/azure/postgres/server/firewall-rule)
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Po powodzenie, dane wyjściowe polecenia zawiera szczegółowe informacje o reguły zapory, która została określona, domyślnie w formacie JSON. Jeśli występuje błąd, dane wyjściowe wyświetla komunikat o błędzie zamiast tego.

## <a name="delete-firewall-rule"></a>Usuń regułę zapory
Aby odwołać dostęp do zakresu ip do serwera, usuń istniejącą regułę zapory, wykonując polecenie [usuwania reguły zapory serwera az postgres.](/cli/azure/postgres/server/firewall-rule) Podaj nazwę istniejącej reguły zapory.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Po sukcesie nie ma wyjścia. Po niepowodzeniu zwracany jest tekst komunikatu o błędzie.

## <a name="next-steps"></a>Następne kroki
- Podobnie można użyć przeglądarki sieci web do [tworzenia i zarządzania regułami zapory usługi Azure Database dla postgreSQL przy użyciu portalu Azure.](howto-manage-firewall-using-portal.md)
- Dowiedz się więcej o [regułach zapory usługi Azure Database for PostgreSQL Server](concepts-firewall-rules.md).
- Dalsze bezpieczne dostęp do serwera przez [tworzenie i zarządzanie punktami końcowymi usługi sieci wirtualnej i regułami przy użyciu interfejsu wiersza polecenia platformy Azure](howto-manage-vnet-using-cli.md).
- Aby uzyskać pomoc dotyczącą łączenia się z usługą Azure Database dla serwera PostgreSQL, zobacz [Biblioteki połączeń dla usługi Azure Database for PostgreSQL](concepts-connection-libraries.md).
