---
title: Należy użyć programu PowerShell i MapReduce z Hadoop — usługa Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zdalne uruchamianie zadań MapReduce z Hadoop w usłudze HDInsight przy użyciu programu PowerShell.
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
ms.date: 02/16/2018
ms.author: larryfr
ms.openlocfilehash: b6e68b4c93e6385367b5aab01bebe1d29b7b9d13
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Uruchamianie zadań MapReduce z Hadoop w usłudze HDInsight przy użyciu programu PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Ten dokument zawiera przykładowy uruchomienie zadania MapReduce w usługi Hadoop w klastrze usługi HDInsight przy użyciu programu Azure PowerShell.

## <a id="prereq"></a>Wymagania wstępne

* **Klaster usługi Azure HDInsight (Hadoop w usłudze HDInsight)**

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* **Stacja robocza z programem Azure PowerShell**.

## <a id="powershell"></a>Uruchomienie zadania MapReduce

Udostępnia program Azure PowerShell *poleceń cmdlet* umożliwiającą zdalne uruchamianie zadań MapReduce w usłudze HDInsight. Wewnętrznie PowerShell wywołań REST do [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (nazywanych Templeton) uruchomionego w klastrze usługi HDInsight.

Następujące polecenia cmdlet są używane podczas uruchamiania zadań MapReduce w zdalnym klastrze usługi HDInsight.

* **Login-AzureRmAccount**: uwierzytelnianie programu Azure PowerShell do subskrypcji platformy Azure.

* **Nowy AzureRmHDInsightMapReduceJobDefinition**: tworzy nowy *definicji zadania* przy użyciu określonych informacji MapReduce.

* **Start-AzureRmHDInsightJob**: wysyła definicji zadania w usłudze HDInsight i uruchamiania zadania. A *zadania* obiekt jest zwracany.

* **Czekaj-AzureRmHDInsightJob**: używa obiektu zadania, aby sprawdzić stan zadania. Oczekuje, aż do ukończenia zadania lub przekroczono czas oczekiwania.

* **Get-AzureRmHDInsightJobOutput**: służy do pobierania danych wyjściowych zadania.

Poniższe kroki pokazują, jak używać tych poleceń cmdlet, aby uruchomić zadanie w klastrze usługi HDInsight.

1. Za pomocą edytora, Zapisz poniższy kod jako **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Otwórz nowe **programu Azure PowerShell** wiersza polecenia. Zmień katalogi na lokalizację **mapreducejob.ps1** pliku, a następnie użyj następującego polecenia, aby uruchomić skrypt:

        .\mapreducejob.ps1

    Po uruchomieniu skryptu, monit o nazwę klastra usługi HDInsight i logowania do klastra. Może też pojawić się prośba do uwierzytelniania do subskrypcji platformy Azure.

3. Po zakończeniu zadania, pojawi się dane wyjściowe podobne do następującego tekstu:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Te dane wyjściowe wskazuje, czy zadanie zakończone pomyślnie.

    > [!NOTE]
    > Jeśli **ExitCode** jest wartością innego niż 0, zobacz [Rozwiązywanie problemów](#troubleshooting).

    W tym przykładzie przechowuje także pliki pobrane do **output.txt** pliku w katalogu, w którym należy uruchomić skrypt.

### <a name="view-output"></a>Widok danych wyjściowych

Aby zobaczyć słów i liczby utworzonej przez zadanie, otwórz **output.txt** plik w edytorze tekstów.

> [!NOTE]
> Pliki wyjściowe zadania MapReduce są niezmienne. Dlatego jeśli uruchomisz tego przykładu musisz zmienić nazwę pliku wyjściowego.

## <a id="troubleshooting"></a>Rozwiązywanie problemów

Jeśli nie informacje są zwracane po zakończeniu zadania, wyświetlać błędy, zadania. Aby wyświetlić informacje o błędzie dla tego zadania, należy dodać następujące polecenie na końcu **mapreducejob.ps1** , zapisz go, a następnie uruchomić.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

To polecenie cmdlet zwraca informacje, które zostało zapisane STDERR jako zadanie działa.

## <a id="summary"></a>Podsumowanie

Jak widać, programu Azure PowerShell zapewnia prosty sposób uruchamiania zadań MapReduce na klaster usługi HDInsight, monitorować stan zadania i pobrać dane wyjściowe.

## <a id="nextsteps"></a>Następne kroki

Aby uzyskać ogólne informacje na temat zadań MapReduce w usłudze HDInsight:

* [Korzystać z usługi MapReduce na HDInsight Hadoop](hdinsight-use-mapreduce.md)

Aby uzyskać informacje o innych metodach pracy z platformą Hadoop w usłudze HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w usłudze HDInsight](hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight](hdinsight-use-pig.md)
