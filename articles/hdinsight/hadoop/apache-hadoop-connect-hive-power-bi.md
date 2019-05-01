---
title: Wizualizuj dane Apache Hive z usługą Power BI — Azure HDInsight
description: Dowiedz się, jak usługa Microsoft Power BI w celu wizualizacji danych Hive przetwarzanych przez usługi Azure HDInsight.
keywords: hdinsight,hadoop,hive,interactive query,interactive hive,LLAP,odbc
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: hrasheed
ms.openlocfilehash: ac5ca3d9501718cd5b538f6bb8c1fafee78063b2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64695232"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Wizualizuj dane Apache Hive z usługą Microsoft Power BI w usłudze Azure HDInsight przy użyciu interfejsu ODBC

Dowiedz się, jak nawiązać połączenie usługi Azure HDInsight przy użyciu interfejsu ODBC usługi Microsoft Power BI i wizualizuj dane z Apache Hive.

>[!IMPORTANT]
> Można wykorzystać sterownika ODBC programu Hive można zaimportować za pomocą ogólnego łącznika ODBC w programie Power BI Desktop. Jednak nie zaleca dla obciążeń analizy Biznesowej ze względu na charakter nieinterakcyjnych aparatu zapytań programu Hive. [Łącznik interaktywnego zapytania HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) i [łącznika usługi HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) są lepsze możliwości ich wydajność.

W tym samouczku załadujesz dane z tabeli programu Hive hivesampletable do usługi Power BI. W tej tabeli zawiera pewne dane użycia telefonu komórkowego. Następnie można wykreślić dane użycia na mapę świata:

![HDInsight usługi Power BI raport mapy](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Informacje dotyczą również nowy [Interactive Query](../interactive-query/apache-interactive-query-get-started.md) typ klastra. Jak nawiązać połączenie z interaktywnego zapytania HDInsight, za pomocą zapytania bezpośredniego, zobacz [danych wizualizacji interaktywne zapytania programu Hive z usługą Microsoft Power BI za pomocą zapytania bezpośredniego w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).



## <a name="prerequisites"></a>Wymagania wstępne
Przed przejściem w tym artykule, należy dysponować następującymi elementami:

* **Klaster HDInsight**. Klaster może być klastra HDInsight z programu Hive lub nowo wydanego klastra zapytania interakcyjnego. W celu tworzenia klastrów, zobacz [Utwórz klaster](apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Istnieje możliwość pobrania kopii z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Tworzenie źródła danych ODBC usługi Hive

Zobacz [źródła danych ODBC programu Hive na tworzenie](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Ładowanie danych z HDInsight

W tej tabeli hivesampletable jest powiązana z wszystkimi klastrami HDInsight.

1. Zaloguj się do usługi Power BI Desktop.
2. Kliknij przycisk **Home** kliknij pozycję **Pobierz dane** z **dane zewnętrzne** wstążki, a następnie wybierz **więcej**.

    ![Otwórz dane HDInsight usługi Power BI](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)
3. Z **Pobierz dane** okienku kliknij **innych** z lewej strony, kliknij przycisk **ODBC** z prawej strony, a następnie kliknij przycisk **Connect** na dole.
4. Z **z ODBC** okienku wybierz dane utworzone w ostatniej sekcji Nazwa źródła, a następnie kliknij **OK**.
5. Z **Nawigator** okienku rozwiń **ODBC -> HIVE -> domyślny**, wybierz opcję **hivesampletable**, a następnie kliknij przycisk **obciążenia**.

## <a name="visualize-data"></a>Wizualizowanie danych

Kontynuuj w poprzedniej procedurze.

1. W okienku wizualizacje wybierz **mapy**.  Jest ikona globu.

    ![HDInsight usługi Power BI dostosowuje raportu](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. W okienku pola wybierz **kraju** i **devicemake**. Widać danych wykreślić na mapie.
3. Rozwiń mapę.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób wizualizować dane z HDInsight przy użyciu usługi Power BI.  Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Uruchamianie zapytania usługi Apache Hive w usłudze Azure HDInsight za pomocą rozwiązania Zeppelin Apache](./../hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel z HDInsight przy użyciu sterownika ODBC programu Hive z programu Microsoft](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel do platformy Apache Hadoop za pomocą dodatku Power Query](apache-hadoop-connect-excel-power-query.md).
* [Nawiązywanie połączenia usługi Azure HDInsight i uruchomić zapytania usługi Apache Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Narzędzie Azure HDInsight dla programu Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do HDInsight](./../hdinsight-upload-data.md).
