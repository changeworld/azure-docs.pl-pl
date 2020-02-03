---
title: Dodawanie podmiotów zabezpieczeń bazy danych dla Eksplorator danych platformy Azure przy użyciu języka Python
description: W tym artykule dowiesz się, jak dodać podmioty zabezpieczeń bazy danych dla usługi Azure Eksplorator danych przy użyciu języka Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 8b9c4f4d5427b326c273558db0bff808068b192a
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965010"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-python"></a>Dodawanie podmiotów zabezpieczeń bazy danych dla Eksplorator danych platformy Azure przy użyciu języka Python

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Szablon usługi Azure Resource Manager](database-principal-resource-manager.md)

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. W tym artykule opisano Dodawanie podmiotów zabezpieczeń bazy danych dla usługi Azure Eksplorator danych przy użyciu języka Python.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Tworzenie klastra i bazy danych](create-cluster-database-python.md)

## <a name="install-python-package"></a>Zainstaluj pakiet języka Python

Aby zainstalować pakiet języka Python dla usługi Azure Eksplorator danych (Kusto), Otwórz wiersz polecenia, który ma w swojej ścieżce Język Python. Uruchom następujące polecenie:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Dodawanie podmiotu zabezpieczeń bazy danych

Poniższy przykład pokazuje, jak dodać podmiot zabezpieczeń bazy danych programowo.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabasePrincipalAssignment
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
#The cluster and database that is created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.database_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, principal_assignment_name= principal_assignment_name, parameters=DatabasePrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
|---|---|---|
| tenant_id | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy. Znany również jako identyfikator katalogu.|
| subscription_id | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator subskrypcji używany do tworzenia zasobów.|
| client_id | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator klienta aplikacji, który może uzyskiwać dostęp do zasobów w dzierżawie.|
| client_secret | *xxxxxxxxxxxxxx* | Wpis tajny klienta aplikacji, który może uzyskiwać dostęp do zasobów w dzierżawie. |
| resource_group_name | *testrg* | Nazwa grupy zasobów zawierającej klaster.|
| cluster_name | *mykustocluster* | Nazwa klastra.|
| database_name | *mykustodatabase* | Nazwa bazy danych.|
| principal_assignment_name | *databasePrincipalAssignment1* | Nazwa zasobu głównego bazy danych.|
| principal_id | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator podmiotu zabezpieczeń, który może być adresem e-mail użytkownika, IDENTYFIKATORem aplikacji lub grupą zabezpieczeń.|
| role (rola) | *Administratora* | Rola podmiotu zabezpieczeń bazy danych, która może być "admin", "pokarmem", "Monitor", "User", "UnrestrictedViewers", "Viewer".|
| tenant_id_for_principal | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy podmiotu zabezpieczeń.|
| principal_type | *Aplikacja* | Typ podmiotu zabezpieczeń, który może mieć wartość "User", "App" lub "Group"|

## <a name="next-steps"></a>Następne kroki

* [Pozyskiwanie danych przy użyciu biblioteki języka Python Eksplorator danych platformy Azure](python-ingest-data.md)
