---
title: 'Tworzenie klastra usługi Azure Data Explorer & bazy danych przy użyciu języka C #'
description: 'Dowiedz się, jak utworzyć klaster i bazę danych usługi Azure Data Explorer przy użyciu c #'
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 0c32d438ac8551f061343edb747e9fc035b498e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246411"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Tworzenie klastra i bazy danych usługi Azure Data Explorer przy użyciu języka C #

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [Cli](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Szablon usługi Azure Resource Manager](create-cluster-database-resource-manager.md)

Usługa Azure Data Explorer to szybka, w pełni zarządzana usługa do analizy danych, która pozwala w czasie rzeczywistym analizować duże woluminy danych przesyłanych strumieniowo z aplikacji, witryn internetowych, urządzeń IoT i nie tylko. Aby używać usługi Azure Data Explorer, najpierw utwórz klaster, a następnie utwórz w tym klastrze co najmniej jedną bazę danych. Następnie pozyskaj (załaduj) dane do bazy danych, aby uruchamiać w niej zapytania. W tym artykule utworzysz klaster i bazę danych przy użyciu języka C#.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz zainstalowanego programu Visual Studio 2019, możesz pobrać **bezpłatną** [wersję programu Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)i korzystać z niej. Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.
* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

## <a name="authentication"></a>Uwierzytelnianie
Aby uruchomić przykłady w tym artykule, potrzebujemy usługi Azure AD aplikacji i jednostki usługi, które mogą uzyskać dostęp do zasobów. Sprawdź [tworzenie aplikacji usługi Azure AD,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) aby utworzyć bezpłatną aplikację usługi Azure AD i dodać przypisanie roli w zakresie subskrypcji. Pokazuje również, jak `Directory (tenant) ID`uzyskać `Application ID`, `Client Secret`, i .

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
   | skuName (nazwa skuname) | *Standardowa_D13_v2* | Jednostka SKU, która będzie używana na potrzeby klastra. |
   | tier | *Standardowa* | Warstwa SKU. |
   | capacity | *numer* | Liczba wystąpień klastra. |
   | resourceGroupName | *testrg* | Nazwa grupy zasobów, w której zostanie utworzony klaster. |

    > [!NOTE]
    > **Tworzenie klastra** jest długotrwałą operacją, dlatego zaleca się użycie createorupdateAsync zamiast CreateOrUpdate. 

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
    var database = new ReadWriteDatabase(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

        [!NOTE]
        If you are using C# version 2.0.0 or below, use Database instead of ReadWriteDatabase.

   |**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
   |---|---|---|
   | clusterName | *mykustocluster* | Nazwa klastra, w którym zostanie utworzona baza danych.|
   | Databasename | *mykustodatabase* | Nazwa bazy danych.|
   | resourceGroupName | *testrg* | Nazwa grupy zasobów, w której zostanie utworzony klaster. |
   | softDeletePeriod | *3650:00:00:00* | Okres przechowywania danych na potrzeby zapytań. |
   | hotCachePeriod | *3650:00:00:00* | Okres przechowywania danych w pamięci podręcznej. |

2. Uruchom następujące polecenie, aby wyświetlić utworzoną bazę danych:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName) as ReadWriteDatabase;
    ```

Masz teraz klaster i bazę danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

* Jeśli planujesz śledzić nasze inne artykuły, zachowaj utworzone zasoby.
* Aby wyczyścić zasoby, usuń klaster. Usunięcie klastra powoduje również usunięcie znajdujących się w nim baz danych. Użyj następującego polecenia, aby usunąć klaster:

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Następne kroki

* [Pozyskiwania danych przy użyciu standardowego sdk Usługi Azure Data Explorer .NET (wersja zapoznawcza)](net-standard-ingest-data.md)
