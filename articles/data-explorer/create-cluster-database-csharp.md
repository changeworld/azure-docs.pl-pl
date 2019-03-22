---
title: 'Szybki start: Tworzenie klastra Eksplorator danych platformy Azure i bazy danych przy użyciuC#'
description: Dowiedz się, jak utworzyć klaster Eksplorator danych platformy Azure i bazy danych przy użyciuC#
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: d861eba6ce905ccaf0d08a08cdd9998a199889da
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287518"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Tworzenie klastra Eksplorator danych platformy Azure i bazy danych przy użyciuC#

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [Interfejs wiersza polecenia](create-cluster-database-cli.md)
> * [Program PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


Ten przewodnik Szybki Start opisuje sposób tworzenia klastra Azure Eksploratora danych i bazy danych przy użyciu C#.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz jeszcze zainstalowanego programu Visual Studio 2017, możesz pobrać program [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) i używać go **bezpłatnie**. Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.

- Do wykonania kroków tego przewodnika Szybki start jest potrzebna subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="install-c-nuget"></a>Zainstaluj C# nuget

- Konieczne będzie pakiet nuget dla platformy Azure Eksplorator danych (Kusto), można znaleźć tutaj Nuget: https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/
- Należy również nuget Microsoft.IdentityModel.Clients.ActiveDirectory uwierzytelniania https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/


## <a name="create-the-azure-data-explorer-cluster"></a>Tworzenie klastra usługi Azure Data Explorer

1. Tworzenie klastra przy użyciu następującego kodu:

    ```C#-interactive
    string resourceGroupName = "testrg";    
    string clusterName = "mykustocluster";
    string location = "Central US";
    AzureSku sku = new AzureSku("D13_v2", 5);
    Cluster cluster = new Cluster(location, sku);
    
    ar authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantName}");
    var credential = new ClientCredential(clientId: "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx", clientSecret: "xxxxxxxxxxxxxx");
    var result = authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential).Result;
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
     
    KustoManagementClient KustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    };

    KustoManagementClient.Clusters.CreateOrUpdate(resourceGroupName, clusterName, cluster);
    ```

   |**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
   |---|---|---|
   | clusterName | *mykustocluster* | Wybrana nazwa klastra.|
   | sku | *D13_v2* | Jednostka SKU, która będzie używana na potrzeby klastra. |
   | resourceGroupName | *testrg* | Nazwa grupy zasobów, w której zostanie utworzony klaster. |

    Istnieją dodatkowe parametry opcjonalne, których można używać, takie jak pojemność klastra.
    
    Ustaw poświadczenia do poświadczeń użytkownika (Aby uzyskać więcej informacji, zobacz https://docs.microsoft.com/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet )

2. Uruchom następujące polecenie, aby sprawdzić, czy klaster został utworzony pomyślnie:

    ```C#-interactive
    KustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Jeśli wynik zawiera element `ProvisioningState` o wartości `Succeeded`, klaster został utworzony pomyślnie.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Tworzenie bazy danych w klastrze usługi Azure Data Explorer

1. Tworzenie bazy danych przy użyciu następującego kodu:

    ```c#-interactive
    TimeSpan hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    TimeSpan softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    string databaseName = "mykustodatabase";
    Database database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);
    
    KustoManagementClient.Databases.CreateOrUpdate(resourceGroupName, clusterName, databaseName, database);
    ```

   |**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
   |---|---|---|
   | clusterName | *mykustocluster* | Nazwa klastra, w którym zostanie utworzona baza danych.|
   | databaseName | *mykustodatabase* | Nazwa bazy danych.|
   | resourceGroupName | *testrg* | Nazwa grupy zasobów, w której zostanie utworzony klaster. |
   | SoftDeletePeriod | *3650:00:00:00* | Okres przechowywania danych na potrzeby zapytań. |
   | hotCachePeriod | *3650:00:00:00* | Okres przechowywania danych w pamięci podręcznej. |

2. Uruchom następujące polecenie, aby wyświetlić utworzoną bazę danych:

    ```c#-interactive
    KustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName);
    ```

Masz teraz klaster i bazę danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

* Jeśli zamierzasz skorzystać z naszych pozostałych przewodników Szybki start i samouczków, zachowaj utworzone zasoby.
* Aby wyczyścić zasoby, usuń klaster. Usunięcie klastra powoduje również usunięcie znajdujących się w nim baz danych. Użyj następującego polecenia, aby usunąć klaster:

    ```C#-interactive
    KustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Szybki start: Pozyskiwanie danych przy użyciu usługi Azure Data Explorer zestaw .NET Standard SDK (wersja zapoznawcza)](net-standard-ingest-data.md)
