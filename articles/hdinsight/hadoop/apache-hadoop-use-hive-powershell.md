---
title: Use Hadoop Hive używanie przy użyciu programu PowerShell w HDInsight — Azure
description: Uruchamianie zapytań programu Hive na platformie Hadoop w HDInsight przy użyciu programu PowerShell.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: jasonh
ms.openlocfilehash: 16caee1b04b8fb3ae2e83b8105b802e121092f60
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051780"
---
# <a name="run-hive-queries-using-powershell"></a>Uruchamianie zapytań Hive przy użyciu programu PowerShell
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ten dokument zawiera przykład przy użyciu programu Azure PowerShell w trybie grupy zasobów platformy Azure do uruchamiania zapytań programu Hive na platformie Hadoop w klastrze HDInsight.

> [!NOTE]
> Ten dokument zawiera szczegółowy opis działania instrukcje HiveQL, które są używane w przykładach. Informacje na temat HiveQL, który jest używany w tym przykładzie można zobaczyć [korzystanie z programu Hive z usługą Hadoop w HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Opartą na systemie Linux platformą Hadoop w wersji klastra HDInsight 3.4 lub nowszą.

  > [!IMPORTANT]
  > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Klient z programem Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive

Azure PowerShell udostępnia *poleceń cmdlet* umożliwiającą zdalne uruchamianie zapytań programu Hive na HDInsight. Wewnętrznie, poleceń cmdlet wykonywać wywołania REST [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) w klastrze HDInsight.

Następujące polecenia cmdlet są używane podczas uruchamiania zapytań programu Hive w zdalnym klastrze HDInsight:

* `Connect-AzureRmAccount`: Uwierzytelnianie programu Azure PowerShell do subskrypcji platformy Azure.
* `New-AzureRmHDInsightHiveJobDefinition`: Tworzy *definicji zadania* przy użyciu określonego instrukcje HiveQL.
* `Start-AzureRmHDInsightJob`: Wysyła definicji zadania HDInsight i uruchamia zadanie. A *zadania* obiekt jest zwracany.
* `Wait-AzureRmHDInsightJob`: Korzysta z obiektu zadania do sprawdzenia stanu zadania. Oczekuje, aż do ukończenia zadania lub przekroczenia czasu oczekiwania.
* `Get-AzureRmHDInsightJobOutput`: Służy do pobierania danych wyjściowych zadania.
* `Invoke-AzureRmHDInsightHiveJob`: Używane do uruchamiania instrukcje HiveQL. To polecenie cmdlet bloki zapytania kończy, a następnie zwraca wyniki.
* `Use-AzureRmHDInsightCluster`: Ustawia bieżący klaster na potrzeby `Invoke-AzureRmHDInsightHiveJob` polecenia.

Poniższe kroki pokazują, jak używać tych poleceń cmdlet do uruchamiania zadań w klastrze usługi HDInsight:

1. Za pomocą edytora, Zapisz poniższy kod jako `hivejob.ps1`.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Otwórz nowy **programu Azure PowerShell** wiersza polecenia. Zmień katalogi na lokalizację `hivejob.ps1` pliku, a następnie użyj następującego polecenia, aby uruchomić skrypt:

        .\hivejob.ps1

    Po uruchomieniu skryptu monit wprowadź nazwę klastra oraz poświadczenia konta HTTPS/Admin dla klastra. Użytkownik może również monit Zaloguj się do subskrypcji platformy Azure.

3. Po zakończeniu zadania, zwraca informacje podobne do następującego tekstu:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Jak wspomniano wcześniej, `Invoke-Hive` może służyć do uruchamiania zapytania i poczekać na odpowiedź. Użyj następującego skryptu, aby zobaczyć, jak działa Invoke-Hive:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    Dane wyjściowe wyglądają podobnie jak następujący tekst:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > W przypadku dłużej zapytań HiveQL, można użyć programu Azure PowerShell **ciągi tutaj** polecenia cmdlet lub HiveQL pliki skryptów. Poniższy fragment kodu przedstawia sposób użycia `Invoke-Hive` polecenia cmdlet do uruchamiania pliku skryptu HiveQL. Plik skryptu HiveQL muszą być przesłane do wasb: / /.
   >
   > `Invoke-AzureRmHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Aby uzyskać więcej informacji na temat **ciągi tutaj**, zobacz <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">za pomocą Windows PowerShell tutaj ciągi</a>.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli nie informacje są zwracane po zakończeniu zadania, wyświetlać dzienniki błędów. Aby wyświetlić informacje o błędzie dla tego zadania, Dodaj następujący element do końca `hivejob.ps1` pliku, zapisz go i uruchom go ponownie.

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

Jak widać, programu Azure PowerShell umożliwia łatwe uruchamianie zapytań programu Hive w klastrze usługi HDInsight, monitorować stan zadania i pobieranie danych wyjściowych.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać ogólne informacje na temat programu Hive w HDInsight:

* [Korzystanie z programu Hive z usługą Hadoop w HDInsight](hdinsight-use-hive.md)

Aby uzyskać informacje o innych metodach można pracować z platformą Hadoop w HDInsight:

* [Korzystanie z języka Pig z platformą Hadoop w HDInsight](hdinsight-use-pig.md)
* [Korzystanie z technologii MapReduce z platformą Hadoop w HDInsight](hdinsight-use-mapreduce.md)
