---
title: Use Apache Hive przy użyciu programu PowerShell w HDInsight — Azure
description: Uruchamianie zapytań programu Hive na platformie Apache Hadoop w HDInsight przy użyciu programu PowerShell.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 04/23/2018
ms.date: 04/15/2019
ms.author: v-yiso
ms.openlocfilehash: 108a3e7d899eef4ca78ae7507bf4852b861e74d5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095527"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Uruchamianie zapytania usługi Apache Hive przy użyciu programu PowerShell
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ten dokument zawiera przykład przy użyciu programu Azure PowerShell w trybie grupy zasobów platformy Azure do uruchamiania zapytań programu Hive na platformie Apache Hadoop w klastrze HDInsight.

> [!NOTE]  
> Ten dokument zawiera szczegółowy opis działania instrukcje HiveQL, które są używane w przykładach. Informacje na temat HiveQL, który jest używany w tym przykładzie można zobaczyć [używanie programu Apache Hive przy użyciu technologii Apache Hadoop w HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Linux Apache Hadoop oparte na wersji klastra HDInsight 3.4 lub nowszą.

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Klient z programem Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive

Azure PowerShell udostępnia *poleceń cmdlet* umożliwiającą zdalne uruchamianie zapytań programu Hive na HDInsight. Wewnętrznie, poleceń cmdlet wykonywać wywołania REST [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) w klastrze HDInsight.

Następujące polecenia cmdlet są używane podczas uruchamiania zapytań programu Hive w zdalnym klastrze HDInsight:

* `Connect-AzAccount`: Uwierzytelnianie programu Azure PowerShell do subskrypcji platformy Azure.
* `New-AzHDInsightHiveJobDefinition`: Tworzy *definicji zadania* przy użyciu określonego instrukcje HiveQL.
* `Start-AzHDInsightJob`: Wysyła definicji zadania do HDInsight i uruchamia zadanie. A *zadania* obiekt jest zwracany.
* `Wait-AzHDInsightJob`: Używa obiektu zadania, aby sprawdzić stan zadania. Oczekuje, aż do ukończenia zadania lub przekroczenia czasu oczekiwania.
* `Get-AzHDInsightJobOutput`: Używany do pobierania danych wyjściowych zadania.
* `Invoke-AzHDInsightHiveJob`: Używane do uruchamiania instrukcje HiveQL. To polecenie cmdlet bloki zapytania kończy, a następnie zwraca wyniki.
* `Use-AzHDInsightCluster`: Ustawia bieżący klaster na potrzeby `Invoke-AzHDInsightHiveJob` polecenia.

Poniższe kroki pokazują, jak używać tych poleceń cmdlet do uruchamiania zadań w klastrze usługi HDInsight:

1. Za pomocą edytora, Zapisz poniższy kod jako `hivejob.ps1`.

    ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount -EnvironmentName AzureChinaCloud
    }

    #Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"

    #HiveQL
    #Note: set hive.execution.engine=tez; is not required for
    #      Linux-based HDInsight
    $queryString = "set hive.execution.engine=tez;" +
                "DROP TABLE log4jLogs;" +
                "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

    #Create an HDInsight Hive job definition
    $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

    #Submit the job to the cluster
    Write-Host "Start the Hive job..." -ForegroundColor Green

    $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds

    #Wait for the Hive job to complete
    Write-Host "Wait for the job to complete..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

    # Print the output
    Write-Host "Display the standard output..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $hiveJob.JobId `
        -HttpCredential $creds
    ```

2. Otwórz nowy **programu Azure PowerShell** wiersza polecenia. Zmień katalogi na lokalizację `hivejob.ps1` pliku, a następnie użyj następującego polecenia, aby uruchomić skrypt:

        .\hivejob.ps1

    Po uruchomieniu skryptu monit wprowadź nazwę klastra oraz poświadczenia konta administratora HTTPS i klastra. Użytkownik może również monit Zaloguj się do subskrypcji platformy Azure.

3. Po zakończeniu zadania, zwraca informacje podobne do następującego tekstu:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Jak wspomniano wcześniej, `Invoke-Hive` może służyć do uruchamiania zapytania i poczekać na odpowiedź. Użyj następującego skryptu, aby zobaczyć, jak działa Invoke-Hive:

    ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount -EnvironmentName AzureChinaCloud
    }

    #Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"

    # Set the cluster to use
    Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds

    $queryString = "set hive.execution.engine=tez;" +
                "DROP TABLE log4jLogs;" +
                "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/';" +
                "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
    Invoke-AzureRmHDInsightHiveJob `
        -StatusFolder "statusout" `
        -Query $queryString
    ```

    Dane wyjściowe wyglądają podobnie jak następujący tekst:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > W przypadku dłużej zapytań HiveQL, można użyć programu Azure PowerShell **ciągi tutaj** polecenia cmdlet lub HiveQL pliki skryptów. Poniższy fragment kodu przedstawia sposób użycia `Invoke-Hive` polecenia cmdlet do uruchamiania pliku skryptu HiveQL. Plik skryptu HiveQL muszą być przesłane do wasb: / /.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Aby uzyskać więcej informacji na temat **ciągi tutaj**, zobacz <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">za pomocą Windows PowerShell tutaj ciągi</a>.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli nie informacje są zwracane po zakończeniu zadania, wyświetlać dzienniki błędów. Aby wyświetlić informacje o błędzie dla tego zadania, Dodaj następujący element do końca `hivejob.ps1` pliku, zapisz go i uruchom go ponownie.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

To polecenie cmdlet zwraca informacje, które są zapisywane do strumienia wyjściowego STDERR podczas przetwarzania zadania.

## <a name="summary"></a>Podsumowanie

Jak widać, programu Azure PowerShell umożliwia łatwe uruchamianie zapytań programu Hive w klastrze usługi HDInsight, monitorować stan zadania i pobieranie danych wyjściowych.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać ogólne informacje na temat programu Hive w HDInsight:

* [Apache Hive za pomocą technologii Apache Hadoop w HDInsight](hdinsight-use-hive.md)

Aby uzyskać informacje o innych metodach można pracować z platformą Hadoop w HDInsight:

* [Use Apache Pig z platformą Apache Hadoop w HDInsight](hdinsight-use-pig.md)
* [Korzystanie z technologii MapReduce z platformą Apache Hadoop w HDInsight](hdinsight-use-mapreduce.md)
