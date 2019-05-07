---
title: 'Szybki start: Tworzenie klastra Eksplorator danych platformy Azure i bazy danych przy użyciuC#'
description: Dowiedz się, jak utworzyć klaster Eksplorator danych platformy Azure i bazy danych przy użyciuC#
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/25/2019
ms.openlocfilehash: 9ae46d623c0525edf93a4f984393060888fcd009
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143010"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Tworzenie klastra Eksplorator danych platformy Azure i bazy danych przy użyciuC#

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [Interfejs wiersza polecenia](create-cluster-database-cli.md)
> * [Program PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Usługa Azure Data Explorer to szybka, w pełni zarządzana usługa do analizy danych, która pozwala w czasie rzeczywistym analizować duże woluminy danych przesyłanych strumieniowo z aplikacji, witryn internetowych, urządzeń IoT i nie tylko. Aby używać usługi Azure Data Explorer, najpierw utwórz klaster, a następnie utwórz w tym klastrze co najmniej jedną bazę danych. Następnie pozyskaj (załaduj) dane do bazy danych, aby uruchamiać w niej zapytania. W tym przewodniku Szybki Start tworzysz klaster i bazy danych przy użyciu C#.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz zainstalowanego programu Visual Studio 2017, możesz pobrać i używać **bezpłatne** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

## <a name="install-c-nuget"></a>Zainstaluj C# nuget

1. Zainstaluj [pakietu nuget Eksplorator danych platformy Azure (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

1. Zainstaluj [pakietu nuget Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) do uwierzytelniania.

## <a name="create-the-azure-data-explorer-cluster"></a>Tworzenie klastra usługi Azure Data Explorer

1. Tworzenie klastra przy użyciu następującego kodu:

    ```C#-interactive
    string resourceGroupName = "testrg";    
    string clusterName = "mykustocluster";
    string location = "Central US";
    AzureSku sku = new AzureSku("D13_v2", 5);
    Cluster cluster = new Cluster(location, sku);
    
    var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantName}");
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

1. Ustaw [poświadczeń](https://docs.microsoft.com/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)

1. Uruchom następujące polecenie, aby sprawdzić, czy klaster został utworzony pomyślnie:

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
