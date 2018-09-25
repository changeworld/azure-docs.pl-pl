---
title: Tworzenie i zarządzanie nimi — Azure Database for postgresql w warstwie reguł zapory przy użyciu wiersza polecenia platformy Azure
description: W tym artykule opisano sposób tworzenia i zarządzania usługi Azure Database for postgresql w warstwie reguł zapory przy użyciu wiersza polecenia z wiersza polecenia platformy Azure.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 05/4/2018
ms.openlocfilehash: f5133b5da055710208390bfe7fd5d6d7d85696df
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965351"
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Tworzenie i zarządzanie nimi — Azure Database for postgresql w warstwie reguł zapory przy użyciu wiersza polecenia platformy Azure
Reguły zapory na poziomie serwera umożliwiają administratorom zarządzanie dostępem do usługi Azure Database dla serwera PostgreSQL z określonego adresu IP lub zakres adresów IP. Przy użyciu wygodne poleceń interfejsu wiersza polecenia platformy Azure, możesz utworzyć, zaktualizować, Usuń listę i Pokaż reguły zapory, aby zarządzać serwerem. Omówienie usługi Azure Database dla PostgreSQL reguł zapory, zobacz [— Azure Database for reguły zapory serwera PostgreSQL](concepts-firewall-rules.md)

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków w tym przewodniku, potrzebne są:
- Zainstaluj [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) narzędzie wiersza polecenia lub użyj usługi Azure Cloud Shell w przeglądarce.
- [— Azure Database for postgresql w warstwie serwera i bazy danych](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Konfigurowanie reguł zapory dla usługi Azure Database for PostgreSQL
[Az postgres server-reguły zapory](/cli/azure/postgres/server/firewall-rule) polecenia są używane do konfigurowania reguł zapory.

## <a name="list-firewall-rules"></a>Lista reguł zapory 
Aby wyświetlić listę istniejących reguł zapory serwera, uruchom [az postgres server reguły zapory na liście](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_list) polecenia.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Dane wyjściowe wyświetla reguł zapory, formatowanie dowolnej domyślnie w formacie JSON. Może użyć przełącznika `--output table` dla bardziej czytelnym formacie tabeli jako dane wyjściowe.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Tworzenie reguły zapory
Aby utworzyć nową regułę zapory na serwerze, uruchom [tworzenie az postgres server-reguły zapory](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) polecenia. 

Określając 0.0.0.0 jako `--start-ip-address` i wartości 255.255.255.255 jako `--end-ip-address` zakres, poniższy przykład umożliwia wszystkie adresy IP uzyskać dostęp do serwera **mydemoserver.postgres.database.azure.com**
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
Aby zezwolić na dostęp do adresu IP w liczbie pojedynczej, należy podać ten sam adres w `--start-ip-address` i `--end-ip-address`, jak w tym przykładzie.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Aby umożliwić aplikacjom z adresów IP platformy Azure, nawiązać połączenia z usługi Azure Database for postgresql w warstwie serwera, podaj adres IP 0.0.0.0 jako początkowy adres IP i końcowy adres IP, jak w poniższym przykładzie.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

W razie powodzenia dane wyjściowe polecenia Wyświetla szczegóły reguły zapory, które zostały utworzone domyślnie w formacie JSON. W przypadku awarii dane wyjściowe pokazują komunikat o błędzie zamiast tego.

## <a name="update-firewall-rule"></a>Aktualizuj reguły zapory 
Aktualizuj istniejącą regułę zapory na serwerze za pomocą [az postgres server reguły zapory na aktualizację](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_update) polecenia. Podaj nazwę istniejącej reguły zapory jako dane wejściowe, a początkiem adresów IP i końcowy atrybutów adresu IP do aktualizacji.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.255
```
W razie powodzenia dane wyjściowe polecenia Wyświetla szczegóły reguły zapory, które zostały zaktualizowane, domyślnie w formacie JSON. W przypadku awarii dane wyjściowe pokazują komunikat o błędzie zamiast tego.
> [!NOTE]
> Jeśli nie ma reguły zapory, tworzony przez polecenie aktualizacji.

## <a name="show-firewall-rule-details"></a>Pokaż szczegóły reguły zapory
Można również wyświetlić szczegółowe informacje o istniejącą regułę zapory na poziomie serwera, uruchamiając [az postgres server reguły zapory show](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_show) polecenia.
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
W razie powodzenia dane wyjściowe polecenia Wyświetla szczegóły reguły zapory, który został określony, domyślnie w formacie JSON. W przypadku awarii dane wyjściowe pokazują komunikat o błędzie zamiast tego.

## <a name="delete-firewall-rule"></a>Usuń regułę zapory
Aby odwołać dostęp dla zakresu adresów IP do serwera, należy usunąć istniejącą regułę zapory, wykonując [Usuń az postgres server-reguły zapory](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_delete) polecenia. Podaj nazwę istniejącej reguły zapory.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
W razie powodzenia nie ma żadnych danych wyjściowych. W przypadku awarii zwracany jest tekst komunikatu o błędzie.

## <a name="next-steps"></a>Kolejne kroki
- Podobnie, można użyć przeglądarki sieci web w taki sposób, aby [tworzenie i zarządzanie nimi — Azure Database for postgresql w warstwie reguł zapory przy użyciu witryny Azure portal](howto-manage-firewall-using-portal.md).
- Dowiedzieć się więcej o [— Azure Database for reguły zapory serwera PostgreSQL](concepts-firewall-rules.md).
- Aby uzyskać pomoc podczas nawiązywania połączenia z usługi Azure Database for postgresql w warstwie serwera, zobacz [biblioteki połączeń dla usługi Azure Database for postgresql w warstwie](concepts-connection-libraries.md).
