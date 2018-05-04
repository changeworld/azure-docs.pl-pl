---
title: Korzystanie z programu Hadoop Hive przy użyciu programu PowerShell w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Uruchamianie zapytań Hive w Hadoop w usłudze HDInsight przy użyciu programu PowerShell.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cb795b7c-bcd0-497a-a7f0-8ed18ef49195
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: d72d5223373043648aeb3d783477781dbce35f6d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="run-hive-queries-using-powershell"></a>Uruchamianie zapytań Hive przy użyciu programu PowerShell
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ten dokument zawiera przykład uruchamianie zapytań Hive w usługi Hadoop w klastrze usługi HDInsight przy użyciu programu Azure PowerShell w trybie grupy zasobów platformy Azure.

> [!NOTE]
> Niniejszy dokument nie daje szczegółowy opis wykonaj instrukcje HiveQL, które są używane w przykładach. Aby uzyskać informacje na HiveQL, który jest używany w tym przykładzie, zobacz [używanie Hive z usługą Hadoop w usłudze HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Opartych na systemie Linux platformą Hadoop w klastrze usługi HDInsight w wersji 3.4 lub większą.

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Klient z programem Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive

Udostępnia program Azure PowerShell *poleceń cmdlet* umożliwiającą zdalne uruchamianie zapytań Hive w usłudze HDInsight. Wewnętrznie, poleceń cmdlet wywołań REST do [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) w klastrze usługi HDInsight.

Następujące polecenia cmdlet są używane podczas uruchamiania zapytań Hive w zdalnym klastrze usługi HDInsight:

* `Connect-AzureRmAccount`: Uwierzytelnianie programu Azure PowerShell do subskrypcji platformy Azure.
* `New-AzureRmHDInsightHiveJobDefinition`: Tworzy *definicji zadania* przy użyciu określonego instrukcje HiveQL.
* `Start-AzureRmHDInsightJob`: Wysyła definicji zadania w usłudze HDInsight i uruchamiania zadania. A *zadania* obiekt jest zwracany.
* `Wait-AzureRmHDInsightJob`: Używa obiektu zadania w celu sprawdzenia stanu zadania. Oczekuje, aż do ukończenia zadania lub przekroczono czas oczekiwania.
* `Get-AzureRmHDInsightJobOutput`: Używane do pobierania danych wyjściowych zadania.
* `Invoke-AzureRmHDInsightHiveJob`: Służy do uruchamiania instrukcje HiveQL. Bloki tego polecenia cmdlet zapytanie kończy, a następnie zwraca wyniki.
* `Use-AzureRmHDInsightCluster`: Ustawia bieżący klaster na potrzeby `Invoke-AzureRmHDInsightHiveJob` polecenia.

Poniższe kroki pokazują, jak używać tych poleceń cmdlet, aby uruchomić zadanie w klastrze usługi HDInsight:

1. Za pomocą edytora, Zapisz poniższy kod jako `hivejob.ps1`.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Otwórz nowe **programu Azure PowerShell** wiersza polecenia. Zmień katalogi na lokalizację `hivejob.ps1` pliku, a następnie użyj następującego polecenia, aby uruchomić skrypt:

        .\hivejob.ps1

    Po uruchomieniu skryptu zostanie wyświetlony monit o wprowadzenie nazwy klastra i poświadczenia konta administratora/HTTPS klastra. Może również zostać wyświetlony monit logować się do subskrypcji platformy Azure.

3. Po zakończeniu zadania, zwraca informacje podobne do następującego tekstu:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Jak wspomniano wcześniej, `Invoke-Hive` może służyć do uruchamiania kwerendy i czeka na odpowiedź. Aby zobaczyć, jak działa Invoke-Hive, użyj następującego skryptu:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    Dane wyjściowe wyglądają następująco następujący tekst:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > Dłużej HiveQL zapytań, można użyć programu Azure PowerShell **ciągów tutaj** polecenia cmdlet lub HiveQL pliki skryptów. Poniższy fragment kodu przedstawia sposób użycia `Invoke-Hive` polecenia cmdlet, aby uruchomić skrypt HiveQL. Plik skryptu HiveQL, należy przekazać do wasb: / /.
   >
   > `Invoke-AzureRmHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Aby uzyskać więcej informacji na temat **ciągów tutaj**, zobacz <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">przy użyciu systemu Windows PowerShell tutaj — ciągi</a>.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli żadne informacje nie zostanie zwrócone, po zakończeniu zadania, sprawdź dzienniki błędów. Aby wyświetlić informacje o błędzie dla tego zadania, dodaj następującą wartość do końca `hivejob.ps1` , zapisz go, a następnie uruchomić.

```powershell
# Print the output of the Hive job.
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

To polecenie cmdlet zwraca informacje, które są zapisywane do strumienia wyjściowego STDERR podczas przetwarzania zadania.

## <a name="summary"></a>Podsumowanie

Jak widać, programu Azure PowerShell zapewnia prosty sposób do uruchamiania zapytań Hive w klastra usługi HDInsight, monitorować stan zadania i pobrać dane wyjściowe.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać ogólne informacje na temat programu Hive w usłudze HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w usłudze HDInsight](hdinsight-use-hive.md)

Aby uzyskać informacje o innych metodach pracy z platformą Hadoop w usłudze HDInsight:

* [Korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight](hdinsight-use-pig.md)
* [Używanie MapReduce z usługą Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)
