---
title: Architektura łączności Azure SQL Database i SQL Data Warehouse
description: W tym dokumencie opisano architekturę łączności usługi Azure SQL dla połączeń z bazą danych z platformy Azure lub spoza platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 07/02/2019
ms.openlocfilehash: d414276e081407af8ea21ebcbd96909d34455a22
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690849"
---
# <a name="azure-sql-connectivity-architecture"></a>Architektura łączności usługi Azure SQL

W tym artykule wyjaśniono Azure SQL Database i SQL Data Warehouse architekturę łączności, a także sposób, w jaki różne składniki działają w celu kierowania ruchu do wystąpienia usługi Azure SQL. Te składniki łączności umożliwiają kierowanie ruchu sieciowego do Azure SQL Database lub SQL Data Warehouse z klientami łączącymi się z poziomu platformy Azure i z klientami łączącymi się poza platformą Azure. W tym artykule przedstawiono również przykłady skryptów pozwalające zmienić sposób łączności oraz zagadnienia związane z zmianą domyślnych ustawień łączności.

## <a name="connectivity-architecture"></a>Architektura łączności

Na poniższym diagramie przedstawiono ogólne omówienie architektury łączności Azure SQL Database.

![Przegląd architektury](./media/sql-database-connectivity-architecture/connectivity-overview.png)

W poniższych krokach opisano, jak nawiązać połączenie z bazą danych Azure SQL:

- Klienci łączą się z bramą, która ma publiczny adres IP i nasłuchuje na porcie 1433.
- Brama, w zależności od obowiązujących zasad połączenia, przekierowuje lub proxy ruchu do odpowiedniego klastra bazy danych.
- Wewnątrz ruchu klastra bazy danych jest przekazywany do odpowiedniej bazy danych Azure SQL.

## <a name="connection-policy"></a>Zasady połączenia

Azure SQL Database obsługuje następujące trzy opcje dla ustawienia zasad połączenia serwera SQL Database:

