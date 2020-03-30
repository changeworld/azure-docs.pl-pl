---
title: 'Dodawanie podmiotów bazy danych dla Eksploratora danych platformy Azure przy użyciu języka C #'
description: W tym artykule dowiesz się, jak dodać podmioty bazy danych dla Usługi Azure Data Explorer przy użyciu języka C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 797d1253d44739f2026563e3df72bc85a8ef382e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965036"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>Dodawanie podmiotów bazy danych dla Eksploratora danych platformy Azure przy użyciu języka C #

> [!div class="op_single_selector"]
> * [C #](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Szablon usługi Azure Resource Manager](database-principal-resource-manager.md)

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. W tym artykule należy dodać podmioty bazy danych dla Usługi Azure Data Explorer przy użyciu języka C#.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz zainstalowanego programu Visual Studio 2019, możesz pobrać **bezpłatną** [wersję programu Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)i korzystać z niej. Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.
* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* [Tworzenie klastra i bazy danych](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>Zainstaluj C# NuGet

* Zainstaluj [plik Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Zainstaluj [microsoft.rest.clientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) do uwierzytelniania.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Dodawanie podmiotu bazy danych

W poniższym przykładzie pokazano, jak programowo dodać jednostkę bazy danych.

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
| identyfikator dzierżawy | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy. Znany również jako identyfikator katalogu.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator subskrypcji używany do tworzenia zasobów.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator klienta aplikacji, która może uzyskać dostęp do zasobów w dzierżawie.|
| clientSecret | *xxxxxxxxxxxxxx* | Klucz tajny klienta aplikacji, która może uzyskać dostęp do zasobów w dzierżawie. |
| resourceGroupName | *testrg* | Nazwa grupy zasobów zawierającej klaster.|
| clusterName | *mykustocluster* | Nazwa klastra.|
| Databasename | *mykustodatabase* | Nazwa bazy danych.|
| principalAssignmentName | *databasePrincipalAssignment1* | Nazwa zasobu głównego bazy danych.|
| principalId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Główny identyfikator, który może być e-mail użytkownika, identyfikator aplikacji lub nazwa grupy zabezpieczeń.|
| role (rola) | *Administracja* | Rola podmiotu głównej bazy danych, który może być "Admin", "Ingestor", "Monitor", "Użytkownik", "UnrestrictedViewers", "Viewer".|
| tenantIdForPrincipal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy głównego zobowiązanego.|
| Principaltype | *Aplikacja* | Typ podmiotu zabezpieczeń, który może być "Użytkownik", "Aplikacja" lub "Grupa"|

## <a name="next-steps"></a>Następne kroki

* [Pozyskiwanie danych przy użyciu biblioteki środowiska Node w usłudze Azure Data Explorer](node-ingest-data.md)
