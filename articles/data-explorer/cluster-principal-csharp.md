---
title: 'Dodawanie podmiotów klastra dla Eksploratora danych platformy Azure przy użyciu języka C #'
description: W tym artykule dowiesz się, jak dodać podmioty klastra dla Usługi Azure Data Explorer przy użyciu języka C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: e6c3970890dfe2c669dee1acf631e9dd45ab1085
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965062"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-c"></a>Dodawanie podmiotów klastra dla Eksploratora danych platformy Azure przy użyciu języka C #

> [!div class="op_single_selector"]
> * [C #](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Szablon usługi Azure Resource Manager](cluster-principal-resource-manager.md)

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. W tym artykule należy dodać podmioty klastra dla Usługi Azure Data Explorer przy użyciu języka C#.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz zainstalowanego programu Visual Studio 2019, możesz pobrać **bezpłatną** [wersję programu Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)i korzystać z niej. Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.
* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* [Tworzenie klastra](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>Zainstaluj C# NuGet

* Zainstaluj [plik Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Zainstaluj [microsoft.rest.clientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) do uwierzytelniania.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>Dodawanie głównego zobowiązanego klastra

W poniższym przykładzie pokazano, jak programowo dodać jednostkę klastra.

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
//The cluster that is created as part of the Prerequisites
var clusterName = "mykustocluster";
string principalAssignmentName = "clusterPrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "AllDatabasesAdmin";//AllDatabasesAdmin or AllDatabasesViewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var clusterPrincipalAssignment = new ClusterPrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.ClusterPrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, principalAssignmentName, clusterPrincipalAssignment);
```

|**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
|---|---|---|
| identyfikator dzierżawy | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy. Znany również jako identyfikator katalogu.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator subskrypcji używany do tworzenia zasobów.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator klienta aplikacji, która może uzyskać dostęp do zasobów w dzierżawie.|
| clientSecret | *xxxxxxxxxxxxxx* | Klucz tajny klienta aplikacji, która może uzyskać dostęp do zasobów w dzierżawie. |
| resourceGroupName | *testrg* | Nazwa grupy zasobów zawierającej klaster.|
| clusterName | *mykustocluster* | Nazwa klastra.|
| principalAssignmentName | *clusterPrincipalAssignment1* | Nazwa zasobu głównego klastra.|
| principalId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Główny identyfikator, który może być e-mail użytkownika, identyfikator aplikacji lub nazwa grupy zabezpieczeń.|
| role (rola) | *AllDatabasesAdmin* | Rola głównego klastra, który może być "AllDatabasesAdmin" lub "AllDatabasesViewer".|
| tenantIdForPrincipal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy głównego zobowiązanego.|
| Principaltype | *Aplikacja* | Typ podmiotu zabezpieczeń, który może być "Użytkownik", "Aplikacja" lub "Grupa"|

## <a name="next-steps"></a>Następne kroki

* [Dodawanie podmiotów bazy danych](database-principal-csharp.md)