- **Przekierowanie (zalecane):** Klienci nawiązują połączenia bezpośrednio z węzłem hostującym bazę danych. Aby włączyć łączność, klienci muszą zezwolić na wszystkie adresy IP platformy Azure w regionie przy użyciu grup zabezpieczeń sieci (sieciowej grupy zabezpieczeń) z [tagami usług](../virtual-network/security-overview.md#service-tags) dla portów 11000-11999, a nie tylko adresów ip bramy Azure SQL Database na porcie 1433. Ponieważ pakiety przejdą bezpośrednio do bazy danych, opóźnienia i przepływność zwiększają wydajność.
- **Serwer proxy:** W tym trybie wszystkie połączenia są nawiązywane za pośrednictwem bram Azure SQL Database. Aby włączyć łączność, klient musi mieć reguły zapory dla ruchu wychodzącego, które zezwalają tylko na adresy IP bramy Azure SQL Database (zazwyczaj dwa adresy IP na region). Wybranie tego trybu może skutkować większym opóźnieniem i niższą przepływność, w zależności od rodzaju obciążenia. Zdecydowanie zalecamy stosowanie zasad połączenia `Redirect` w ramach zasad połączenia `Proxy` dla najmniejszego opóźnienia i najwyższej przepływności.
- **Wartość domyślna:** Jest to zasada połączenia obowiązująca na wszystkich serwerach po utworzeniu, chyba że jawnie zmienisz zasady połączenia na `Proxy` lub `Redirect`. Obowiązujące zasady zależą od tego, czy połączenia pochodzą z platformy Azure (`Redirect`), czy poza platformą Azure (`Proxy`).

## <a name="connectivity-from-within-azure"></a>Łączność z poziomu platformy Azure

W przypadku łączenia się z poziomu platformy Azure połączenia mają domyślnie zasady połączenia `Redirect`. Zasady `Redirect` oznacza, że po nawiązaniu sesji protokołu TCP z usługą Azure SQL Database sesja klienta zostanie przekierowana do odpowiedniego klastra bazy danych z zmianą docelowego wirtualnego adresu IP z bramy Azure SQL Database Gateway do usługi hosta. Następnie wszystkie kolejne pakiety przepływają bezpośrednio do klastra, pomijając Azure SQL Database bramę. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![Przegląd architektury](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Łączność spoza platformy Azure

Jeśli łączysz się z spoza systemu Azure, połączenia mają domyślnie zasady połączeń `Proxy`. Zasady `Proxy` oznacza, że sesja protokołu TCP jest ustanawiana za pośrednictwem bramy Azure SQL Database i wszystkie kolejne pakiety są przesyłane przez bramę. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![Przegląd architektury](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Adresy IP bramy usługi Azure SQL Database

W poniższej tabeli przedstawiono adresy IP bram według regionów. Aby nawiązać połączenie z Azure SQL Database, musisz zezwolić na ruch sieciowy & ze **wszystkich** bram dla regionu.

Szczegóły dotyczące sposobu migrowania ruchu do nowych bram w określonych regionach znajdują się w następującym artykule: [Azure SQL Database migracji ruchu do nowszych bram](sql-database-gateway-migration.md)


| Nazwa regionu          | Adresy IP bramy |
| --- | --- |
| Australia Środkowa    | 20.36.105.0 |
| Australia Central2   | 20.36.113.0 |
| Australia Wschodnia       | 13.75.149.87, 40.79.161.1 |
| Australia Południowo-Wschodnia | 191.239.192.109, 13.73.109.251 |
| Brazylia Południowa         | 104.41.11.5, 191.233.200.14 |
| Kanada Środkowa       | 40.85.224.249      |
| Kanada Wschodnia          | 40.86.226.166      |
| Środkowe stany USA           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 | 
| Chiny Wschodnie           | 139.219.130.35     |
| Chiny Wschodnie 2         | 40.73.82.1         |
| Chiny Północne          | 139.219.15.17      |
| Chiny Północne 2        | 40.73.50.0         |
| Azja Wschodnia            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| Wschodnie stany USA              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| Wschodnie stany USA 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 | 
| Francja Środkowa       | 40.79.137.0, 40.79.129.1 |
| Niemcy Środkowe      | 51.4.144.100       |
| Niemcy Północne wschód   | 51.5.144.179       |
| Indie Środkowe        | 104.211.96.159     |
| Indie Południowe          | 104.211.224.146    |
| Indie Zachodnie           | 104.211.160.80     |
| Japonia Wschodnia           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 | 
| Japonia Zachodnia           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| Korea Środkowa        | 52.231.32.42       |
| Korea Południowa          | 52.231.200.86      |
| Środkowo-północne stany USA     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Europa Północna         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| Północna Republika Południowej Afryki   | 102.133.152.0      |
| Północna Republika Południowej Afryki    | 102.133.24.0       |
| Środkowo-południowe stany USA     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| Azja Południowo-Wschodnia      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| Środkowe Zjednoczone Emiraty Arabskie          | 20.37.72.64        |
| Północne Zjednoczone Emiraty Arabskie            | 65.52.248.0        |
| Południowe Zjednoczone Królestwo             | 51.140.184.11      |
| Zachodnie Zjednoczone Królestwo              | 51.141.8.11        |
| Środkowo-zachodnie stany USA      | 13.78.145.25       |
| Europa Zachodnia          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| Zachodnie stany USA              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| Zachodnie stany USA 2            | 13.66.226.202      |
|                      |                    |

## <a name="change-azure-sql-database-connection-policy"></a>Zmień zasady połączenia Azure SQL Database

Aby zmienić Azure SQL Database zasad połączenia dla serwera Azure SQL Database, użyj polecenia [poł-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) .

- Jeśli zasady połączenia są ustawione na `Proxy`, wszystkie pakiety sieciowe będą przepływać przez bramę Azure SQL Database. Dla tego ustawienia należy zezwolić na ruch wychodzący tylko do Azure SQL Database IP bramy. Użycie ustawienia `Proxy` ma więcej opóźnień niż ustawienie `Redirect`.
- Jeśli ustawienia zasad połączenia są skonfigurowane `Redirect`, wszystkie pakiety sieciowe będą przepływać bezpośrednio do klastra bazy danych. Dla tego ustawienia należy zezwolić na ruch wychodzący do wielu adresów IP.

## <a name="script-to-change-connection-settings-via-powershell"></a>Skrypt służący do zmiany ustawień połączenia za pośrednictwem programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Poniższy skrypt wymaga [modułu Azure PowerShell](/powershell/azure/install-az-ps).

Poniższy skrypt programu PowerShell przedstawia sposób zmiany zasad połączenia.

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Skrypt służący do zmiany ustawień połączenia za pośrednictwem interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Ten skrypt wymaga [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Interfejs wiersza polecenia platformy Azure w powłoce bash

> [!IMPORTANT]
> Ten skrypt wymaga [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

Poniższy skrypt interfejsu wiersza polecenia przedstawia sposób zmiany zasad połączenia w powłoce bash.

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Interfejs wiersza polecenia platformy Azure z wiersza poleceń systemu Windows

> [!IMPORTANT]
> Ten skrypt wymaga [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

Poniższy skrypt interfejsu wiersza polecenia pokazuje, w jaki sposób zmienić zasady połączenia z wiersza poleceń systemu Windows (z zainstalowanym interfejsem CLI platformy Azure).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat zmiany Azure SQL Database zasad połączenia dla serwera Azure SQL Database, zobacz Connection [-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Aby uzyskać informacje dotyczące zachowania Azure SQL Database połączenia dla klientów korzystających z ADO.NET 4,5 lub nowszej wersji, zobacz [porty wykraczające poza 1433 dla ADO.NET 4,5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Ogólne informacje dotyczące opracowywania aplikacji można znaleźć w temacie [Omówienie tworzenia aplikacji SQL Database](sql-database-develop-overview.md).
