---
title: Zarządzanie klastrami Apache Hadoop w HDInsight przy użyciu programu PowerShell — platformy Azure
description: Dowiedz się, jak wykonywać zadania administracyjne dla klastrów Apache Hadoop w HDInsight przy użyciu programu Azure PowerShell.
services: hdinsight
ms.reviewer: tyfox
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/6/2018
ms.author: tylerfox
ms.openlocfilehash: 4177320285f62397a2f512a5ecaf0dc333065545
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53011593"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Zarządzanie klastrami Apache Hadoop w HDInsight przy użyciu programu Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Program Azure PowerShell może służyć do kontrolowania i zautomatyzować wdrożenie i zarządzanie obciążeń na platformie Azure. W tym artykule dowiesz się, jak zarządzać [Apache Hadoop](https://hadoop.apache.org/) klastrów w usłudze Azure HDInsight przy użyciu programu Azure PowerShell. Aby uzyskać listę poleceń cmdlet programu HDInsight PowerShell, zobacz [Dokumentacja poleceń cmdlet HDInsight](https://msdn.microsoft.com/library/azure/dn479228.aspx).

**Wymagania wstępne**

Przed przystąpieniem do wykonywania w tym artykule, musi mieć następujące elementy:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="install-azure-powershell"></a>Instalowanie programu Azure PowerShell
[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Jeśli masz zainstalowanego programu Azure PowerShell w wersji 0.9 x, należy go odinstalować przed zainstalowaniem nowszej wersji.

Aby sprawdzić wersję zainstalowanego programu PowerShell:

```powershell
Get-Module *azure*
```

Aby odinstalować starszej wersji, uruchom programy i funkcje w Panelu sterowania.

## <a name="create-clusters"></a>Tworzenie klastrów
Zobacz [opartych na systemie Linux z Tworzenie klastrów w HDInsight przy użyciu programu Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Wyświetlanie listy klastrów
Aby wyświetlić listę wszystkich klastrów w bieżącej subskrypcji, użyj następującego polecenia:

```powershell
Get-AzureRmHDInsightCluster
```

## <a name="show-cluster"></a>Pokaż klastra
Aby wyświetlić szczegóły dotyczące określonego klastra w bieżącej subskrypcji, użyj następującego polecenia:

```powershell
Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Usuwanie klastrów
Aby usunąć klaster, użyj następującego polecenia:

```powershell
Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>
```

Można również usunąć klaster, usuwając grupę zasobów zawierającą klaster. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów w grupie, w tym domyślne konto magazynu.

```powershell
Remove-AzureRmResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Skalowanie klastrów
Skalowanie funkcji klastra umożliwia zmianę liczby węzłów procesu roboczego używany przez klaster, który jest uruchamiany w usłudze Azure HDInsight bez konieczności ponownego tworzenia klastra.

> [!NOTE]
> Tylko klastry HDInsight w wersji 3.1.3 lub nowszej są obsługiwane. Jeśli masz pewności, jaka wersja klastra, możesz sprawdzić na stronie właściwości.  Zobacz [listy i wyświetlaniu klastrów](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
>
>

Wpływ zmianę liczby węzłów danych dla każdego typu klastra obsługiwane przez HDInsight:

* Apache Hadoop

    Możesz bezproblemowo zwiększyć liczbę węzłów procesu roboczego w klastrze usługi Hadoop, w którym jest uruchomiony bez wywierania wpływu na wszystkie oczekujące lub uruchomione zadania. Nowe zadania należy dostarczyć również w przypadku, gdy operacja jest w toku. Błędy trwaniem skalowania bez problemu zmieniała są obsługiwane, dzięki czemu klaster zawsze pozostanie w stanie działać.

    Gdy klaster Hadoop jest skalowane w dół dzięki zmniejszeniu liczby węzłów danych, zostaną ponownie uruchomione niektóre z tych usług w klastrze. Ponowne uruchamianie usług powoduje, że wszystkie uruchomione i oczekujące zadania na zakończenie operacji skalowania. Można jednak ponownie przesłać zadania po zakończeniu operacji.
* Apache HBase

    Możesz bezproblemowo Dodawanie lub usuwanie węzłów do klastra HBase jest uruchomiona. Serwery regionalne automatycznie są równoważone w ciągu kilku minut od zakończenia operacji skalowania. Można jednak również ręcznie równoważyć serwerów regionalnych, logując się do węzła głównego klastra i następnie uruchom następujące polecenia z okna wiersza polecenia:

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm

    Bezproblemowo można dodać lub usunąć węzły danych z klastrem Storm, jest uruchomiona. Jednak po pomyślnym zakończeniu operacji skalowania, konieczne będzie ponowne zrównoważenie topologii.

    Ponowne równoważenie może się odbywać na dwa sposoby:

  * Interfejs użytkownika sieci web systemu STORM
  * Narzędzia interfejsu wiersza polecenia (CLI)

    Zapoznaj się [dokumentacji platformy Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) Aby uzyskać więcej informacji.

    Interfejs użytkownika sieci web systemu Storm jest dostępny w klastrze HDInsight:

    ![HDInsight storm skalowania w ponownego równoważenia](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

    Oto przykład jak ponowne zrównoważenie topologii Storm za pomocą polecenia interfejsu wiersza polecenia:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Aby zmienić rozmiar klastra usługi Hadoop przy użyciu programu Azure PowerShell, uruchom następujące polecenie z komputera klienckiego:

```powershell
Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


## <a name="grantrevoke-access"></a>Dostęp do przydzielenia/odwołania
Klastry HDInsight mają następujące usługi sieci web HTTP (wszystkie te usługi mają punktów końcowych RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Domyślnie te usługi są przyznawane dostępu. Możesz można odwołać/Udziel dostępu. Aby można było odwołać:

```powershell
Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>
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

Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]
> Przez przyznanie/odbieranie prawa dostępu, możesz zresetować klastra, nazwę użytkownika i hasło.
>
>

Przyznanie i odbieranie prawa dostępu, jest możliwe również przy użyciu portalu. Zobacz [administrowania HDInsight przy użyciu witryny Azure portal][hdinsight-admin-portal].

## <a name="update-http-user-credentials"></a>Aktualizowanie poświadczeń użytkownika HTTP
Jest tej samej procedurze co [dostępu Grant/revoke HTTP](#grant/revoke-access). W przypadku klastra przyznano dostęp HTTP, należy je najpierw odwołać.  A następnie przyznać dostęp z nowymi poświadczeniami użytkownika protokołu HTTP.

## <a name="find-the-default-storage-account"></a>Znajdź domyślne konto magazynu
Poniższy skrypt programu PowerShell pokazuje, jak domyślna nazwa konta magazynu i powiązane informacje:

```powershell
#Connect-AzureRmAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```


## <a name="find-the-resource-group"></a>Znajdź grupę zasobów
W trybie usługi Resource Manager każdy klaster HDInsight należy do grupy zasobów platformy Azure.  Aby znaleźć grupy zasobów:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```


## <a name="submit-jobs"></a>Przesyłanie zadań
**Aby przesłać zadania Apache Hadoop MapReduce**

Zobacz [uruchomić przykłady Apache Hadoop MapReduce zawarte w HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Aby przesłać zadania technologii Hive**

Zobacz [uruchamianie Apache zapytań Hive przy użyciu programu PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Aby przesłać zadania Apache Pig**

Zobacz [zadania uruchamiania Apache Pig, przy użyciu programu PowerShell](hadoop/apache-hadoop-use-pig-powershell.md).

**Do przesyłania zadań z wykorzystaniem narzędzia Apache Sqoop**

Zobacz [z HDInsight przy użyciu narzędzia Apache Sqoop](hadoop/hdinsight-use-sqoop.md).

**Aby przesłać zadania programu Apache Oozie**

Zobacz [Użyj Apache Oozie z usługą Apache Hadoop do definiowania i uruchomić przepływ pracy w HDInsight](hdinsight-use-oozie.md).

## <a name="upload-data-to-azure-blob-storage"></a>Przekazywanie danych do usługi Azure Blob storage
Zobacz [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Zobacz też
* [Dokumentacja poleceń cmdlet HDInsight](https://msdn.microsoft.com/library/azure/dn479228.aspx)
* [Administrowanie HDInsight za pomocą witryny Azure portal][hdinsight-admin-portal]
* [Administrowanie HDInsight przy użyciu interfejsu wiersza polecenia][hdinsight-admin-cli]
* [Tworzenie klastrów HDInsight][hdinsight-provision]
* [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data]
* [Przesyłanie zadań Apache Hadoop programowe][hdinsight-submit-jobs]
* [Wprowadzenie do usługi Azure HDInsight][hdinsight-get-started]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png
