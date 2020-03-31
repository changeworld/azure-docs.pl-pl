---
title: Zestaw SDK usługi Azure HDInsight dla go
description: Materiał referencyjny do korzystania z zestawu Azure HDInsight SDK dla klastrów Go i Apache Hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 01/03/2020
ms.openlocfilehash: 292496c4d458621213fe62105149ac845d78891e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479590"
---
# <a name="hdinsight-sdk-for-go-preview"></a>HDInsight SDK for Go (wersja zapoznawcza)

## <a name="overview"></a>Omówienie
Zestawu HDInsight SDK for Go udostępnia klasy i funkcje, które umożliwiają zarządzanie klastrami HDInsight. Obejmuje operacje tworzenia, usuwania, aktualizowania, listy, ponownego rozmiaru, wykonywania akcji skryptów, monitorowania, uzyskania właściwości klastrów HDInsight i innych.

> [!NOTE]  
>Materiał referencyjny GoDoc dla tego SDK jest również [dostępny tutaj](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* [ `go get` Narzędzie](https://github.com/golang/go/wiki/GoGetTools).
* [Idź](https://golang.org/dl/).

## <a name="sdk-installation"></a>Instalacja SDK

Z lokalizacji GOPATH uruchom`go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Uwierzytelnianie

Zestaw SDK musi najpierw zostać uwierzytelniony za pomocą subskrypcji platformy Azure.  Postępuj zgodnie z poniższym przykładem, aby utworzyć jednostkę usługi i użyć go do uwierzytelniania. Po wykonaniu tej czynności będziesz mieć wystąpienie `ClustersClient`, który zawiera wiele funkcji (opisane w poniższych sekcjach), które mogą być używane do wykonywania operacji zarządzania.

> [!NOTE]  
> Istnieją inne sposoby uwierzytelniania oprócz poniższego przykładu, które potencjalnie mogą być lepiej dostosowane do Twoich potrzeb. Wszystkie funkcje są opisane w tym miejscu: [Funkcje uwierzytelniania w usłudze Azure SDK for Go](https://docs.microsoft.com/azure/go/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Przykład uwierzytelniania przy użyciu jednostki usługi

Najpierw zaloguj się do [usługi Azure Cloud Shell](https://shell.azure.com/bash). Sprawdź, czy aktualnie używasz subskrypcji, w której chcesz utworzyć jednostkę usługi.

```azurecli-interactive
az account show
```

Informacje o subskrypcji są wyświetlane jako JSON.

```json
{
  "environmentName": "AzureCloud",
  "id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "isDefault": true,
  "name": "XXXXXXX",
  "state": "Enabled",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "user": {
    "cloudShellID": true,
    "name": "XXX@XXX.XXX",
    "type": "user"
  }
}
```

Jeśli nie jesteś zalogowany do właściwej subskrypcji, wybierz właściwą, uruchamiając: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> Jeśli dostawca zasobów HDInsight nie został jeszcze zarejestrowany przez inną funkcję (na przykład przez utworzenie klastra HDInsight za pośrednictwem witryny Azure portal), należy to zrobić raz, zanim będzie można uwierzytelnić. Można to zrobić za pomocą usługi [Azure Cloud Shell,](https://shell.azure.com/bash) uruchamiając następujące polecenie:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Następnie wybierz nazwę jednostki usługi i utwórz ją za pomocą następującego polecenia:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

Informacje o jednostki usługi są wyświetlane jako JSON.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Skopiuj poniższy fragment `TENANT_ID` `CLIENT_ID`kodu `CLIENT_SECRET`i `SUBSCRIPTION_ID` wypełnij , , i z ciągami z JSON, który został zwrócony po uruchomieniu polecenia, aby utworzyć jednostkę usługi.

```golang
package main

import (
    "context"
    "github.com/Azure/go-autorest/autorest/azure/auth"
    hdi "github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight"
    "github.com/Azure/go-autorest/autorest/to"
)

func main() {
    var err error

    // Tenant ID for your Azure Subscription
    var TENANT_ID = ""
    // Your Service Principal App Client ID
    var CLIENT_ID = ""
    // Your Service Principal Client Secret
    var CLIENT_SECRET = ""
    // Azure Subscription ID
    var SUBSCRIPTION_ID = ""

    var credentials = auth.NewClientCredentialsConfig(CLIENT_ID, CLIENT_SECRET, TENANT_ID)
    var client = hdi.NewClustersClient(SUBSCRIPTION_ID)

    client.Authorizer, err = credentials.Authorizer()
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
```

## <a name="cluster-management"></a>Zarządzanie klastrami

> [!NOTE]  
> W tej sekcji przyjęto założenie, że `ClusterClient` zostało już uwierzytelnione `client`i skonstruowane wystąpienie i przechowywanie go w zmiennej o nazwie . Instrukcje dotyczące uwierzytelniania i `ClusterClient` uzyskiwania można znaleźć w sekcji Uwierzytelnianie powyżej.

### <a name="create-a-cluster"></a>Tworzenie klastra

Nowy klaster można utworzyć, wywołując program `client.Create()`. 

#### <a name="example"></a>Przykład

W tym przykładzie pokazano, jak utworzyć klaster [Platformy Spark Apache](https://spark.apache.org/) z dwoma węzłami głównymi i jednym węzłem procesu roboczego.

> [!NOTE]  
> Najpierw musisz utworzyć konto grupy zasobów i magazynu, jak wyjaśniono poniżej. Jeśli zostały już utworzone, można pominąć te kroki.

##### <a name="creating-a-resource-group"></a>Tworzenie grupy zasobów

Grupę zasobów można utworzyć przy użyciu [usługi Azure Cloud Shell,](https://shell.azure.com/bash) uruchamiając

```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```

##### <a name="creating-a-storage-account"></a>Tworzenie konta magazynu

Konto magazynu można utworzyć przy użyciu usługi [Azure Cloud Shell,](https://shell.azure.com/bash) uruchamiając:

```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```

Teraz uruchom następujące polecenie, aby uzyskać klucz dla konta magazynu (będzie to potrzebne do utworzenia klastra):

```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```

---
Poniższy fragment kodu Go tworzy klaster platformy Spark z dwoma węzłami głównymi i jednym węzłem procesu roboczego. Wypełnij puste zmienne, jak wyjaśniono w komentarzach i możesz zmienić inne parametry, aby dostosować je do konkretnych potrzeb.

```golang
// The name for the cluster you are creating
var clusterName = "";
// The name of your existing Resource Group
var resourceGroupName = "";
// Choose a username
var username = "";
// Choose a password
var password = "";
// Replace <> with the name of your storage account
var storageAccount = "<>.blob.core.windows.net";
// Storage account key you obtained above
var storageAccountKey = "";
// Choose a region
var location = "";
var container = "default";

var parameters = hdi.ClusterCreateParametersExtended {
    Location: to.StringPtr(location),
    Tags: make(map[string]*string),
    Properties: &hdi.ClusterCreateProperties {
        ClusterVersion: to.StringPtr("3.6"),
        OsType: hdi.Linux,
        ClusterDefinition: &hdi.ClusterDefinition {
            Kind: to.StringPtr("spark"),
            Configurations: map[string]map[string]interface{}{
                "gateway": {
                    "restAuthCredential.isEnabled": "True",
                    "restAuthCredential.username":  username,
                    "restAuthCredential.password":  password,
                },
            },
        },
        Tier: hdi.Standard,
        ComputeProfile: &hdi.ComputeProfile {
            Roles: &[]hdi.Role {
                hdi.Role {
                    Name: to.StringPtr("headnode"),
                    TargetInstanceCount: to.Int32Ptr(2),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
                hdi.Role {
                    Name: to.StringPtr("workernode"),
                    TargetInstanceCount: to.Int32Ptr(1),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
            },
        },
        StorageProfile: &hdi.StorageProfile {
            Storageaccounts: &[]hdi.StorageAccount {
                hdi.StorageAccount {
                    Name: to.StringPtr(storageAccount),
                    Key: to.StringPtr(storageAccountKey),
                    Container: to.StringPtr(container),
                    IsDefault: to.BoolPtr(true),
                },
            },
        },
    },
}
client.Create(context.Background(), resourceGroupName, clusterName, parameters)
```

### <a name="get-cluster-details"></a>Uzyskaj szczegółowe informacje o klastrze

Aby uzyskać właściwości dla danego klastra:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Przykład

Można użyć, `get` aby potwierdzić, że pomyślnie utworzono klaster.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

Dane wyjściowe powinny wyglądać następująco:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Listy klastrów

#### <a name="list-clusters-under-the-subscription"></a>Wyświetlanie listy klastrów w ramach subskrypcji

```golang
client.List()
```

#### <a name="list-clusters-by-resource-group"></a>Wyświetlanie listy klastrów według grup zasobów

```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> Oba `List()` `ListByResourceGroup()` i `ClusterListResultPage` zwracać strukturę. Aby uzyskać następną stronę, `Next()`możesz zadzwonić . Można to powtórzyć `ClusterListResultPage.NotDone()` `false`do momentu zwrotu , jak pokazano w poniższym przykładzie.

#### <a name="example"></a>Przykład

Poniższy przykład drukuje właściwości wszystkich klastrów dla bieżącej subskrypcji:

```golang
page, err := client.List(context.Background())
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, cluster := range page.Values() {
        fmt.Println(*cluster.Name)
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="delete-a-cluster"></a>Usuwanie klastra

Aby usunąć klaster:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Aktualizowanie znaczników klastra

Można zaktualizować znaczniki danego klastra w taki sposób:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```

#### <a name="example"></a>Przykład

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Ponowne rozmiary klastra

Można zmienić rozmiar liczby węzłów procesu roboczego danego klastra, określając nowy rozmiar w taki sposób:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Monitorowanie klastrów

Zestaw SDK zarządzania hdinsight może być również używany do zarządzania monitorowaniem klastrów za pośrednictwem pakietu Operations Management Suite (OMS).

Podobnie jak utworzone `ClusterClient` do użycia dla operacji zarządzania, `ExtensionClient` należy utworzyć do użycia do monitorowania operacji. Po zakończeniu powyższej sekcji Uwierzytelnianie możesz `ExtensionClient` utworzyć w ten sposób:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Poniższe przykłady monitorowania zakładają, `ExtensionClient` że `extClient` już `Authorizer` zainicjowano wywołanie i ustaw jego, jak pokazano powyżej.

### <a name="enable-oms-monitoring"></a>Włącz monitorowanie systemu OMS

> [!NOTE]  
> Aby włączyć monitorowanie systemu oms, musisz mieć istniejący obszar roboczy usługi Log Analytics. Jeśli jeszcze go nie utworzono, możesz dowiedzieć się, jak to zrobić tutaj: [Utwórz obszar roboczy usługi Log Analytics w witrynie Azure portal](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

Aby włączyć monitorowanie systemu oms w klastrze:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Wyświetlanie stanu monitorowania systemu OMS

Aby uzyskać stan usługi OMS w klastrze:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>Wyłączanie monitorowania systemu OMS

Aby wyłączyć usługę OMS w klastrze:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Akcje skryptu

Usługa HDInsight udostępnia funkcję konfiguracji o nazwie akcje skryptu, która wywołuje skrypty niestandardowe w celu dostosowania klastra.

> [!NOTE]  
> Więcej informacji na temat używania akcji skryptów można znaleźć tutaj: [Dostosowywanie klastrów HDInsight opartych na systemie Linux przy użyciu akcji skryptów](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)

### <a name="execute-script-actions"></a>Wykonywanie akcji skryptu

Akcje skryptu w danym klastrze można wykonać w taki sposób:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

W przypadku operacji "Usuń akcję skryptu" i "Lista akcji `ScriptActionsClient`skryptu utrwalone" należy utworzyć program , podobnie jak utworzony `ClusterClient` do użycia w operacjach zarządzania. Po zakończeniu powyższej sekcji Uwierzytelnianie możesz `ScriptActionsClient` utworzyć podobny sposób:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Poniższe przykłady akcji skryptu zakładają, `scriptActionsClient` że `Authorizer` już zainicjowano wywołanie `ScriptActionsClient` i ustawiłeś jego, jak pokazano powyżej.

### <a name="delete-script-action"></a>Akcja usuń skrypt

Aby usunąć określoną akcję utrwalonego skryptu w danym klastrze:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Lista akcji skryptu utrwalonych

> [!NOTE]  
> Oba `ListByCluster()` zwraca `ScriptActionsListPage` strukturę. Aby uzyskać następną stronę, `Next()`możesz zadzwonić . Można to powtórzyć `ClusterListResultPage.NotDone()` `false`do momentu zwrotu , jak pokazano w poniższym przykładzie.

Aby wyświetlić listę wszystkich akcji skryptu utrwalonych dla określonego klastra:
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Przykład

```golang
page, err := scriptActionsClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="list-all-scripts-execution-history"></a>Wyświetl listę historii wykonywania wszystkich skryptów

W przypadku tej operacji należy `ScriptExecutionHistoryClient`utworzyć program , `ClusterClient` podobnie jak utworzony do użycia dla operacji zarządzania. Po zakończeniu powyższej sekcji Uwierzytelnianie możesz `ScriptActionsClient` utworzyć podobny sposób:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Poniżej założono, że już `ScriptExecutionHistoryClient` `scriptExecutionHistoryClient` zainicjowano `Authorizer` wywołanie i ustaw jego, jak pokazano powyżej.

Aby wyświetlić listę historii wykonywania wszystkich skryptów dla określonego klastra:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Przykład

W tym przykładzie odbite wszystkie szczegóły dla wszystkich poprzednich wykonań skryptu.

```golang
page, err := scriptExecutionHistoryClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [materiałem referencyjnym GoDoc](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight). GoDocs zawierają dokumentację referencyjną dla wszystkich funkcji w SDK.
