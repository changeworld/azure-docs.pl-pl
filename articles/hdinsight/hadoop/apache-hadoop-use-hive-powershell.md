---
title: Korzystanie z gałęzi apache z programem PowerShell w programie HDInsight — Azure
description: Uruchamianie zapytań gałęzi apache w programie Apache Hadoop w usłudze Azure HDInsight za pomocą programu PowerShell
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: deaa934b257fab74830d75e308a283e7608dc590
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552597"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Uruchamianie zapytań gałęzi Apache przy użyciu programu PowerShell

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ten dokument zawiera przykład użycia programu Azure PowerShell do uruchamiania zapytań gałęzi Apache w apache Hadoop w klastrze HDInsight.

> [!NOTE]  
> Ten dokument nie zawiera szczegółowy opis instrukcji HiveQL, które są używane w przykładach zrobić. Aby uzyskać informacje na temat HiveQL, który jest używany w tym przykładzie, zobacz [Użyj Apache Hive z Apache Hadoop na HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Hadoop w programie HDInsight. Zobacz [Wprowadzenie do HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Zainstalowano [moduł Az](https://docs.microsoft.com/powershell/azure/overview) programu PowerShell.

## <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive

Program Azure PowerShell udostępnia *polecenia cmdlet,* które umożliwiają zdalne uruchamianie zapytań hive w programie HDInsight. Wewnętrznie polecenia cmdlet wywołać REST do [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) w klastrze HDInsight.

Następujące polecenia cmdlet są używane podczas uruchamiania zapytań hive w zdalnym klastrze HDInsight:

* `Connect-AzAccount`: Uwierzytelnia usługę Azure PowerShell do subskrypcji platformy Azure.
* `New-AzHDInsightHiveJobDefinition`: Tworzy *definicję zadania* przy użyciu określonych instrukcji HiveQL.
* `Start-AzHDInsightJob`: Wysyła definicję zadania do hdinsight i uruchamia zadanie. Zwracany jest obiekt *zadania.*
* `Wait-AzHDInsightJob`: Używa obiektu zadania do sprawdzania stanu zadania. Czeka, aż zadanie zostanie ukończone lub czas oczekiwania zostanie przekroczony.
* `Get-AzHDInsightJobOutput`: Służy do pobierania danych wyjściowych zadania.
* `Invoke-AzHDInsightHiveJob`: Służy do uruchamiania instrukcji HiveQL. To polecenie cmdlet blokuje zakończenie kwerendy, a następnie zwraca wyniki.
* `Use-AzHDInsightCluster`: Ustawia bieżący klaster `Invoke-AzHDInsightHiveJob` do użycia dla polecenia.

W poniższych krokach pokazano, jak używać tych poleceń cmdlet do uruchamiania zadania w klastrze USŁUGI HDInsight:

1. Za pomocą edytora zapisz `hivejob.ps1`następujący kod jako .

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Otwórz nowy wiersz polecenia **programu Azure PowerShell.** Zmień katalogi na lokalizację `hivejob.ps1` pliku, a następnie użyj następującego polecenia, aby uruchomić skrypt:

        .\hivejob.ps1

    Po uruchomieniu skryptu zostanie wyświetlony monit o wprowadzenie nazwy klastra i poświadczeń konta HTTPS/Cluster Admin. Może również zostać wyświetlony monit o zalogowanie się do subskrypcji platformy Azure.

3. Po zakończeniu zadania zwraca informacje podobne do następującego tekstu:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Jak wspomniano wcześniej, `Invoke-Hive` może służyć do uruchamiania kwerendy i czekać na odpowiedź. Użyj następującego skryptu, aby zobaczyć, jak działa Invoke-Hive:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    Dane wyjściowe wyglądają następująco:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]  
   > W przypadku dłuższych zapytań hiveQl można użyć polecenia cmdlet azure powershell **here-strings** lub plików skryptów HiveQL. Poniższy fragment kodu pokazuje, jak `Invoke-Hive` używać polecenia cmdlet do uruchamiania pliku skryptu HiveQL. Plik skryptu HiveQL musi zostać przekazany do wasbs://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Aby uzyskać więcej informacji na temat **ciągów tutaj,** zobacz <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">Korzystanie z programu Windows PowerShell Here-Strings</a>.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli po zakończeniu zadania nie są zwracane żadne informacje, wyświetl dzienniki błędów. Aby wyświetlić informacje o błędzie dla tego zadania, dodaj następujące elementy na końcu `hivejob.ps1` pliku, zapisz go, a następnie uruchom ponownie.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

To polecenie cmdlet zwraca informacje zapisywane do stderr podczas przetwarzania zadania.

## <a name="summary"></a>Podsumowanie

Jak widać, program Azure PowerShell zapewnia łatwy sposób uruchamiania zapytań hive w klastrze HDInsight, monitorowania stanu zadania i pobierania danych wyjściowych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać ogólne informacje na temat hive w hdinsight:

* [Użyj Apache Hive z Apache Hadoop na HDInsight](hdinsight-use-hive.md)

Aby uzyskać informacje o innych sposobach pracy z Hadoop w programie HDInsight:

* [Użyj MapReduce z Apache Hadoop na HDInsight](hdinsight-use-mapreduce.md)
