---
title: Architektura bazy danych SQL Azure w łączności | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano Azure SQLDB łączności architekturę z platformy Azure lub z poza platformą Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 01/24/2018
ms.author: carlrab
ms.openlocfilehash: 76af1ead671a6d6b279202eaf7886463d4cd8bb7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="azure-sql-database-connectivity-architecture"></a>Architektura połączenia bazy danych Azure SQL 

W tym artykule opisano architekturę połączenia bazy danych SQL Azure oraz wyjaśniono, jak różnych składników funkcji umożliwiających kierowanie ruchem do Twojego wystąpienia bazy danych SQL Azure. Te bazy danych SQL Azure łączności składniki funkcja kierowanie ruchem do bazy danych platformy Azure z klientów łączących się w obrębie platformy Azure i klientów łączących się z poza platformą Azure. Ten artykuł zawiera również przykłady skryptów, aby zmienić sposób występuje łączności i zagadnienia związane z Zmienianie domyślnych ustawień połączenia. 

## <a name="connectivity-architecture"></a>Architektura łączności

Następujący diagram zawiera omówienie architektury połączenia bazy danych SQL Azure.

![Przegląd architektury](./media/sql-database-connectivity-architecture/architecture-overview.png)


W poniższych krokach opisano, jak jest nawiązywane połączenie z bazą danych Azure SQL za pośrednictwem usługi Azure SQL Database oprogramowania równoważenia obciążenia (Programowego) i bramy bazy danych SQL Azure.

- Klienci w obrębie platformy Azure lub na zewnątrz Azure nawiązać Programowego, który ma publicznego adresu IP i nasłuchuje na porcie 1433.
- Programowego kieruje ruch do bramy bazy danych SQL Azure.
- Brama przekierowuje ruch z oprogramowaniem pośredniczącym poprawne serwera proxy.
- Oprogramowanie pośredniczące serwera proxy przekierowuje ruch z odpowiednią bazą danych Azure SQL.

> [!IMPORTANT]
> Każdy z tych składników został rozesłany odmowa wbudowanych w sieci i warstwy aplikacji protection service (DDoS).
>

## <a name="connectivity-from-within-azure"></a>Łączność między w obrębie platformy Azure

Jeśli nawiązujesz w obrębie platformy Azure połączenia mają zasady połączenia **przekierowania** domyślnie. Zasady **przekierowania** oznacza, że połączenia po TCP ustanowieniu sesji w bazie danych Azure SQL, sesja klienta jest przekierowywane do oprogramowania pośredniczącego serwera proxy z zmianę wirtualnego adresu IP docelowej niż bramy bazy danych SQL Azure do tego oprogramowania pośredniczącego serwera proxy. Następnie wszystkie kolejne pakiety przepływ bezpośrednio za pomocą oprogramowania pośredniczącego serwera proxy, pomijanie bramy bazy danych SQL Azure. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![Przegląd architektury](./media/sql-database-connectivity-architecture/connectivity-from-within-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Łączność z poza platformą Azure

Jeśli łączysz się z zewnętrznej platformy Azure, połączenia mają zasady połączenia **Proxy** domyślnie. Zasady **Proxy** oznacza, że ustanowieniu sesji TCP za pośrednictwem bramy bazy danych SQL Azure i wszystkie kolejne pakiety przepływu za pośrednictwem bramy. Na poniższym diagramie przedstawiono ten przepływ ruchu.

![Przegląd architektury](./media/sql-database-connectivity-architecture/connectivity-from-outside-azure.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Adresy IP bramy bazy danych SQL Azure

Aby połączyć się z bazą danych Azure SQL z lokalnymi zasobami, musisz zezwolić wychodzącego ruchu sieciowego do bramy usługi Azure SQL Database w Twoim regionie Azure. Połączenia postępować tylko za pośrednictwem bramy podczas nawiązywania połączenia w trybie serwera Proxy, który jest domyślnym podczas nawiązywania połączenia z zasobami lokalnymi.

W poniższej tabeli wymieniono głównych i dodatkowych adresów IP bramy bazy danych SQL Azure dla wszystkich obszarów danych. W pewnych regionach istnieją dwa adresy IP. W tych regionów podstawowego adresu IP jest bieżący adres IP bramy i drugi adres IP jest adresem IP trybu failover. Adres pracy awaryjnej jest adres, do którego możemy przenieść serwer, aby zapewnić wysoką dostępność usług. Dla tych regionów zaleca się, że musisz zezwolić na ruch wychodzący do adresów IP. Jako drugiego adresu IP jest własnością firmy Microsoft i nie będzie nasłuchiwać na wszystkich usług, dopóki nie zostanie aktywowany przez bazę danych SQL Azure do akceptowania połączeń.

| Nazwa regionu | Podstawowy adres IP | Pomocniczy adres IP |
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
| Wielka Brytania Południowa 1 | 51.140.184.11 | |
| Południowe Zjednoczone Królestwo 2 | 13.87.34.7 | |
| Zachodnie Zjednoczone Królestwo | 51.141.8.11  | |
| Środkowo-zachodnie stany USA | 13.78.145.25 | |
| Europa Zachodnia | 191.237.232.75 | 40.68.37.158 |
| Zachodnie stany USA 1 | 23.99.34.75 | 104.42.238.205 |
| Zachodnie stany USA 2 | 13.66.226.202  | |
||||

\* **Uwaga:** *wschodnie stany USA 2* również ma adres IP trzeciorzędny `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Zmień zasady połączenia bazy danych SQL Azure

Aby zmienić zasady połączenia bazy danych SQL Azure dla serwera bazy danych SQL Azure, użyj [zasad Poł](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) polecenia.

- Jeśli ustawiono zasady połączenia **Proxy**, wszystkich sieci przepływu pakietów za pośrednictwem bramy bazy danych SQL Azure. Dla tego ustawienia, należy zezwolić na ruch wychodzący do IP bramy bazy danych SQL Azure. Przy użyciu ustawienie **Proxy** ma opóźnienia więcej niż ustawienie **przekierowania**.
- Jeśli to ustawienie zasad połączenia **przekierowania**, wszystkich sieci przepływu pakietów bezpośrednio do serwera proxy oprogramowania pośredniczącego. Dla tego ustawienia, należy zezwolić na ruch wychodzący do wielu adresów IP.

## <a name="script-to-change-connection-settings-via-powershell"></a>Skrypt, aby zmienić ustawienia połączenia za pomocą programu PowerShell

> [!IMPORTANT]
> Ten skrypt wymaga [modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).
>

Poniższy skrypt programu PowerShell pokazuje, jak zmienić zasad połączenia.

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

## <a name="script-to-change-connection-settings-via-azure-cli-20"></a>Skrypt, aby zmienić ustawienia połączenia za pośrednictwem 2.0 interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Ten skrypt wymaga [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
>

Poniższy skrypt interfejsu wiersza polecenia pokazuje, jak zmienić zasad połączenia.

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

- Aby uzyskać informacje dotyczące zmieniania bazy danych SQL Azure zasady połączenia dla serwera bazy danych SQL Azure, zobacz [Poł zasad](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Uzyskać informacji dotyczących zachowania połączenia bazy danych SQL Azure dla klientów używających ADO.NET 4.5 lub nowszej wersji, zobacz [porty inne niż 1433 dla ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Informacje ogólne programowanie ogólne aplikacji, zobacz [Omówienie projektowania aplikacji bazy danych SQL](sql-database-develop-overview.md).
