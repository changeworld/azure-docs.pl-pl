---
title: Korzystanie z języka Pig Hadoop przy użyciu programu PowerShell w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak umożliwiają przesyłanie zadań Pig do klastra usługi Hadoop w usłudze HDInsight przy użyciu programu Azure PowerShell.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 737089c1-b494-4387-9def-7b4dac3be532
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: a3e62647ec41cfdfc7f0f7bb55474215ab435ee8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33939444"
---
# <a name="use-azure-powershell-to-run-pig-jobs-with-hdinsight"></a>Uruchamianie zadań Pig z usługą HDInsight przy użyciu programu Azure PowerShell

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Ten dokument zawiera przykład do przesyłania zadań Pig do platformy Hadoop w klastrze usługi HDInsight przy użyciu programu Azure PowerShell. Pig umożliwia zapisu zadań MapReduce przy użyciu języka (Pig Latin) tego przekształcenia danych modeli, a nie mapy i zmniejszyć funkcji.

> [!NOTE]
> Niniejszy dokument nie daje szczegółowy opis wykonaj instrukcje Pig Latin przykłady. Aby uzyskać informacje na temat Pig Latin używana w tym przykładzie, zobacz [Use Pig z usługą Hadoop w usłudze HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Wymagania wstępne

* **Klaster Azure HDInsight**

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* **Stacja robocza z programem Azure PowerShell**.

## <a id="powershell"></a>Uruchom zadanie Pig

Udostępnia program Azure PowerShell *poleceń cmdlet* umożliwiającą zdalne uruchamianie zadań Pig w usłudze HDInsight. Wewnętrznie PowerShell korzysta z wywołań REST [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) uruchomione w klastrze usługi HDInsight.

Następujące polecenia cmdlet są używane podczas uruchamiania zadań Pig w zdalnym klastrze usługi HDInsight:

* **Connect-AzureRmAccount**: uwierzytelnianie programu Azure PowerShell do subskrypcji platformy Azure.
* **Nowy AzureRmHDInsightPigJobDefinition**: tworzy *definicji zadania* przy użyciu określonego instrukcje Pig Latin.
* **Start-AzureRmHDInsightJob**: wysyła definicji zadania w usłudze HDInsight i uruchamiania zadania. A *zadania* obiekt jest zwracany.
* **Czekaj-AzureRmHDInsightJob**: używa obiektu zadania, aby sprawdzić stan zadania. Oczekuje, aż zadanie zostało zakończone, lub czas oczekiwania został przekroczony.
* **Get-AzureRmHDInsightJobOutput**: służy do pobierania danych wyjściowych zadania.

Poniższe kroki pokazują, jak używać tych poleceń cmdlet, aby uruchomić zadanie w klastrze usługi HDInsight.

1. Za pomocą edytora, Zapisz poniższy kod jako **pigjob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Otwórz wiersz polecenia programu Windows PowerShell. Zmień katalogi na lokalizację **pigjob.ps1** pliku, a następnie użyj następującego polecenia, aby uruchomić skrypt:

        .\pigjob.ps1

    Zostanie wyświetlony monit logowania do Twojej subskrypcji platformy Azure. Następnie zostanie wyświetlona prośba o HTTPs/Admin nazwę konta i hasło dla klastra usługi HDInsight.

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

Jeśli żadne informacje nie zostanie zwrócone, po zakończeniu zadania, sprawdź dzienniki błędów. Aby wyświetlić informacje o błędzie dla tego zadania, należy dodać następujące polecenie na końcu **pigjob.ps1** , zapisz go, a następnie uruchomić.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

To polecenie cmdlet zwraca informacje, które zostało zapisane STDERR podczas przetwarzania zadania.

## <a id="summary"></a>Podsumowanie
Jak widać, programu Azure PowerShell zapewnia prosty sposób uruchamiania zadań Pig na klaster usługi HDInsight, monitorować stan zadania i pobrać dane wyjściowe.

## <a id="nextsteps"></a>Następne kroki
Aby uzyskać ogólne informacje na temat Pig w usłudze HDInsight:

* [Korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight](hdinsight-use-pig.md)

Aby uzyskać informacje o innych metodach pracy z platformą Hadoop w usłudze HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w usłudze HDInsight](hdinsight-use-hive.md)
* [Używanie MapReduce z usługą Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)
