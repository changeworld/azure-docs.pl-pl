---
title: Zarządzanie klastrami Hadoop w HDInsight przy użyciu programu PowerShell - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonywać zadania administracyjne dla klastrów Hadoop w usłudze HDInsight przy użyciu programu Azure PowerShell.
services: hdinsight
editor: cgronlun
manager: jhubbard
tags: azure-portal
author: mumian
documentationcenter: ''
ms.assetid: bfdfa754-18e5-4ef9-b0d6-2dbdcebc0283
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: jgao
ms.openlocfilehash: 7d2f68773c78eed001f14ca86a46fb9e18563d77
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Zarządzanie klastrami Hadoop w usłudze HDInsight przy użyciu programu Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Program Azure PowerShell umożliwia sterowanie oraz jej automatyzację wdrażania i zarządzania obciążeń na platformie Azure. W tym artykule Dowiedz się jak zarządzać klastrów platformy Hadoop w usłudze Azure HDInsight przy użyciu programu Azure PowerShell. Aby uzyskać listę poleceń cmdlet programu PowerShell usługi HDInsight, zobacz [dokumentacji poleceń cmdlet usługi HDInsight][hdinsight-powershell-reference].

**Wymagania wstępne**

Przed rozpoczęciem tego artykułu, musi mieć następujące elementy:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="install-azure-powershell"></a>Instalowanie programu Azure PowerShell
[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Jeśli zainstalowano program Azure PowerShell w wersji 0,9 x, należy go odinstalować przed zainstalowaniem nowszej wersji.

Aby sprawdzić wersję zainstalowanego środowiska PowerShell:

```powershell
Get-Module *azure*
```

Aby odinstalować starszej wersji, należy uruchomić programy i funkcje w Panelu sterowania.

## <a name="create-clusters"></a>Tworzenie klastrów
Zobacz [opartych na systemie Linux z tworzenia klastrów w usłudze HDInsight przy użyciu programu Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Lista klastrów
Aby wyświetlić listę wszystkich klastrów w bieżącej subskrypcji, użyj następującego polecenia:

```powershell
Get-AzureRmHDInsightCluster
```

## <a name="show-cluster"></a>Pokaż klastra
Zawiera szczegółowe informacje dotyczące konkretnego klastra w bieżącej subskrypcji, użyj następującego polecenia:

```powershell
Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Usuwanie klastrów
Aby usunąć klaster, użyj następującego polecenia:

```powershell
Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>
```

Można również usunąć klaster przez usunięcie grupy zasobów zawierającej klaster. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów w grupie, w tym domyślne konto magazynu.

```powershell
Remove-AzureRmResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Skalowanie klastrów
Skalowanie funkcji klastra umożliwia zmianę liczby węzłów procesu roboczego używane przez klaster, który jest uruchomiony w usłudze Azure HDInsight bez konieczności ponownego tworzenia klastra.

> [!NOTE]
> Tylko klastry usługi HDInsight w wersji 3.1.3 lub nowszym są obsługiwane. Jeśli masz pewności, jaka wersja klastra, można sprawdzić na stronie właściwości.  Zobacz [klastrów listy i Pokaż](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
>
>

Wpływ zmianę liczby węzłów danych dla każdego typu obsługiwanych przez HDInsight klastra:

* Hadoop

    Można bezproblemowo zwiększyć liczbę węzłów procesu roboczego w klastrze platformy Hadoop, który jest uruchomiony bez wpływu na wszystkie oczekujące lub uruchomione zadania. Również można przesłać nowe zadania, gdy operacja jest w toku. Błędy w operacji skalowania bezpiecznie obsługi tak, aby zawsze pozostanie w stanie funkcjonalności klastra.

    Podczas skalowania klastra usługi Hadoop w dół dzięki zmniejszeniu liczby węzłów danych są ponownie uruchamiane niektóre z tych usług w klastrze. Ponowne uruchamianie usługi powoduje, że wszystkie uruchomione i oczekujące zadania się niepowodzeniem po zakończeniu operacji skalowania. Można jednak Prześlij ponownie zadania po zakończeniu operacji.
* HBase

    Można bezproblemowo dodać lub usunąć węzły do klastra HBase jest uruchomiona. Serwery regionalne automatycznie równoważy w ciągu kilku minut od zakończenia operacji skalowania. Można jednak również ręcznie saldo serwery regionalne po zalogowaniu się do headnode klastra, a następnie uruchom następujące polecenia z poziomu okna wiersza polecenia:

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Storm

    Bezproblemowo można dodawać i usuwać dane węzły do klastra Storm, jest uruchomiona. Jednak po pomyślnym zakończeniu operacji skalowania, konieczne będzie ponowne zrównoważenie topologii.

    Ponowne równoważenie można zrobić na dwa sposoby:

  * STORM interfejsu użytkownika sieci web
  * Narzędzia interfejsu wiersza polecenia (CLI)

    Zapoznaj się [dokumentację Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) więcej szczegółów.

    Interfejs użytkownika sieci web Storm jest dostępna w klastrze usługi HDInsight:

    ![Zrównoważ skali storm w usłudze HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

    Oto przykład sposobu użycia polecenia interfejsu wiersza polecenia do ponowne zrównoważenie topologii Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Aby zmienić rozmiar klastra usługi Hadoop przy użyciu programu Azure PowerShell, uruchom następujące polecenie na komputerze klienckim:

```powershell
Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


## <a name="grantrevoke-access"></a>Dostęp do przydzielenia/odwołania
Klastry HDInsight są następujące usługi sieci web HTTP (wszystkie te usługi mają RESTful punkty końcowe):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Domyślnie te usługi są przyznawane dostępu. Możesz można odwołać/udzielenie dostępu. Aby można było odwołać:

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
> Przez przyznawanie/odwoływanie dostępu, można zresetować klastra, nazwa użytkownika i hasło.
>
>

Udzielanie i odwoływanie dostępu również mogą być przeprowadzane za pośrednictwem portalu. Zobacz [administrowania HDInsight przy użyciu portalu Azure][hdinsight-admin-portal].

## <a name="update-http-user-credentials"></a>Zaktualizuj poświadczenia użytkownika HTTP
Jest tej samej procedury co [dostępu Grant/revoke HTTP](#grant/revoke-access). Jeśli klaster przyznano dostęp HTTP, należy je najpierw odwołać.  A następnie przyznać dostęp z nowymi poświadczeniami użytkownika HTTP.

## <a name="find-the-default-storage-account"></a>Znajdź domyślne konto magazynu
Poniższy skrypt programu PowerShell pokazano, jak pobrać domyślna nazwa konta magazynu oraz powiązane informacje:

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
W trybie Menedżera zasobów poszczególnych klastrów HDInsight należy do grupy zasobów platformy Azure.  Aby znaleźć grupy zasobów:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```


## <a name="submit-jobs"></a>Przesyłanie zadań
**Aby przesłać zadania MapReduce**

Zobacz [próbek Uruchom MapReduce z Hadoop w HDInsight opartych na systemie Windows](hdinsight-run-samples.md).

**Do przesyłania zadań Hive**

Zobacz [uruchamianie zapytań Hive przy użyciu programu PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Do przesyłania zadań Pig**

Zobacz [Pig uruchomienie zadania przy użyciu programu PowerShell](hadoop/apache-hadoop-use-pig-powershell.md).

**Do przesyłania zadań Sqoop**

Zobacz [Sqoop za pomocą usługi HDInsight](hadoop/hdinsight-use-sqoop.md).

**Do przesyłania zadań Oozie**

Zobacz [Oozie użycia z usługą Hadoop do definiowania i uruchomić przepływ pracy w usłudze HDInsight](hdinsight-use-oozie.md).

## <a name="upload-data-to-azure-blob-storage"></a>Przekazywanie danych do magazynu obiektów Blob platformy Azure
Zobacz [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Zobacz też
* [Dokumentację referencyjną polecenia cmdlet usługi HDInsight][hdinsight-powershell-reference]
* [Administrowanie HDInsight przy użyciu portalu Azure][hdinsight-admin-portal]
* [Administrowanie HDInsight przy użyciu interfejsu wiersza polecenia][hdinsight-admin-cli]
* [Tworzenie klastrów usługi HDInsight][hdinsight-provision]
* [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data]
* [Przesyłanie zadań Hadoop programowo][hdinsight-submit-jobs]
* [Wprowadzenie do usługi Azure HDInsight][hdinsight-get-started]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

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
