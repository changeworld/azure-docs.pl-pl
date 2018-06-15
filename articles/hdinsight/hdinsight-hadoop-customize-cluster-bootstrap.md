---
title: Dostosowywanie klastrów usługi HDInsight przy użyciu ładowania początkowego - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostosować klastry usługi HDInsight przy użyciu ładowania początkowego.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ab2ebf0c-e961-4e95-8151-9724ee22d769
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 2fdbb8730d350023035038d60d17a5ad12c98bc0
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34272135"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Dostosowywanie klastrów usługi HDInsight przy użyciu ładowania początkowego

Czasami użytkownik chce skonfigurować pliki konfiguracyjne, które obejmują:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred lokacji
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* Server.Properties (brokera kafka konfiguracji)

Istnieją trzy metody do początkowego użycia:

* Korzystanie z programu Azure PowerShell
* Korzystanie z zestawu SDK dla platformy .NET
* Korzystanie z szablonu usługi Azure Resource Manager

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Aby uzyskać informacje na temat instalowania dodatkowych składników w klastrze usługi HDInsight podczas tworzenia zobacz:

* [Dostosowywanie klastrów usługi HDInsight przy użyciu akcji skryptu (Linux)](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Poniższy kod programu PowerShell dostosowuje konfiguracji gałęzi:

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzureRmHDInsightClusterConfig `
    | Set-AzureRmHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzureRmHDInsightConfigValues `
        -HiveSite $hiveConfigValues 

New-AzureRmHDInsightCluster `
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
2. Z menu po lewej stronie kliknij **klastrów usługi HDInsight**. Jeśli nie widzisz, kliknij przycisk **wszystkie usługi** pierwszy.
3. Kliknij utworzony przy użyciu skryptu środowiska PowerShell klastra.
4. Kliknij przycisk **pulpitu nawigacyjnego** od górnej krawędzi bloku, aby otworzyć Interfejs użytkownika narzędzia Ambari.
5. Kliknij przycisk **Hive** z menu po lewej stronie.
6. Kliknij przycisk **serwera HiveServer2** z **Podsumowanie**.
7. Kliknij przycisk **Configs** kartę.
8. Kliknij przycisk **Hive** z menu po lewej stronie.
9. Kliknij przycisk **zaawansowane** kartę.
10. Przewiń w dół, a następnie rozwiń węzeł **zaawansowane witryny hive**.
11. Wyszukaj **hive.metastore.client.socket.timeout** w sekcji.

Niektóre przykłady więcej o dostosowywaniu inne pliki konfiguracji:

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
Aby uzyskać więcej informacji, zobacz blog Azim Uddin zatytułowany [tworzenia klastra usługi HDInsight dostosowywanie](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).

## <a name="use-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET
Zobacz [opartych na systemie Linux z tworzenia klastrów w usłudze HDInsight przy użyciu zestawu .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Użyj Menedżera zasobów szablonu
Bootstrap można użyć w szablonie usługi Resource Manager:

```json
"configurations": {
    …
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![HDInsight Hadoop dostosowuje klastra ładowania początkowego szablonu usługi Azure Resource Manager](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Zobacz także
* [Tworzenie klastrów Hadoop w usłudze HDInsight] [ hdinsight-provision-cluster] zawiera instrukcje dotyczące sposobu tworzenia klastra usługi HDInsight przy użyciu niestandardowych opcji.
* [Tworzenie skryptów akcji skryptu dla usługi HDInsight][hdinsight-write-script]
* [Zainstalować i używać platformy Spark w usłudze hdinsight][hdinsight-install-spark]
* [Zainstalować i używać Solr w klastrach HDInsight](hdinsight-hadoop-solr-install.md).
* [Zainstalować i używać Giraph w klastrach HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Etapy podczas tworzenia klastra"

## <a name="appendix-powershell-sample"></a>Dodatku: Przykładowe PowerShell
Ten skrypt programu PowerShell tworzy klaster usługi HDInsight i dostosowuje ustawienie gałęzi:

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>" 
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"
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

$location = "East US 2"
#endregion

# Treat all errors as terminating
$ErrorActionPreference = "Stop"

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Connect-AzureRmAccount}
#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzureRmResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -Type Standard_GRS

$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
$defaultStorageContext = New-AzureStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey
New-AzureStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzureRmHDInsightClusterConfig `
    | Set-AzureRmHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzureRmHDInsightConfigValues `
        -HiveSite $hiveConfigValues 

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzureRmHDInsightCluster `
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
Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

#endregion
```
