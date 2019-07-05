---
title: Wizualizuj dane Apache Hive z usługą Power BI — Azure HDInsight
description: Dowiedz się, jak usługa Microsoft Power BI w celu wizualizacji danych Hive przetwarzanych przez usługi Azure HDInsight.
keywords: hdinsight,hadoop,hive,interactive query,interactive hive,LLAP,odbc
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: hrasheed
ms.openlocfilehash: 69353968f6b38f0d16b68c58b9b00c3e6d45850b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446868"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Wizualizuj dane Apache Hive z usługą Microsoft Power BI w usłudze Azure HDInsight przy użyciu interfejsu ODBC

Dowiedz się, jak połączyć program Microsoft Power BI Desktop z usługi Azure HDInsight przy użyciu interfejsu ODBC i wizualizuj dane z Apache Hive.

>[!IMPORTANT]
> Można wykorzystać sterownika ODBC programu Hive można zaimportować za pomocą ogólnego łącznika ODBC w programie Power BI Desktop. Jednak nie zaleca dla obciążeń analizy Biznesowej ze względu na charakter nieinterakcyjnych aparatu zapytań programu Hive. [Łącznik interaktywnego zapytania HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) i [łącznika usługi HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) są lepsze możliwości ich wydajność.

W tym artykule możesz załadować dane z `hivesampletable` tabelę programu Hive w usłudze Power BI. W tej tabeli zawiera pewne dane użycia telefonu komórkowego. Następnie można wykreślić dane użycia na mapę świata:

![HDInsight usługi Power BI raport mapy](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Informacje dotyczą również nowy [Interactive Query](../interactive-query/apache-interactive-query-get-started.md) typ klastra. Jak nawiązać połączenie z interaktywnego zapytania HDInsight, za pomocą zapytania bezpośredniego, zobacz [danych wizualizacji interaktywne zapytania programu Hive z usługą Microsoft Power BI za pomocą zapytania bezpośredniego w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed przejściem w tym artykule, należy dysponować następującymi elementami:

* **Klaster HDInsight**. Klaster może być klastra HDInsight z programu Hive lub nowo wydanego klastra zapytania interakcyjnego. W celu tworzenia klastrów, zobacz [Utwórz klaster](apache-hadoop-linux-tutorial-get-started.md#create-cluster).

* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** . Istnieje możliwość pobrania kopii z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Tworzenie źródła danych ODBC usługi Hive

Zobacz [źródła danych ODBC programu Hive na tworzenie](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Ładowanie danych z HDInsight

W tej tabeli hivesampletable jest powiązana z wszystkimi klastrami HDInsight.

1. Uruchom program Power BI Desktop.

2. Z górnego menu, przejdź do **Home** > **Pobierz dane** > **więcej...** .

    ![Otwórz dane HDInsight usługi Power BI](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

3. Z **Pobierz dane** okno dialogowe, wybierz opcję **innych** z lewej strony, wybierz **ODBC** z prawej strony, a następnie wybierz pozycję **Connect** w dolnej.

4. Z **z ODBC** okno dialogowe, wybierz źródło danych nazwę utworzoną w ostatniej sekcji z listy rozwijanej i następnie wybierz pozycję **OK**.

5. Z **Nawigator** okno dialogowe, rozwiń **ODBC > HIVE > domyślne**, wybierz pozycję **hivesampletable**, a następnie wybierz pozycję **obciążenia**.

6. Z **sterownika ODBC** okno dialogowe, wybierz opcję **domyślny lub niestandardowy**, a następnie wybierz **Connect**.

## <a name="visualize-data"></a>Wizualizowanie danych

Kontynuuj w poprzedniej procedurze.

1. W okienku wizualizacje wybierz **mapy**.  Jest ikona globu.

    ![HDInsight usługi Power BI dostosowuje raportu](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. Z **pola** okienku wybierz **kraju** i **devicemake**. Widać danych wykreślić na mapie.
3. Rozwiń mapę.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób wizualizować dane z HDInsight przy użyciu usługi Power BI.  Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Uruchamianie zapytania usługi Apache Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin Apache](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel z HDInsight przy użyciu sterownika ODBC programu Hive z programu Microsoft](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel do platformy Apache Hadoop za pomocą dodatku Power Query](apache-hadoop-connect-excel-power-query.md).
* [Nawiązywanie połączenia usługi Azure HDInsight i uruchomić zapytania usługi Apache Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do HDInsight](./../hdinsight-upload-data.md).
