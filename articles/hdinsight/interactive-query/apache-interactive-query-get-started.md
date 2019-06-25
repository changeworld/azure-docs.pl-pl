---
title: Co to jest interakcyjnych zapytań w usłudze Azure HDInsight?
description: Wprowadzenie do interakcyjnego zapytania w usłudze Azure HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 06/14/2019
ms.openlocfilehash: ea17ddeab21c371f41cc57115df4dd91277c3c42
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151195"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Co to jest interakcyjne zapytania w usłudze Azure HDInsight

Zapytania interakcyjnego (nazywane również LLAP programu Hive Apache lub [Niskie opóźnienie analitycznego przetwarzania](https://cwiki.apache.org/confluence/display/Hive/LLAP)) jest usługi Azure HDInsight [typ klastra](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). Zapytanie interakcyjne obsługuje buforowanie w pamięci, co sprawia, że zapytania usługi Apache Hive szybsze i bardziej interaktywny. Aby zapytania o dane przechowywane w usłudze Azure storage i usługi Azure Data Lake Storage w sposób superszybkiego, klienci korzystają interakcyjnych zapytań. Zapytanie interakcyjne ułatwia analityk deweloperów i danych do pracy z danymi big data przy użyciu swoich ulubionych narzędzi do analizy Biznesowej najczęściej. Zapytanie interakcyjne HDInsight obsługuje kilka narzędzi, aby uzyskać dostęp do danych big Data w łatwy sposób.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Klaser zapytania interaktywnego różni się od klastra Apache Hadoop. Zawiera on tylko usługi Hive.

Możesz uzyskać dostęp do usługi Hive, w to klastra zapytania interakcyjnego tylko przy użyciu widoku Hive narzędzia Ambari Apache, z usługi Beeline i sterownika Microsoft Hive Open Database Connectivity (ODBC programu Hive). Nie masz dostępu do jego za pośrednictwem konsoli programu Hive, Templeton, klasyczny interfejs wiersza polecenia platformy Azure lub programu Azure PowerShell.

## <a name="create-an-interactive-query-cluster"></a>Utwórz klaser zapytania interaktywnego

Aby dowiedzieć się, jak tworzenie klastra HDInsight, zobacz [klastrów utworzyć Apache Hadoop w HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Wybierz typ klastra zapytania interakcyjnego.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Wykonywanie zapytania usługi Apache Hive z zapytaniem interakcyjnym

Aby wykonać zapytania programu Hive, dostępne są następujące opcje:

* Użyj usługi Microsoft Power BI

    Zobacz [danych wizualizacji interaktywnych zapytań technologii Hive z usługą Power BI w usłudze Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) zobacz [wizualizowanie danych big data dzięki usłudze Power BI w usłudze Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).

* Korzystanie z programu Visual Studio

    Zobacz [nawiązywanie połączenia z usługi Azure HDInsight i uruchamianie zapytań technologii Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).

* Use Visual Studio Code

    Zobacz [Użyj programu Visual Studio Code dla Apache Hive, LLAP lub pySpark](../hdinsight-for-vscode.md).
* Uruchom Apache Hive, korzystając z widoku Hive narzędzia Ambari Apache.
  
    Zobacz [Apache Hive widoku za pomocą technologii Apache Hadoop w usłudze Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).

* Uruchom Apache Hive, korzystając z usługi Beeline.
  
    Zobacz [Apache Hive za pomocą technologii Apache Hadoop w HDInsight z usługą Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Korzystania z usługi Beeline z głównego węzła lub pustego węzła krawędzi. Firma Microsoft zaleca korzystanie z usługi Beeline od pustego węzła krawędzi. Aby uzyskać informacje dotyczące tworzenia klastra usługi HDInsight za pomocą pustego węzła krawędzi, zobacz [używanie pustych węzłów brzegowych w HDInsight](../hdinsight-apps-use-edge-node.md).
* Uruchom Apache Hive, przy użyciu programu Hive ODBC.
  
    Zobacz [Connect Excel to Apache Hadoop przy użyciu sterownika Hive ODBC firmy Microsoft](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Aby znaleźć parametry połączenia łączność bazy danych języka Java (JDBC):

1. Zaloguj się do systemu Apache Ambari, korzystając z następującego adresu URL: `https://<cluster name>.AzureHDInsight.net`.
2. W menu po lewej stronie wybierz **Hive**.
3. Aby skopiować adres URL, wybierz ikonę Schowka:

   ![JDBC LLAP interakcyjnych zapytań usługi HDInsight Hadoop](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [tworzenie klastrami interakcyjnych zapytań w HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Dowiedz się, jak [wizualizowanie danych big data dzięki usłudze Power BI w usłudze Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Dowiedz się, jak [uruchomić zapytania usługi Apache Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin Apache](../interactive-query/hdinsight-connect-hive-zeppelin.md).
