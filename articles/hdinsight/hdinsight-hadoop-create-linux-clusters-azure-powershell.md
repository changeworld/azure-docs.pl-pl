---
title: Tworzenie klastrów usługi Apache Hadoop przy użyciu programu PowerShell — Azure HDInsight
description: Dowiedz się, jak utworzyć klastry Apache Hadoop, Apache HBase, Apache Storm lub Apache Spark w systemie Linux dla HDInsight przy użyciu programu Azure PowerShell.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 30154c55e60b7150257729c9bc90ee07a561e08e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264543"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Tworzenie klastrów opartych na systemie Linux w HDInsight przy użyciu programu Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell to zaawansowane środowisko obsługi skryptów, którego można użyć do kontrolowania i zautomatyzować wdrożenie i zarządzanie obciążeń na platformie Microsoft Azure. Ten dokument zawiera informacje o tym, jak utworzyć klaster HDInsight opartych na systemie Linux przy użyciu programu Azure PowerShell. Zawiera również przykładowy skrypt.

> [!NOTE]  
> Program Azure PowerShell jest dostępna tylko na komputerach klienckich Windows. Jeśli używasz klienta systemu Linux, Unix lub Mac OS X, zobacz [Tworzenie klastra HDInsight opartych na systemie Linux przy użyciu wiersza polecenia platformy Azure Classic](hdinsight-hadoop-create-linux-clusters-azure-cli.md) informacji o używaniu klasyczny interfejs wiersza polecenia, aby utworzyć klaster.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Musi być następujące oprogramowanie przed rozpoczęciem tej procedury:

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-Az-ps)

    > [!IMPORTANT]  
    > Obsługa programu Azure PowerShell do celów zarządzania zasobami usługi HDInsight przy użyciu usługi Azure Service Manager jest **przestarzała** i została usunięta z dniem 1 stycznia 2017 r. W czynnościach opisanych w niniejszym dokumencie są używane nowe polecenia cmdlet usługi HDInsight współpracujące z usługą Azure Resource Manager.
    >
    > Wykonaj poniższe kroki w [Instalowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) Aby zainstalować najnowszą wersję programu Azure PowerShell. Jeśli masz skrypty wymagające modyfikacji w celu użycia nowych poleceń cmdlet współpracujących z usługą Azure Resource Manager, zobacz temat [Migrowanie do narzędzi programistycznych opartych na usłudze Azure Resource Manager w celu obsługi klastrów usługi HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md), aby uzyskać więcej informacji.

## <a name="create-cluster"></a>Tworzenie klastra

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Aby utworzyć klaster usługi HDInsight przy użyciu programu Azure PowerShell, należy wykonać następujące procedury:

* Utwórz grupę zasobów platformy Azure
* Tworzenie konta usługi Azure Storage
* Tworzenie kontenera obiektów Blob platformy Azure
* Tworzenie klastra HDInsight

Poniższy skrypt pokazuje, jak utworzyć nowy klaster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Wartości, które określisz dla danych logowania klastra są używane do tworzenia konta użytkownika usługi Hadoop dla klastra. Aby nawiązać połączenie usług hostowanych w klastrze, takich jak web UI lub interfejsów API REST, należy użyć tego konta.

Wartości, które określisz dla użytkownika SSH są używane do tworzenia użytkownika SSH dla klastra. Uruchom sesję zdalną SSH w klastrze oraz uruchamianie zadań, należy użyć tego konta. Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]  
> Jeśli zamierzasz używać więcej niż 32 węzły procesu roboczego (lub podczas tworzenia klastra przy użyciu skalowania klastra po utworzeniu), należy także określić rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM.
>
> Aby uzyskać więcej informacji o rozmiarach węzła i powiązanych kosztach, zobacz [Cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Może potrwać do 20 minut, aby utworzyć klaster.

## <a name="create-cluster-configuration-object"></a>Tworzenie klastra: Obiekt konfiguracji

Można również utworzyć HDInsight konfiguracji obiekt przy użyciu `New-AzHDInsightClusterConfig` polecenia cmdlet. Następnie można zmodyfikować ten obiekt konfiguracji, aby włączyć dodatkowe opcje konfiguracji dla klastra. Na koniec użyj `-Config` parametru `New-AzHDInsightCluster` polecenia cmdlet do korzystania z konfiguracji.

Poniższy skrypt tworzy obiekt konfiguracji, aby skonfigurować serwer R Server na typ klastra HDInsight. Konfiguracja umożliwia węzeł krawędzi, RStudio i dodatkowe konto magazynu.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> Za pomocą konta magazynu w innej lokalizacji niż klaster HDInsight nie jest obsługiwana. Korzystając z tego przykładu, należy utworzyć dodatkowe konto magazynu w tej samej lokalizacji co serwer.

## <a name="customize-clusters"></a>Dostosowywanie klastrów

* Zobacz [HDInsight Dostosowywanie klastrów za pomocą narzędzia Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Kolejne kroki

Teraz, że udało Ci się utworzyć klaster usługi HDInsight, użyj następujących zasobów, aby dowiedzieć się, jak pracować z klastrem.

### <a name="apache-hadoop-clusters"></a>Klastry platformy Apache Hadoop

* [Use Apache Hive z HDInsight](hadoop/hdinsight-use-hive.md)
* [Apache Pig za pomocą HDInsight](hadoop/hdinsight-use-pig.md)
* [Używanie technologii MapReduce z usługą HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Klastry Apache HBase

* [Rozpoczynanie pracy z usługą Apache HBase na HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Twórz aplikacje Java dla bazy danych Apache HBase na HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Klastry STORM

* [Opracowywanie topologii języka Java dla systemu Storm w HDInsight](storm/apache-storm-develop-java-topology.md)
* [Użyj języka Python składników systemu Storm w HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii z systemu Storm w HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Klastry platformy Apache Spark

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](spark/apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze usługi Apache Spark przy użyciu programu Livy Apache](spark/apache-spark-livy-rest-interface.md)
* [Platforma Apache Spark przy użyciu Power BI: Interakcyjna analiza danych przy użyciu platformy Spark w HDInsight przy użyciu narzędzi do analizy Biznesowej](spark/apache-spark-use-bi-tools.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do przewidywania wyników kontroli żywności](spark/apache-spark-machine-learning-mllib-ipython.md)

