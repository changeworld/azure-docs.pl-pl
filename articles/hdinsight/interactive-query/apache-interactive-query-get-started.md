---
title: Interakcyjne zapytania za pomocą usługi Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć kwerendy interakcyjne (Hive LLAP) z usługą HDInsight.
keywords: ''
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: 9450f60b88c63e2cd818130521d67daf435f0b17
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31407109"
---
# <a name="use-interactive-query-with-hdinsight"></a>Użyj interaktywnego zapytania z usługą HDInsight
Interakcyjne zapytania (skrót Hive LLAP lub [Live długich i procesu](https://cwiki.apache.org/confluence/display/Hive/LLAP)) to Azure HDInsight [typ klastra](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). Interakcyjne Query obsługuje buforowanie w pamięci, co czyni szybsze i bardziej interakcyjnych zapytań programu Hive.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Klastra interakcyjne zapytanie różni się od klastra usługi Hadoop. Zawiera usługę Hive. 

> [!NOTE]
> Można uzyskać dostępu do usługi Hive w klastrze interakcyjne zapytania tylko przy użyciu widoku Hive narzędzia Ambari, Beeline i sterownik Microsoft Hive ODBC (ODBC programu Hive). Nie masz dostępu do go za pomocą konsoli programu Hive, Templeton, narzędzia wiersza polecenia platformy Azure (Azure CLI) lub Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-query-cluster"></a>Tworzenie klastra interakcyjne zapytania
Aby uzyskać informacje dotyczące tworzenia klastra usługi HDInsight, zobacz [klastrów utworzyć Hadoop w HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Wybierz typ zapytania interakcyjne klastra.

## <a name="execute-hive-queries-from-interactive-query"></a>Wykonywanie zapytań Hive z interaktywnego kwerendy
Do wykonywania zapytań programu Hive, masz następujące opcje:

* Przy użyciu usługi Power BI

    Zobacz [wizualizacji interakcyjne Hive zapytania danych za pomocą usługi Power BI w usłudze Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) zobacz [wizualizacji danych big data w usłudze Power BI w usłudze Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
 
* Korzystanie z rozwiązania Zeppelin

    Zobacz [Zeppelin używany do uruchamiania zapytań Hive w usłudze Azure HDInsight ](../hdinsight-connect-hive-zeppelin.md).

* Korzystanie z programu Visual Studio

    Zobacz [Podłącz Azure HDInsight i uruchamiania zapytań Hive przy użyciu narzędzi Data Lake Tools dla programu Visual Studio, aby](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).

* Użyj kodu programu Visual Studio

    Zobacz [Użyj Visual Studio Code Hive, LLAP lub pySpark](../hdinsight-for-vscode.md).
* Uruchom Hive za pomocą widoku Hive narzędzia Ambari.
  
    Zobacz [użyć widoku Hive z usługą Hadoop w usłudze Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).
* Uruchom gałęzi przy użyciu Beeline.
  
    Zobacz [korzystanie z programu Hive z usługą Hadoop w usłudze HDInsight z Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Możesz użyć Beeline z głównego węzła lub z węzłem krawędzi puste. Zalecamy używanie Beeline z węzłem krawędzi puste. Informacje o tworzeniu klastra usługi HDInsight przy użyciu węzła krawędzi pusta, zobacz [użycia węzłów pusty krawędzi w usłudze HDInsight](../hdinsight-apps-use-edge-node.md).
* Uruchom gałęzi przy użyciu Hive ODBC.
  
    Zobacz [łączenie programu Excel do platformy Hadoop za pomocą sterownika Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Aby znaleźć ciąg połączenia Java połączenia bazy danych (JDBC):

1. Zaloguj się do narzędzia Ambari, korzystając z następującego adresu URL: https://\<nazwy klastra\>. AzureHDInsight.net.
2. W menu po lewej stronie wybierz **Hive**.
3. Aby skopiować adres URL, wybierz ikonę Schowka:
   
   ![JDBC LLAP interakcyjne zapytania HDInsight Hadoop](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [Tworzenie klastrów interakcyjne zapytania w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Dowiedz się, jak [wizualizacji danych big data w usłudze Power BI w usłudze Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Dowiedz się, jak [umożliwia uruchamianie zapytań Hive w usłudze Azure HDInsight Zeppelin ](../hdinsight-connect-hive-zeppelin.md).
* Dowiedz się, jak [uruchamianie zapytań Hive przy użyciu narzędzi Data Lake Tools dla programu Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).
* Dowiedz się, jak [użycia narzędzi HDInsight Tools dla Visual Studio Code](../hdinsight-for-vscode.md).
* Dowiedz się, jak [użyć widoku Hive z usługą Hadoop w usłudze HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md)
* Dowiedz się, jak [umożliwia wysyłanie zapytań programu Hive w usłudze HDInsight Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
* Dowiedz się, jak [Uzyskuj dostęp do programu Excel do platformy Hadoop sterownik Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

