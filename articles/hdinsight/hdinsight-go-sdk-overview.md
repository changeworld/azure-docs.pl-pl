---
title: Zestaw SDK usługi Azure HDInsight z rzeczywistym użyciem
description: Dokumentacja dotycząca usługi Azure HDInsight Go SDK
author: tylerfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: tyfox
ms.custom: seodec18
ms.openlocfilehash: 2e5b7816fda89e25dcb0de26f526e5187e0640b9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098562"
---
# <a name="hdinsight-go-management-sdk-preview"></a>HDInsight Go Management SDK w wersji zapoznawczej

## <a name="overview"></a>Omówienie
Zestaw SDK języka Go HDInsight zawiera klasy i funkcje, które umożliwiają zarządzanie klastrami usługi HDInsight. Obejmuje ona na tworzenie, usuwanie, aktualizowanie, listy, zmiany rozmiaru, wykonywanie akcji skryptu, monitorować i pobieranie właściwości klastrów HDInsight i innych operacji.

> [!NOTE]  
>Materiał referencyjny GoDoc dla tego zestawu SDK jest także [dostępne tutaj](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight).

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure. Jeśli nie masz, [bezpłatna wersja próbna](https://azure.microsoft.com/free/).
* [Go](https://golang.org/dl/).

## <a name="sdk-installation"></a>Instalacja zestawu SDK

W Twojej lokalizacji GOPATH Uruchom polecenie `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Authentication

Zestaw SDK musi najpierw uwierzytelnić się za pomocą subskrypcji platformy Azure.  Postępuj zgodnie z przykładu poniżej, aby utworzyć jednostkę usługi i używać go do uwierzytelniania. Po zakończeniu tej operacji będzie mieć wystąpienia `ClustersClient`, który zawiera wiele funkcji (opisane w poniższych sekcjach), które mogą służyć do wykonywania operacji zarządzania.

> [!NOTE]  
> Istnieją inne sposoby uwierzytelniania poza poniższym przykładzie, który potencjalnie może jej lepszego dostosowania do własnych potrzeb. Wszystkie funkcje są opisane w tym miejscu: [Funkcje uwierzytelniania w zestawie Azure SDK dla języka Go](https://docs.microsoft.com/go/azure/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Przykład uwierzytelniania przy użyciu jednostki usługi

Po pierwsze, zaloguj się do [usługi Azure Cloud Shell](https://shell.azure.com/bash). Sprawdź, czy obecnie używasz subskrypcję, w której utworzono nazwę główną usługi. 

```azurecli-interactive
az account show
```

Informacje o subskrypcji jest wyświetlana jako JSON.

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

Jeśli one nie zalogował się do poprawną subskrypcję, wybierz właściwy, uruchamiając: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> Jeśli nie została już zarejestrowana HDInsight dostawcy zasobów przez inną funkcję (takich jak przez utworzenie klastra usługi HDInsight za pośrednictwem witryny Azure portal), należy to zrobić, gdy wcześniej można wybrać metodę uwierzytelniania. Można to zrobić z [usługi Azure Cloud Shell](https://shell.azure.com/bash) , uruchamiając następujące polecenie:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Następnie wybierz nazwę główną usługi i utwórz go za pomocą następującego polecenia:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

Informacji o jednostce usługi jest wyświetlane w formacie JSON.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Skopiuj poniższy fragment kodu i wypełnij `TENANT_ID`, `CLIENT_ID`, `CLIENT_SECRET`, i `SUBSCRIPTION_ID` z ciągami z formatu JSON, który został zwrócony po uruchomieniu polecenia, aby utworzyć jednostkę usługi.

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
> W tej sekcji założono już uwierzytelniony i skonstruowany `ClusterClient` wystąpienia i zapisz go w zmiennej o nazwie `client`. Instrukcje dotyczące uwierzytelniania i uzyskania `ClusterClient` znajdują się w powyższej sekcji uwierzytelnianie.

### <a name="create-a-cluster"></a>Tworzenie klastra

Nowy klaster można utworzyć przez wywołanie metody `client.Create()`. 

#### <a name="example"></a>Przykład

Ten przykład przedstawia sposób tworzenia [platformy Apache Spark](https://spark.apache.org/) klastra z 2 węzłami głównymi i węzłem procesu roboczego 1.

> [!NOTE]  
> Najpierw należy utworzyć grupę zasobów i konta magazynu, co zostało opisane poniżej. Jeśli utworzono już one, możesz pominąć tę procedurę.

##### <a name="creating-a-resource-group"></a>Tworzenie grupy zasobów

Można utworzyć grupę zasobów za pomocą [usługi Azure Cloud Shell](https://shell.azure.com/bash) , uruchamiając
```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```
##### <a name="creating-a-storage-account"></a>Tworzenie konta magazynu

Można utworzyć konta magazynu przy użyciu [usługi Azure Cloud Shell](https://shell.azure.com/bash) , uruchamiając:
```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```
Teraz uruchom następujące polecenie, aby pobrać klucz konta magazynu (będzie on potrzebny do utworzenia klastra):
```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```
---
Poniżej Go fragment kodu tworzy klaster Spark z 2 węzłami głównymi i węzłem procesu roboczego 1. Wypełnij puste zmienne, jak wyjaśniono w komentarzach i możesz zmienić inne parametry w zależności od określonych potrzeb.

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

### <a name="get-cluster-details"></a>Pobierz szczegóły klastra

Aby wyświetlić właściwości dla danego klastra:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Przykład

Możesz użyć `get` aby upewnić się, że udało Ci się utworzyć klaster.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

Dane wyjściowe powinny wyglądać:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Wyświetlanie listy klastrów

#### <a name="list-clusters-under-the-subscription"></a>Wyświetlanie listy klastrów w ramach subskrypcji
```golang
client.List()
```
#### <a name="list-clusters-by-resource-group"></a>Wyświetlanie listy klastrów w grupie zasobów
```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> Zarówno `List()` i `ListByResourceGroup()` zwracają `ClusterListResultPage` struktury. Aby uzyskać następnej strony, można wywołać `Next()`. To można powtórzyć, dopóki `ClusterListResultPage.NotDone()` zwraca `false`, jak pokazano w poniższym przykładzie.

#### <a name="example"></a>Przykład
Poniższy przykład wyświetla właściwości wszystkich klastrów w ramach bieżącej subskrypcji:

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

### <a name="update-cluster-tags"></a>Aktualizacji tagów klastra

Możesz zaktualizować tagi dany klaster w następujący sposób:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```
#### <a name="example"></a>Przykład

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Zmień rozmiar klastra

Możesz zmienić rozmiar klastra podanej liczby węzłów procesu roboczego, określając nowy rozmiar w następujący sposób:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Monitorowanie klastrów

HDInsight Management SDK może służyć także do zarządzania, monitorowania w klastrach za pośrednictwem konsoli Operations Management Suite (OMS).

Podobnie do sposobu tworzenia `ClusterClient` na potrzeby operacji zarządzania, należy utworzyć `ExtensionClient` na potrzeby monitorowania operacji. Po wykonaniu powyższej sekcji uwierzytelnianie, możesz utworzyć `ExtensionClient` w następujący sposób:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Poniższe przykłady monitorowania przyjęto założenie, zostały już zainicjowane `ExtensionClient` o nazwie `extClient` i ustaw jego `Authorizer` jak pokazano powyżej.

### <a name="enable-oms-monitoring"></a>Aby włączyć monitorowanie pakietu OMS

> [!NOTE]  
> Aby włączyć monitorowanie pakietu OMS, musi mieć istniejący obszar roboczy usługi Log Analytics. Jeśli nie utworzono już jeden, możesz dowiedzieć się, jak to zrobić tutaj: [Utwórz obszar roboczy usługi Log Analytics w witrynie Azure portal](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

Aby włączyć monitorowanie pakietu OMS, w klastrze:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Wyświetlanie stanu monitorowania pakietu OMS

Aby wyświetlić stan pakietu OMS w klastrze:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>Wyłącz monitorowanie pakietu OMS

Aby wyłączyć pakietu OMS w klastrze:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Działania skryptu

HDInsight udostępnia funkcję konfiguracji o nazwie akcji skryptu, wywołująca skryptów niestandardowych, aby dostosować klastra.

> [!NOTE]  
> Więcej informacji na temat sposobu użyj akcji skryptu, można znaleźć tutaj: [Dostosowywanie klastrów HDInsight opartych na systemie Linux za pomocą akcji skryptu](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)

### <a name="execute-script-actions"></a>Wykonywanie akcji skryptu

Można wykonać akcji skryptów w danym klastrze w następujący sposób:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

Dla operacji "Usuń akcję skryptu" i "Działania skryptu utrwalone listy", musisz utworzyć `ScriptActionsClient`, podobnie do sposobu tworzenia `ClusterClient` na potrzeby operacji zarządzania. Po wykonaniu powyższej sekcji uwierzytelnianie, możesz utworzyć `ScriptActionsClient` w następujący sposób:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Poniżej akcji skryptu przykładach założono, zostały już zainicjowane `ScriptActionsClient` o nazwie `scriptActionsClient` i ustaw jego `Authorizer` jak pokazano powyżej.

### <a name="delete-script-action"></a>Usuń akcję skryptu

Aby usunąć akcję określonego utrwalonego skryptu w danym klastrze:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Lista Akcje utrwalonego skryptu

> [!NOTE]  
> Zarówno `ListByCluster()` zwraca `ScriptActionsListPage` struktury. Aby uzyskać następnej strony, można wywołać `Next()`. To można powtórzyć, dopóki `ClusterListResultPage.NotDone()` zwraca `false`, jak pokazano w poniższym przykładzie.

Aby wyświetlić listę wszystkich akcji utrwalonego skryptu dla określonego klastra:
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

### <a name="list-all-scripts-execution-history"></a>Listy historii wykonywania wszystkie skrypty

Dla tej operacji, należy utworzyć `ScriptExecutionHistoryClient`, podobnie do sposobu tworzenia `ClusterClient` na potrzeby operacji zarządzania. Po wykonaniu powyższej sekcji uwierzytelnianie, możesz utworzyć `ScriptActionsClient` w następujący sposób:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Poniżej założono, zostały już zainicjowane `ScriptExecutionHistoryClient` o nazwie `scriptExecutionHistoryClient` i ustaw jego `Authorizer` jak pokazano powyżej.

Aby wyświetlić historię wykonywania wszystkie skrypty dla określonego klastra:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Przykład

Wszystkie szczegółowe informacje w tym przykładzie drukuje wszystkie wcześniejsze wykonania skryptu.

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

## <a name="next-steps"></a>Kolejne kroki

* Zapoznaj się z [materiały referencyjne GoDoc](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight). GoDocs zawierają dokumentację referencyjną dla wszystkich funkcji w zestawie SDK.
