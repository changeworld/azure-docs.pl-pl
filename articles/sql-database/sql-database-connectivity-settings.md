---
title: Ustawienia łączności dla usługi Azure SQL Database and Data Warehouse
description: W tym dokumencie wyjaśniono wybór wersji protokołu TLS oraz ustawienie proxy i przekierowanie dla usługi Azure SQL
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: d18fdee85bd0fbabe68fe9890c4a2dc74366041d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366091"
---
# <a name="azure-sql-connectivity-settings"></a>Ustawienia łączności SQL platformy Azure
> [!NOTE]
> Ten artykuł dotyczy serwera SQL platformy Azure oraz baz danych SQL Database i baz danych SQL Data Warehouse utworzonych na serwerze SQL platformy Azure. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

> [!IMPORTANT]
> Ten artykuł *nie* dotyczy **wystąpienia zarządzanego usługi Azure SQL Database**

W tym artykule przedstawiono ustawienia sterujące łącznością z usługą Azure SQL Database na poziomie serwera. Te ustawienia dotyczą **wszystkich** baz danych SQL i baz danych SQL Data Warehouse skojarzonych z serwerem.

> [!NOTE]
> Po zastosowaniu tych ustawień, **stają się one skuteczne natychmiast** i może spowodować utratę połączenia dla klientów, jeśli nie spełniają wymagań dla każdego ustawienia.

Ustawienia łączności są dostępne z **zapory i sieci wirtualnych** bloku, jak pokazano na poniższym zrzucie ekranu:

 ![Zrzut ekranu przedstawiający ustawienia łączności][1]


## <a name="deny-public-network-access"></a>Odmowa dostępu do sieci publicznej
W witrynie Azure portal, gdy **deny dostępu do sieci publicznej** ustawienie jest **ustawiona**na Tak , tylko połączenia za pośrednictwem prywatnych punktów końcowych są dozwolone. Gdy to ustawienie jest ustawione na **Nie,** klienci mogą łączyć się przy użyciu prywatnego lub publicznego punktu końcowego.

Po ustawieniu **Odmowa dostępu do sieci publicznej** **tak,** próby logowania od klientów korzystających z publicznego punktu końcowego zakończyć zakończyć zakończyć się niepowodzeniem z następującym błędem:

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Zmienianie dostępu do sieci publicznej za pośrednictwem programu PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne. Poniższy skrypt wymaga [modułu programu Azure PowerShell](/powershell/azure/install-az-ps).

Następujący skrypt programu PowerShell `Get` `Set` pokazuje, jak i **właściwość Public Network Access** na poziomie serwera logicznego:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>Zmienianie dostępu do sieci publicznej za pośrednictwem interfejsu wiersza polecenia
> [!IMPORTANT]
> Wszystkie skrypty w tej sekcji wymagają [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Narzędzie CLI platformy Azure w powłoce bash
Następujący skrypt interfejsu wiersza polecenia pokazuje, jak zmienić **dostęp do sieci publicznej** w powłoce bash:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>Zasady połączeń
[Zasady połączenia](sql-database-connectivity-architecture.md#connection-policy) określa, jak klienci łączą się z usługą Azure SQL Server. 

## <a name="change-connection-policy-via-powershell"></a>Zmienianie zasad połączeń za pośrednictwem programu PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne. Poniższy skrypt wymaga [modułu programu Azure PowerShell](/powershell/azure/install-az-ps).

Następujący skrypt programu PowerShell pokazuje, jak zmienić zasady połączenia przy użyciu programu PowerShell:

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

## <a name="change-connection-policy-via-azure-cli"></a>Zmienianie zasad połączeń za pośrednictwem interfejsu wiersza polecenia platformy Azure
> [!IMPORTANT]
> Wszystkie skrypty w tej sekcji wymagają [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Narzędzie CLI platformy Azure w powłoce bash
Następujący skrypt interfejsu wiersza polecenia pokazuje, jak zmienić zasady połączenia w powłoce bash: 

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

### <a name="azure-cli-from-a-windows-command-prompt"></a>Narzędzie cli platformy Azure z wiersza polecenia systemu Windows
Poniższy skrypt interfejsu wiersza polecenia pokazuje, jak zmienić zasady połączenia z wiersza polecenia systemu Windows (z zainstalowanym interfejsu wiersza polecenia platformy Azure).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Następne kroki
- Omówienie działania łączności w bazie danych SQL usługi Azure, zobacz [Architektura łączności sql azure](sql-database-connectivity-architecture.md)
- Aby uzyskać informacje na temat zmiany zasad połączenia usługi Azure SQL Database dla serwera bazy danych SQL azure, zobacz [zasady conn.](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
