---
title: Architektura łączności usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: W tym dokumencie wyjaśniono Azure SQLDB architektura łączności z w obrębie platformy Azure lub z spoza platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DhruvMsft
ms.author: dhruv
ms.reviewer: carlrab
manager: craigg
ms.date: 01/24/2018
ms.openlocfilehash: 66f558db713ab951864fe694f27f2e60d52e875a
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064150"
---
# <a name="azure-sql-database-connectivity-architecture"></a>Architektura łączności bazy danych Azure SQL 

W tym artykule opisano architekturę łączności usługi Azure SQL Database i wyjaśniono, jak różne składniki funkcji kierować ruch do wystąpienia usługi Azure SQL Database. Te usługi Azure SQL Database łączności składniki funkcji do kierowania ruchu sieciowego do bazy danych platformy Azure z klientów łączących się z w obrębie platformy Azure i klientów łączących się z spoza platformy Azure. Ten artykuł zawiera także przykłady skryptów, aby zmienić sposób występuje łączności i zagadnienia związane z Zmienianie domyślnych ustawień połączenia. 

## <a name="connectivity-architecture"></a>Architektura łączności

Na poniższym diagramie przedstawiono ogólny przegląd architektury połączenia usługi Azure SQL Database.

![Omówienie architektury](./media/sql-database-connectivity-architecture/architecture-overview.png)


W poniższych krokach opisano, jak jest nawiązywane połączenie z bazą danych Azure SQL za pomocą usługi Azure SQL Database oprogramowania równoważenia obciążenia (SLB) i bramy usługi Azure SQL Database.

- Klientom w obrębie platformy Azure i spoza platformy Azure, nawiązać SLB, która ma publiczny adres IP i nasłuchuje na porcie 1433.
- SLB kieruje ruch do bramy usługi Azure SQL Database.
- Brama przekierowuje ruch z oprogramowaniem pośredniczącym prawidłowy serwer proxy.
- Oprogramowanie pośredniczące serwera proxy przekierowuje ruch z odpowiednią bazą danych Azure SQL.

> [!IMPORTANT]
> Każdego z tych składników ma distributed denial protection service (DDoS) wbudowanych w sieci i warstwy aplikacji.
>

## <a name="connectivity-from-within-azure"></a>Łączność z w obrębie platformy Azure

