---
title: Dostosowywanie konfiguracji klastra Azure HDInsight za pomocą narzędzia bootstrap
description: Dowiedz się, jak dostosować konfigurację klastra HDInsight programowo przy użyciu platformy .net, PowerShell i Menedżera zasobów szablonów.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: b1bc0a68a9cf52e886c0664a474a4dbb75126698
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003534"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Dostosowywanie klastrów HDInsight za pomocą narzędzia Bootstrap

Skrypty uruchamiania umożliwiają instalowanie i konfigurowanie składników w usłudze Azure HDInsight programowo. 

Istnieją trzy sposoby ustawienia pliku konfiguracji podczas tworzenia klastra usługi HDInsight:

* Korzystanie z programu Azure PowerShell
* Korzystanie z zestawu SDK dla platformy .NET
* Korzystanie z szablonu usługi Azure Resource Manager

Korzystając z tych metod programistycznych, możesz na przykład skonfigurować opcje w tych plikach:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* Server.Properties (Konfiguracja brokera platformy kafka)

Aby uzyskać informacje na temat instalowania dodatkowych składników w klastrze HDInsight podczas tworzenia, zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli przy użyciu programu PowerShell, konieczne będzie [modułu Az](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Następujący kod PowerShell dostosowuje [Apache Hive](https://hive.apache.org/) konfiguracji:

> [!IMPORTANT]  
> Parametr `Spark2Defaults` może być konieczne do użycia z [AzHDInsightConfigValues Dodaj](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalues). Wartości puste można przekazać do parametru, jak pokazano w poniższym przykładzie kodu.


```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValues `
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

Zakończenie pracy skryptu programu PowerShell można znaleźć w [dodatku](#appendix-powershell-sample).

**Aby sprawdzić zmiany:**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie kliknij **klastry HDInsight**. Jeśli nie widzisz, kliknij przycisk **wszystkich usług** pierwszy.
3. Kliknij klaster, nowo utworzoną za pomocą skryptu programu PowerShell.
4. Kliknij przycisk **pulpit nawigacyjny** w górnej części bloku aby otworzyć interfejsu użytkownika Ambari.
5. Kliknij przycisk **Hive** menu po lewej stronie.
6. Kliknij przycisk **serwera HiveServer2** z **Podsumowanie**.
7. Kliknij przycisk **Configs** kartę.
8. Kliknij przycisk **Hive** menu po lewej stronie.
9. Kliknij kartę **Zaawansowane**.
10. Przewiń w dół, a następnie rozwiń węzeł **zaawansowane witryny hive**.
11. Wyszukaj **hive.metastore.client.socket.timeout** w sekcji.

Przykłady więcej o dostosowywaniu inne pliki konfiguracji:

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
Zobacz [opartych na systemie Linux z Tworzenie klastrów w HDInsight przy użyciu zestawu .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Szablon usługi Resource Manager Użyj
Usługa ładowania początkowego można użyć w szablonie usługi Resource Manager:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![HDInsight Hadoop dostosowuje bootstrap szablonu usługi Azure Resource Manager klastra](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Zobacz także
* [Tworzenie klastrów usługi Apache Hadoop w HDInsight] [ hdinsight-provision-cluster] zawiera instrukcje dotyczące sposobu tworzenia klastra usługi HDInsight przy użyciu niestandardowych opcji.
* [Tworzenie akcji skryptu skryptów dla HDInsight][hdinsight-write-script]
* [Instalowanie i używanie platformy Apache Spark w klastrach HDInsight][hdinsight-install-spark]
* [Instalowanie i używanie Apache Giraph w klastrach HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions-linux.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Etapy podczas tworzenia klastra"

## <a name="appendix-powershell-sample"></a>Dodatek: Przykładowy skrypt programu PowerShell

Ten skrypt programu PowerShell umożliwia utworzenie klastra usługi HDInsight i dostosowuje ustawienie gałęzi. Pamiętaj wprowadzić wartości `$nameToken`, `$httpPassword`, i `$sshPassword`.

> [!IMPORTANT]  
> Wartości `DefaultStorageAccount`, i `DefaultStorageContainer` nie są zwracane z [Get AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) podczas [bezpieczny transfer](../storage/common/storage-require-secure-transfer.md) jest włączona na koncie magazynu.

> [!WARNING]  
> Rodzaj konta magazynu `BlobStorage` nie można używać w przypadku klastrów HDInsight.


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
    | Add-AzHDInsightConfigValues `
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
