---
title: Dodawanie podmiotów zabezpieczeń klastra dla usługi Azure Eksplorator danych przy użyciu języka Python
description: W tym artykule dowiesz się, jak dodać podmioty zabezpieczeń klastra dla usługi Azure Eksplorator danych przy użyciu języka Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 637efdfe31d1f2eb0eaa5dd532dd9e9e67de5ce2
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965140"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-python"></a>Dodawanie podmiotów zabezpieczeń klastra dla usługi Azure Eksplorator danych przy użyciu języka Python

> [!div class="op_single_selector"]
> * [C#](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Szablon usługi Azure Resource Manager](cluster-principal-resource-manager.md)

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. W tym artykule opisano Dodawanie podmiotów zabezpieczeń klastra dla usługi Azure Eksplorator danych przy użyciu języka Python.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Utwórz klaster](create-cluster-database-python.md).

## <a name="install-python-package"></a>Zainstaluj pakiet języka Python

Aby zainstalować pakiet języka Python dla usługi Azure Eksplorator danych (Kusto), Otwórz wiersz polecenia, który ma w swojej ścieżce Język Python. Uruchom następujące polecenie:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>Dodawanie podmiotu zabezpieczeń klastra

Poniższy przykład pokazuje, jak dodać podmiot zabezpieczeń klastra programowo.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import ClusterPrincipalAssignment
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
#The cluster that is created as part of the Prerequisites
cluster_name = "mykustocluster"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.cluster_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, principal_assignment_name= principal_assignment_name, parameters=ClusterPrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
|---|---|---|
| tenant_id | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy. Znany również jako identyfikator katalogu.|
| subscription_id | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator subskrypcji używany do tworzenia zasobów.|
| client_id | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator klienta aplikacji, który może uzyskiwać dostęp do zasobów w dzierżawie.|
| client_secret | *xxxxxxxxxxxxxx* | Wpis tajny klienta aplikacji, który może uzyskiwać dostęp do zasobów w dzierżawie. |
| resource_group_name | *testrg* | Nazwa grupy zasobów zawierającej klaster.|
| cluster_name | *mykustocluster* | Nazwa klastra.|
| principal_assignment_name | *clusterPrincipalAssignment1* | Nazwa głównego zasobu klastra.|
| principal_id | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator podmiotu zabezpieczeń, który może być adresem e-mail użytkownika, IDENTYFIKATORem aplikacji lub grupą zabezpieczeń.|
| role (rola) | *AllDatabasesAdmin* | Rola podmiotu zabezpieczeń klastra, która może mieć wartość "AllDatabasesAdmin'" lub "AllDatabasesViewer".|
| tenant_id_for_principal | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy podmiotu zabezpieczeń.|
| principal_type | *Aplikacja* | Typ podmiotu zabezpieczeń, który może mieć wartość "User", "App" lub "Group"|

## <a name="next-steps"></a>Następne kroki

* [Dodawanie podmiotów zabezpieczeń bazy danych](database-principal-python.md)
