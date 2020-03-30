---
title: Tworzenie połączenia danych usługi Azure Data Explorer w ramach usługi Event Grid przy użyciu języka Python
description: W tym artykule dowiesz się, jak utworzyć połączenie danych usługi Event Grid dla Usługi Azure Data Explorer przy użyciu języka Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1439383598517f57bc77e718d4ded7f53941d3bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444200"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-python"></a>Tworzenie połączenia danych usługi Azure Data Explorer w ramach usługi Event Grid przy użyciu języka Python

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Szablon usługi Azure Resource Manager](data-connection-event-grid-resource-manager.md)

W tym artykule utworzysz połączenie danych usługi Event Grid dla Usługi Azure Data Explorer przy użyciu języka Python. Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Azure Data Explorer oferuje pozyskiwania lub ładowania danych, z Usługi eventów, Usługi IoT Hubs i obiektów blob napisane do kontenerów obiektów blob.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4+](https://www.python.org/downloads/).

* [Klaster i baza danych](create-cluster-database-python.md).

* [Mapowanie tabel i kolumn](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Zasady bazy danych i tabel](database-table-policies-csharp.md) (opcjonalnie).

* [Konto magazynu z subskrypcją usługi Event Grid](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account).

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Dodawanie połączenia danych z siatką zdarzeń

W poniższym przykładzie pokazano, jak programowo dodać połączenie danych usługi Event Grid. Zobacz [tworzenie połączenia danych usługi Event Grid w Eksploratorze danych platformy Azure](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) w celu dodania połączenia danych w usłudze Event Grid przy użyciu witryny Azure portal.


```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventGridDataConnection
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
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The event hub and storage account that are created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx"
storage_account_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=EventGridDataConnection(storage_account_resource_id=storage_account_resource_id, event_hub_resource_id=event_hub_resource_id, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```
|**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy. Znany również jako identyfikator katalogu.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator subskrypcji używany do tworzenia zasobów.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator klienta aplikacji, która może uzyskać dostęp do zasobów w dzierżawie.|
| client_secret | *xxxxxxxxxxxxxx* | Klucz tajny klienta aplikacji, która może uzyskać dostęp do zasobów w dzierżawie. |
| resource_group_name | *testrg* | Nazwa grupy zasobów zawierającej klaster.|
| cluster_name | *mykustocluster* | Nazwa klastra.|
| Nazwa_bazy_danych | *mykustodatabase* | Nazwa docelowej bazy danych w klastrze.|
| data_connection_name | *połączenie myeventhub* | Żądana nazwa połączenia danych.|
| Nazwa_tabeli | *StormEvents (Burza)* | Nazwa tabeli docelowej w docelowej bazie danych.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Nazwa mapowania kolumn powiązana z tabelą docelową.|
| data_format | *Csv* | Format danych wiadomości.|
| event_hub_resource_id | *Identyfikator zasobu* | Identyfikator zasobu centrum zdarzeń, w którym siatka zdarzeń jest skonfigurowana do wysyłania zdarzeń. |
| storage_account_resource_id | *Identyfikator zasobu* | Identyfikator zasobu konta magazynu, który przechowuje dane do pozyskiwania. |
| consumer_group | *$Default* | Grupa odbiorców Centrum zdarzeń.|
| location | *Środkowe stany USA* | Lokalizacja zasobu połączenia danych.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]