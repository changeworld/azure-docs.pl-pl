---
title: Zainstalować i używać Giraph na klastrów platformy Hadoop w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostosować klastra usługi HDInsight z Giraph i sposobu użycia Giraph.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 77a1d0e0-55de-4e61-98a0-060914fb7ca0
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: c4cd643d4bdd95493f63bb5b1c1f855bc95bf226
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="install-and-use-giraph-on-windows-based-hdinsight-clusters"></a>Zainstalować i używać Giraph w klastrach HDInsight opartych na systemie Windows

Dowiedz się, jak dostosować klastra usługi HDInsight opartych na systemie Windows za pomocą Giraph za pomocą akcji skryptów i sposobu użycia Giraph przetwarzania dużych wykresów. Uzyskać informacji o używaniu Giraph z klastra opartych na systemie Linux, zobacz [zainstalować Giraph w klastrach HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md).

> [!IMPORTANT]
> Kroki opisane w tym dokumencie pracować tylko z klastrami HDInsight opartych na systemie Windows. HDInsight jest dostępna tylko w systemie Windows dla wersji starszej niż HDInsight 3.4. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows). Aby uzyskać informacje dotyczące sposobu instalowania Giraph w klastrze usługi HDInsight opartej na systemie Linux, zobacz [zainstalować Giraph w klastrach HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md).


Giraph można zainstalować w klastrze (na platformie Hadoop, Storm, HBase, Spark) w usłudze Azure HDInsight dowolnego typu za pomocą *akcji skryptu*. Przykładowy skrypt do zainstalowania Giraph w klastrze usługi HDInsight jest dostępna z obiektu blob magazynu Azure w trybie tylko do odczytu w [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). Przykładowy skrypt działa tylko w przypadku klastra HDInsight w wersji 3.1. Aby uzyskać więcej informacji o wersjach klastra usługi HDInsight, zobacz [wersji klastra usługi HDInsight](hdinsight-component-versioning.md).

**Pokrewne artykuły**

