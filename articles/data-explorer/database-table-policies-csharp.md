---
title: Tworzenie zasad przy użyciu zestawu SDK C#Eksploratora danych platformy Azure
description: W tym artykule dowiesz się, jak tworzyć zasady przy użyciu języka C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 17312840b0081056ad04723f2b2c241c47902021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667303"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>Tworzenie zasad bazy danych i tabel dla Eksploratora danych platformy Azure przy użyciu języka C #

> [!div class="op_single_selector"]
> * [C #](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. W tym artykule utworzysz zasady bazy danych i tabel dla Eksploratora danych platformy Azure przy użyciu języka C#.

## <a name="prerequisites"></a>Wymagania wstępne

* Visual Studio 2019. Jeśli nie masz programu Visual Studio 2019, możesz pobrać bezpłatną [aplikację Visual Studio Community 2019](https://www.visualstudio.com/downloads/)i korzystać z *niej.* Pamiętaj, aby wybrać **tworzenie platformy Azure** podczas konfiguracji programu Visual Studio.
* Subskrypcja platformy Azure. Jeśli zajdzie taka potrzeba, możesz utworzyć [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* [Klaster testowy i baza danych](create-cluster-database-csharp.md).
* [Tabela testowa](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

## <a name="install-c-nuget"></a>Zainstaluj C# NuGet

* Zainstaluj [pakiet Azure Data Explorer (Kusto) NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Zainstaluj [pakiet Microsoft.Azure.Kusto.Data.NETStandard NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/). (Opcjonalnie, do przesiewania zasad tabeli.)
* Zainstaluj [pakiet Microsoft.IdentityModel.Clients.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/), do uwierzytelniania.

## <a name="authentication"></a>Uwierzytelnianie
Aby uruchomić przykłady w tym artykule, potrzebujesz aplikacji i jednostki usługi Azure Active Directory (Azure AD), która może uzyskiwać dostęp do zasobów. Tej samej aplikacji usługi Azure AD można użyć do uwierzytelniania z [klastra testowego i bazy danych](create-cluster-database-csharp.md#authentication). Jeśli chcesz użyć innej aplikacji usługi Azure AD, zobacz [tworzenie aplikacji usługi Azure AD,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) aby utworzyć bezpłatną aplikację usługi Azure AD i dodać przypisanie roli w zakresie subskrypcji. W tym artykule pokazano `Directory (tenant) ID` `Application ID`również, `Client secret`jak uzyskać , , i . Może być konieczne dodanie nowej aplikacji usługi Azure AD jako podmiotu zabezpieczeń w bazie danych. Aby uzyskać więcej informacji, zobacz [Zarządzanie uprawnieniami bazy danych Usługi Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions).

## <a name="alter-database-retention-policy"></a>Zmienianie zasad przechowywania bazy danych
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

## <a name="alter-database-cache-policy"></a>Zmienianie zasad pamięci podręcznej bazy danych
Ustawia zasady pamięci podręcznej dla bazy danych. Poprzednie pięć dni danych będzie na ssd klastra.

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

## <a name="alter-table-cache-policy"></a>Zmienianie zasad pamięci podręcznej tabel
Ustawia zasady pamięci podręcznej dla tabeli. Poprzednie pięć dni danych będzie na ssd klastra.

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
Dodaje nową aplikację usługi Azure AD jako podmiot administracyjny bazy danych.

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

* [Dowiedz się więcej o zasadach bazy danych i tabel](https://docs.microsoft.com/azure/kusto/management/policies)
