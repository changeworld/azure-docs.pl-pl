---
title: Używanie zapytania interakcyjnego za pomocą usługi Azure HDInsight
description: Dowiedz się, jak używać zapytania interakcyjnego (LLAP programu Hive) z HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: 9636157182e8b40914bde2515c5b295d0480255a
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510997"
---
# <a name="use-interactive-query-with-hdinsight"></a>Używanie zapytania interakcyjnego z HDInsight
Zapytania interakcyjnego (nazywane również LLAP programu Hive Apache lub [Niskie opóźnienie analitycznego przetwarzania](https://cwiki.apache.org/confluence/display/Hive/LLAP)) jest usługi Azure HDInsight [typ klastra](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). Zapytanie interakcyjne obsługuje buforowanie w pamięci, co sprawia, że zapytania usługi Apache Hive szybsze i bardziej interaktywny.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Klaser zapytania interaktywnego różni się od klastra Apache Hadoop. Zawiera on tylko usługi Hive. 

> [!NOTE]  
> Możesz uzyskać dostęp do usługi Hive, w to klastra zapytania interakcyjnego tylko przy użyciu widoku Hive narzędzia Ambari Apache, z usługi Beeline i sterownika Microsoft Hive Open Database Connectivity (ODBC programu Hive). Nie masz dostępu do jego za pośrednictwem konsoli programu Hive, Templeton, klasyczny interfejs wiersza polecenia platformy Azure lub programu Azure PowerShell. 

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
* Dowiedz się, jak [uruchomić zapytania usługi Apache Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin Apache](../hdinsight-connect-hive-zeppelin.md).
* Dowiedz się, jak [uruchomienia zapytania usługi Apache Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).
* Dowiedz się, jak [użycia narzędzi HDInsight Tools for Visual Studio Code](../hdinsight-for-vscode.md).
* Dowiedz się, jak [Apache Hive View za pomocą technologii Apache Hadoop w HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md)
* Dowiedz się, jak [korzystanie z usługi Beeline można przesłać zapytania usługi Apache Hive w HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md).
* Dowiedz się, jak [łączenie programu Excel do platformy Apache Hadoop przy użyciu sterownika Hive ODBC firmy Microsoft](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

