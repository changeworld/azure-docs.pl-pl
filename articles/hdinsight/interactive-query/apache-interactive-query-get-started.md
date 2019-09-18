---
title: Co to jest zapytanie interaktywne w usłudze Azure HDInsight?
description: Wprowadzenie do interaktywnego zapytania, nazywanego również Apache Hive LLAP, w usłudze Azure HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 09/17/2019
ms.openlocfilehash: 3e0fddc03ad880d36876276827198b0ac180a00d
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076361"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Co to jest zapytanie interaktywne w usłudze Azure HDInsight

Zapytanie interaktywne (nazywane również Apache Hive LLAP lub [przetwarzania analitycznego o małym opóźnieniu](https://cwiki.apache.org/confluence/display/Hive/LLAP)) to [Typ klastra](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types)usługi Azure HDInsight. Zapytanie interaktywne obsługuje buforowanie w pamięci, co sprawia, że Apache Hive zapytań szybciej i bardziej interaktywnie. Klienci używają interakcyjnego zapytania do wykonywania zapytań dotyczących danych przechowywanych w usłudze Azure Storage & Azure Data Lake Storage w sposób bardzo szybki. Zapytanie interaktywne ułatwia deweloperom i analitykom danych współpracują z danymi Big Data przy użyciu narzędzi do analizy biznesowej, które są najbardziej największe. Zapytanie interaktywne usługi HDInsight obsługuje kilka narzędzi, które ułatwiają uzyskiwanie dostępu do danych Big Data.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Interaktywny klaster zapytań różni się od klastra Apache Hadoop. Zawiera tylko usługę Hive.

Można uzyskać dostęp do usługi Hive w klastrze zapytań interakcyjnych tylko za pośrednictwem platformy Apache Ambari Hive View, Z usługi Beeline i sterownika Open Database Connectivity Hive firmy Microsoft (Hive ODBC). Nie możesz uzyskać do niego dostępu za pośrednictwem konsoli programu Hive, Templeton, klasycznego interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

## <a name="create-an-interactive-query-cluster"></a>Utwórz interaktywny klaster zapytań

Aby uzyskać informacje na temat tworzenia klastra usługi HDInsight, zobacz [Tworzenie klastrów Apache Hadoop w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Wybierz typ klastra zapytań interaktywnych.

> [!IMPORTANT]
> Minimalny rozmiar węzła głównego klastrów zapytań interaktywnych to Standard_D13_v2. Aby uzyskać więcej informacji, zobacz [Wykres z rozmiarem maszyny wirtualnej platformy Azure](../../cloud-services/cloud-services-sizes-specs.md#dv2-series).

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Wykonaj zapytania Apache Hive z zapytania interaktywnego

Aby wykonać zapytania programu Hive, dostępne są następujące opcje:

* Korzystanie z programu Microsoft Power BI

    Zobacz [wizualizacje interakcyjne zapytań Apache Hive danych za pomocą Power BI w usłudze Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) zobacz [wizualizowanie danych big data za pomocą Power BI w usłudze Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).

* Korzystanie z programu Visual Studio

    Zobacz [nawiązywanie połączenia z usługą Azure HDInsight i uruchamianie zapytań Apache Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).

* Use Visual Studio Code

    Zobacz [używanie Visual Studio Code dla Apache Hive, LLAP lub pySpark](../hdinsight-for-vscode.md).
* Uruchom Apache Hive przy użyciu widoku Hive usługi Apache Ambari.
  
    Zobacz [Używanie widoku Apache Hive z Apache Hadoop w usłudze Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).

* Uruchom Apache Hive przy użyciu Z usługi Beeline.
  
    Zobacz [używanie Apache Hive z Apache Hadoop w usłudze HDInsight z z usługi Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Można użyć Z usługi Beeline z węzła głównego lub pustego węzła krawędzi. Zalecamy używanie Z usługi Beeline z pustego węzła krawędzi. Aby uzyskać informacje dotyczące tworzenia klastra usługi HDInsight za pomocą pustego węzła krawędzi, zobacz [używanie pustych węzłów brzegowych w usłudze HDInsight](../hdinsight-apps-use-edge-node.md).
* Uruchom Apache Hive, używając ODBC programu Hive.
  
    Zobacz [łączenie programu Excel, aby Apache Hadoop za pomocą sterownika Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Aby znaleźć parametry połączenia z usługą Java Database Connectivity (JDBC):

1. Zaloguj się do usługi Apache Ambari, korzystając z następującego adresu `https://<cluster name>.AzureHDInsight.net`URL:.
2. W menu po lewej stronie wybierz pozycję **Hive**.
3. Aby skopiować adres URL, wybierz ikonę schowka:

   ![Zapytanie interaktywne usługi HDInsight Hadoop LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [tworzyć interaktywne klastry zapytań w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Dowiedz się, jak [wizualizować dane Big Data za pomocą Power BI w usłudze Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Dowiedz się, jak [używać oprogramowania Apache Zeppelin do uruchamiania zapytań Apache Hive w usłudze Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
