---
title: Tworzenie połączenia danych usługi IoT Hub dla Eksploratora danych platformy Azure przy użyciu języka Python
description: W tym artykule dowiesz się, jak utworzyć połączenie danych usługi IoT Hub dla Usługi Azure Data Explorer przy użyciu języka Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 76c8ca24882f465bf2a973dc59736745178fc61f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669526"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-python-preview"></a>Tworzenie połączenia danych usługi IoT Hub dla Eksploratora danych platformy Azure przy użyciu języka Python (Wersja zapoznawcza)

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C #](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Szablon usługi Azure Resource Manager](data-connection-iot-hub-resource-manager.md)

W tym artykule utworzysz połączenie danych usługi IoT Hub dla Usługi Azure Data Explorer przy użyciu języka Python. Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Azure Data Explorer oferuje pozyskiwania lub ładowania danych, z Usługi eventów, Usługi IoT Hubs i obiektów blob napisane do kontenerów obiektów blob.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4+](https://www.python.org/downloads/).

* [Klaster i baza danych](create-cluster-database-python.md).

* [Mapowanie tabel i kolumn](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Zasady bazy danych i tabel](database-table-policies-python.md) (opcjonalnie).

* [Centrum IoT z skonfigurowaną zasadą dostępu współdzielonego](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Dodawanie połączenia danych usługi IoT Hub 

W poniższym przykładzie pokazano, jak programowo dodać połączenie danych usługi IoT Hub. Zobacz [łączenie tabeli Usługi Azure Data Explorer z usługą IoT Hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) w celu dodania połączenia danych usługi Iot Hub przy użyciu witryny Azure portal.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import IotHubDataConnection
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
#The IoT hub that is created as part of the Prerequisites
iot_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
shared_access_policy_name = "iothubforread"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=IotHubDataConnection(iot_hub_resource_id=iot_hub_resource_id, shared_access_policy_name=shared_access_policy_name, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy. Znany również jako identyfikator katalogu.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator subskrypcji używany do tworzenia zasobów.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator klienta aplikacji, która może uzyskać dostęp do zasobów w dzierżawie.|
| client_secret | *xxxxxxxxxxxxxx* | Klucz tajny klienta aplikacji, która może uzyskać dostęp do zasobów w dzierżawie. |
| resource_group_name | *testrg* | Nazwa grupy zasobów zawierającej klaster.|
| cluster_name | *mykustocluster* | Nazwa klastra.|
| Nazwa_bazy_danych | *mykustodatabase* | Nazwa docelowej bazy danych w klastrze.|
| data_connection_name | *połączenie myeventhub* | Żądana nazwa połączenia danych.|
| Nazwa_tabeli | *StormEvents (Burza)* | Nazwa tabeli docelowej w docelowej bazie danych.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Nazwa mapowania kolumn powiązana z tabelą docelową.|
| data_format | *Csv* | Format danych wiadomości.|
| iot_hub_resource_id | *Identyfikator zasobu* | Identyfikator zasobu centrum IoT hub, który przechowuje dane do pozyskiwania.|
| shared_access_policy_name | *iothubforread* | Nazwa zasady dostępu współdzielonego, która definiuje uprawnienia dla urządzeń i usług do łączenia się z Centrum IoT Hub. |
| consumer_group | *$Default* | Grupa odbiorców centrum zdarzeń.|
| location | *Środkowe stany USA* | Lokalizacja zasobu połączenia danych.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]