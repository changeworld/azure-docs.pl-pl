---
title: Tworzenie zasad przy użyciu zestawu Azure Eksplorator danych C# SDK
description: W tym artykule przedstawiono sposób tworzenia zasad przy użyciu programu C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 8a5ea692bfdec7f676a80cc670f686af66152e6f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606606"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>Tworzenie zasad bazy danych i tabel dla usługi Azure Eksplorator danych przy użyciuC#

> [!div class="op_single_selector"]
> * [C#](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. W tym artykule opisano tworzenie zasad bazy danych i tabel dla usługi Azure Eksplorator danych przy użyciu C#programu.

## <a name="prerequisites"></a>Wymagania wstępne

* Program Visual Studio 2019. Jeśli nie masz programu Visual Studio 2019, możesz pobrać i użyć *bezpłatnej* [społeczności programu Visual Studio 2019](https://www.visualstudio.com/downloads/). Pamiętaj o wybraniu opcji **Programowanie na platformie Azure** podczas instalacji programu Visual Studio.

* Subskrypcja platformy Azure. Jeśli chcesz, możesz utworzyć [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.

* [Klaster testowy i baza danych](create-cluster-database-csharp.md).

* [Tabela testowa](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

## <a name="install-c-nuget"></a>Zainstaluj C# pakiet NuGet

* Zainstaluj [pakiet NuGet platformy Azure Eksplorator danych (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Zainstaluj [pakiet NuGet Microsoft. Azure. Kusto. Data. Standard](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/). (Opcjonalnie, aby zmienić zasady tabeli).

* Zainstaluj [pakiet NuGet Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/), aby uzyskać uwierzytelnianie.

## <a name="authentication"></a>Authentication
Aby uruchomić przykłady z tego artykułu, potrzebna jest aplikacja Azure Active Directory (Azure AD) i nazwa główna usługi, która może uzyskiwać dostęp do zasobów. Możesz użyć tej samej aplikacji usługi Azure AD do uwierzytelniania z [klastra testowego i bazy danych](create-cluster-database-csharp.md#authentication). Jeśli chcesz użyć innej aplikacji usługi Azure AD, zobacz [Tworzenie aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) w celu utworzenia bezpłatnej aplikacji usługi Azure AD i Dodawanie przypisania roli do zakresu subskrypcji. W tym artykule pokazano również, jak uzyskać `Directory (tenant) ID`, `Application ID`i `Client secret`. Może być konieczne dodanie nowej aplikacji usługi Azure AD jako podmiotu zabezpieczeń w bazie danych. Aby uzyskać więcej informacji, zobacz [Zarządzanie uprawnieniami usługi Azure Eksplorator danych Database](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions).

## <a name="alter-database-retention-policy"></a>Zmień zasady przechowywania bazy danych
Ustawia zasady przechowywania z 10-dniowym okresem usuwania nietrwałego.
    
```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(softDeletePeriod: TimeSpan.FromDays(10)));
```

## <a name="alter-database-cache-policy"></a>Zmień zasady pamięci podręcznej bazy danych
Ustawia zasady pamięci podręcznej dla bazy danych. Dane z ostatnich pięciu dni będą znajdować się na dysku SSD klastra.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(hotCachePeriod: TimeSpan.FromDays(5)));
```

## <a name="alter-table-cache-policy"></a>Zmień zasady pamięci podręcznej tabel
Ustawia zasady pamięci podręcznej dla tabeli. Dane z ostatnich pięciu dni będą znajdować się na dysku SSD klastra.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var databaseName = "<DatabaseName>";
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var tableName = "<TableName>"

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = databaseName,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    //dataHotSpan and indexHotSpan should have the same value
    var hotSpan = TimeSpan.FromDays(5);
    var command1 = CslCommandGenerator.GenerateAlterTableCachingPolicyCommand(tableName: tableName,
                    dataHotSpan: hotSpan, indexHotSpan: hotSpan);

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="add-a-new-principal-for-the-database"></a>Dodawanie nowego podmiotu zabezpieczeń dla bazy danych
Dodaje nową aplikację usługi Azure AD jako podmiot zabezpieczeń dla bazy danych.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var clientIdToAdd = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.AddPrincipalsAsync(resourceGroupName, clusterName, databaseName,
                new DatabasePrincipalListRequest()
                {
                    Value = new List<DatabasePrincipal>()
                    {
                        new DatabasePrincipal("Admin", "<database_principle_name>", "App", appId: clientIdToAdd, tenantName:tenantId)
                    }
                });
```
## <a name="next-steps"></a>Następne kroki

* [Przeczytaj więcej na temat zasad bazy danych i tabeli](https://docs.microsoft.com/azure/kusto/management/policies)