Jeśli połączenie jest nawiązywane z w obrębie platformy Azure, Twoje połączenia mają zasady połączenia **przekierowania** domyślnie. Zasady **przekierowania** oznacza, że połączenia po TCP ustanowieniu sesji w bazie danych Azure SQL, sesja klienta jest następnie przekierowywane do oprogramowania pośredniczącego serwera proxy w przypadku zmiany docelowego wirtualnego adresu IP niż platformy Azure Brama bazy danych SQL do tego oprogramowania pośredniczącego serwera proxy. Dzięki temu wszystkie kolejne pakiety przepływ bezpośrednio za pomocą oprogramowania pośredniczącego serwera proxy, z pominięciem bramy usługi Azure SQL Database. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![Omówienie architektury](./media/sql-database-connectivity-architecture/connectivity-from-within-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Łączność z spoza platformy Azure

Jeśli nawiązujesz połączenie spoza platformy Azure, Twoje połączenia mają zasady połączenia **Proxy** domyślnie. Zasady **Proxy** oznacza, że zostanie utworzona sesja protokołu TCP, za pośrednictwem bramy usługi Azure SQL Database i wszystkie kolejne pakiety przepływu za pośrednictwem bramy. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![Omówienie architektury](./media/sql-database-connectivity-architecture/connectivity-from-outside-azure.png)

> [!IMPORTANT]
> Podczas korzystania z punktów końcowych usługi za pomocą usługi Azure SQL Database zasad jest **Proxy** domyślnie. Aby włączyć łączność z wewnątrz sieci wirtualnej, musisz zezwolić na połączenia wychodzące adresy IP bramy bazy danych SQL Azure określone na liście poniżej. Korzystając z punktów końcowych usługi zdecydowanie zaleca się zmianę zasad połączenia do **przekierowania** umożliwiające lepszą wydajność. W przypadku zmiany zasad połączenia do **przekierowania** nie będą wystarczające, aby umożliwić ruchu wychodzącego w sieciowej grupie zabezpieczeń do bramy usługi Azure SQLDB adresów IP wymienionych poniżej, musisz zezwolić na ruch wychodzący do wszystkich adresów IP SQLDB usługi Azure. Można to zrobić za pomocą tagów usługi sieciowej grupy zabezpieczeń (sieciowych grup zabezpieczeń). Aby uzyskać więcej informacji, zobacz [tagi usługi](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

## <a name="azure-sql-database-gateway-ip-addresses"></a>Adresy IP bramy usługi Azure SQL Database

Aby nawiązać połączenie z bazą danych Azure SQL z zasobami lokalnymi, należy zezwolić na wychodzący ruch sieciowy do bramy usługi Azure SQL Database dla regionu platformy Azure. Połączenia przejść tylko za pośrednictwem bramy podczas nawiązywania połączenia w trybie serwera Proxy, który jest domyślną kolekcją podczas nawiązywania połączenia z zasobami lokalnymi.

Poniższa tabela zawiera listę podstawowych i pomocniczych adresów IP bramy usługi Azure SQL Database dla wszystkich obszarach danych. W niektórych regionach istnieją dwa adresy IP. W tych regionach podstawowy adres IP jest bieżący adres IP bramy, a drugi adres IP jest adresem IP trybu failover. Adres trybu failover jest adresem, do którego możemy przenieść serwer zapewnienie wysokiej dostępności usługi. W tych regionach zaleca się zezwolenie wychodzące adresy IP. Drugi adres IP jest własnością firmy Microsoft i nie będzie nasłuchiwać na wszystkie usługi, dopóki nie zostanie aktywowany przez usługę Azure SQL Database do akceptowania połączeń.

> [!IMPORTANT]
> Jeśli łączysz się z w obrębie platformy Azure będzie zasad połączenia **przekierowania** domyślnie (z wyjątkiem sytuacji, jeśli używasz punktów końcowych usługi). Nie będzie wystarczająca do następujących adresów IP. Musisz zezwolić na wszystkie adresy IP bazy danych SQL Azure. Jeśli połączenie jest nawiązywane z w obrębie sieci wirtualnej, można to zrobić za pomocą tagów usługi sieciowej grupy zabezpieczeń (sieciowych grup zabezpieczeń). Aby uzyskać więcej informacji, zobacz [tagi usługi](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

| Nazwa regionu | Podstawowy adres IP | Adres IP pomocniczego |
| --- | --- |--- |
| Australia Wschodnia | 191.238.66.109 | 13.75.149.87 |
| Australia Południowo-Wschodnia | 191.239.192.109 | 13.73.109.251 |
| Brazylia Południowa | 104.41.11.5 | |
| Kanada Środkowa | 40.85.224.249 | |
| Kanada Wschodnia | 40.86.226.166 | |
| Środkowe stany USA | 23.99.160.139 | 13.67.215.62 |
| Azja Wschodnia | 191.234.2.139 | 52.175.33.150 |
| Wschodnie stany USA 1 | 191.238.6.43 | 40.121.158.30 |
| Wschodnie stany USA 2 | 191.239.224.107 | 40.79.84.180 * |
| Indie Środkowe | 104.211.96.159  | |
| Indie Południowe | 104.211.224.146  | |
| Indie Zachodnie | 104.211.160.80 | |
| Japonia Wschodnia | 191.237.240.43 | 13.78.61.196 |
| Japonia Zachodnia | 191.238.68.11 | 104.214.148.156 |
| Korea Środkowa | 52.231.32.42 | |
| Korea Południowa | 52.231.200.86 |  |
| Środkowo-północne stany USA | 23.98.55.75 | 23.96.178.199 |
| Europa Północna | 191.235.193.75 | 40.113.93.91 |
| Środkowo-południowe stany USA | 23.98.162.75 | 13.66.62.124 |
| Azja Południowo-Wschodnia | 23.100.117.95 | 104.43.15.0 |
| Północne Zjednoczone Królestwo | 13.87.97.210 | |
| Południowe Zjednoczone Królestwo 1 | 51.140.184.11 | |
| Południowe Zjednoczone Królestwo 2 | 13.87.34.7 | |
| Zachodnie Zjednoczone Królestwo | 51.141.8.11  | |
| Środkowo-zachodnie stany USA | 13.78.145.25 | |
| Europa Zachodnia | 191.237.232.75 | 40.68.37.158 |
| Zachodnie stany USA 1 | 23.99.34.75 | 104.42.238.205 |
| Zachodnie stany USA 2 | 13.66.226.202  | |
||||

\* **Uwaga:** *wschodnie stany USA 2* ma również trzeciorzędny adres IP `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Zmień zasady połączenia usługi Azure SQL Database

Aby zmienić zasady połączenia usługi Azure SQL Database dla serwera usługi Azure SQL Database, użyj [zasad połąc](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) polecenia.

- Jeśli ustawiono zasady połączenia **Proxy**, wszystkich sieci przepływu pakietów za pośrednictwem bramy usługi Azure SQL Database. To ustawienie, należy zezwolić na ruch wychodzący do IP bramy usługi Azure SQL Database. Przy użyciu ustawień programu **Proxy** ma więcej opóźnienia, niż ustawienie **przekierowania**.
- Jeśli to ustawienie zasad połączenia **przekierowania**, wszystkich sieci przepływu pakietów bezpośrednio do serwera proxy oprogramowania pośredniczącego. To ustawienie, należy zezwolić na ruch wychodzący do wielu adresów IP.

## <a name="script-to-change-connection-settings-via-powershell"></a>Skrypt, aby zmienić ustawienia połączenia za pośrednictwem programu PowerShell

> [!IMPORTANT]
> Ten skrypt wymaga [modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).
>

Poniższy skrypt programu PowerShell pokazuje, jak zmienić zasady połączenia.

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <Subscription Name>

# Azure Active Directory ID
$tenantId = "<Azure Active Directory GUID>"
$authUrl = "https://login.microsoftonline.com/$tenantId"

# Subscription ID
$subscriptionId = "<Subscription GUID>"

# Create an App Registration in Azure Active Directory.  Ensure the application type is set to NATIVE
# Under Required Permissions, add the API:  Windows Azure Service Management API

# Specify the redirect URL for the app registration
$uri = "<NATIVE APP - REDIRECT URI>"

# Specify the application id for the app registration
$clientId = "<NATIVE APP - APPLICATION ID>"

# Logical SQL Server Name
$serverName = "<LOGICAL DATABASE SERVER - NAME>"

# Resource Group where the SQL Server is located
$resourceGroupName= "<LOGICAL DATABASE SERVER - RESOURCE GROUP NAME>"


# Login and acquire a bearer token
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$result = $AuthContext.AcquireToken(
"https://management.core.windows.net/",
$clientId,
[Uri]$uri,
[Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Auto
)

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

#Get current connection Policy
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method GET -Headers $authHeader

#Set connection policy to Proxy
$connectionType="Proxy" <#Redirect / Default are other options#>
$body = @{properties=@{connectionType=$connectionType}} | ConvertTo-Json

# Apply Changes
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Skrypt, aby zmienić ustawienia połączenia za pośrednictwem wiersza polecenia platformy Azure

> [!IMPORTANT]
> Ten skrypt wymaga [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
>

Poniższy skrypt interfejsu wiersza polecenia pokazuje, jak zmienić zasady połączenia.

<pre>
# Get SQL Server ID
sqlserverid=$(az sql server show -n <b>sql-server-name</b> -g <b>sql-server-group</b> --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy 
az resource show --ids $id

# Update connection policy 
az resource update --ids $id --set properties.connectionType=Proxy

</pre>

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacje na temat zmiany zasad połączenia usługi Azure SQL Database dla serwera usługi Azure SQL Database, zobacz [zasad połąc](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Dla informacji o zachowanie połączenia usługi Azure SQL Database dla klientów korzystających z platformy ADO.NET 4.5 lub nowszej wersji, zobacz [portów wyższych niż 1433 dla platformy ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Informacje ogólne programowania ogólnego zastosowania, zobacz [omówienie tworzenia aplikacji bazy danych SQL](sql-database-develop-overview.md).
