---
title: Dostosowywanie konfiguracji klastra usługi Azure HDInsight przy użyciu narzędzia bootstrap
description: Dowiedz się, jak programowo dostosowywać konfigurację klastra HDInsight przy użyciu szablonów .NET, PowerShell i Resource Manager.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/01/2020
ms.openlocfilehash: 796dbc53d1adf310028e06dea319b9a60d5cf54b
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529345"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Dostosowywanie klastrów HDInsight przy użyciu bootstrap

Skrypty bootstrap umożliwiają programowe instalowanie i konfigurowanie składników w usłudze Azure HDInsight.

Istnieją trzy podejścia do ustawiania ustawień plików konfiguracyjnych podczas tworzenia klastra HDInsight:

* Korzystanie z programu Azure PowerShell
* Korzystanie z zestawu SDK dla platformy .NET
* Korzystanie z szablonu usługi Azure Resource Manager

Na przykład za pomocą tych metod programowych można skonfigurować opcje w tych plikach:

* clusterIdentity.xml
* core-site.xml
* plik gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* strona ul.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* burza-site.xml
* tez-site.xml
* webhcat-site.xml
* przędza-site.xml
* server.properties (konfiguracja kafka-broker)

Aby uzyskać informacje na temat instalowania dodatkowych składników w klastrze HDInsight w czasie tworzenia, zobacz [Dostosowywanie klastrów HDInsight przy użyciu akcji skryptów (Linux).](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli korzystasz z programu PowerShell, potrzebny jest [moduł Az](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Następujący kod programu PowerShell dostosowuje konfigurację [gałęzi Apache:](https://hive.apache.org/)

> [!IMPORTANT]  
> Może `Spark2Defaults` być konieczne użycie [parametru z dodatkiem Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). Można przekazać puste wartości do parametru, jak pokazano w przykładzie kodu poniżej.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90s" }

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

Kompletny działający skrypt programu PowerShell można znaleźć w [dodatku](#appendix-powershell-sample).

**Aby zweryfikować zmianę:**

1. Przejdź `https://CLUSTERNAME.azurehdinsight.net/` do `CLUSTERNAME` miejsca, w którym znajduje się nazwa klastra.
1. W menu po lewej stronie przejdź do **pozycji Hive** > **Configs** > **Advanced**.
1. Rozwiń **zaawansowaną lokację ul**.
1. Znajdź **hive.metastore.client.socket.timeout** i upewnij się, że wartość to **90s**.

Kilka przykładów dostosowywania innych plików konfiguracyjnych:

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

Zobacz [Zestaw SDK usługi Azure HDInsight dla platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).

## <a name="use-resource-manager-template"></a>Używanie szablonu usługi Resource Manager

W szablonie Menedżera zasobów można użyć boottrapu:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![Hadoop dostosowuje szablon usługi Azure Resource Manager z zestawem inautu](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

Przykładowy fragment szablonu Menedżera zasobów, aby przełączyć konfigurację w domyślnych spark2, aby okresowo czyścić dzienniki zdarzeń z magazynu.  

```json
"configurations": {
    "spark2-defaults": {
        "spark.history.fs.cleaner.enabled": "true",
        "spark.history.fs.cleaner.interval": "7d",
        "spark.history.fs.cleaner.maxAge": "90d"
    }
}
```

## <a name="see-also"></a>Zobacz też

* [Tworzenie klastrów Apache Hadoop w programie HDInsight](hdinsight-hadoop-provision-linux-clusters.md) zawiera instrukcje dotyczące tworzenia klastra HDInsight przy użyciu innych opcji niestandardowych.
* [Tworzenie skryptów akcji skryptów dla hdinsight](hdinsight-hadoop-script-actions-linux.md)
* [Instalowanie i używanie aplikacji Apache Spark w klastrach HDInsight](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [Zainstaluj i używaj Apache Giraph w klastrach HDInsight](hdinsight-hadoop-giraph-install.md).

## <a name="appendix-powershell-sample"></a>Dodatek: Przykład programu PowerShell

Ten skrypt programu PowerShell tworzy klaster HDInsight i dostosowuje ustawienie gałęzi. Pamiętaj, aby wprowadzić `$nameToken` `$httpPassword`wartości `$sshPassword`dla , i .

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

# Note: Storage account kind BlobStorage cannot be used as primary storage.

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
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90s"}

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
