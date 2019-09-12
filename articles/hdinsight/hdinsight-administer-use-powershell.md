---
title: Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu programu PowerShell — Azure
description: Dowiedz się, jak wykonywać zadania administracyjne dla klastrów Apache Hadoop w usłudze HDInsight przy użyciu Azure PowerShell.
ms.reviewer: tyfox
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: tyfox
ms.openlocfilehash: 3f4ccd8de1f26ea898b0e7ec4bb57aa20b1be209
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885343"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell może służyć do kontrolowania i automatyzowania wdrażania obciążeń i zarządzania nimi na platformie Azure. W tym artykule dowiesz się, jak zarządzać klastrami [Apache Hadoop](https://hadoop.apache.org/) w usłudze Azure HDInsight przy użyciu Azure PowerShell AZ module. Listę poleceń cmdlet programu PowerShell dla usługi HDInsight można znaleźć w [dokumentacji AZ. HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Program PowerShell [AZ module](https://docs.microsoft.com/powershell/azure/overview) został zainstalowany.

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
Funkcja skalowania klastra pozwala zmienić liczbę węzłów procesu roboczego używanych przez klaster, który działa w usłudze Azure HDInsight bez konieczności ponownego tworzenia klastra.

Wpływ zmiany liczby węzłów danych dla każdego typu klastra obsługiwanego przez usługi HDInsight:

* Apache Hadoop

    Można bezproblemowo zwiększyć liczbę węzłów procesu roboczego w klastrze Hadoop uruchomionym bez wpływu na zadania oczekujące lub uruchomione. Nowe zadania mogą być również przesyłane, gdy operacja jest w toku. Błędy w operacji skalowania są bezpiecznie obsługiwane, aby klaster zawsze pozostawał w stanie funkcjonalności.

    Po skalowaniu klastra Hadoop przez zmniejszenie liczby węzłów danych, niektóre usługi w klastrze są ponownie uruchamiane. Ponowne uruchomienie usług powoduje niepowodzenie wszystkich uruchomionych i oczekujących zadań po zakończeniu operacji skalowania. Można jednak ponownie przesłać zadania po zakończeniu operacji.
* Apache HBase

    Można bezproblemowo dodawać lub usuwać węzły do klastra HBase, gdy jest on uruchomiony. Serwery regionalne są automatycznie równoważone w ciągu kilku minut od zakończenia operacji skalowania. Można również ręcznie zrównoważyć serwery regionalne, logując się do węzła głównego klastra, a następnie uruchamiając następujące polecenia w oknie wiersza polecenia:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

* Apache Storm

    Można bezproblemowo dodawać lub usuwać węzły danych do klastra burzy, gdy jest on uruchomiony. Ale po pomyślnym zakończeniu operacji skalowania należy ponownie zrównoważyć topologię.

    Ponowne równoważenie można wykonać na dwa sposoby:

  * Interfejs użytkownika sieci Web burzy
  * Narzędzie interfejsu wiersza polecenia (CLI)

    Więcej informacji można znaleźć w [dokumentacji Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) .

    Interfejs użytkownika sieci Web burzy jest dostępny w klastrze usługi HDInsight:

    ![Ponowne równoważenie skali burzy usługi HDInsight](./media/hdinsight-administer-use-powershell/portal-scale-cluster.png)

    Oto przykład sposobu użycia interfejsu wiersza polecenia w celu ponownego zrównoważenia topologii burzy:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Aby zmienić rozmiar klastra Hadoop przy użyciu Azure PowerShell, uruchom następujące polecenie z komputera klienckiego:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


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
$hadoopUserPassword = "<Enter the Password>"
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
Ta sama procedura jest taka sama jak w przypadku dostępu do protokołu HTTP Grant/Revoke. Jeśli do klastra udzielono dostępu przy użyciu protokołu HTTP, należy najpierw go odwołać.  A następnie Udziel dostępu przy użyciu nowych poświadczeń użytkownika HTTP.

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

* [Dokumentacja dotycząca poleceń cmdlet usługi HDInsight](https://msdn.microsoft.com/library/azure/dn479228.aspx)
* [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu Azure Portal](hdinsight-administer-use-portal-linux.md)
* [Administrowanie usługą HDInsight przy użyciu interfejsu wiersza polecenia](hdinsight-administer-use-command-line.md)
* [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Programowe przesyłanie Apache Hadoop zadań](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
