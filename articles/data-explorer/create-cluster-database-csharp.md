---
title: Tworzenie klastra Eksplorator danych i bazy danych platformy Azure przy użyciu programuC#
description: Dowiedz się, jak utworzyć klaster Eksplorator danych i bazę danych platformy Azure przy użyciuC#
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 1260b7754f80a3d008084f632d299c684162628e
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667874"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Tworzenie klastra Eksplorator danych i bazy danych platformy Azure przy użyciu programuC#

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [Interfejs wiersza polecenia](create-cluster-database-cli.md)
> * [Program PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Szablon usługi Azure Resource Manager](create-cluster-database-resource-manager.md)

Usługa Azure Data Explorer to szybka, w pełni zarządzana usługa do analizy danych, która pozwala w czasie rzeczywistym analizować duże woluminy danych przesyłanych strumieniowo z aplikacji, witryn internetowych, urządzeń IoT i nie tylko. Aby używać usługi Azure Data Explorer, najpierw utwórz klaster, a następnie utwórz w tym klastrze co najmniej jedną bazę danych. Następnie pozyskaj (załaduj) dane do bazy danych, aby uruchamiać w niej zapytania. W tym artykule opisano tworzenie klastra i bazy danych przy użyciu programu C#.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz zainstalowanego programu Visual Studio 2019, możesz pobrać i korzystać **bezpłatnie** z programu [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.
* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

## <a name="install-c-nuget"></a>Zainstaluj C# pakiet NuGet

* Zainstaluj [pakiet NuGet platformy Azure Eksplorator danych (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Zainstaluj [pakiet NuGet Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) na potrzeby uwierzytelniania.

## <a name="authentication"></a>Uwierzytelnianie
Aby uruchomić przykłady z tego artykułu, potrzebujemy aplikacji usługi Azure AD i nazwy głównej usługi, która może uzyskać dostęp do zasobów. Zaznacz opcję [Utwórz aplikację usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) , aby utworzyć bezpłatną aplikację usługi Azure AD, a następnie Dodaj przypisanie roli do zakresu subskrypcji. Przedstawiono w nim również, jak uzyskać `Directory (tenant) ID`, `Application ID`i `Client Secret`.

## <a name="create-the-azure-data-explorer-cluster"></a>Tworzenie klastra usługi Azure Data Explorer

1. Utwórz klaster przy użyciu następującego kodu:

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
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
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var cluster = new Cluster(location, sku);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

   |**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
   |---|---|---|
   | clusterName | *mykustocluster* | Wybrana nazwa klastra.|
   | skuName | *Standard_D13_v2* | Jednostka SKU, która będzie używana na potrzeby klastra. |
   | tier | *Standard* | Warstwa SKU. |
   | capacity | *Liczba* | Liczba wystąpień klastra. |
   | resourceGroupName | *testrg* | Nazwa grupy zasobów, w której zostanie utworzony klaster. |

    > [!NOTE]
    > **Tworzenie klastra** to długotrwała operacja, dlatego zdecydowanie zaleca się użycie CreateOrUpdateAsync zamiast metodę createorupdate. 

1. Uruchom następujące polecenie, aby sprawdzić, czy klaster został utworzony pomyślnie:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Jeśli wynik zawiera element `ProvisioningState` o wartości `Succeeded`, klaster został utworzony pomyślnie.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Tworzenie bazy danych w klastrze usługi Azure Data Explorer

1. Utwórz bazę danych przy użyciu następującego kodu:

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

   |**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
   |---|---|---|
   | clusterName | *mykustocluster* | Nazwa klastra, w którym zostanie utworzona baza danych.|
   | Bazy | *mykustodatabase* | Nazwa bazy danych.|
   | resourceGroupName | *testrg* | Nazwa grupy zasobów, w której zostanie utworzony klaster. |
   | softDeletePeriod | *3650:00:00:00* | Okres przechowywania danych na potrzeby zapytań. |
   | hotCachePeriod | *3650:00:00:00* | Okres przechowywania danych w pamięci podręcznej. |

2. Uruchom następujące polecenie, aby wyświetlić utworzoną bazę danych:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName);
    ```

Masz teraz klaster i bazę danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

* Jeśli planujesz postępować zgodnie z innymi artykułami, Zachowaj utworzone zasoby.
* Aby wyczyścić zasoby, usuń klaster. Usunięcie klastra powoduje również usunięcie znajdujących się w nim baz danych. Użyj następującego polecenia, aby usunąć klaster:

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Następne kroki

* [Pozyskiwanie danych przy użyciu usługi Azure Eksplorator danych .NET Standard SDK (wersja zapoznawcza)](net-standard-ingest-data.md)
