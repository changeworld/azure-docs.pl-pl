---
title: Tworzenie klastrów Apache Hadoop przy użyciu programu PowerShell — usługa Azure HDInsight
description: Dowiedz się, jak utworzyć apache Hadoop, Apache HBase, Apache Storm lub Apache Spark klastrów w systemie Linux dla HDInsight przy użyciu programu Azure PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 3c93eca493275612ac14a995140b2e91cc40fe98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644668"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu programu Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell to zaawansowane środowisko skryptów, którego można używać do kontrolowania i automatyzacji wdrażania i zarządzania obciążeniami na platformie Microsoft Azure. Ten dokument zawiera informacje dotyczące tworzenia klastra HDInsight opartego na systemie Linux przy użyciu programu Azure PowerShell. Zawiera również przykładowy skrypt.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Program Azure PowerShell](/powershell/azure/install-Az-ps) Moduł Az.

## <a name="create-cluster"></a>Tworzenie klastra

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Aby utworzyć klaster usługi HDInsight przy użyciu programu Azure PowerShell, należy wykonać następujące procedury:

* Tworzenie grupy zasobów platformy Azure
* Tworzenie konta usługi Azure Storage
* Tworzenie kontenera obiektów blob platformy Azure
* Tworzenie klastra HDInsight

> [!NOTE]
> Za pomocą programu PowerShell do tworzenia klastra HDInsight z usługi Azure Data Lake Storage Gen2 nie jest obecnie obsługiwany.

Poniższy skrypt pokazuje, jak utworzyć nowy klaster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Wartości określone dla logowania klastra są używane do tworzenia konta użytkownika Hadoop dla klastra. To konto służy do łączenia się z usługami hostowanymi w klastrze, takimi jak interfejsy API sieci Web lub interfejsy API REST.

Wartości określone dla użytkownika SSH są używane do tworzenia użytkownika SSH dla klastra. To konto służy do uruchamiania zdalnej sesji SSH w klastrze i uruchamiania zadań. Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]  
> Jeśli planujesz użyć więcej niż 32 węzłów procesu roboczego (podczas tworzenia klastra lub skalowania klastra po utworzeniu), należy również określić rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM.
>
> Aby uzyskać więcej informacji o rozmiarach węzła i powiązanych kosztach, zobacz [Cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Utworzenie klastra może potrwać do 20 minut.

## <a name="create-cluster-configuration-object"></a>Tworzenie klastra: obiekt konfiguracji

Można również utworzyć obiekt konfiguracji HDInsight przy użyciu polecenia [`New-AzHDInsightClusterConfig`](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) cmdlet. Następnie można zmodyfikować ten obiekt konfiguracji, aby włączyć dodatkowe opcje konfiguracji dla klastra. Na koniec użyj `-Config` parametru [`New-AzHDInsightCluster`](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) polecenia cmdlet, aby użyć konfiguracji.

Poniższy skrypt tworzy obiekt konfiguracji w celu skonfigurowania serwera R na typie klastra HDInsight. Konfiguracja umożliwia węzeł krawędzi, RStudio i dodatkowe konto magazynu.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> Korzystanie z konta magazynu w innej lokalizacji niż klaster HDInsight nie jest obsługiwane. Korzystając z tego przykładu, należy utworzyć dodatkowe konto magazynu w tej samej lokalizacji co serwer.

## <a name="customize-clusters"></a>Dostosowywanie klastrów

* Zobacz [Dostosowywanie klastrów HDInsight przy użyciu bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Zobacz [Dostosowywanie klastrów HDInsight przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy pomyślnie utworzono klaster HDInsight, użyj następujących zasobów, aby dowiedzieć się, jak pracować z klastrem.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop klastrów

* [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z mapReduce z HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Klastry Apache HBase

* [Wprowadzenie do Apache HBase w programie HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Tworzenie aplikacji Java dla Apache HBase w programie HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Klastry burzowe

* [Opracowanie topologii Java dla Storm na HDInsight](storm/apache-storm-develop-java-topology.md)
* [Używanie składników języka Python w programie Storm w programie HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii dzięki storm na hdinsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Klastry Platformy Spark Apache

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](spark/apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark z analizą biznesową: wykonywanie interaktywnej analizy danych przy użyciu funkcji Spark w funkcji HDInsight za pomocą narzędzi analizy biznesowej](spark/apache-spark-use-bi-tools.md)
* [Apache Spark z uczeniem maszynowym: użyj iskry w hdinsight, aby przewidzieć wyniki inspekcji żywności](spark/apache-spark-machine-learning-mllib-ipython.md)