* [Zainstaluj Giraph na klastrów platformy Hadoop w HDInsight (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Tworzenie klastrów Hadoop w usłudze HDInsight](hdinsight-provision-clusters.md): ogólne informacje na temat tworzenia klastrów usługi HDInsight.
* [Dostosowywanie klastrów usługi HDInsight przy użyciu akcji skryptu][hdinsight-cluster-customize]: ogólne informacje na temat Dostosowywanie klastrów usługi HDInsight przy użyciu akcji skryptu.
* [Tworzenie skryptów akcji skryptu dla usługi HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-giraph"></a>Co to jest Giraph?
<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> umożliwia wykonywanie wykresu przetwarzanie przy użyciu platformy Hadoop i mogą być używane z usługi Azure HDInsight. Wykresy modelu relacje między obiektami, takimi jak połączeń między routerami w dużych sieci, takich jak Internet lub relacje między osób w sieciach społecznościowych (czasem określana jako społecznościowych wykresu). Wykres przetwarzania umożliwia przeglądanie informacji o relacje między obiektami na wykresie, takich jak:

* Identyfikuje potencjalne znajomych oparte na bieżącej relacji.
* Identyfikowanie najkrótszy trasy między dwoma komputerami w sieci.
* Obliczanie rangę strony stron sieci Web.

## <a name="install-giraph-using-portal"></a>Zainstaluj Giraph przy użyciu portalu
1. Rozpocznij tworzenie klastra przy użyciu **Utwórz niestandardowy** opcji, zgodnie z opisem w [klastrów utworzyć Hadoop w HDInsight](hdinsight-provision-clusters.md).
2. Na **akcji skryptu** strony kreatora, kliknij przycisk **dodać akcję skryptu** do udostępnienia szczegółów dotyczących akcji skryptu, jak pokazano poniżej:

    ![Aby dostosować klastra, użyj akcji skryptu](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "użyj akcji skryptu, aby dostosować klastra")

    <table border='1'>
        <tr><th>Właściwość</th><th>Wartość</th></tr>
        <tr><td>Name (Nazwa)</td>
            <td>Określ nazwę akcji skryptu. Na przykład <b>zainstalować Giraph</b>.</td></tr>
        <tr><td>Identyfikator URI skryptu</td>
            <td>Określ identyfikator URI (Uniform Resource) do skryptu, które jest wywoływane, aby dostosować klastra. Na przykład <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
        <tr><td>Typ węzła</td>
            <td>Określ węzły, na których uruchomiono skryptu dostosowania. Możesz wybrać <b>we wszystkich węzłach</b>, <b>tylko węzły główne</b>, lub <b>tylko węzłów procesu roboczego</b>.
        <tr><td>Parametry</td>
            <td>Określ parametry, jeśli jest to wymagane przez skrypt. Skrypt do zainstalowania Giraph nie wymaga żadnych parametrów, więc można to pole pozostanie puste.</td></tr>
    </table>

    Można dodać więcej niż jedna akcja skryptu, aby zainstalować wiele składników w klastrze. Po dodaniu skryptów, kliknij znacznik wyboru, aby rozpocząć tworzenie klastra.

## <a name="use-giraph"></a>Korzystanie z systemu Giraph
Możemy użyć przykładowego SimpleShortestPathsComputation pokazują podstawowe <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> implementacji odnajdywania najkrótszy ścieżki między obiektami na wykresie. Przekaż przykładowe dane i jar próbki, uruchom zadanie przy użyciu przykład SimpleShortestPathsComputation, wykonaj następujące kroki, a następnie przejrzyj wyniki.

1. Przekaż przykładowy plik danych do magazynu obiektów Blob platformy Azure. Na lokalnej stacji roboczej, Utwórz nowy plik o nazwie **tiny_graph.txt**. Powinien on zawierać następujące wiersze:

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Przekaż plik tiny_graph.txt do podstawowego magazynu dla klastra usługi HDInsight. Aby uzyskać instrukcje na temat przekazywania danych, zobacz [przekazywanie danych dotyczących zadań Hadoop w usłudze HDInsight](hdinsight-upload-data.md).

    Te dane w tym artykule opisano relacje między obiektami w ukierunkowanego wykresu, w formacie [źródła\_identyfikator, źródło\_wartości [[dest\_identyfikator], [krawędzi\_wartość],...]]. Każdy wiersz zawiera relację między **źródła\_identyfikator** obiektu i co najmniej jeden **dest\_identyfikator** obiektów. **Krawędzi\_wartość** (lub wagi) mogą być uważane za siłę lub odległość połączenie między **source_id** i **dest\_identyfikator**.

    Rysowane, i przy użyciu wartości (lub wagi) jako odległość między obiektami, powyższe dane może wyglądać następująco:

    ![tiny_graph.txt rysowane jako okręgi wiersze z różnymi odległość między](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)
2. Uruchom przykład SimpleShortestPathsComputation. Użyj następujących poleceń cmdlet programu Azure PowerShell do uruchamiania w przykładzie przy użyciu pliku tiny_graph.txt jako dane wejściowe.

    > [!IMPORTANT]
    > Obsługa programu Azure PowerShell do celów zarządzania zasobami usługi HDInsight przy użyciu usługi Azure Service Manager jest **przestarzała** i została usunięta z dniem 1 stycznia 2017 r. W czynnościach opisanych w niniejszym dokumencie są używane nowe polecenia cmdlet usługi HDInsight współpracujące z usługą Azure Resource Manager.
    >
    > Wykonaj kroki opisane w temacie [Instalowanie i konfigurowanie środowiska Azure PowerShell](/powershell/azureps-cmdlets-docs), aby zainstalować najnowszą wersję środowiska Azure PowerShell. Jeśli masz skrypty wymagające modyfikacji w celu użycia nowych poleceń cmdlet współpracujących z usługą Azure Resource Manager, zobacz temat [Migrowanie do narzędzi programistycznych opartych na usłudze Azure Resource Manager w celu obsługi klastrów usługi HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md), aby uzyskać więcej informacji.

    ```powershell
    $clusterName = "clustername"
    # Giraph examples jar
    $jarFile = "wasb:///example/jars/giraph-examples.jar"
    # Arguments for this job
    $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
                    "-ca", "mapred.job.tracker=headnodehost:9010",
                    "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
                    "-vip", "wasb:///example/data/tiny_graph.txt",
                    "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
                    "-op",  "wasb:///example/output/shortestpaths",
                    "-w", "2"
    # Create the definition
    $jobDefinition = New-AzureHDInsightMapReduceJobDefinition
        -JarFile $jarFile
        -ClassName "org.apache.giraph.GiraphRunner"
        -Arguments $jobArguments

    # Run the job, write output to the Azure PowerShell window
    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureHDInsightJob -Job $job
    Write-Host "STDERR"
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
    ```

    W powyższym przykładzie Zastąp **clustername** o nazwie z klastrem usługi HDInsight, zawierający Giraph zainstalowane.
3. Przejrzyj wyniki. Po zakończeniu zadania, wyniki będą przechowywane w dwóch plikach danych wyjściowych w **wasb: / / / przykład/out/shotestpaths** folderu. Pliki są nazywane **części m-00001** i **części m-00002**. Wykonaj poniższe kroki, aby pobrać i wyświetlić dane wyjściowe:

    ```powershell
    $subscriptionName = "<SubscriptionName>"       # Azure subscription name
    $storageAccountName = "<StorageAccountName>"   # Azure Storage account name
    $containerName = "<ContainerName>"             # Blob storage container name

    # Select the current subscription
    Select-AzureSubscription $subscriptionName

    # Create the Storage account context object
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    # Download the job output to the workstation
    Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
    Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force
    ```

    Spowoduje to utworzenie **shortestpaths przykład/wyjście** struktura katalogów w bieżącym katalogu na stacji roboczej i pobierania dwa pliki wyjściowe do tej lokalizacji.

    Użyj **Cat** polecenia cmdlet, aby wyświetlić zawartość plików:

        Cat example/output/shortestpaths/part*

    Dane wyjściowe powinny wyglądać podobnie jak poniżej:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation, który przykład jest trudna do uruchomienia z kodowanego obiektu ID 1 i znaleźć najkrótszy ścieżki do innych obiektów. Dlatego dane wyjściowe powinny zostać odczytany jako `destination_id distance`, gdzie odległości jest wartość (lub wagi) krawędzi przemieścić się między obiektu ID 1 i identyfikatora docelowego.

    Ta wizualizacja, można sprawdzić wyniki za podróży najkrótszy ścieżek między 1 Identyfikatora i wszystkie inne obiekty. Należy pamiętać, że najkrótszy ścieżki między ID 1 i 4 identyfikator 5. Jest to całkowita liczba odległość między <span style="color:orange">ID 1 i 3</span>, a następnie <span style="color:red">identyfikator 3 i 4</span>.

    ![Rysowanie obiektów jako kółka za pomocą najmniejszej ścieżek między](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-aure-powershell"></a>Zainstaluj Giraph przy użyciu programu Azure PowerShell
Zobacz [HDInsight dostosować klastry za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  Przykład pokazuje, jak zainstalować Spark przy użyciu programu Azure PowerShell. Należy dostosować skrypt, aby użyć [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="install-giraph-using-net-sdk"></a>Zainstaluj Giraph przy użyciu zestawu .NET SDK
Zobacz [HDInsight dostosować klastry za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). Przykład pokazuje, jak zainstalować Spark przy użyciu zestawu .NET SDK. Należy dostosować skrypt, aby użyć [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="see-also"></a>Zobacz także
* [Zainstaluj Giraph na klastrów platformy Hadoop w HDInsight (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Tworzenie klastrów Hadoop w usłudze HDInsight](hdinsight-provision-clusters.md): ogólne informacje na temat tworzenia klastrów usługi HDInsight.
* [Dostosowywanie klastrów usługi HDInsight przy użyciu akcji skryptu][hdinsight-cluster-customize]: ogólne informacje na temat Dostosowywanie klastrów usługi HDInsight przy użyciu akcji skryptu.
* [Tworzenie skryptów akcji skryptu dla usługi HDInsight](hdinsight-hadoop-script-actions.md).
* [Zainstalować i używać platformy Spark w klastrach HDInsight][hdinsight-install-spark]: Akcja skryptu próbki o instalowaniu Spark.
* [Zainstaluj w klastrach HDInsight Solr](hdinsight-hadoop-solr-install.md): Akcja skryptu próbki o instalowaniu Solr.

[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
