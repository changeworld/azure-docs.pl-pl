---
title: Tworzenie IoT Hub połączenia danych dla Eksplorator danych platformy Azure przy użyciu języka Python
description: W tym artykule dowiesz się, jak utworzyć IoT Hub połączenie danych dla Eksplorator danych platformy Azure przy użyciu języka Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1e6cd94e18382b3e66fc6a9c284539bc178e199c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031645"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-python-preview"></a>Tworzenie IoT Hub połączenia danych dla Eksplorator danych platformy Azure przy użyciu języka Python (wersja zapoznawcza)

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Azure Eksplorator danych oferuje pozyskiwanie (ładowanie danych) z Event Hubs, centrów IoT i obiektów blob, które są zapisywane do kontenerów obiektów BLOB. W tym artykule opisano tworzenie IoT Hub połączenia danych dla usługi Azure Eksplorator danych przy użyciu języka Python.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

* Tworzenie [klastra i bazy danych](create-cluster-database-csharp.md)

* Tworzenie [mapowania tabeli i kolumny](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)

* Ustawianie [zasad bazy danych i tabeli](database-table-policies-csharp.md) (opcjonalnie)

* Utwórz [Centrum IoT Hub z skonfigurowanymi zasadami dostępu współdzielonego](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Dodawanie połączenia danych IoT Hub 

W poniższym przykładzie pokazano, jak w programie programowo dodać połączenie danych IoT Hub. IoT Hub aby dowiedzieć się, jak dodać połączenie danych usługi IoT Hub przy użyciu Azure Portal, zobacz temat [łączenie tabeli Eksplorator danych Azure](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) .

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

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";
data_connection_name = "myeventhubconnect";
#The IoT hub that is created as part of the Prerequisites
iot_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
shared_access_policy_name = "iothubforread";
consumer_group = "$Default";
location = "Central US";
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents";
mapping_rule_name = "StormEvents_CSV_Mapping";
data_format = "csv";

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=IotHubDataConnection(iot_hub_resource_id=iot_hub_resource_id, shared_access_policy_name=shared_access_policy_name, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
|---|---|---|
| tenant_id | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy. Znany również jako identyfikator katalogu.|
| subscriptionId | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator subskrypcji używany do tworzenia zasobów.|
| client_id | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator klienta aplikacji, który może uzyskiwać dostęp do zasobów w dzierżawie.|
| client_secret | *xxxxxxxxxxxxxx* | Wpis tajny klienta aplikacji, który może uzyskiwać dostęp do zasobów w dzierżawie. |
| resource_group_name | *testrg* | Nazwa grupy zasobów zawierającej klaster.|
| cluster_name | *mykustocluster* | Nazwa klastra.|
| database_name | *mykustodatabase* | Nazwa docelowej bazy danych w klastrze.|
| data_connection_name | *myeventhubconnect* | Wymagana nazwa połączenia danych.|
| table_name | *StormEvents* | Nazwa tabeli docelowej w docelowej bazie danych.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Nazwa mapowania kolumny powiązanej z tabelą docelową.|
| data_format | *CSV* | Format danych wiadomości.|
| iot_hub_resource_id | *Identyfikator zasobu* | Identyfikator zasobu Centrum IoT, który przechowuje dane do pozyskiwania.|
| shared_access_policy_name | *iothubforread* | Nazwa zasad dostępu współdzielonego, które definiują uprawnienia dla urządzeń i usług w celu nawiązania połączenia z IoT Hub. |
| consumer_group | *$Default* | Grupa konsumentów centrum zdarzeń.|
| location | *Środkowe stany USA* | Lokalizacja zasobu połączenia danych.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]