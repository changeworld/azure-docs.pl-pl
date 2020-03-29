---
title: Dodawanie podmiotów klastra dla Eksploratora danych platformy Azure przy użyciu języka Python
description: W tym artykule dowiesz się, jak dodać podmioty klastra dla Usługi Azure Data Explorer przy użyciu języka Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 637efdfe31d1f2eb0eaa5dd532dd9e9e67de5ce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965140"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-python"></a>Dodawanie podmiotów klastra dla Eksploratora danych platformy Azure przy użyciu języka Python

> [!div class="op_single_selector"]
> * [C #](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Szablon usługi Azure Resource Manager](cluster-principal-resource-manager.md)

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. W tym artykule można dodać podmioty klastra dla Usługi Azure Data Explorer przy użyciu języka Python.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* [Tworzenie klastra](create-cluster-database-python.md).

## <a name="install-python-package"></a>Instalowanie pakietu Python

Aby zainstalować pakiet Python dla usługi Azure Data Explorer (Kusto), otwórz wiersz polecenia, który ma Pythona w swojej ścieżce. Uruchom następujące polecenie:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>Dodawanie głównego zobowiązanego klastra

W poniższym przykładzie pokazano, jak programowo dodać jednostkę klastra.

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
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy. Znany również jako identyfikator katalogu.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator subskrypcji używany do tworzenia zasobów.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator klienta aplikacji, która może uzyskać dostęp do zasobów w dzierżawie.|
| client_secret | *xxxxxxxxxxxxxx* | Klucz tajny klienta aplikacji, która może uzyskać dostęp do zasobów w dzierżawie. |
| resource_group_name | *testrg* | Nazwa grupy zasobów zawierającej klaster.|
| cluster_name | *mykustocluster* | Nazwa klastra.|
| principal_assignment_name | *clusterPrincipalAssignment1* | Nazwa zasobu głównego klastra.|
| Principal_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Główny identyfikator, który może być e-mail użytkownika, identyfikator aplikacji lub nazwa grupy zabezpieczeń.|
| role (rola) | *AllDatabasesAdmin* | Rola głównego klastra, który może być "AllDatabasesAdmin" lub "AllDatabasesViewer".|
| tenant_id_for_principal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy głównego zobowiązanego.|
| principal_type | *Aplikacja* | Typ podmiotu zabezpieczeń, który może być "Użytkownik", "Aplikacja" lub "Grupa"|

## <a name="next-steps"></a>Następne kroki

* [Dodawanie podmiotów bazy danych](database-principal-python.md)
