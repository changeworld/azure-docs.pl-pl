---
title: Użyj języka R w usłudze HDInsight, aby dostosować klastry - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować język R za pomocą akcji skryptu, a następnie użyj języka R w klastrach usługi HDInsight.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: be851270-afa5-4af0-a69e-2d343a4deeb7
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: c5fb38de8b1023a05709d07ba17b0c776c87957e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31405875"
---
# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Instalowanie i używanie języka R w klastrach usługi Hadoop w usłudze HDInsight

Dowiedz się, dostosowywanie systemu Windows na podstawie klastra usługi HDInsight przy użyciu akcji skryptu języka R i sposobu użycia języka R w usłudze HDInsight clusters. [Oferty HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) obejmuje R Server w ramach klastra usługi HDInsight. Dzięki temu R skryptów na potrzeby uruchamiania rozproszone obliczenia MapReduce i Spark. Aby uzyskać więcej informacji, zobacz temat [Rozpoczęcie pracy z platformą R Server w usłudze HDInsight](r-server/r-server-get-started.md). Uzyskać informacji na temat używania R z systemem Linux klastrem, zobacz [instalacji i używania R w klastrach HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md).

Można zainstalować język R w klastrze (na platformie Hadoop, Storm, HBase, Spark) w usłudze Azure HDInsight dowolnego typu za pomocą *akcji skryptu*. Przykładowy skrypt, aby zainstalować język R w klastrze usługi HDInsight jest dostępna z obiektu blob magazynu Azure w trybie tylko do odczytu w [ https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1 ](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

**Pokrewne artykuły**

* [Zainstaluj i użyj języka R w klastrach HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [Tworzenie klastrów Hadoop w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md): ogólne informacje na temat tworzenia klastrów usługi HDInsight
* [Dostosowywanie klastrów usługi HDInsight przy użyciu akcji skryptu][hdinsight-cluster-customize]: ogólne informacje na temat Dostosowywanie klastrów usługi HDInsight przy użyciu akcji skryptu
* [Tworzenie skryptów akcji skryptu dla usługi HDInsight](hdinsight-hadoop-script-actions.md)

## <a name="what-is-r"></a>Co to jest R?
<a href="http://www.r-project.org/" target="_blank">R projektu do statystycznego przetwarzania danych</a> jest otwarty języka źródłowego i środowiska do statystycznego przetwarzania danych. R zawiera setki kompilacji funkcji statystycznych i własnej język programowania, łączącą aspekty funkcjonalne i zorientowanym obiektowo programowania. Umożliwia także rozbudowane funkcje graficzne. R jest preferowanym środowisko programowania najbardziej professional chi i służące w wielu różnych pól.

R jest zgodny z magazynu obiektów Blob Azure (WASB), aby dane przechowywane mogą być przetwarzane przy użyciu języka R w usłudze HDInsight.  

## <a name="install-r"></a>Zainstalować język R
A [przykładowy skrypt](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) zainstalować język R w usłudze HDInsight klastra jest dostępny w trybie tylko do odczytu obiektów blob w usłudze Azure Storage. Ta sekcja zawiera instrukcje dotyczące sposobu użyć przykładowego skryptu podczas tworzenia klastra przy użyciu portalu Azure.

> [!NOTE]
> Przykładowy skrypt został wprowadzony z klastra usługi HDInsight w wersji 3.1. Aby uzyskać więcej informacji o wersjach klastra usługi HDInsight, zobacz [wersji klastra usługi HDInsight](hdinsight-component-versioning.md).
>
>

1. Podczas tworzenia klastra usługi HDInsight w portalu, kliknij przycisk **konfiguracji opcjonalnej**, a następnie kliknij przycisk **akcji skryptu**.
2. Na **akcji skryptu** strony, wprowadź następujące wartości:

    ![Aby dostosować klastra, użyj akcji skryptu](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "użyj akcji skryptu, aby dostosować klastra")

    <table border='1'>
        <tr><th>Właściwość</th><th>Wartość</th></tr>
        <tr><td>Name (Nazwa)</td>
            <td>Na przykład określić nazwę akcji skryptu <b>zainstalować R</b>.</td></tr>
        <tr><td>Identyfikator URI skryptu</td>
            <td>Określ identyfikator URI do skryptu, które jest wywoływane, aby dostosować klastra, na przykład <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
        <tr><td>Typ węzła</td>
            <td>Określ węzły, na których uruchomiono skryptu dostosowania. Możesz wybrać <b>we wszystkich węzłach</b>, <b>tylko węzły główne</b>, lub <b>węzłów procesu roboczego</b> tylko.
        <tr><td>Parametry</td>
            <td>Określ parametry, jeśli jest to wymagane przez skrypt. Jednak skryptu, aby zainstalować język R nie wymaga żadnych parametrów, dzięki czemu można to pole pozostanie puste.</td></tr>
    </table>

    Można dodać więcej niż jedna akcja skryptu, aby zainstalować wiele składników w klastrze. Po dodaniu skryptów, kliknij znacznik wyboru, aby uruchomić crating klastra.

Można także użyć skryptu zainstalować język R w usłudze HDInsight przy użyciu programu Azure PowerShell lub zestawu .NET SDK usługi HDInsight. Instrukcje dotyczące tych procedur znajdują się w dalszej części tego artykułu.

## <a name="run-r-scripts"></a>Uruchom skrypty języka R
W tej sekcji opisano sposób uruchamiania skryptu języka R w klastrze Hadoop z usługą HDInsight.

1. **Ustanów połączenie pulpitu zdalnego w klastrze**: Z portalu włączenie pulpitu zdalnego dla klastra zostały utworzone z języka R zainstalowanego, a następnie połącz się z klastrem. Aby uzyskać instrukcje, zobacz [Connect do klastrów usługi HDInsight za pomocą protokołu RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Otwórz konsolę R**: instalacji języka R umieszcza łącze do konsoli R na pulpicie węzła głównego. Kliknij, aby otworzyć konsolę R.
3. **Uruchom skrypt języka R**: skrypt języka R można uruchomić bezpośrednio z konsoli R wklejenie go, wybierając ją i naciskając klawisz ENTER. Poniżej przedstawiono prosty przykład skrypt generuje liczb od 1 do 100 i mnoży je przez 2.

        library(rmr2)
        library(rhdfs)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
        from.dfs(calc)

Pierwsze dwa wiersze wywołania bibliotek RHadoop, które są instalowane z R. Końcowego wiersza drukuje wyniki w konsoli. Dane wyjściowe powinny wyglądać następująco:

    [1,]  1 2
    [2,]  2 4
    .
    .
    .
    [98,]  98 196
    [99,]  99 198
    [100,] 100 200


## <a name="install-r-using-aure-powershell"></a>Zainstalować język R przy użyciu programu Azure PowerShell
Zobacz [HDInsight dostosować klastry za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  Przykład pokazuje, jak zainstalować Spark przy użyciu programu Azure PowerShell. Należy dostosować skrypt, aby użyć [ https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1 ](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

## <a name="install-r-using-net-sdk"></a>Zainstalować język R przy użyciu zestawu .NET SDK
Zobacz [HDInsight dostosować klastry za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). Przykład pokazuje, jak zainstalować Spark przy użyciu zestawu .NET SDK. Należy dostosować skrypt, aby użyć [ https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1 ](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11).

## <a name="see-also"></a>Zobacz także
* [Zainstaluj i użyj języka R w klastrach HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [Tworzenie klastrów Hadoop w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md): ogólne informacje na temat tworzenia klastrów usługi HDInsight
* [Dostosowywanie klastrów usługi HDInsight przy użyciu akcji skryptu][hdinsight-cluster-customize]: ogólne informacje na temat Dostosowywanie klastrów usługi HDInsight przy użyciu akcji skryptu
* [Tworzenie skryptów akcji skryptu dla usługi HDInsight](hdinsight-hadoop-script-actions.md)
* [Zainstalować i używać platformy Spark w klastrach HDInsight][hdinsight-install-spark]: Akcja skryptu próbki o instalowaniu Spark
* [Zainstaluj w klastrach HDInsight Giraph](hdinsight-hadoop-giraph-install.md): Akcja skryptu próbki o instalowaniu Giraph
* [Zainstaluj w klastrach HDInsight Solr](hdinsight-hadoop-solr-install-linux.md): Akcja skryptu próbki o instalowaniu Solr.

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]:spark/apache-spark-jupyter-spark-sql.md
