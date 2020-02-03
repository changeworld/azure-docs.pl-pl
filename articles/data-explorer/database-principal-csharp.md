---
title: Dodawanie podmiotów zabezpieczeń bazy danych dla Eksplorator danych platformy Azure za pomocąC#
description: W tym artykule dowiesz się, jak dodać podmioty zabezpieczeń bazy danych dla usługi Azure Eksplorator danych C#przy użyciu programu.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 797d1253d44739f2026563e3df72bc85a8ef382e
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965036"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>Dodawanie podmiotów zabezpieczeń bazy danych dla Eksplorator danych platformy Azure za pomocąC#

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Szablon usługi Azure Resource Manager](database-principal-resource-manager.md)

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. W tym artykule opisano Dodawanie podmiotów zabezpieczeń bazy danych dla usługi Azure Eksplorator danych przy C#użyciu programu.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz zainstalowanego programu Visual Studio 2019, możesz pobrać i korzystać **bezpłatnie** z programu [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.
* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Utwórz klaster i bazę danych](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>Zainstaluj C# pakiet NuGet

* Zainstaluj [Microsoft. Azure. Management. Kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Zainstaluj [Microsoft. Rest. ClientRuntime. Azure. Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) na potrzeby uwierzytelniania.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Dodawanie podmiotu zabezpieczeń bazy danych

Poniższy przykład pokazuje, jak dodać podmiot zabezpieczeń bazy danych programowo.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
string principalAssignmentName = "databasePrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "Admin";//Admin, Ingestor, Monitor, User, UnrestrictedViewers, Viewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var databasePrincipalAssignment = new DatabasePrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.DatabasePrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, principalAssignmentName, databasePrincipalAssignment);
```

|**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
|---|---|---|
| tenantId | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy. Znany również jako identyfikator katalogu.|
| subscriptionId | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator subskrypcji używany do tworzenia zasobów.|
| clientId | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator klienta aplikacji, który może uzyskiwać dostęp do zasobów w dzierżawie.|
| clientSecret | *xxxxxxxxxxxxxx* | Wpis tajny klienta aplikacji, który może uzyskiwać dostęp do zasobów w dzierżawie. |
| resourceGroupName | *testrg* | Nazwa grupy zasobów zawierającej klaster.|
| clusterName | *mykustocluster* | Nazwa klastra.|
| databaseName | *mykustodatabase* | Nazwa bazy danych.|
| principalAssignmentName | *databasePrincipalAssignment1* | Nazwa zasobu głównego bazy danych.|
| principalId | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator podmiotu zabezpieczeń, który może być adresem e-mail użytkownika, IDENTYFIKATORem aplikacji lub grupą zabezpieczeń.|
| role (rola) | *Administratora* | Rola podmiotu zabezpieczeń bazy danych, która może być "admin", "pokarmem", "Monitor", "User", "UnrestrictedViewers", "Viewer".|
| tenantIdForPrincipal | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy podmiotu zabezpieczeń.|
| Nazwa podmiotu zabezpieczeń | *Aplikacja* | Typ podmiotu zabezpieczeń, który może mieć wartość "User", "App" lub "Group"|

## <a name="next-steps"></a>Następne kroki

* [Pozyskiwanie danych przy użyciu biblioteki węzłów Eksplorator danych platformy Azure](node-ingest-data.md)
