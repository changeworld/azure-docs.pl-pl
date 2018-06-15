---
title: Tworzenie klastrów Hadoop przy użyciu programu PowerShell - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć klastry Hadoop, HBase, Storm i Spark w systemie Linux dla usługi HDInsight przy użyciu programu Azure PowerShell.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4208deca-d64a-45e1-8948-2673d5d7678c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 57ae7c3cab6c0084bb43585f65d79ae6cc265356
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400456"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Tworzenie klastrów z systemem Linux w usłudze HDInsight przy użyciu programu Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Program Azure PowerShell jest zaawansowane środowisko obsługi skryptów, który służy do kontrolowania i automatyzowania wdrażania i zarządzania obciążeń w systemie Microsoft Azure. Ten dokument zawiera informacje o sposobie tworzenia klastra usługi HDInsight opartej na systemie Linux przy użyciu programu Azure PowerShell. Zawiera również przykład skryptu.

> [!NOTE]
> Program Azure PowerShell jest dostępny tylko na komputerach klienckich z systemem Windows. Jeśli używasz klienta systemu Linux, Unix lub Mac OS X, zobacz [tworzenia klastra usługi HDInsight opartej na systemie Linux przy użyciu interfejsu wiersza polecenia Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) informacji o przy użyciu wiersza polecenia platformy Azure, aby utworzyć klaster.

## <a name="prerequisites"></a>Wymagania wstępne
Musi mieć następujące przed rozpoczęciem tej procedury:

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-azurerm-ps)

    > [!IMPORTANT]
    > Obsługa programu Azure PowerShell do celów zarządzania zasobami usługi HDInsight przy użyciu usługi Azure Service Manager jest **przestarzała** i została usunięta z dniem 1 stycznia 2017 r. W czynnościach opisanych w niniejszym dokumencie są używane nowe polecenia cmdlet usługi HDInsight współpracujące z usługą Azure Resource Manager.
    >
    > Wykonaj kroki opisane w [instalacji programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) do zainstalowania najnowszej wersji programu Azure PowerShell. Jeśli masz skrypty wymagające modyfikacji w celu użycia nowych poleceń cmdlet współpracujących z usługą Azure Resource Manager, zobacz temat [Migrowanie do narzędzi programistycznych opartych na usłudze Azure Resource Manager w celu obsługi klastrów usługi HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md), aby uzyskać więcej informacji.

## <a name="create-cluster"></a>Tworzenie klastra

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Aby utworzyć klaster usługi HDInsight przy użyciu programu Azure PowerShell, należy wykonać następujące procedury:

* Tworzenie grupy zasobów platformy Azure
* Tworzenie konta usługi Azure Storage
* Tworzenie kontenera obiektów Blob platformy Azure
* Tworzenie klastra usługi HDInsight

Poniższy skrypt pokazuje, jak utworzyć nowy klaster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Wartości określone dla logowania do klastra są używane do utworzenia konta użytkownika platformy Hadoop dla klastra. Podłącz do usług hostowanych w klastrze, takich jak web UI lub interfejsów API REST za pomocą tego konta.

Wartości określone dla użytkownika SSH są używane do utworzenia użytkownika SSH dla klastra. Aby uruchomić sesję zdalną SSH w klastrze i uruchom zadania, należy użyć tego konta. Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Jeśli planujesz użyć więcej niż 32 węzłami procesów roboczych (lub podczas tworzenia klastra przez skalowanie klastra po utworzeniu), należy również określić rozmiaru węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM.
>
> Aby uzyskać więcej informacji na węzeł rozmiary i koszty, zobacz [cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Może upłynąć do 20 minut na utworzenie klastra.

## <a name="create-cluster-configuration-object"></a>Tworzenie klastra: obiekt konfiguracji

Można również utworzyć HDInsight konfiguracji obiektów przy użyciu `New-AzureRmHDInsightClusterConfig` polecenia cmdlet. Następnie można zmodyfikować ten obiekt konfiguracji, aby włączyć dodatkowe opcje konfiguracji klastra. Na koniec użyj `-Config` parametr `New-AzureRmHDInsightCluster` polecenia cmdlet do korzystania z konfiguracji.

Poniższy skrypt tworzy obiekt konfiguracji, aby skonfigurować serwer R na typ klastra usługi HDInsight. Konfiguracja umożliwia węzeł krawędzi, programu RStudio i konta dodatkowego miejsca do magazynowania.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-98)]

> [!WARNING]
> Używanie konta magazynu w innej lokalizacji niż klastra usługi HDInsight nie jest obsługiwane. Korzystając z tego przykładu, należy utworzyć konto dodatkowego magazynu w tej samej lokalizacji co serwer.

## <a name="customize-clusters"></a>Dostosowywanie klastrów

* Zobacz [HDInsight dostosować klastry za pomocą początkowego](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Zobacz [HDInsight dostosować klastry za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Kolejne kroki

Teraz, że pomyślnie utworzono klaster usługi HDInsight, użyj następujących zasobów, aby dowiedzieć się, jak pracować z klastra.

### <a name="hadoop-clusters"></a>Klastry Hadoop

* [Korzystanie z programu Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą HDInsight](hadoop/hdinsight-use-pig.md)
* [Korzystać z usługi MapReduce z usługą HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Klastrów HBase

* [Wprowadzenie do usługi HBase w usłudze HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Tworzenie aplikacji Java bazy danych hbase w usłudze HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Klastry STORM

* [Tworzenie topologii Java dla Storm w usłudze HDInsight](storm/apache-storm-develop-java-topology.md)
* [Użyj składników języka Python w Storm w usłudze HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii z systemu Storm w usłudze HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Klastry Spark

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](spark/apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Spark przy użyciu programu Livy](spark/apache-spark-livy-rest-interface.md)
* [Platforma Spark i analiza biznesowa: interakcyjna analiza danych na platformie Spark w usłudze HDInsight z użyciem narzędzi do analizy biznesowej](spark/apache-spark-use-bi-tools.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](spark/apache-spark-machine-learning-mllib-ipython.md)

