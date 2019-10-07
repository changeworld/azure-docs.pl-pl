---
title: Tworzenie klastra Eksplorator danych i bazy danych platformy Azure przy użyciu języka Python
description: Dowiedz się, jak utworzyć klaster Eksplorator danych i bazę danych platformy Azure przy użyciu języka Python.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: b3329ccb3edb3077a45e3bbf9ba7b48d7e3a93a2
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996231"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Tworzenie klastra Eksplorator danych i bazy danych platformy Azure przy użyciu języka Python

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [Interfejs wiersza polecenia](create-cluster-database-cli.md)
> * [Narzędzia](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Szablon ARM](create-cluster-database-resource-manager.md)

Azure Eksplorator danych to szybka i w pełni zarządzana usługa analizy danych do analizy w czasie rzeczywistym w przypadku dużych ilości danych przesyłanych strumieniowo z aplikacji, witryn sieci Web, urządzeń IoT i innych. Aby korzystać z usługi Azure Eksplorator danych, należy najpierw utworzyć klaster i utworzyć co najmniej jedną bazę danych w tym klastrze. Następnie Pozyskaj (Załaduj) dane do bazy danych, tak aby można było uruchamiać zapytania względem tego programu. W tym artykule opisano tworzenie klastra i bazy danych przy użyciu języka Python.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

## <a name="install-python-package"></a>Zainstaluj pakiet języka Python

Aby zainstalować pakiet języka Python dla usługi Azure Eksplorator danych (Kusto), Otwórz wiersz polecenia, który ma w swojej ścieżce Język Python. Uruchom następujące polecenie:

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>Uwierzytelnianie
Aby uruchomić przykłady z tego artykułu, potrzebujemy aplikacji usługi Azure AD i nazwy głównej usługi, która może uzyskać dostęp do zasobów. Zaznacz opcję [Utwórz aplikację usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) , aby utworzyć bezpłatną aplikację usługi Azure AD, a następnie Dodaj przypisanie roli do zakresu subskrypcji. Przedstawiono w nim również, jak uzyskać `Directory (tenant) ID`, `Application ID` i `Client Secret`.

## <a name="create-the-azure-data-explorer-cluster"></a>Tworzenie klastra usługi Azure Eksplorator danych

1. Utwórz klaster przy użyciu następującego polecenia:

    ```Python
    from azure.mgmt.kusto import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku
    from azure.common.credentials import ServicePrincipalCredentials

    #Directory (tenant) ID
    tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    #Application ID
    client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    #Client Secret
    client_secret = "xxxxxxxxxxxxxx"
    subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )

    location = 'Central US'
    sku_name = 'Standard_D13_v2'
    capacity = 5
    tier = "Standard"
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku_name, capacity=capacity, tier=tier))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    poller = cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**Konfigurowania** | **Sugerowana wartość** | **Opis pola**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Wymagana nazwa klastra.|
   | sku_name | *Standard_D13_v2* | Jednostka SKU, która będzie używana w klastrze. |
   | warstwa | *Standardowa* | Warstwa SKU. |
   | pojemności | *Liczba* | Liczba wystąpień klastra. |
   | resource_group_name | *testrg* | Nazwa grupy zasobów, w której zostanie utworzony klaster. |

    > [!NOTE]
    > **Tworzenie klastra** jest długotrwałą operacją. Metoda **create_or_update** zwraca wystąpienie elementu LROPoller, zobacz [Klasa LROPoller](/python/api/msrest/msrest.polling.lropoller?view=azure-python) , aby uzyskać więcej informacji.

1. Uruchom następujące polecenie, aby sprawdzić, czy klaster został pomyślnie utworzony:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Jeśli wynik zawiera `provisioningState` z wartością `Succeeded`, klaster został pomyślnie utworzony.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Tworzenie bazy danych w klastrze usługi Azure Eksplorator danych

1. Utwórz bazę danych za pomocą następującego polecenia:

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = Database(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    #Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
    poller =database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

   |**Konfigurowania** | **Sugerowana wartość** | **Opis pola**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Nazwa klastra, w którym zostanie utworzona baza danych.|
   | database_name | *mykustodatabase* | Nazwa bazy danych.|
   | resource_group_name | *testrg* | Nazwa grupy zasobów, w której zostanie utworzony klaster. |
   | soft_delete_period | *3650 dni, 0:00:00* | Ilość czasu, przez jaką dane będą przechowywane do zapytania. |
   | hot_cache_period | *3650 dni, 0:00:00* | Ilość czasu przechowywania danych w pamięci podręcznej. |

1. Uruchom następujące polecenie, aby wyświetlić utworzoną bazę danych:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Masz teraz klaster i bazę danych.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

* Jeśli planujesz postępować zgodnie z innymi artykułami, Zachowaj utworzone zasoby.
* Aby wyczyścić zasoby, Usuń klaster. Usunięcie klastra spowoduje również usunięcie wszystkich znajdujących się w nim baz danych. Aby usunąć klaster, użyj następującego polecenia:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Następne kroki

* [Pozyskiwanie danych przy użyciu biblioteki języka Python Eksplorator danych platformy Azure](python-ingest-data.md)
