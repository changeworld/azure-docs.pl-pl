---
title: Korzystanie z języka Pig z usługi Hadoop w przy użyciu programu PowerShell w HDInsight — Azure
description: Dowiedz się, jak przesyłać zadania Pig do klastra usługi Hadoop w HDInsight przy użyciu programu Azure PowerShell.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: jasonh
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: ff08e632c1bfd8eb4040e4e746ce08335eba8b08
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047378"
---
# <a name="use-azure-powershell-to-run-pig-jobs-with-hdinsight"></a>Wykonywanie zadań Pig z usługą HDInsight przy użyciu programu Azure PowerShell

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Ten dokument zawiera przykład przesyłanie zadania Pig z usługą Hadoop w klastrze HDInsight przy użyciu programu Azure PowerShell. Pig pozwala na zapis zadań MapReduce przy użyciu języka (Pig Latin) tej transformacji danych modeli, zamiast mapowania i redukcji funkcji.

> [!NOTE]
> Ten dokument zawiera szczegółowy opis działania instrukcji Pig Latin przykłady. Aby uzyskać informacje na temat Pig Latin, używany w tym przykładzie, zobacz [korzystanie z języka Pig z platformą Hadoop w HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Wymagania wstępne

* **Klaster usługi Azure HDInsight**

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* **Stacja robocza z programem Azure PowerShell**.

## <a id="powershell"></a>Uruchamianie zadania Pig

Azure PowerShell udostępnia *poleceń cmdlet* umożliwiającą zdalne uruchamianie zadań Pig na HDInsight. Wewnętrznie, programu PowerShell używa wywołania REST do [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) uruchomionego w klastrze HDInsight.

Następujące polecenia cmdlet są używane podczas uruchamiania zadania Pig w zdalnym klastrze HDInsight:

* **Connect-AzureRmAccount**: uwierzytelnianie programu Azure PowerShell do subskrypcji platformy Azure.
* **Nowe AzureRmHDInsightPigJobDefinition**: tworzy *definicji zadania* przy użyciu określonej instrukcji Pig Latin.
* **Start-AzureRmHDInsightJob**: wysyła definicji zadania do HDInsight i uruchamia zadanie. A *zadania* obiekt jest zwracany.
* **Oczekiwania AzureRmHDInsightJob**: używa obiektu zadania, aby sprawdzić stan zadania. Oczekuje, aż zadanie zostało zakończone, lub przekroczono czas oczekiwania.
* **Get-AzureRmHDInsightJobOutput**: używane do pobierania danych wyjściowych zadania.

Poniższe kroki pokazują, jak używać tych poleceń cmdlet, aby uruchomić zadanie w klastrze usługi HDInsight.

1. Za pomocą edytora, Zapisz poniższy kod jako **pigjob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Otwórz wiersz polecenia programu Windows PowerShell. Zmień katalogi na lokalizację **pigjob.ps1** pliku, a następnie użyj następującego polecenia, aby uruchomić skrypt:

        .\pigjob.ps1

    Monit, zaloguj się do subskrypcji platformy Azure. Następnie zostanie wyświetlona prośba o HTTPs/Admin nazwę konta i hasło dla klastra HDInsight.

2. Po zakończeniu zadania, jego powinny zostać zwrócone informacje podobne do następującego tekstu:

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="troubleshooting"></a>Rozwiązywanie problemów

Jeśli nie informacje są zwracane po zakończeniu zadania, wyświetlać dzienniki błędów. Aby wyświetlić informacje o błędzie dla tego zadania, Dodaj następujące polecenie na końcu **pigjob.ps1** pliku, zapisz go i uruchom go ponownie.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

To polecenie cmdlet zwraca informacje, które zostały zapisane do strumienia wyjściowego STDERR podczas przetwarzania zadania.

## <a id="summary"></a>Podsumowanie
Jak widać, programu Azure PowerShell udostępnia prosty sposób uruchamiania zadania Pig w klastrze usługi HDInsight, monitorować stan zadania i pobieranie danych wyjściowych.

## <a id="nextsteps"></a>Następne kroki
Aby uzyskać ogólne informacje na temat Pig w HDInsight:

* [Korzystanie z języka Pig z platformą Hadoop w HDInsight](hdinsight-use-pig.md)

Aby uzyskać informacje o innych metodach można pracować z platformą Hadoop w HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w HDInsight](hdinsight-use-hive.md)
* [Korzystanie z technologii MapReduce z platformą Hadoop w HDInsight](hdinsight-use-mapreduce.md)
