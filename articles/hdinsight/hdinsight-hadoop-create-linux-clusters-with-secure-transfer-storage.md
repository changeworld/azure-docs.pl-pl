---
title: Apache Hadoop & bezpiecznego magazynu — Azure HDInsight
description: Dowiedz się, jak tworzyć klastry usługi HDInsight z kontami magazynu platformy Azure z bezpiecznym transferem.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/24/2018
ms.openlocfilehash: b8a5d48e977009cf592f271760232a3b0138c8c5
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044960"
---
# <a name="create-apache-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Tworzenie klastra Apache Hadoop z kontami magazynu Secure transfer w usłudze Azure HDInsight

Funkcja [Wymagany bezpieczny transfer](../storage/common/storage-require-secure-transfer.md) poprawia bezpieczeństwo konta usługi Azure Storage poprzez wymuszanie kierowania wszystkich zapytań do konta przez zabezpieczone połączenie. Funkcja ta oraz schemat wasbs są obsługiwane tylko w klastrze usługi HDInsight w wersji 3.6 lub nowszym.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego artykułu musisz dysponować:

* **Subskrypcja platformy Azure**: aby utworzyć bezpłatne konto próbne na jeden miesiąc, przejdź do [azure.microsoft.com/free](https://azure.microsoft.com/free).
* **Konto usługi Azure Storage z włączonym bezpiecznym transferem**. Aby uzyskać instrukcje, zobacz [Tworzenie konta magazynu](../storage/common/storage-quickstart-create-account.md) oraz [Wymaganie bezpiecznego transferu](../storage/common/storage-require-secure-transfer.md).
* **Kontener obiektów blob na koncie magazynu**.

## <a name="create-cluster"></a>Tworzenie klastra

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

W tej sekcji tworzysz klaster Hadoop w usłudze HDInsight przy użyciu [szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). Szablon znajduje się w serwisie [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/). Znajomość szablonów usługi Resource Manager nie jest wymagana do korzystania z tego artykułu. Aby poznać inne metody tworzenia klastrów i zrozumieć właściwości używane w tym artykule, zobacz [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Kliknij poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon usługi Resource Manager w witrynie Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Postępuj zgodnie z instrukcjami, aby utworzyć klaster o następujących specyfikacjach: 

    - Określ wersję usługi HDInsight na 3.6. Wymagana jest wersja 3.6 lub nowsza.
    - Określ konto magazynu z włączonym bezpiecznym transferem.
    - Użyj krótkiej nazwy konta magazynu.
    - Konto magazynu i kontener obiektów blob należy utworzyć wcześniej.

      Aby uzyskać instrukcje, zobacz [Tworzenie klastra](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

Jeśli użyjesz akcji skryptu do udostępnienia własnych plików konfiguracyjnych, musisz użyć rozwiązania wasbs w następujących ustawieniach:

- fs.defaultFS (lokacja podstawowa)
- spark.eventLog.dir 
- spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>Dodawanie kolejnych kont magazynu

Dostępnych jest kilka opcji dodawania kolejnych kont magazynu z bezpiecznym transferem:

- Zmodyfikuj szablon usługi Azure Resource Manager w ostatniej sekcji.
- Utwórz klaster przy użyciu witryny [Azure Portal](https://portal.azure.com) i określ połączone konto magazynu.
- Użyj akcji skryptu, aby dodać kolejne konta magazynu z bezpiecznym transferem do istniejącego klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Dodawanie kolejnych kont magazynu do usługi HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób tworzenia klastra usługi HDInsight i włączania bezpiecznego transferu do kont magazynu.

Aby dowiedzieć się więcej na temat analizowania danych za pomocą usługi HDInsight, zobacz następujące artykuły:

* Aby dowiedzieć się więcej o korzystaniu z [Apache Hive](https://hive.apache.org/) z usługą HDInsight, w tym o tym, jak wykonywać zapytania Hive z programu Visual Studio, zobacz [Korzystanie z Apache Hive z usługą HDInsight][hdinsight-use-hive].
* Aby dowiedzieć się więcej o programie [Apache wieprz](https://pig.apache.org/), języku używanym do przekształcania danych, zobacz [Korzystanie z Apache świni z usługą HDInsight][hdinsight-use-pig].
* Aby dowiedzieć się więcej o [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), sposób pisania programów przetwarzających dane w usłudze Hadoop, zobacz [Używanie Apache Hadoop MapReduce z usługą HDInsight][hdinsight-use-mapreduce].
* Aby dowiedzieć się więcej o korzystaniu z narzędzi HDInsight Tools for Visual Studio do analizowania danych w usłudze HDInsight, zobacz [Rozpoczynanie pracy z narzędziami Apache Hadoop programu Visual Studio dla usługi HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Aby dowiedzieć się więcej o sposobie przechowywania danych przez usługę HDInsight lub sposobie przesyłania danych do usługi HDInsight, zobacz następujące artykuły:

* Aby uzyskać informacje o sposobie używania usługi Azure Storage przez usługę HDInsight, zobacz [Używanie usługi Azure Storage z usługą HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Aby uzyskać informacje na temat przekazywania danych do usługi HDInsight, zobacz [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data].

Aby dowiedzieć się więcej o tworzeniu klastra usługi HDInsight i zarządzaniu nim, zobacz następujące artykuły:

* Aby uzyskać więcej informacji na temat zarządzania opartym na systemie Linux klastrem usługi HDInsight, zobacz artykuł [Zarządzanie klastrami usługi HDInsight za pomocą narzędzia Apache Ambari](hdinsight-hadoop-manage-ambari.md).
* Aby dowiedzieć się więcej na temat opcji, które można wybrać podczas tworzenia klastra usługi HDInsight, zobacz [Tworzenie klastra usługi HDInsight w systemie Linux przy użyciu niestandardowych opcji](hdinsight-hadoop-provision-linux-clusters.md).
* Jeśli znasz system Linux i Apache Hadoop, ale chcesz poznać szczegóły dotyczące platformy Hadoop w usłudze HDInsight, zobacz [Praca z usługą HDInsight w systemie Linux](hdinsight-hadoop-linux-information.md). Artykuł zawiera następujące informacje:

  * Adresy URL usług hostowanych w klastrze, takie jak [Apache Ambari](https://ambari.apache.org/) i [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)
  * Lokalizacja [Apache Hadoop](https://hadoop.apache.org/) plików i przykładów w lokalnym systemie plików
  * Korzystanie z usługi Azure Storage (WASB) zamiast [Apache HADOOP HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) jako domyślnego magazynu danych

[1]: ../HDInsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
