---
title: Tworzenie klastrów usługi Hadoop przy użyciu programu PowerShell — Azure HDInsight
description: Informacje o sposobie tworzenia klastrów Hadoop, HBase, Storm i Spark w systemie Linux dla HDInsight przy użyciu programu Azure PowerShell.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: jasonh
ms.openlocfilehash: 4d2649a98ea7dfe971a32d014aebc789705b4f9d
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592112"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Tworzenie klastrów opartych na systemie Linux w HDInsight przy użyciu programu Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell to zaawansowane środowisko obsługi skryptów, którego można użyć do kontrolowania i zautomatyzować wdrożenie i zarządzanie obciążeń na platformie Microsoft Azure. Ten dokument zawiera informacje o tym, jak utworzyć klaster HDInsight opartych na systemie Linux przy użyciu programu Azure PowerShell. Zawiera również przykładowy skrypt.

> [!NOTE]
> Program Azure PowerShell jest dostępna tylko na komputerach klienckich Windows. Jeśli używasz klienta systemu Linux, Unix lub Mac OS X, zobacz [Tworzenie klastra HDInsight opartych na systemie Linux przy użyciu wiersza polecenia platformy Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) informacji o używaniu interfejsu wiersza polecenia platformy Azure do utworzenia klastra.

## <a name="prerequisites"></a>Wymagania wstępne
Musi być następujące oprogramowanie przed rozpoczęciem tej procedury:

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-azurerm-ps)

    > [!IMPORTANT]
    > Obsługa programu Azure PowerShell do celów zarządzania zasobami usługi HDInsight przy użyciu usługi Azure Service Manager jest **przestarzała** i została usunięta z dniem 1 stycznia 2017 r. W czynnościach opisanych w niniejszym dokumencie są używane nowe polecenia cmdlet usługi HDInsight współpracujące z usługą Azure Resource Manager.
    >
    > Wykonaj poniższe kroki w [Instalowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) Aby zainstalować najnowszą wersję programu Azure PowerShell. Jeśli masz skrypty wymagające modyfikacji w celu użycia nowych poleceń cmdlet współpracujących z usługą Azure Resource Manager, zobacz temat [Migrowanie do narzędzi programistycznych opartych na usłudze Azure Resource Manager w celu obsługi klastrów usługi HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md), aby uzyskać więcej informacji.

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

## <a name="create-cluster-configuration-object"></a>Tworzenie klastra: obiekt konfiguracji

Można również utworzyć HDInsight konfiguracji obiekt przy użyciu `New-AzureRmHDInsightClusterConfig` polecenia cmdlet. Następnie można zmodyfikować ten obiekt konfiguracji, aby włączyć dodatkowe opcje konfiguracji dla klastra. Na koniec użyj `-Config` parametru `New-AzureRmHDInsightCluster` polecenia cmdlet do korzystania z konfiguracji.

Poniższy skrypt tworzy obiekt konfiguracji, aby skonfigurować serwer R Server na typ klastra HDInsight. Konfiguracja umożliwia węzeł krawędzi, RStudio i dodatkowe konto magazynu.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-98)]

> [!WARNING]
> Za pomocą konta magazynu w innej lokalizacji niż klaster HDInsight nie jest obsługiwana. Korzystając z tego przykładu, należy utworzyć dodatkowe konto magazynu w tej samej lokalizacji co serwer.

## <a name="customize-clusters"></a>Dostosowywanie klastrów

* Zobacz [HDInsight Dostosowywanie klastrów za pomocą narzędzia Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Kolejne kroki

Teraz, że udało Ci się utworzyć klaster usługi HDInsight, użyj następujących zasobów, aby dowiedzieć się, jak pracować z klastrem.

### <a name="hadoop-clusters"></a>Klastry usługi Hadoop

* [Korzystanie z programu Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą HDInsight](hadoop/hdinsight-use-pig.md)
* [Używanie technologii MapReduce z HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Klastry baz danych HBase

* [Wprowadzenie do usługi HBase w usłudze HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Opracowywanie aplikacji w języku Java dla bazy danych HBase na HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Klastry STORM

* [Opracowywanie topologii języka Java dla systemu Storm w HDInsight](storm/apache-storm-develop-java-topology.md)
* [Użyj języka Python składników systemu Storm w HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii z systemu Storm w HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Klastry Spark

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](spark/apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Spark przy użyciu programu Livy](spark/apache-spark-livy-rest-interface.md)
* [Platforma Spark i analiza biznesowa: interakcyjna analiza danych na platformie Spark w usłudze HDInsight z użyciem narzędzi do analizy biznesowej](spark/apache-spark-use-bi-tools.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](spark/apache-spark-machine-learning-mllib-ipython.md)

