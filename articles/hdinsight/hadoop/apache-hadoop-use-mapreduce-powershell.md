---
title: Używanie MapReduce i programu PowerShell przy użyciu technologii Apache Hadoop — Azure HDInsight
description: Dowiedz się, jak i zdalne uruchamianie zadań MapReduce z usługą Apache Hadoop w HDInsight przy użyciu programu PowerShell.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21b56d32-1785-4d44-8ae8-94467c12cfba
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 05/09/2018
ms.date: 04/15/2019
ms.author: v-yiso
ms.openlocfilehash: 29e23d5919a953566c803f2b7825a75a2993723c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129029"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Wykonywanie zadań MapReduce z usługą Apache Hadoop w HDInsight przy użyciu programu PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]



Ten dokument zawiera przykład przy użyciu programu Azure PowerShell do uruchamiania zadania MapReduce na platformie Hadoop w klastrze HDInsight.

## <a id="prereq"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* **Klaster usługi Azure HDInsight (Hadoop w HDInsight)**

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* **Stacja robocza z programem Azure PowerShell**.

## <a id="powershell"></a>Uruchom zadanie MapReduce

Azure PowerShell udostępnia *poleceń cmdlet* umożliwiającą zdalne uruchamianie zadań MapReduce w HDInsight. Wewnętrznie, programu PowerShell sprawia, że wywołania REST do [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (dawniej nazywanych Templeton) działającej w klastrze HDInsight.

Następujące polecenia cmdlet są używane podczas uruchamiania zadań MapReduce w zdalnym klastrze HDInsight.

* **Połącz AzAccount**: Uwierzytelnianie programu Azure PowerShell do subskrypcji platformy Azure.

* **New-AzHDInsightMapReduceJobDefinition**: Tworzy nową *definicji zadania* przy użyciu określonej informacji o MapReduce.

* **Start-AzHDInsightJob**: Wysyła definicji zadania do HDInsight i uruchamia zadanie. A *zadania* obiekt jest zwracany.

* **Wait-AzHDInsightJob**: Używa obiektu zadania, aby sprawdzić stan zadania. Oczekuje, aż do ukończenia zadania lub przekroczenia czasu oczekiwania.

* **Get-AzHDInsightJobOutput**: Używany do pobierania danych wyjściowych zadania.

Poniższe kroki pokazują, jak używać tych poleceń cmdlet do uruchamiania zadań w klastrze usługi HDInsight.

1. Za pomocą edytora, Zapisz poniższy kod jako **mapreducejob.ps1**.

    ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount -EnvironmentName AzureChinaCloud
    }

    # Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"

    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    #NOTE: This assumes that the storage account is in the same resource
    #      group as the cluster. If it is not, change the
    #      --ResourceGroupName parameter to the group that contains storage.
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
    -ResourceGroupName $resourceGroup)[0].Value

    #Create a storage context
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey

    #Define the MapReduce job
    #NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
    # -JarFile = the JAR containing the MapReduce application
    # -ClassName = the class of the application
    # -Arguments = The input file, and the output directory
    $wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
        -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
        -ClassName "wordcount" `
        -Arguments `
            "/example/data/gutenberg/davinci.txt", `
            "/example/data/WordCountOutput"

    #Submit the job to the cluster
    Write-Host "Start the MapReduce job..." -ForegroundColor Green
    $wordCountJob = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $wordCountJobDefinition `
        -HttpCredential $creds

    #Wait for the job to complete
    Write-Host "Wait for the job to complete..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
        -Blob 'example/data/WordCountOutput/part-r-00000' `
        -Container $container `
        -Destination output.txt `
        -Context $context
    # Print the output of the job.
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds
    ```

2. Otwórz nowy **programu Azure PowerShell** wiersza polecenia. Zmień katalogi na lokalizację **mapreducejob.ps1** pliku, a następnie użyj następującego polecenia, aby uruchomić skrypt:

        .\mapreducejob.ps1

    Po uruchomieniu skryptu, wyświetlany jest monit o nazwę klastra HDInsight i logowania do klastra. Użytkownik może również się monit o uwierzytelnienie do subskrypcji platformy Azure.

3. Po zakończeniu zadania, otrzymasz dane wyjściowe podobne do następującego tekstu:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Te dane wyjściowe wskazuje, że zadanie zakończyło się powodzeniem.

    > [!NOTE]
    > Jeśli **ExitCode** jest wartością innego niż 0, zobacz [Rozwiązywanie problemów](#troubleshooting).

    W tym przykładzie przechowuje także pliki pobrane do **output.txt** pliku w katalogu, w którym należy uruchomić skrypt.

### <a name="view-output"></a>Wyświetlanie danych wyjściowych

Aby wyświetlić wyrazy i liczby utworzone przez zadanie, otwórz **output.txt** plik w edytorze tekstów.

> [!NOTE]
> Pliki wyjściowe zadania MapReduce są niezmienne. Dlatego jeśli uruchomisz tego przykładu, należy zmienić nazwę pliku wyjściowego.

## <a id="troubleshooting"></a>Rozwiązywanie problemów

Jeśli nie informacje są zwracane po zakończeniu zadania, należy przejrzeć błędy, zadania. Aby wyświetlić informacje o błędzie dla tego zadania, Dodaj następujące polecenie na końcu **mapreducejob.ps1** pliku, zapisz go i uruchom go ponownie.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

To polecenie cmdlet zwraca informacje, które zostały zapisane do strumienia wyjściowego STDERR po uruchomieniu zadania.

## <a id="summary"></a>Podsumowanie

Jak widać, programu Azure PowerShell umożliwia łatwe uruchamianie zadań MapReduce w klastrze usługi HDInsight, monitorować stan zadania i pobrać dane wyjściowe.

## <a id="nextsteps"></a>Następne kroki

Aby uzyskać ogólne informacje na temat zadań MapReduce w HDInsight:

* [Korzystanie z technologii MapReduce w usłudze HDInsight Hadoop](hdinsight-use-mapreduce.md)

Aby uzyskać informacje o innych metodach można pracować z platformą Hadoop w HDInsight:

* [Apache Hive za pomocą technologii Apache Hadoop w HDInsight](hdinsight-use-hive.md)
* [Use Apache Pig z platformą Apache Hadoop w HDInsight](hdinsight-use-pig.md)
