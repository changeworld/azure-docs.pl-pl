---
title: 'Tworzenie zasad dla klastra i bazy danych usługi Azure Data Explorer przy użyciu biblioteki Usługi Azure Data Explorer Języka Python '
description: W tym artykule dowiesz się, jak tworzyć zasady przy użyciu języka Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: a0fe86e2dcb802b822cb08ed0922b5da9c5cfd1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667273"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-python"></a>Tworzenie zasad bazy danych i tabel dla Eksploratora danych platformy Azure przy użyciu języka Python

> [!div class="op_single_selector"]
> * [C #](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. W tym artykule można utworzyć zasady bazy danych i tabel dla Usługi Azure Data Explorer przy użyciu języka Python.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* [Klaster testowy i baza danych](create-cluster-database-python.md)
* [Tabela testowa](python-ingest-data.md#create-a-table-on-your-cluster)

## <a name="install-the-data-libraries"></a>Instalowanie bibliotek danych

```
pip install azure-common
pip install azure-mgmt-kusto
pip install azure-kusto-data (Optional, for changing table's policies)
```

## <a name="authentication"></a>Uwierzytelnianie
Aby uruchomić przykłady w tym artykule, potrzebujemy usługi Azure AD aplikacji i jednostki usługi, które mogą uzyskać dostęp do zasobów. Do uwierzytelniania z [klastra testowego i bazy danych](create-cluster-database-csharp.md#authentication)można użyć tej samej aplikacji usługi Azure AD. Jeśli chcesz użyć innej aplikacji usługi Azure AD, zobacz [tworzenie aplikacji usługi Azure AD,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) aby utworzyć bezpłatną aplikację usługi Azure AD i dodać przypisanie roli w zakresie subskrypcji. Pokazuje również, jak `Directory (tenant) ID`uzyskać `Application ID`, `Client Secret`, i . Może być konieczne dodanie nowej aplikacji usługi Azure AD jako podmiotu w bazie danych, zobacz [Zarządzanie uprawnieniami bazy danych Usługi Azure Data Explorer.](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions)    

## <a name="alter-database-retention-policy"></a>Zmienianie zasad przechowywania bazy danych
Ustawia zasady przechowywania z 10-dniowym okresem usuwania nietrwałego.

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabaseUpdate
from azure.common.credentials import ServicePrincipalCredentials
import datetime

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

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kustoManagementClient.databases.update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name,
                                           parameters=DatabaseUpdate(soft_delete_period=datetime.timedelta(days=10)))
```

## <a name="alter-database-cache-policy"></a>Zmienianie zasad pamięci podręcznej bazy danych
Ustawia zasady pamięci podręcznej dla bazy danych, że ostatnie pięć dni danych będzie na ssd klastra.

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabaseUpdate
from azure.common.credentials import ServicePrincipalCredentials
import datetime

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

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kustoManagementClient.databases.update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name,
                                           parameters=DatabaseUpdate(hot_cache_period=datetime.timedelta(days=5)))
```

## <a name="alter-table-cache-policy"></a>Zmienianie zasad pamięci podręcznej tabel
Ustawia zasady pamięci podręcznej dla tabeli, że ostatnie pięć dni danych będzie na ssd klastra.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
kusto_uri = "https://<ClusterName>.<Region>.kusto.windows.net"
database_name = "<DatabaseName>"
#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
#Application ID
client_id_to_add = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

kusto_connection_string_builder = KustoConnectionStringBuilder.with_aad_application_key_authentication(connection_string=kusto_uri, aad_app_id=client_id, app_key=client_secret, authority_id=tenant_id)

#The table that is created as part of the Prerequisites
table_name = "<TableName>"
caching_policy = r'hot = 5d'
command = '.alter table {} policy caching '.format(table_name) +  caching_policy
kusto_client.execute_mgmt(database_name, command)
```

## <a name="add-a-new-principal-for-database"></a>Dodawanie nowego podmiotu zabezpieczeń dla bazy danych
Dodawanie nowej aplikacji usługi Azure AD jako podmiotu administracyjnego bazy danych

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabasePrincipal
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
#Application ID
client_id_to_add = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
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
role = "Admin"
principle_name = "<database_principle_name>";
type_name = "App"
kustoManagementClient.databases.add_principals(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name,
                         value=[DatabasePrincipal(role=role, name=principle_name, type=type_name, app_id=client_id_to_add, tenant_name=tenant_id)])
```

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o zasadach bazy danych i tabel](https://docs.microsoft.com/azure/kusto/management/policies)
