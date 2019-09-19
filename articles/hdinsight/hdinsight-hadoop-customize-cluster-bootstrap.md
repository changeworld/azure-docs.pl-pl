---
title: Dostosowywanie konfiguracji klastrów usługi Azure HDInsight przy użyciu narzędzia Bootstrap
description: Dowiedz się, jak dostosować konfigurację klastra usługi HDInsight programowo przy użyciu szablonów programu .NET, programu PowerShell i Menedżer zasobów.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 15d08b14e38f097e8e9c3e0db893efb1d6efe44d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098671"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Dostosowywanie klastrów usługi HDInsight przy użyciu narzędzia Bootstrap

Skrypty Bootstrap umożliwiają programowe Instalowanie i Konfigurowanie składników w usłudze Azure HDInsight.

Istnieją trzy podejścia do ustawiania ustawień pliku konfiguracji podczas tworzenia klastra usługi HDInsight:

* Korzystanie z programu Azure PowerShell
* Korzystanie z zestawu SDK dla platformy .NET
* Korzystanie z szablonu usługi Azure Resource Manager

Na przykład przy użyciu tych metod programistycznych można skonfigurować opcje w następujących plikach:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred — lokacja
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* Server. Properties (Konfiguracja Kafka-Broker)

Aby uzyskać informacje na temat instalowania dodatkowych składników w klastrze usługi HDInsight podczas tworzenia, zobacz [Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Wymagania wstępne

* W przypadku korzystania z programu PowerShell konieczne jest polecenie [AZ module](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Poniższy kod programu PowerShell dostosowuje konfigurację [Apache Hive](https://hive.apache.org/) :

> [!IMPORTANT]  
> Może być `Spark2Defaults` konieczne użycie parametru [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). Można przekazać wartości puste do parametru, jak pokazano w poniższym przykładzie kodu.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

Pełny działający skrypt programu PowerShell znajduje się w [dodatku](#appendix-powershell-sample).

**Aby sprawdzić zmianę:**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie kliknij pozycję **Klastry usługi HDInsight**. Jeśli nie widzisz go, najpierw kliknij pozycję **wszystkie usługi** .
3. Kliknij właśnie utworzony klaster przy użyciu skryptu programu PowerShell.
4. Kliknij pozycję **pulpit nawigacyjny** w górnej części bloku, aby otworzyć interfejs użytkownika Ambari.
5. W menu po lewej stronie kliknij pozycję **gałąź Hive** .
6. Kliknij pozycję **serwera hiveserver2** z **podsumowania**.
7. Kliknij kartę **konfiguracje** .
8. W menu po lewej stronie kliknij pozycję **gałąź Hive** .
9. Kliknij przycisk **zaawansowane** kartę.
10. Przewiń w dół, a następnie rozwiń pozycję **zaawansowana witryna Hive**.
11. Znajdź **gałąź Hive. metadanych. Client. Socket. Timeout** w sekcji.

Więcej przykładów na temat dostosowywania innych plików konfiguracji:

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET
Zobacz [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu zestawu .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Używanie szablonu usługi Resource Manager
Narzędzia Bootstrap można użyć w szablonie Menedżer zasobów:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![Usługa Hadoop dostosowuje szablon Azure Resource Manager ładowania początkowego klastra](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Zobacz także

* [Tworzenie klastrów Apache Hadoop w usłudze HDInsight][hdinsight-provision-cluster] zawiera instrukcje dotyczące tworzenia klastra usługi HDInsight przy użyciu innych opcji niestandardowych.
* [Tworzenie skryptów akcji skryptu dla usługi HDInsight][hdinsight-write-script]
* [Instalowanie i używanie Apache Spark w klastrach usługi HDInsight][hdinsight-install-spark]
* [Instalowanie i używanie oprogramowania Apache Giraph w klastrach usługi HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions-linux.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Etapy podczas tworzenia klastra"

## <a name="appendix-powershell-sample"></a>Dodatek Przykładowy skrypt programu PowerShell

Ten skrypt programu PowerShell tworzy klaster usługi HDInsight i dostosowuje ustawienie Hive. Pamiętaj, aby wprowadzić wartości dla `$nameToken`, `$httpPassword`i `$sshPassword`.

> [!WARNING]  
> Nie można używać `BlobStorage` rodzaju konta magazynu dla klastrów usługi HDInsight.

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>" 
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>' 

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>' 
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```
