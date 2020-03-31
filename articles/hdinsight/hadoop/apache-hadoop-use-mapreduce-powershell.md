---
title: Korzystanie z map MapReduce i PowerShell z Apache Hadoop — Usługa Azure HDInsight
description: Dowiedz się, jak zdalnie uruchamiać zadania MapReduce za pomocą aplikacji Apache Hadoop w programie HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: b3c1abb7bff54e3e2d294b073b867c6c0e06f482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75830075"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Uruchamianie zadań MapReduce z Apache Hadoop na hdinsight za pomocą programu PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Ten dokument zawiera przykład użycia programu Azure PowerShell do uruchamiania zadania MapReduce w hadoop w klastrze HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Hadoop w programie HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu portalu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

* Zainstalowano [moduł Az](https://docs.microsoft.com/powershell/azure/overview) programu PowerShell.

## <a name="run-a-mapreduce-job"></a>Uruchamianie zadania MapReduce

Usługa Azure PowerShell udostępnia *polecenia cmdlet,* które umożliwiają zdalne uruchamianie zadań MapReduce w programie HDInsight. Wewnętrznie program PowerShell wywołuje REST do [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (dawniej nazywany Templeton) działającego w klastrze HDInsight.

Następujące polecenia cmdlet są używane podczas uruchamiania mapReduce zadań w zdalnym klastrze HDInsight.

|Polecenie cmdlet | Opis |
|---|---|
|Connect-AzAccount|Uwierzytelnia usługę Azure PowerShell do subskrypcji platformy Azure.|
|Nowa-AzHDInsightMapReduceJobDefinition|Tworzy nową *definicję zadania* przy użyciu określonych informacji MapReduce.|
|Start-AzHDInsightJob|Wysyła definicję zadania do hdinsight i uruchamia zadanie. Zwracany jest obiekt *zadania.*|
|Czekaj-AzHDInsightJob|Używa obiektu zadania do sprawdzania stanu zadania. Czeka, aż zadanie zostanie ukończone lub czas oczekiwania zostanie przekroczony.|
|Get-AzHDInsightJobOutput|Służy do pobierania danych wyjściowych zadania.|

W poniższych krokach pokazano, jak używać tych poleceń cmdlet do uruchamiania zadania w klastrze HDInsight.

1. Za pomocą edytora zapisz następujący kod jako **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Otwórz nowy wiersz polecenia **programu Azure PowerShell.** Zmień katalogi na lokalizację pliku **mapreducejob.ps1,** a następnie użyj następującego polecenia, aby uruchomić skrypt:

        .\mapreducejob.ps1

    Po uruchomieniu skryptu zostanie wyświetlony monit o nazwę klastra HDInsight i logowania klastra. Może również zostać wyświetlony monit o uwierzytelnienie się w ramach subskrypcji platformy Azure.

3. Po zakończeniu zadania otrzymasz dane wyjściowe podobne do następującego tekstu:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    To dane wyjściowe wskazuje, że zadanie zostało ukończone pomyślnie.

    > [!NOTE]  
    > Jeśli **kod zakończenia** jest wartością inną niż 0, zobacz [Rozwiązywanie problemów](#troubleshooting).

    W tym przykładzie przechowuje również pobrane pliki do pliku **output.txt** w katalogu, z którego uruchamiasz skrypt.

### <a name="view-output"></a>Wyświetl dane wyjściowe

Aby wyświetlić wyrazy i liczby wywoływane przez zadanie, otwórz plik **output.txt** w edytorze tekstu.

> [!NOTE]  
> Pliki wyjściowe zadania MapReduce są niezmienne. Więc po ponownym toruniu tego przykładu, należy zmienić nazwę pliku wyjściowego.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli po zakończeniu zadania nie są zwracane żadne informacje, wyświetl błędy zadania. Aby wyświetlić informacje o błędzie dla tego zadania, dodaj następujące polecenie na końcu pliku **mapreducejob.ps1.** Następnie zapisz plik i uruchom ponownie skrypt.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

To polecenie cmdlet zwraca informacje, które zostały zapisane do STDERR w miarę wykonywania zadania.

## <a name="next-steps"></a>Następne kroki

Jak widać, program Azure PowerShell zapewnia łatwy sposób uruchamiania zadań MapReduce w klastrze HDInsight, monitorowania stanu zadania i pobierania danych wyjściowych. Aby uzyskać informacje o innych sposobach pracy z Hadoop w programie HDInsight:

* [Użyj MapReduce na HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Użyj Apache Hive z Apache Hadoop na HDInsight](hdinsight-use-hive.md)
