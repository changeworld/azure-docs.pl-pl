---
title: Zarządzanie klastrami Apache Hadoop za pomocą programu PowerShell — Usługa Azure HDInsight
description: Dowiedz się, jak wykonywać zadania administracyjne dla klastrów Apache Hadoop w programie HDInsight przy użyciu programu Azure PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/13/2020
ms.openlocfilehash: 104975e6424ed96d43434a588997957033c31d93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560358"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Zarządzanie klastrami Apache Hadoop w programie HDInsight przy użyciu programu Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Program Azure PowerShell może służyć do kontrolowania i automatyzacji wdrażania i zarządzania obciążeniami na platformie Azure. W tym artykule dowiesz się, jak zarządzać klastrami [Apache Hadoop](https://hadoop.apache.org/) w usłudze Azure HDInsight przy użyciu modułu Azure PowerShell Az. Aby zapoznać się z listą poleceń cmdlet programu HDInsight PowerShell, zobacz [odwołanie Az.HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zainstalowano [moduł Az](https://docs.microsoft.com/powershell/azure/overview) programu PowerShell.

## <a name="create-clusters"></a>Tworzenie klastrów

Zobacz [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu programu Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Listy klastrów

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

Użyj następującego polecenia, aby usunąć klaster:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

Można również usunąć klaster, usuwając grupę zasobów zawierającą klaster. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów w grupie, w tym domyślnego konta magazynu.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Skalowanie klastrów

Funkcja skalowania klastra umożliwia zmianę liczby węzłów procesu roboczego używanych przez klaster uruchomiony w usłudze Azure HDInsight bez konieczności ponownego tworzenia klastra. Aby zmienić rozmiar klastra Hadoop przy użyciu programu Azure PowerShell, uruchom następujące polecenie z komputera klienckiego:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 Aby uzyskać więcej informacji na temat skalowania klastrów, zobacz [Skalowanie klastrów HDInsight](./hdinsight-scaling-best-practices.md).

## <a name="update-http-user-credentials"></a>Aktualizowanie poświadczeń użytkownika HTTP

[Set-AzHDInsightGatewayCredential](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightgatewaycredential) ustawia poświadczenia HTTP bramy klastra usługi Azure HDInsight.

```powershell
$clusterName = "CLUSTERNAME"
$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Set-AzHDInsightGatewayCredential -ClusterName $clusterName -HttpCredential $credential
```

## <a name="find-the-default-storage-account"></a>Znajdowanie domyślnego konta magazynu

Następujący skrypt programu PowerShell pokazuje, jak uzyskać domyślną nazwę konta magazynu i powiązane informacje:

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

W trybie Menedżera zasobów każdy klaster USŁUGI HDInsight należy do grupy zasobów platformy Azure.  Aby znaleźć grupę zasobów:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>Przesyłanie ofert pracy

**Aby przesłać zadania MapReduce**

Zobacz [Uruchamianie przykładów MapReduce zawartych w pliku HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Aby przesłać oferty pracy Apache Hive**

Zobacz [Uruchamianie zapytań gałęzi Apache przy użyciu programu PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Aby przesłać oferty pracy Apache Sqoop**

Zobacz [Korzystanie z Apache Sqoop z HDInsight](hadoop/hdinsight-use-sqoop.md).

**Aby przesłać oferty pracy Apache Oozie**

Zobacz [Używanie apache Oozie z Apache Hadoop do definiowania i uruchamiania przepływu pracy w pliku HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Przekazywanie danych do magazynu obiektów blob platformy Azure

Zobacz [Przekazywanie danych do usługi HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Zobacz też

* [Polecenia cmdlet Az.HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight/?view=azps-3.1.0#hdinsight)
* [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu portalu Azure](hdinsight-administer-use-portal-linux.md)
* [Administrowanie programem HDInsight za pomocą interfejsu wiersza polecenia](hdinsight-administer-use-command-line.md)
* [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Prześlij apache Hadoop oferty pracy programowo](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
