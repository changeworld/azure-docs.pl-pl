---
title: Instalowanie i korzystanie z systemu Giraph w klastrach usługi Hadoop w HDInsight — Azure
description: Dowiedz się, jak dostosować klastra HDInsight z systemu Giraph i jak za pomocą systemu Giraph.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 37e8b0a5cc89eded1890eebbeb93cc82d54c9f05
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360898"
---
# <a name="install-and-use-apache-giraph-on-windows-based-hdinsight-clusters"></a>Instalowanie i używanie Apache Giraph w klastrach HDInsight z systemem Windows

Dowiedz się, jak dostosować Windows oparte na klastrze HDInsight z systemu Apache Giraph przy użyciu akcji skryptów i sposobu przetwarzanie dużych wykresów za pomocą systemu Giraph. Aby uzyskać informacji na temat użycia systemu Giraph w klastrze opartych na systemie Linux, zobacz [zainstalować Apache Giraph w klastrach usługi HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md).

> [!IMPORTANT]  
> Kroki opisane w tym dokumencie działają tylko z klastrami HDInsight z systemem Windows. HDInsight jest dostępna tylko na Windows dla wersji starszej niż HDInsight 3.4. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows). Aby uzyskać informacje o tym, jak zainstalować system Giraph w klastrze HDInsight opartych na systemie Linux, zobacz [zainstalować Apache Giraph w klastrach usługi HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md).


