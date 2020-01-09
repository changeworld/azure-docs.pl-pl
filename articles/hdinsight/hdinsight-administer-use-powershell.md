---
title: Zarządzanie klastrami Apache Hadoop przy użyciu programu PowerShell — Azure HDInsight
description: Dowiedz się, jak wykonywać zadania administracyjne dla klastrów Apache Hadoop w usłudze HDInsight przy użyciu Azure PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: e37571b0078b4966aab9f505ddf88c2edb353197
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435625"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell może służyć do kontrolowania i automatyzowania wdrażania obciążeń i zarządzania nimi na platformie Azure. W tym artykule dowiesz się, jak zarządzać klastrami [Apache Hadoop](https://hadoop.apache.org/) w usłudze Azure HDInsight przy użyciu Azure PowerShell AZ module. Listę poleceń cmdlet programu PowerShell dla usługi HDInsight można znaleźć w [dokumentacji AZ. HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Program PowerShell [AZ module](https://docs.microsoft.com/powershell/azure/overview) został zainstalowany.

## <a name="create-clusters"></a>Tworzenie klastrów

Zobacz [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Wyświetl listę klastrów

Użyj następującego polecenia, aby wyświetlić listę wszystkich klastrów w bieżącej subskrypcji:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Pokaż klaster

Użyj następującego polecenia, aby wyświetlić szczegóły określonego klastra w bieżącej subskrypcji:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Usuwanie klastrów

Aby usunąć klaster, użyj następującego polecenia:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

Możesz również usunąć klaster, usuwając grupę zasobów zawierającą klaster. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów z grupy, łącznie z domyślnym kontem magazynu.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Skalowanie klastrów

Funkcja skalowania klastra pozwala zmienić liczbę węzłów procesu roboczego używanych przez klaster, który działa w usłudze Azure HDInsight bez konieczności ponownego tworzenia klastra. Aby zmienić rozmiar klastra Hadoop przy użyciu Azure PowerShell, uruchom następujące polecenie z komputera klienckiego:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 Aby uzyskać więcej informacji na temat skalowania klastrów, zobacz [skalowanie klastrów usługi HDInsight](./hdinsight-scaling-best-practices.md).

## <a name="grantrevoke-access"></a>Udzielanie/odwoływanie dostępu

Klastry HDInsight mają następujące usługi sieci Web HTTP (wszystkie te usługi mają RESTful punkty końcowe):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Domyślnie te usługi są przyznawane na potrzeby dostępu. Możesz odwołać/udzielić dostępu. Aby odwołać:

```powershell
Revoke-AzHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

Aby udzielić:

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = '<Enter the Password>'
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]  
> Przez przyznanie/cofnięcie dostępu, należy zresetować nazwę użytkownika i hasło do klastra.

Udzielanie i cofanie dostępu można także wykonać za pośrednictwem portalu. Zobacz [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu Azure Portal](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Aktualizowanie poświadczeń użytkownika HTTP

Jest to taka sama procedura jak przydzielenie/odwoływanie dostępu HTTP. Jeśli do klastra udzielono dostępu przy użyciu protokołu HTTP, należy najpierw go odwołać.  A następnie Udziel dostępu przy użyciu nowych poświadczeń użytkownika HTTP.

## <a name="find-the-default-storage-account"></a>Znajdowanie domyślnego konta magazynu

Poniższy skrypt programu PowerShell pokazuje, jak uzyskać domyślną nazwę konta magazynu i powiązane informacje:

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```

## <a name="find-the-resource-group"></a>Znajdowanie grupy zasobów

W trybie Menedżer zasobów każdy klaster usługi HDInsight należy do grupy zasobów platformy Azure.  Aby znaleźć grupę zasobów:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>Prześlij zadania

**Aby przesłać zadania MapReduce**

Zobacz [Uruchamianie przykładów MapReduce zawartych w usłudze HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Aby przesłać Apache Hive zadania**

Zobacz [uruchamianie Apache Hive zapytań przy użyciu programu PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Aby przesłać zadania Apache Sqoop**

Zobacz [Korzystanie z platformy Apache Sqoop z usługą HDInsight](hadoop/hdinsight-use-sqoop.md).

**Aby przesłać zadania Apache Oozie**

[Aby zdefiniować i uruchomić przepływ pracy w usłudze HDInsight, zobacz temat Korzystanie z platformy Apache Oozie z usługą Apache Hadoop](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Przekazywanie danych do usługi Azure Blob Storage

Zobacz [przekazywanie danych do usługi HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Zobacz też

* [Polecenia cmdlet AZ. HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight/?view=azps-3.1.0#hdinsight)
* [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu Azure Portal](hdinsight-administer-use-portal-linux.md)
* [Administrowanie usługą HDInsight przy użyciu interfejsu wiersza polecenia](hdinsight-administer-use-command-line.md)
* [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Programowe przesyłanie Apache Hadoop zadań](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
