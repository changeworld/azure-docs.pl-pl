---
title: Co to jest zapytanie interaktywne w usłudze Azure HDInsight?
description: Wprowadzenie do interaktywnej kwerendy, zwanej również Apache Hive LLAP, w usłudze Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: e133e08e333cb478269a93cce963566e195d6949
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78271951"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Co to jest zapytanie interaktywne w usłudze Azure HDInsight

Interakcyjne zapytanie (nazywane również Apache Hive LLAP lub [Low Latency Analytical Processing](https://cwiki.apache.org/confluence/display/Hive/LLAP)) jest [typem klastra](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type)usługi Azure HDInsight. Interakcyjne zapytanie obsługuje buforowanie w pamięci, co sprawia, że apache hive zapytania szybciej i znacznie bardziej interaktywne. Klienci używają zapytania interaktywnego do wykonywania zapytań o dane przechowywane w magazynie platformy Azure & usługi Azure Data Lake Storage w bardzo szybki sposób. Interaktywne zapytanie ułatwia deweloperom i analitykowi danych pracę z dużymi zbiorami danych przy użyciu narzędzi analizy biznesowej, które najbardziej kochają. Interaktywne zapytanie HDInsight obsługuje kilka narzędzi umożliwiających łatwy dostęp do dużych zbiorów danych.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Klaster zapytań interaktywnych różni się od klastra Apache Hadoop. Zawiera tylko usługę Hive.

Dostęp do usługi Hive można uzyskać w klastrze zapytań interaktywnych tylko za pośrednictwem apache Ambari Hive View, Beeline i microsoft hive open database connectivity driver (Hive ODBC). Nie można uzyskać do niego dostępu za pośrednictwem konsoli hive, Templeton, interfejsu wiersza polecenia klasycznego platformy Azure lub programu Azure PowerShell.

## <a name="create-an-interactive-query-cluster"></a>Tworzenie interaktywnego klastra zapytań

Aby uzyskać informacje dotyczące tworzenia klastra HDInsight, zobacz [Tworzenie klastrów Apache Hadoop w udziale usługi HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Wybierz typ klastra kwerendinterakcyjnych.

> [!IMPORTANT]
> Minimalny rozmiar węzła czołowego dla klastrów zapytań interaktywnych jest Standard_D13_v2. Aby uzyskać więcej informacji, zobacz [wykres rozmiaru maszyny wirtualnej platformy Azure.](../../cloud-services/cloud-services-sizes-specs.md#dv2-series)

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Wykonywanie zapytań gałęzi Apache z kwerendinterakcyjnych

Aby wykonać zapytania hive, dostępne są następujące opcje:

|Metoda |Opis |
|---|---|
|Microsoft Power BI|Zobacz [Wizualizacja danych gałęzi apache aplikacji Interactive Query za pomocą usługi Power BI w usłudze Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md)i [wizualizuj duże zbiory danych za pomocą usługi Power BI w usłudze Azure HDInsight.](../hadoop/apache-hadoop-connect-hive-power-bi.md)|
|Visual Studio|Zobacz [Łączenie się z usługą Azure HDInsight i uruchamianie zapytań gałąź apache przy użyciu narzędzi usługi Data Lake Tools for Visual Studio.](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries)|
|Visual Studio Code|Zobacz [Używanie kodu programu Visual Studio dla gałęzi apache, LLAP lub pySpark](../hdinsight-for-vscode.md).|
|Widok ula Apache Ambari|Zobacz [Korzystanie z widoku gałęzi Apache z apache Hadoop w usłudze Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md). Widok gałęzi nie jest dostępny dla hdinsight 4.0.|
|Apache Beeline|Zobacz [Korzystanie z ula Apache z Apache Hadoop w hdinsight z Beeline](../hadoop/apache-hadoop-use-hive-beeline.md). Funkcji Beeline można użyć z węzła głównego lub z pustego węzła krawędzi. Zalecamy użycie Beeline z pustego węzła krawędziowego. Aby uzyskać informacje dotyczące tworzenia klastra HDInsight przy użyciu pustego węzła krawędziowego, zobacz [Używanie pustych węzłów krawędzi w umiaśnieniu HDInsight](../hdinsight-apps-use-edge-node.md).|
|Ul ODBC|Zobacz [Łączenie programu Excel z apache Hadoop ze sterownikiem Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).|

Aby znaleźć parametry połączenia java database connectivity (JDBC):

1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`przejdź `CLUSTERNAME` do miejsca , gdzie jest nazwa klastra.
1. Aby skopiować adres URL, wybierz ikonę schowka:

   ![Interaktywne zapytanie HDInsight Hadoop LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [tworzyć klastry zapytań interaktywnych w programie HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Dowiedz się, jak [wizualizować duże zbiory danych za pomocą usługi Power BI w usłudze Azure HDInsight.](../hadoop/apache-hadoop-connect-hive-power-bi.md)
* Dowiedz się, jak [używać apache zeppelin do uruchamiania zapytań apache hive w usłudze Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
