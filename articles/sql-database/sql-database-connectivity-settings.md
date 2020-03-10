---
title: Ustawienia łączności dla Azure SQL Database i magazynu danych
description: W tym dokumencie wyjaśniono wybór wersji protokołu TLS oraz ustawienia proxy i przekierowania dla usługi Azure SQL
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: 8d40dd09144bddc41347947c0123988530f93f90
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945425"
---
# <a name="azure-sql-connectivity-settings"></a>Ustawienia łączności z usługą Azure SQL
> [!NOTE]
> Ten artykuł ma zastosowanie do programu Azure SQL Server oraz do baz danych SQL Database i SQL Data Warehouse utworzonych na serwerze SQL platformy Azure. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

> [!IMPORTANT]
> Ten artykuł *nie* dotyczy **Azure SQL Database wystąpienia zarządzanego**

W tym artykule wprowadzono ustawienia kontrolujące łączność Azure SQL Database na poziomie serwera. Te ustawienia mają zastosowanie do **wszystkich** baz danych SQL Database i SQL Data Warehouse skojarzonych z serwerem.

> [!NOTE]
> Po zastosowaniu tych ustawień **zaczną obowiązywać od razu** i mogą spowodować utratę połączenia dla klientów, jeśli nie spełnią wymagań dla poszczególnych ustawień.

Ustawienia łączności są dostępne z poziomu bloku **zapory i sieci wirtualne** , jak pokazano na poniższym zrzucie ekranu:

 ![Zrzut ekranu ustawień łączności][1]


## <a name="deny-public-network-access"></a>Odmów dostępu do sieci publicznej
W Azure Portal, gdy ustawienie **Odmów dostępu do sieci publicznej** ma wartość **tak**, dozwolone są tylko połączenia za pośrednictwem prywatnych punktów końcowych. Jeśli to ustawienie ma wartość **nie**, klienci mogą łączyć się przy użyciu prywatnego lub publicznego punktu końcowego.

Po ustawieniu opcji **Odmów dostępu do sieci publicznej** **wartość tak**, próby logowania z klientów korzystających z publicznego punktu końcowego zakończą się niepowodzeniem z powodu następującego błędu:

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Zmień dostęp do sieci publicznej za pomocą programu PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Poniższy skrypt wymaga [modułu Azure PowerShell](/powershell/azure/install-az-ps).

Poniższy skrypt programu PowerShell przedstawia sposób `Get` i `Set` Właściwość **publicznego dostępu do sieci** na poziomie serwera logicznego:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>Zmień dostęp do sieci publicznej za pomocą interfejsu wiersza polecenia
> [!IMPORTANT]
> Wszystkie skrypty w tej sekcji wymagają [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Interfejs wiersza polecenia platformy Azure w powłoce bash
Poniższy skrypt interfejsu wiersza polecenia przedstawia sposób zmiany **dostępu do sieci publicznej** w bash Shell:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```

## <a name="minimal-tls-version"></a>Minimalna wersja protokołu TLS 
Minimalna wersja protokołu TLS umożliwia klientowi kontrolowanie wersji [Transport Layer Security](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) SQL Server platformy Azure.

Zalecamy ustawienie minimalnej wersji protokołu TLS na 1,2. W przypadku klientów mających aplikacje korzystające ze starszej wersji protokołu TLS zalecamy ustawienie minimalnej wersji protokołu TLS zgodnie z wymaganiami aplikacji. W przypadku klientów korzystających z aplikacji do łączenia się z nieszyfrowanym połączeniem nie zaleca się stosowania żadnej minimalnej wersji protokołu TLS. Aby uzyskać dodatkowe informacje, zobacz [zagadnienia dotyczące protokołu TLS dotyczące SQL Database łączności](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

Po ustawieniu minimalnej wersji protokołu TLS próby logowania z klientów korzystających z wersji TLS mniejszej niż minimalna wersja protokołu TLS serwera zakończą się niepowodzeniem z powodu następującego błędu:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Ustawianie minimalnej wersji protokołu TLS za pośrednictwem programu PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Poniższy skrypt wymaga [modułu Azure PowerShell](/powershell/azure/install-az-ps).

Poniższy skrypt programu PowerShell przedstawia sposób `Get` i `Set` Właściwość **minimalnej wersji protokołu TLS** na poziomie serwera logicznego:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Ustawianie minimalnej wersji protokołu TLS za pośrednictwem interfejsu wiersza polecenia platformy Azure
> [!IMPORTANT]
> Wszystkie skrypty w tej sekcji wymagają [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Interfejs wiersza polecenia platformy Azure w powłoce bash
Poniższy skrypt interfejsu wiersza polecenia przedstawia sposób zmiany ustawienia **minimalnej wersji protokołu TLS** w bash Shell:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```

## <a name="connection-policy"></a>Zasady połączenia
[Zasady połączeń](sql-database-connectivity-architecture.md#connection-policy) określają, jak klienci nawiązują połączenie z usługą Azure SQL Server. 

## <a name="change-connection-policy-via-powershell"></a>Zmiana zasad połączenia za pomocą programu PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Poniższy skrypt wymaga [modułu Azure PowerShell](/powershell/azure/install-az-ps).

Poniższy skrypt programu PowerShell przedstawia sposób zmiany zasad połączenia przy użyciu programu PowerShell:

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

## <a name="change-connection-policy-via-azure-cli"></a>Zmiana zasad połączenia za pomocą interfejsu wiersza polecenia platformy Azure
> [!IMPORTANT]
> Wszystkie skrypty w tej sekcji wymagają [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Interfejs wiersza polecenia platformy Azure w powłoce bash
Poniższy skrypt interfejsu wiersza polecenia przedstawia sposób zmiany zasad połączenia w bash Shell: 

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
- Aby uzyskać informacje na temat sposobu działania łączności w Azure SQL Database, zobacz [Architektura łączności usługi Azure SQL](sql-database-connectivity-architecture.md) .
- Aby uzyskać informacje na temat zmiany Azure SQL Database zasad połączenia dla serwera Azure SQL Database, zobacz Connection [-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