Można zainstalować system Giraph z każdym typem klastra (Hadoop, Storm, HBase, Spark) w usłudze Azure HDInsight przy użyciu *akcji skryptu*. Przykładowy skrypt, aby zainstalować system Giraph w klastrze usługi HDInsight jest dostępna tylko do odczytu z usługi Azure storage blob pod [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). Przykładowy skrypt działa tylko w przypadku klastra HDInsight w wersji 3.1. Aby uzyskać więcej informacji na temat wersji klastra HDInsight, zobacz [wersji klastra HDInsight](hdinsight-component-versioning.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Powiązane artykuły**

* [Instalowanie systemu Apache Giraph w klastrach usługi HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Tworzenie klastrów usługi Apache Hadoop w HDInsight](hdinsight-provision-clusters.md): ogólne informacje na temat tworzenia klastrów HDInsight.
* [Dostosowywanie klastrów HDInsight za pomocą akcji skryptu] [hdinsight — klastra — Dostosowywanie]: ogólne informacje na temat dostosowywania klastrów HDInsight za pomocą akcji skryptu.
* [Tworzenie akcji skryptu skryptów dla HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-giraph"></a>Co to jest system Giraph?
<a href="https://giraph.apache.org/" target="_blank">Apache Giraph</a> umożliwia wykonywanie wykres przetwarzania za pomocą usługi Hadoop i mogą być używane z usługi Azure HDInsight. Wykresy modelu relacje między obiektami, takimi jak połączeń między routerami w dużej sieci, takich jak Internet lub relacji między osobami w sieciach społecznościowych (czasami określane jako wykres społecznościowych). Przetwarzanie wykresów umożliwia przeglądanie informacji o relacji między obiektami w grafie, takich jak:

* Identyfikowanie potencjalnych znajomych, na podstawie Twojej bieżącej relacji.
* Identyfikowanie najkrótszej trasy między dwoma komputerami w sieci.
* Obliczanie rangę strony stron sieci Web.

## <a name="install-giraph-using-portal"></a>Zainstalować system Giraph przy użyciu portalu
1. Rozpocznij tworzenie klastra przy użyciu **tworzenie niestandardowe** opcji, zgodnie z opisem w [Tworzenie klastrów usługi Hadoop w HDInsight](hdinsight-provision-clusters.md).
2. Na **akcji skryptu** strony kreatora, kliknij przycisk **Dodaj akcję skryptu** dostarczanie szczegółowych informacji o akcji skryptu, jak pokazano poniżej:

    ![Użyć akcji skryptu, aby dostosować klaster](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "użyj akcji skryptu, aby dostosować klastra")

    |Właściwość|Wartość|  
    |---|---|  
    |Name (Nazwa)|Określ nazwę dla akcji skryptu. Na przykład **zainstalować system Giraph**|
    |Identyfikator URI skryptu|Określ identyfikator URI (Uniform Resource) do skryptu, który jest wywoływana w celu dostosowania do klastra. Na przykład *https:\//hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1*|
    |Typ węzła|Określ węzły, na których jest uruchamiany skrypt dostosowywania. Możesz wybrać **wszystkie węzły**, **tylko węzłami głównymi**, lub **węzłów procesu roboczego tylko**.
    |Parametry|Określ parametry, jeśli jest to wymagane przez skrypt. Skrypt, aby zainstalować system Giraph nie wymaga żadnych parametrów, dzięki czemu użytkownik może pozostaw to pole puste.|  

    Możesz dodać więcej niż jedna akcja skryptu do zainstalowania wiele składników w klastrze. Po dodaniu skryptów, kliknij znacznik wyboru, aby rozpocząć tworzenie klastra.

## <a name="use-giraph"></a>Korzystanie z systemu Giraph
Używamy przykład SimpleShortestPathsComputation aby zademonstrować podstawowa <a href = "https://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> wdrażania służące do znajdowania najkrótsze ścieżki między obiektami w grafie. Wykonaj następujące kroki, aby przekazywanie przykładowych danych i przykładowy plik jar, uruchomić zadanie przy użyciu przykładu SimpleShortestPathsComputation, a następnie przejrzyj wyniki.

1. Przekaż przykładowy plik danych do usługi Azure Blob storage. Na lokalnej stacji roboczej, Utwórz nowy plik o nazwie **tiny_graph.txt**. Musi on zawierać następujące wiersze:

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Przekaż plik tiny_graph.txt do podstawowy magazyn dla klastra usługi HDInsight. Aby uzyskać instrukcje na temat przekazywania danych, zobacz [przekazywanie danych na potrzeby zadań usługi Apache Hadoop w HDInsight](hdinsight-upload-data.md).

    Te dane w tym artykule opisano relację między obiektami na grafie skierowanym w formacie [źródła\_identyfikator, źródło\_wartość [[dest\_identyfikator], [krawędzi\_wartość],...]]. Każdy wiersz reprezentuje relację między **źródła\_identyfikator** obiektu i co najmniej jeden **dest\_identyfikator** obiektów. **Krawędzi\_wartość** (lub waga) mogą być uważane za siłę lub odległości połączenie między **source_id** i **dest\_identyfikator**.

    Rysowania, i przy użyciu wartości (lub waga) jako odległość między obiektami, powyższe dane może wyglądać następująco:

    ![tiny_graph.txt rysowana w formie okręgów linie różnych odległość między](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)

2. Uruchom przykład SimpleShortestPathsComputation. Użyj następujących poleceń cmdlet programu Azure PowerShell, aby uruchomić przykład z użyciem tiny_graph.txt pliku jako dane wejściowe.

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

    W powyższym przykładzie Zastąp **clustername** nazwą klastra usługi HDInsight, który ma zainstalowany system Giraph.
3. Przejrzyj wyniki. Po zakończeniu zadania wyniki będą przechowywane w dwóch plikach danych wyjściowych w **wasb: / / / przykład/out/shotestpaths** folderu. Pliki są nazywane **część m-00001** i **część m-00002**. Wykonaj poniższe kroki, aby pobrać i wyświetlić dane wyjściowe:

    ```powershell
    $subscriptionName = "<SubscriptionName>"       # Azure subscription name
    $storageAccountName = "<StorageAccountName>"   # Azure Storage account name
    $containerName = "<ContainerName>"             # Blob storage container name

    # Select the current subscription
    Select-AzureSubscription $subscriptionName

    # Create the Storage account context object
    $storageAccountKey = Get-AzStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    # Download the job output to the workstation
    Get-AzStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
    Get-AzStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force
    ```

    Spowoduje to utworzenie **przykład/wyjście/shortestpaths** strukturę katalogów w bieżącym katalogu na stacji roboczej i pobierania dwa pliki wyjściowe do tej lokalizacji.

    Użyj **Cat** polecenia cmdlet, aby wyświetlić zawartość plików:

        Cat example/output/shortestpaths/part*

    Dane wyjściowe powinny wyglądać podobnie do następującego:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation, jest przykład twardych kodowanego zaczynać obiekt o identyfikatorze 1 i najkrótsze ścieżki do innych obiektów. Aby dane wyjściowe powinny być odczytywane jako `destination_id distance`, gdzie jest wartość (lub waga) krawędzi przemieścić się między tymi obiekt ID 1 i identyfikatora celu.

    To wizualizacji, można sprawdzić wyniki, podróżowanie najkrótsze ścieżki między 1 identyfikator i innych obiektów. Należy pamiętać, że najkrótsze ścieżki między ID 1 i 4 identyfikator wynosi 5. Jest to łączna liczba odległość między <span style="color:orange">ID 1 i 3</span>, a następnie <span style="color:red">Identyfikatorem 3 i 4</span>.

    ![Rysowanie obiektów jako kółka, przy użyciu najkrótsze ścieżki między](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-azure-powershell"></a>Zainstalować system Giraph przy użyciu programu Azure PowerShell
Zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).  W przykładzie pokazano, jak zainstalować platformę Apache Spark przy użyciu programu Azure PowerShell. Należy dostosować skrypt, który chcesz użyć [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="install-giraph-using-net-sdk"></a>Zainstalować system Giraph przy użyciu zestawu .NET SDK
Zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md). W przykładzie pokazano sposób instalowania Spark za pomocą zestawu .NET SDK. Należy dostosować skrypt, który chcesz użyć [ https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1 ](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="see-also"></a>Zobacz także
* [Instalowanie systemu Apache Giraph w klastrach usługi HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Tworzenie klastrów usługi Apache Hadoop w HDInsight](hdinsight-provision-clusters.md): ogólne informacje na temat tworzenia klastrów HDInsight.
* [Dostosowywanie klastrów HDInsight za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): ogólne informacje na temat dostosowywania klastrów HDInsight za pomocą akcji skryptu.
* [Tworzenie akcji skryptu skryptów dla HDInsight](hdinsight-hadoop-script-actions.md).
* [Instalowanie i używanie platformy Apache Spark w klastrach HDInsight][hdinsight-install-spark]: Przykład akcji skryptu o instalowaniu platformy Spark.

[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
