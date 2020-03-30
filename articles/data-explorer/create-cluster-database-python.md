---
title: Tworzenie klastra usługi Azure Data Explorer & bazy danych przy użyciu języka Python
description: Dowiedz się, jak utworzyć klaster i bazę danych usługi Azure Data Explorer przy użyciu języka Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 8425058c9f6ac5b90c37a99f749a810672b406fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560511"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Tworzenie klastra i bazy danych usługi Azure Data Explorer przy użyciu języka Python

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [Cli](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Szablon ARM](create-cluster-database-resource-manager.md)

W tym artykule utworzysz klaster i bazę danych usługi Azure Data Explorer przy użyciu języka Python. Usługa Azure Data Explorer to szybka, w pełni zarządzana usługa do analizy danych, która pozwala w czasie rzeczywistym analizować duże woluminy danych przesyłanych strumieniowo z aplikacji, witryn internetowych, urządzeń IoT i nie tylko. Aby użyć Usługi Azure Data Explorer, najpierw utwórz klaster i utwórz jedną lub więcej baz danych w tym klastrze. Następnie pozyskiwania lub ładowania danych do bazy danych, dzięki czemu można uruchamiać kwerendy przeciwko niemu.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4+](https://www.python.org/downloads/).

* [Usługa Azure AD Aplikacji i jednostki usługi, które mogą uzyskać dostęp do zasobów](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). Pobierz wartości `Directory (tenant) ID` `Application ID`dla `Client Secret`, i .

## <a name="install-python-package"></a>Instalowanie pakietu Python

Aby zainstalować pakiet Python dla usługi Azure Data Explorer (Kusto), otwórz wiersz polecenia, który ma Pythona w swojej ścieżce. Uruchom następujące polecenie:

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>Uwierzytelnianie
Aby uruchomić przykłady w tym artykule, potrzebujemy usługi Azure AD aplikacji i jednostki usługi, które mogą uzyskać dostęp do zasobów. Sprawdź [tworzenie aplikacji usługi Azure AD,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) aby utworzyć bezpłatną aplikację usługi Azure AD i dodać przypisanie roli w zakresie subskrypcji. Pokazuje również, jak `Directory (tenant) ID`uzyskać `Application ID`, `Client Secret`, i .

## <a name="create-the-azure-data-explorer-cluster"></a>Tworzenie klastra usługi Azure Data Explorer

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

   |**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Wybrana nazwa klastra.|
   | sku_name | *Standardowa_D13_v2* | Jednostka SKU, która będzie używana na potrzeby klastra. |
   | tier | *Standardowa* | Warstwa SKU. |
   | capacity | *numer* | Liczba wystąpień klastra. |
   | resource_group_name | *testrg* | Nazwa grupy zasobów, w której zostanie utworzony klaster. |

    > [!NOTE]
    > **Tworzenie klastra** jest długotrwałą operacją. Metoda **create_or_update** zwraca wystąpienie LROPoller, zobacz [LROPoller klasy,](/python/api/msrest/msrest.polling.lropoller?view=azure-python) aby uzyskać więcej informacji.

1. Uruchom następujące polecenie, aby sprawdzić, czy klaster został utworzony pomyślnie:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Jeśli wynik zawiera element `provisioningState` o wartości `Succeeded`, klaster został utworzony pomyślnie.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Tworzenie bazy danych w klastrze usługi Azure Data Explorer

1. Utwórz bazę danych przy użyciu następującego polecenia:

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = ReadWriteDatabase(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    #Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
    poller =database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

        [!NOTE]
        If you are using Python version 0.4.0 or below, use Database instead of ReadWriteDatabase.

   |**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Nazwa klastra, w którym zostanie utworzona baza danych.|
   | Nazwa_bazy_danych | *mykustodatabase* | Nazwa bazy danych.|
   | resource_group_name | *testrg* | Nazwa grupy zasobów, w której zostanie utworzony klaster. |
   | soft_delete_period | *3650 dni, 0:00:00* | Okres przechowywania danych na potrzeby zapytań. |
   | hot_cache_period | *3650 dni, 0:00:00* | Okres przechowywania danych w pamięci podręcznej. |

1. Uruchom następujące polecenie, aby wyświetlić utworzoną bazę danych:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Masz teraz klaster i bazę danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

* Jeśli planujesz śledzić nasze inne artykuły, zachowaj utworzone zasoby.
* Aby wyczyścić zasoby, usuń klaster. Usunięcie klastra powoduje również usunięcie znajdujących się w nim baz danych. Użyj następującego polecenia, aby usunąć klaster:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Następne kroki

* [pozyskiwanie danych przy użyciu biblioteki języka Python w usłudze Azure Data Explorer](python-ingest-data.md)
