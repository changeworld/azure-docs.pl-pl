---
title: Tworzenie klastra Eksplorator danych i bazy danych platformy Azure przy użyciu programuC#
description: Dowiedz się, jak utworzyć klaster Eksplorator danych i bazę danych platformy Azure przy użyciuC#
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 35f11ee9bce4dc7c68e12749f69d2f2e4253d4bc
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996251"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Tworzenie klastra Eksplorator danych i bazy danych platformy Azure przy użyciu programuC#

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [Interfejs wiersza polecenia](create-cluster-database-cli.md)
> * [Narzędzia](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Szablon ARM](create-cluster-database-resource-manager.md)

Azure Eksplorator danych to szybka i w pełni zarządzana usługa analizy danych do analizy w czasie rzeczywistym w przypadku dużych ilości danych przesyłanych strumieniowo z aplikacji, witryn sieci Web, urządzeń IoT i innych. Aby korzystać z usługi Azure Eksplorator danych, należy najpierw utworzyć klaster i utworzyć co najmniej jedną bazę danych w tym klastrze. Następnie Pozyskaj (Załaduj) dane do bazy danych, tak aby można było uruchamiać zapytania względem tego programu. W tym artykule opisano tworzenie klastra i bazy danych przy użyciu programu C#.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz zainstalowanego programu Visual Studio 2019, możesz pobrać i korzystać **bezpłatnie** z programu [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Upewnij się, że włączasz **Programowanie na platformie Azure** podczas instalacji programu Visual Studio.

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

## <a name="install-c-nuget"></a>Zainstaluj C# pakiet NuGet

1. Zainstaluj [pakiet NuGet platformy Azure Eksplorator danych (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

1. Zainstaluj [pakiet NuGet Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) na potrzeby uwierzytelniania.

## <a name="authentication"></a>Uwierzytelnianie
Aby uruchomić przykłady z tego artykułu, potrzebujemy aplikacji usługi Azure AD i nazwy głównej usługi, która może uzyskać dostęp do zasobów. Zaznacz opcję [Utwórz aplikację usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) , aby utworzyć bezpłatną aplikację usługi Azure AD, a następnie Dodaj przypisanie roli do zakresu subskrypcji. Przedstawiono w nim również, jak uzyskać `Directory (tenant) ID`, `Application ID` i `Client Secret`.

## <a name="create-the-azure-data-explorer-cluster"></a>Tworzenie klastra usługi Azure Eksplorator danych

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

   |**Konfigurowania** | **Sugerowana wartość** | **Opis pola**|
   |---|---|---|
   | clusterName | *mykustocluster* | Wymagana nazwa klastra.|
   | skuName | *Standard_D13_v2* | Jednostka SKU, która będzie używana w klastrze. |
   | warstwa | *Standardowa* | Warstwa SKU. |
   | pojemności | *Liczba* | Liczba wystąpień klastra. |
   | resourceGroupName | *testrg* | Nazwa grupy zasobów, w której zostanie utworzony klaster. |

    > [!NOTE]
    > **Tworzenie klastra** to długotrwała operacja, dlatego zdecydowanie zaleca się użycie CreateOrUpdateAsync zamiast metodę createorupdate. 

1. Uruchom następujące polecenie, aby sprawdzić, czy klaster został pomyślnie utworzony:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Jeśli wynik zawiera `ProvisioningState` z wartością `Succeeded`, klaster został pomyślnie utworzony.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Tworzenie bazy danych w klastrze usługi Azure Eksplorator danych

1. Utwórz bazę danych przy użyciu następującego kodu:

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

   |**Konfigurowania** | **Sugerowana wartość** | **Opis pola**|
   |---|---|---|
   | clusterName | *mykustocluster* | Nazwa klastra, w którym zostanie utworzona baza danych.|
   | Bazy | *mykustodatabase* | Nazwa bazy danych.|
   | resourceGroupName | *testrg* | Nazwa grupy zasobów, w której zostanie utworzony klaster. |
   | softDeletePeriod | *3650:00:00:00* | Ilość czasu, przez jaką dane będą przechowywane do zapytania. |
   | hotCachePeriod | *3650:00:00:00* | Ilość czasu przechowywania danych w pamięci podręcznej. |

2. Uruchom następujące polecenie, aby wyświetlić utworzoną bazę danych:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName);
    ```

Masz teraz klaster i bazę danych.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

* Jeśli planujesz postępować zgodnie z innymi artykułami, Zachowaj utworzone zasoby.
* Aby wyczyścić zasoby, Usuń klaster. Usunięcie klastra spowoduje również usunięcie wszystkich znajdujących się w nim baz danych. Aby usunąć klaster, użyj następującego polecenia:

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Następne kroki

* [Pozyskiwanie danych przy użyciu usługi Azure Eksplorator danych .NET Standard SDK (wersja zapoznawcza)](net-standard-ingest-data.md)
