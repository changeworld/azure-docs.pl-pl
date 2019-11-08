---
title: Dołączanie baz danych w usłudze Azure Eksplorator danych przy użyciu funkcji programu databaseal
description: Dowiedz się więcej na temat sposobu dołączania baz danych na platformie Azure Eksplorator danych przy użyciu funkcji działania bazy danych.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: a46cf78d902ec8391d7dc3667a6d66daa78927ab
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828567"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Używanie bazy danych programu do uzupełniania w celu dołączania baz danych na platformie Azure Eksplorator danych

Funkcja programu do wykonywania w **bazie danych** umożliwia dołączenie bazy danych znajdującej się w innym klastrze do klastra usługi Azure Eksplorator danych. **Baza danych** programu sprawdzającego jest dołączona w trybie *tylko do odczytu* , dzięki czemu można wyświetlać dane i uruchamiać zapytania dotyczące danych, które zostały wprowadzone do **bazy danych lidera**. Baza danych programu do wykonania synchronizuje zmiany w bazach danych liderów. Ze względu na synchronizację istnieje opóźnienie danych z kilku sekund do kilku minut w czasie dostępności danych. Długość opóźnienia czasu zależy od całkowitego rozmiaru metadanych bazy danych lidera. Bazy danych lidera i programu do wykonania używają tego samego konta magazynu, aby pobrać dane. Magazyn jest własnością lidera bazy danych. Baza danych programu, która wyświetla dane bez konieczności pozyskiwania danych. Ponieważ dołączona baza danych jest bazą danych tylko do odczytu, nie można modyfikować danych, tabel i zasad w bazie danych z wyjątkiem [zasad buforowania](#configure-caching-policy), [podmiotów zabezpieczeń](#manage-principals)i [uprawnień](#manage-permissions). Nie można usunąć dołączonych baz danych. Muszą być odłączone przez lidera lub osoby wykonujące i tylko wtedy, gdy można je usunąć. 

Dołączanie bazy danych do innego klastra przy użyciu funkcji działania w ramach infrastruktury do udostępniania danych między organizacjami i zespołami. Ta funkcja jest przydatna do segregowania zasobów obliczeniowych w celu ochrony środowiska produkcyjnego z nieprodukcyjnych przypadków użycia. W celu skojarzenia kosztu klastra usługi Azure Eksplorator danych z firmą, która uruchamia zapytania dotyczące danych, można również użyć programu.

## <a name="which-databases-are-followed"></a>Które bazy danych są obserwowane?

* Klaster może być zgodny z jedną bazą danych, kilkoma bazami danych lub wszystkimi bazami danych klastra lidera. 
* Pojedynczy klaster może być zgodny z bazami danych z wielu klastrów liderów. 
* Klaster może zawierać obie bazy danych i liderów baz danych

## <a name="prerequisites"></a>Wymagania wstępne

1. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
1. [Utwórz klaster i bazę danych](/azure/data-explorer/create-cluster-database-portal) dla lidera i podążania.
1. Pozyskiwanie [danych](/azure/data-explorer/ingest-sample-data) do lidera bazy danych przy użyciu jednej z różnych metod omówionych w [omówieniu](/azure/data-explorer/ingest-data-overview)pozyskiwania.

## <a name="attach-a-database"></a>Dołączanie bazy danych

Istnieją różne metody, których można użyć do dołączenia bazy danych. W tym artykule omówiono sposób dołączania bazy danych C# przy użyciu szablonu lub Azure Resource Manager. Aby dołączyć bazę danych, musisz mieć uprawnienia do klastra lidera i klastra programu z instrukcjami. Aby uzyskać więcej informacji o uprawnieniach, zobacz [Zarządzanie uprawnieniami](#manage-permissions).

### <a name="attach-a-database-using-c"></a>Dołącz bazę danych przy użyciu poleceniaC#

**Wymagane NuGet**

* Zainstaluj [Microsoft. Azure. Management. Kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Zainstaluj [Microsoft. Rest. ClientRuntime. Azure. Authentication na potrzeby uwierzytelniania](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication).


```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "adc";
var databaseName = "db" // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Dołączanie bazy danych przy użyciu szablonu Azure Resource Manager

W tej sekcji dowiesz się, jak dołączyć bazę danych przy użyciu [szablonu Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the follower cluster."
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
                "description": "Name of the leader cluster to create."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "",
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
            "name": "[parameters('followerClusterName')]",
            "type": "Microsoft.Kusto/clusters",
            "sku": {
                "name": "Standard_D13_v2",
                "tier": "Standard",
                "capacity": 2
            },
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]"
        },
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Kusto/clusters', parameters('followerClusterName'))]"
            ],
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

Szablon Azure Resource Manager można wdrożyć za [pomocą Azure Portal](https://portal.azure.com) lub przy użyciu programu PowerShell.

   ![wdrożenie szablonu](media/follower/template-deployment.png)


|**Ustawienie**  |**Opis**  |
|---------|---------|
|Nazwa klastra z flagami     |  Nazwa klastra z flagami       |
|Nazwa dołączonych konfiguracji bazy danych    |    Nazwa dołączonego obiektu konfiguracji bazy danych. Nazwa musi być unikatowa na poziomie klastra.     |
|Nazwa bazy danych     |      Nazwa bazy danych, która ma zostać zastosowana. Jeśli chcesz postępować zgodnie z bazami danych lidera, użyj znaku "*".   |
|Identyfikator zasobu klastra lidera    |   Identyfikator zasobu dla klastra lidera.      |
|Rodzaj modyfikacji domyślnych podmiotów zabezpieczeń    |   Domyślny rodzaj modyfikacji głównej. Może być `Union`, `Replace` lub `None`. Aby uzyskać więcej informacji na temat domyślnego rodzaju modyfikacji podmiotu zabezpieczeń, zobacz [Podstawowe polecenie kontroli rodzaju modyfikacji](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind).      |
|Lokalizacja   |   Lokalizacja wszystkich zasobów. Lider i osoba wykonująca musi znajdować się w tej samej lokalizacji.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Weryfikowanie, czy baza danych została pomyślnie dołączona

Aby sprawdzić, czy baza danych została pomyślnie dołączona, Znajdź dołączone bazy danych w [Azure Portal](https://portal.azure.com). 

1. Przejdź do klastra programu reserwer i wybierz pozycję **bazy danych** .
1. Wyszukaj nowe bazy danych tylko do odczytu z listy baz danych.

    ![Baza danych z flagami tylko do odczytu](media/follower/read-only-follower-database.png)

Alternatywne

1. Przejdź do klastra lidera i wybierz pozycję **bazy danych**
2. Sprawdź, czy odpowiednie bazy danych są oznaczone jako **udostępniane innym osobom** > **tak**

    ![Odczytuj i zapisuj dołączone bazy danych](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>Odłączanie bazy danych programu używającego programuC# 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Odłączanie dołączonej bazy danych programu z instrukcjami z klastra programu reserwer

Klaster programu monitujących może odłączać wszelkie dołączone bazy danych w następujący sposób:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "adc";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Odłączanie dołączonej bazy danych programu do wykonania z klastra lidera

Klaster lidera może odłączyć dowolną dołączoną bazę danych w następujący sposób:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "adc",
        ClusterResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Zarządzaj podmiotami zabezpieczeń, uprawnieniami i zasadami buforowania

### <a name="manage-principals"></a>Zarządzaj podmiotami zabezpieczeń

Podczas dołączania bazy danych określ **domyślny rodzaj modyfikacji podmiotu zabezpieczeń**. Domyślnie zachowuje lidera kolekcja baz danych [autoryzowanych podmiotów zabezpieczeń](/azure/kusto/management/access-control/index.md#authorization)

|**Natur** |**Opis**  |
|---------|---------|
|**Unii**     |   Dołączone podmioty zabezpieczeń bazy danych zawsze będą zawierać pierwotne podmioty zabezpieczeń bazy danych oraz dodatkowe nowe podmioty zabezpieczeń dodane do bazy danych programu do wykonania.      |
|**Stępować**   |    Brak dziedziczenia podmiotów zabezpieczeń z oryginalnej bazy danych. Dla dołączonej bazy danych należy utworzyć nowe podmioty zabezpieczeń. Należy dodać co najmniej jednego podmiot zabezpieczeń, aby zablokować dziedziczenie podmiotu zabezpieczeń.     |
|**Dawaj**   |   Dołączone podmioty zabezpieczeń bazy danych zawierają tylko główne bazy danych bez dodatkowych podmiotów zabezpieczeń.      |

Aby uzyskać więcej informacji na temat używania poleceń sterowania do konfigurowania autoryzowanych podmiotów zabezpieczeń, zobacz [polecenia sterowania do zarządzania klastrem programu](/azure/kusto/management/cluster-follower.md).

### <a name="manage-permissions"></a>Zarządzaj uprawnieniami

Zarządzanie uprawnieniem do bazy danych tylko do odczytu jest takie samo jak w przypadku wszystkich typów baz danych. Zobacz [Zarządzanie uprawnieniami w Azure Portal](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Konfigurowanie zasad buforowania

Administrator bazy danych może zmodyfikować [Zasady buforowania](/azure/kusto/management/cache-policy) dołączonej bazy danych lub dowolnej z jej tabel w klastrze hostingu. Wartość domyślna zachowuje lidera kolekcja baz danych i zasad buforowania na poziomie tabeli. Można na przykład mieć 30-dniową zasadę buforowania dla lidera bazy danych do uruchamiania miesięcznego raportowania oraz trzy dni zasad buforowania w bazie danych programu, aby wykonać zapytanie dotyczące tylko najnowszych danych w celu rozwiązywania problemów. Aby uzyskać więcej informacji na temat używania poleceń sterowania do konfigurowania zasad buforowania w bazie danych lub tabeli programu, zobacz [polecenia sterowania do zarządzania klastrem programu](/azure/kusto/management/cluster-follower.md).

## <a name="limitations"></a>Ograniczenia

* Te klastry muszą znajdować się w tym samym regionie.
* Pozyskiwanie [strumieniowe](/azure/data-explorer/ingest-data-streaming) nie może być używane w przypadku bazy danych, która jest stosowana.
* Nie można usunąć bazy danych, która jest dołączona do innego klastra przed odłączeniem.
* Nie można usunąć klastra, który ma bazę danych dołączoną do innego klastra przed odłączeniem.
* Nie można zatrzymać klastra, który ma połączone z nim bazy danych (y). 

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat konfiguracji klastra z flagami, zobacz [polecenia sterowania do zarządzania klastrem programu](/azure/kusto/management/cluster-follower.md).