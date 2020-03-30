---
title: Dołączanie baz danych w Eksploratorze danych platformy Azure za pomocą funkcji bazy danych zwolenników
description: Dowiedz się, jak dołączyć bazy danych w Eksploratorze danych platformy Azure przy użyciu funkcji bazy danych elementu śledzącego.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: f6dbdb54c1c5a5d477c3ccb988963758faab83b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79140018"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Dołączanie baz danych w Eksploratorze danych platformy Azure za pomocą bazy danych zwolenników

Funkcja **bazy danych elementu śledzącego** umożliwia dołączenie bazy danych znajdującej się w innym klastrze do klastra usługi Azure Data Explorer. **Baza danych zwolennika** jest dołączona w trybie tylko do *odczytu,* dzięki czemu można wyświetlić dane i uruchomić kwerendy dotyczące danych, które zostały pojone do **bazy danych lidera**. Baza danych zwolennika synchronizuje zmiany w bazach danych lidera. Ze względu na synchronizację, istnieje opóźnienie danych od kilku sekund do kilku minut w dostępności danych. Długość opóźnienia zależy od ogólnego rozmiaru metadanych bazy danych lidera. Bazy danych lidera i zwolennika używają tego samego konta magazynu do pobierania danych. Magazyn jest własnością bazy danych leader. Baza danych osoby śledzącej wyświetla dane bez konieczności ich pozyskiwania. Ponieważ załączona baza danych jest bazą danych tylko do odczytu, danych, tabel i zasad w bazie danych nie można modyfikować z wyjątkiem [zasad buforowania,](#configure-caching-policy) [podmiotów](#manage-principals)i [uprawnień.](#manage-permissions) Nie można usunąć dołączonych baz danych. Muszą być odłączone przez lidera lub zwolennika i tylko wtedy mogą zostać usunięte. 

Dołączanie bazy danych do innego klastra przy użyciu możliwości zwolennika jest używany jako infrastruktura do udostępniania danych między organizacjami i zespołami. Funkcja ta jest przydatna do segregować zasoby obliczeniowe w celu ochrony środowiska produkcyjnego przed nieprodukcyjnymi przypadkami użycia. Osoba śledząca może również służyć do skojarzenia kosztu klastra usługi Azure Data Explorer z stroną, która uruchamia kwerendy dotyczące danych.

## <a name="which-databases-are-followed"></a>Które bazy danych są przestrzegane?

* Klaster może śledzić jedną bazę danych, kilka baz danych lub wszystkie bazy danych klastra linii odniesienia. 
* Pojedynczy klaster może śledzić bazy danych z wielu klastrów linii odniesienia. 
* Klaster może zawierać zarówno bazy danych zwolenników, jak i bazy danych lidera

## <a name="prerequisites"></a>Wymagania wstępne

1. Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
1. [Tworzenie klastra i bazy danych](/azure/data-explorer/create-cluster-database-portal) dla lidera i zwolennika.
1. [Połknąć dane](/azure/data-explorer/ingest-sample-data) do bazy danych lidera przy użyciu jednej z różnych metod omówionych w [przeglądzie pozyskiwania](/azure/data-explorer/ingest-data-overview).

## <a name="attach-a-database"></a>Dołączanie bazy danych

Istnieją różne metody, których można użyć do dołączenia bazy danych. W tym artykule omówimy dołączanie bazy danych przy użyciu języka C# lub szablonu usługi Azure Resource Manager. Aby dołączyć bazę danych, musisz mieć uprawnienia do klastra linii odniesienia i klastra wpisowego. Aby uzyskać więcej informacji o uprawnieniach, zobacz [zarządzanie uprawnieniami](#manage-permissions).

### <a name="attach-a-database-using-c"></a>Dołączanie bazy danych przy użyciu języka C #

#### <a name="needed-nugets"></a>Potrzebne NuGets

* Zainstaluj [plik Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Zainstaluj [system Microsoft.Rest.ClientRuntime.Azure.Authentication w celu uwierzytelnienia](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication).

#### <a name="code-example"></a>Przykład kodu

```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "followerResouceGroup";
var leaderResourceGroup = "leaderResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "uniqueNameForAttachedDatabaseConfiguration";
var databaseName = "db"; // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{leaderSubscriptionId}/resourceGroups/{leaderResourceGroup}/providers/Microsoft.Kusto/Clusters/{leaderClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-python"></a>Dołączanie bazy danych przy użyciu języka Python

#### <a name="needed-modules"></a>Potrzebne moduły

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="code-example"></a>Przykład kodu

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import AttachedDatabaseConfiguration
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
leader_resouce_group_name = "leaderResouceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_Configuration_name = "uniqueNameForAttachedDatabaseConfiguration"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Dołączanie bazy danych przy użyciu szablonu usługi Azure Resource Manager

W tej sekcji nauczysz się dołączać bazę danych do istniejącego clusera przy użyciu [szablonu usługi Azure Resource Manager](../azure-resource-manager/management/overview.md). 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the cluster to which the database will be attached."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of the leader cluster."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "Union",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>Wdrożenie szablonu 

Szablon usługi Azure Resource Manager można wdrożyć [przy użyciu witryny Azure portal](https://portal.azure.com) lub przy użyciu programu PowerShell.

   ![wdrażanie szablonów](media/follower/template-deployment.png)


|**Ustawienie**  |**Opis**  |
|---------|---------|
|Nazwa klastra awersji     |  Nazwa klastra naśladowczego; gdzie zostanie wdrożony szablon.  |
|Nazwa dołączonych konfiguracji bazy danych    |    Nazwa dołączonego obiektu konfiguracji bazy danych. Nazwa może być dowolny ciąg, który jest unikatowy na poziomie klastra.     |
|Nazwa bazy danych     |      Nazwa bazy danych, której należy przestrzegać. Jeśli chcesz śledzić wszystkie bazy danych lidera, użyj '*'.   |
|Identyfikator zasobu klastra linii odniesienia    |   Identyfikator zasobu klastra linii odniesienia.      |
|Domyślny rodzaj modyfikacji zleceniodawców    |   Domyślny główny rodzaj modyfikacji. Może `Union`być `Replace` `None`, lub . Aby uzyskać więcej informacji na temat domyślnego rodzaju modyfikacji głównej, zobacz [polecenie kontroli rodzaju modyfikacji głównej](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind).      |
|Lokalizacja   |   Lokalizacja wszystkich zasobów. Lider i naśladowca muszą znajdować się w tym samym miejscu.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Sprawdzanie, czy baza danych została pomyślnie dołączona

Aby sprawdzić, czy baza danych została pomyślnie dołączona, znajdź dołączone bazy danych w [witrynie Azure portal](https://portal.azure.com). 

1. Przejdź do klastra zwolenników i wybierz pozycję **Bazy danych**
1. Wyszukaj nowe bazy danych tylko do odczytu na liście baz danych.

    ![Baza danych osób obserwowanych tylko do odczytu](media/follower/read-only-follower-database.png)

Inna możliwość:

1. Przejdź do klastra linii odniesienia i wybierz **pozycję Bazy danych**
2. Sprawdź, czy odpowiednie bazy danych są oznaczone jako **udostępnione innym** > **Tak**

    ![Odczytywanie i zapisywanie załączonych baz danych](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>Odłącz bazę danych zwolennika za pomocą języka C # 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Odłącz załączoną bazę danych zwolennika od klastra zwolennika

Klaster zwolennika może odłączyć dowolną dołączoną bazę danych w następujący sposób:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "uniqueName";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Odłącz załączoną bazę danych zwolennika od klastra linii odniesienia

Klaster linii odniesienia może odłączyć dowolną dołączoną bazę danych w następujący sposób:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = leaderSubscriptionId
};

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "uniqueName",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>Odłącz bazę danych zwolennika za pomocą języka Python

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Odłącz załączoną bazę danych zwolennika od klastra zwolennika

Klaster zwolennika może odłączyć dowolną dołączoną bazę danych w następujący sposób:

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
follower_cluster_name = "follower"
attached_database_configurationName = "uniqueName"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Odłącz załączoną bazę danych zwolennika od klastra linii odniesienia

Klaster linii odniesienia może odłączyć dowolną dołączoną bazę danych w następujący sposób:

```python

from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import FollowerDatabaseDefinition
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResourceGroup"
leader_resource_group_name = "leaderResourceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_configuration_name = "uniqueName"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Zarządzanie podmiotami, uprawnieniami i zasadami buforowania

### <a name="manage-principals"></a>Zarządzanie podmiotami

Podczas dołączania bazy danych należy określić **"domyślny rodzaj modyfikacji podmiotów"**. Domyślnie jest utrzymanie kolekcji bazy danych lidera [upoważnionych zleceniodawców](/azure/kusto/management/access-control/index#authorization)

|**Rodzaju** |**Opis**  |
|---------|---------|
|**Unia**     |   Dołączone podmioty bazy danych zawsze będzie zawierać oryginalne podmioty bazy danych oraz dodatkowe nowe podmioty dodane do bazy danych zwolennika.      |
|**Zastąpić**   |    Brak dziedziczenia zleceniodawców z oryginalnej bazy danych. Nowe podmioty muszą zostać utworzone dla dołączonej bazy danych.     |
|**Brak**   |   Dołączone podmioty bazy danych obejmują tylko podmioty oryginalnej bazy danych bez dodatkowych podmiotów.      |

Aby uzyskać więcej informacji na temat używania poleceń sterujących do konfigurowania autoryzowanych podmiotów, zobacz [Sterowanie poleceniami zarządzania klastrem zwolenników](/azure/kusto/management/cluster-follower).

### <a name="manage-permissions"></a>Zarządzaj uprawnieniami

Zarządzanie uprawnieniami do bazy danych tylko do odczytu jest taka sama jak dla wszystkich typów baz danych. Zobacz [zarządzanie uprawnieniami w witrynie Azure portal](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Konfigurowanie zasad buforowania

Administrator bazy danych zwolennika można zmodyfikować [zasady buforowania](/azure/kusto/management/cache-policy) dołączonej bazy danych lub dowolnej z jego tabel w klastrze hostingu. Domyślnie jest utrzymanie kolekcji bazy danych lidera bazy danych i zasad buforowania na poziomie tabeli. Można na przykład mieć 30 dni buforowania zasad w bazie danych lidera do uruchamiania raportowania miesięcznego i trzydniowe zasady buforowania w bazie danych zwolenników do kwerendy tylko ostatnie dane do rozwiązywania problemów. Aby uzyskać więcej informacji na temat używania poleceń sterujących do konfigurowania zasad buforowania w bazie danych lub tabeli [zwolennika, zobacz Sterowanie poleceniami zarządzania klastrem zwolenników](/azure/kusto/management/cluster-follower).

## <a name="limitations"></a>Ograniczenia

* Grupowanie naśladowcze i liderów musi znajdować się w tym samym regionie.
* Nie można używać [pozyskiwania danych strumieniowych](/azure/data-explorer/ingest-data-streaming) w bazie danych, która jest przestrzegana.
* Szyfrowanie danych przy użyciu [kluczy zarządzanych przez klienta](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault) nie jest obsługiwane zarówno w klastrach linii odniesienia, jak i dla osób śledzących. 
* Nie można usunąć bazy danych, która jest dołączona do innego klastra przed odłączeniem go.
* Nie można usunąć klastra, który ma bazę danych dołączoną do innego klastra przed odłączeniem go.
* Nie można zatrzymać klastra, który ma dołączonej bazy danych zwolennika lub lidera. 

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat konfiguracji klastra [zwolenników, zobacz Sterowanie poleceniami zarządzania klastrem zwolenników](/azure/kusto/management/cluster-follower).
